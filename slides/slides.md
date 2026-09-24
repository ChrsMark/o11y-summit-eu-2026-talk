---
theme: dracula
background: https://cover.sli.dev
title: From Schema to Shipping Data
class: text-center
transition: slide-left
mdc: true
layout: intro
fonts:
  sans: 'Inter'
  mono: 'Fira Code'
---

# From Schema to Shipping Data

<p class="intro-subtitle">Making OpenTelemetry Stable by Default</p>

<div class="intro-meta">
  <p class="intro-speakers">Christos Markou <span class="intro-org">(Elastic)</span> · Pablo Baeyens <span class="intro-org">(Datadog)</span></p>
</div>

<QrArrow />

<div class="kceu-logo-block">
  <img src="/observability-summit-eu-logo-color-2.svg" class="kceu-logo" />
  <span class="kceu-logo-year">2026</span>
</div>

<!-- PABLO

Welcome everyone, and thank you for joining this session.

Today we're going to talk about one of the most impactful challenges in the OpenTelemetry project right now: how do you make a project this large, this widely deployed, truly stable by default?

Not just "stable as in the project won't crash" — but stable as in: your users can upgrade with confidence that nothing will silently break.

I'm Pablo, and this is Christos. Let us introduce ourselves.

-->

---

# About us

<div class="speakers-grid">
  <div class="speaker">
    <img src="/chrismark.jpeg" class="speaker-pic" />
    <div class="speaker-name"><a href="https://github.com/ChrsMark">Christos Markou</a></div>
    <div class="speaker-org">Elastic</div>
    <div class="speaker-roles">
      <span>Principal Software Engineer</span>
      <span>OTel Collector Contrib Maintainer</span>
      <span>SemConv Approver (system, k8s, containers)</span>
      <span>CNCF Ambassador</span>
    </div>
  </div>
  <div class="speaker">
    <img src="/pablo.jpeg" class="speaker-pic" />
    <div class="speaker-name"><a href="https://github.com/mx-psi">Pablo Baeyens</a></div>
    <div class="speaker-org">Datadog</div>
    <div class="speaker-roles">
      <span>Senior Software Engineer</span>
      <span>OTel Collector Maintainer</span>
      <span>OpenTelemetry GC member</span>
    </div>
  </div>
</div>

<!-- PABLO/CHRISTOS

[PABLO] I'm Pablo — Senior Software Engineer at Datadog. I maintain the OpenTelemetry Collector and serve on the Governance Committee.

[CHRISTOS] And I'm Christos — Principal Software Engineer at Elastic. I maintain the Collector Contrib project and serve as a Semantic Conventions Approver for system, Kubernetes, and container metrics. I'm also a CNCF Ambassador.

-->

---

# What is OpenTelemetry?

<div class="otel-slide-wrap">
<div class="otel-big-icon"><img src="/otel-icon.png" class="otel-icon-main" /></div>
<div class="icon-grid otel-slide-bullets">
<carbon-chart-multitype v-click="1" class="icon" />
<span v-click="1">An <strong>observability framework</strong></span>
<carbon-document-multiple-01 v-click="2" class="icon" />
<span v-click="2">A set of <strong>specifications and implementations</strong> for observability</span>
<carbon-trophy v-click="3" class="icon" />
<span v-click="3"><strong>2nd largest CNCF project</strong></span>
<carbon-education v-click="4" class="icon" />
<span v-click="4"><strong>CNCF graduated</strong> project</span>
</div>
</div>

<!-- CHRISTOS

I'll start with a quick two-minute overview — most of you know what OpenTelemetry is, so I'll keep it brief.

OpenTelemetry is the open standard for observability. It defines how applications emit telemetry data — traces, metrics, logs, and profiles — and how that data flows to your observability tools.

It's the second-largest CNCF project, with contributions from virtually every major vendor in the space. And as a graduated project, it's considered production-ready by the foundation.

-->

---
clicks: 6
---

# The OpenTelemetry Ecosystem

<EcoSystem />

<!-- CHRISTOS

<click> The four signals: traces, metrics, logs, and profiles.

<click> Your application connects via auto-instrumentation agents or the OTel SDK and API.

<click> Everything flows to the OTel Collector — a vendor-neutral pipeline that receives, processes, and exports your telemetry.

<click> And the Collector forwards to your observability backends.

<click> Underneath everything — the layer that ties the whole ecosystem together — are Semantic Conventions. Standard names like system.cpu.time, host.name, and k8s.pod.name that every tool in the ecosystem agrees on.

<click> And these two — the Collector and Semantic Conventions — are exactly what today's talk is about. The Collector receivers and processors emit telemetry attributes using the names defined in Semantic Conventions. If a convention renames an attribute, every Collector component that emits it has to change too — and if that isn't handled carefully, users silently see different data on the next upgrade. That's the core challenge, and it's what we're here to talk about.

-->

---

# Story 1 > Kubelet Stats

<div class="icon-grid">
  <carbon-warning-alt v-click="1" class="icon" />
  <span v-click="1"><code>k8s.node.cpu.utilization</code> > "utilization" in OTel semconv means a ratio (0–1). These were actually raw <strong>nanocore</strong> values. Fix: rename to <code>k8s.node.cpu.usage</code>.</span>
  <carbon-misuse v-click="2" class="icon" />
  <span v-click="2">Real user pain: Silent disappearance on upgrade, no compile error?</span>
  <carbon-time v-click="4" class="icon" />
  <span v-click="4">Multi-release migration/deprecation process. (Oct 2024): 10+ releases before gate reached beta. <a href="https://github.com/open-telemetry/opentelemetry-collector-contrib/issues/27885">#27885</a></span>
</div>

<!-- CHRISTOS

Let me start with a concrete story.

The kubeletstats receiver had been emitting metrics with "utilization" in their names. In OpenTelemetry's semantic conventions, "utilization" means a ratio between 0 and 1. But these metrics were actually raw nanosecond CPU values. The names were semantically wrong.

The fix was to rename them to "usage." But here's where it gets painful.

When users upgraded their Collector, the old metric names silently disappeared. No warning, no error — just gone.

Some users were mid-migration: they had removed the old dashboard panels but hadn't added the new ones yet. A real observability gap.

The responsible fix required over 10 releases. This became one of the canonical examples for why we needed a proper migration system.

-->

---

# Story 2 > HTTP Semantic Conventions

<div class="icon-grid">
  <carbon-data-share v-click="1" class="icon" />
  <div v-click="1" class="http-rename-table">
    <div class="http-rename-row"><code class="http-old">http.method</code><span>→</span><code class="http-new">http.request.method</code></div>
    <div class="http-rename-row"><code class="http-old">http.url</code><span>→</span><code class="http-new">url.full</code></div>
    <div class="http-rename-row"><code class="http-old">http.status_code</code><span>→</span><code class="http-new">http.response.status_code</code></div>
    <div class="http-rename-row"><code class="http-old">net.peer.name</code><span>→</span><code class="http-new">server.address</code></div>
    <div class="http-rename-row http-rename-more"><span class="http-more">+ more</span></div>
  </div>
  <carbon-scales v-click="2" class="icon" />
  <span v-click="2">Affected every OTel implementation: Collector components, language SDKs, telemetry pipelines, etc.</span>
</div>

<!-- CHRISTOS

The HTTP semantic convention migration was even more painful because of its scope.

This wasn't one receiver. Renaming http.method, http.url, and http.status_code touched every HTTP instrumentation library, every Collector component, and every dashboard users had built.

The first attempt was a global environment variable. It worked — barely — but it was blunt: you couldn't control it per-component, you couldn't roll back, and it wasn't native to the Collector's config model.

This experience proved that the old approach wasn't enough — and directly shaped what we'll talk about next.

-->

---
layout: center
class: text-center
---

<div class="pain-scenario-strip">
  <div class="pain-step">
    <carbon-document-multiple-01 class="pain-step-icon" />
    <span class="pain-step-label">SemConv ships rename</span>
    <div class="pain-step-rename">
      <code class="pain-step-code">http.method</code>
      <span class="pain-step-rename-arrow">→</span>
      <code class="pain-step-code pain-step-code-new">http.request.method</code>
    </div>
  </div>
  <div class="pain-step-arrow">→</div>
  <div class="pain-step">
    <carbon-settings-adjust class="pain-step-icon pain-step-collector" />
    <span class="pain-step-label">Collector implements it</span>
    <code class="pain-step-code pain-step-code-new">http.request.method</code>
  </div>
  <div class="pain-step-arrow">→</div>
  <div class="pain-step">
    <carbon-chart-multitype class="pain-step-icon pain-step-bad" />
    <span class="pain-step-label">Your dashboards break</span>
    <code class="pain-step-code pain-step-code-missing">http.method = ?</code>
  </div>
</div>

<p class="pain-tagline">Schema changes surfaced in implementation &nbsp;=&nbsp; big impact</p>

<!-- CHRISTOS

This is the core of the problem. There's no compile-time check. No test failure. No alert.

A user upgrades their Collector on a Tuesday, and their dashboards quietly start showing different data — or nothing at all.

This is what we set out to fix. And before I show you what we did, let me hand over to Pablo to explain the framework we're working within.

-->

---

# Users want stability

  <ul>
    <li>OTel's graduation from CNCF comes with an adopter feedback process</li>
    <li>CNCF Technical Oversight Committee flagged: critical components still Beta, breaking changes too frequent</li>
    <li>Community surveys: what components and what people care about</li>
  </ul>

<!-- PABLO

Add slides covering the CNCF ToC feedback:
- When it happened and what was specifically said
- The concerns raised (Beta components in production, SemConv churn)
- How the community received it and what changed as a result
- Why external accountability matters for a project at this scale

-->
---

# Defining Stability in OpenTelemetry

<div class="pablo-stub">
  <div class="pablo-stub-badge">PABLO</div>
  <div class="comparison-grid">
    <div class="info-box">
      <h3>Specifications &amp; SemConv</h3>
      <ul>
        <li v-click="1">Development → Experimental → <strong>Stable</strong></li>
        <li v-click="2">Stable = guaranteed backwards compatibility for attribute names</li>
        <li v-click="3">Users can rely on names never silently changing</li>
      </ul>
    </div>
    <div class="info-box">
      <h3>Collector Components</h3>
      <ul>
        <li v-click="1">Development → Alpha → Beta → <strong>Stable</strong></li>
        <li v-click="2">Stable = configuration compatibility + no silent breakage on upgrade</li>
        <li v-click="3">Most heavily-used components are still Beta</li>
      </ul>
    </div>
  </div>
</div>

<!-- PABLO

The key insight is that "stable" means something specific in OpenTelemetry — and it's different for specs vs. implementations.

For Semantic Conventions: once stable, attribute names are guaranteed not to change. You build dashboards on system.cpu.time and they're there forever.

For Collector components: stable means configuration won't silently break, and there's a defined migration path for any changes.

The problem: most of the most heavily-used components — kubeletstats, hostmetrics — are still Beta. Even though they're running in production at thousands of companies.

-->

---

# Coming up with a plan

<div class="icon-grid">
  <carbon-function v-click="1" class="icon" />
  <span v-click="1">Collector was already stabilizing core libraries and APIs.</span>
  <carbon-idea v-click="2" class="icon" />
  <span v-click="2">What about the components that collect the telemetry?</span>
</div>

<!-- CHRISTOS

-->

---

# The 7 Highest-Priority Components

<div class="priority-components">
  <div class="priority-row row-2">
    <div class="component-box"><code>filelog</code></div>
    <div class="component-box"><code>k8sattributes</code></div>
  </div>
  <div class="priority-row row-3">
    <div class="component-box"><code>hostmetrics</code></div>
    <div class="component-box"><code>prometheus</code></div>
    <div class="component-box"><code>resourcedetection</code></div>
  </div>
  <div class="priority-row row-2">
    <div class="component-box"><code>transform</code></div>
    <div class="component-box"><code>filter</code></div>
  </div>
</div>

<p class="priority-tracking">
  <carbon-link class="icon" />
  Tracking issue: <a href="https://github.com/open-telemetry/opentelemetry-collector-contrib/issues/44130">opentelemetry-collector-contrib#44130</a>
</p>

<!-- CHRISTOS

-->

<!-- ---

# Two Sides of the Same Coin

<div class="two-sides-grid">
  <div v-click="1" class="info-box side-semconv">
    <h3>Schema Side</h3>
    <p style="opacity:0.7; font-size:0.9rem; padding-bottom:0">System &amp; K8s SemConv SIGs</p>
    <ul>
      <li>Rigorous promotion criteria before declaring stable</li>
      <li>System &amp; process metrics stabilization</li>
    </ul>
  </div>
  <div v-click="2" class="coin-bridge">
    <div class="coin-rule">A convention is not<br>called <strong>stable</strong> until<br>the Collector has a<br><strong>migration path ready</strong></div>
  </div>
  <div v-click="3" class="info-box side-collector">
    <h3>Implementation Side</h3>
    <p style="opacity:0.7; font-size:0.9rem; padding-bottom:0">Collector SIG</p>
    <ul>
      <li>Per-component feature gates for safe migration</li>
      <li>7 priority components for first-wave stabilization</li>
    </ul>
  </div>
</div> -->

<!-- CHRISTOS

Here's how the community responded. Two parallel efforts, working in lockstep.

On the schema side: the System and K8s SemConv SIGs adopted rigorous promotion criteria. A metric can't be called stable until it's been reviewed, tested, and approved.

On the implementation side: the Collector SIG created an RFC for per-component feature gates. Each component gets a pair of gates — users migrate on their own timeline.

The key constraint that ties these together: we don't call a convention "stable" until the Collector has a migration path ready. Schema stability and implementation stability are coupled.

Let me walk through each side in detail.

-->

---

# From Schema to Shipping Data

<div class="migration-slide">
  <div class="migration-gates">
    <div v-click="1" class="gate-pair">
      <code class="gate">&lt;kind&gt;.&lt;id&gt;.EmitV1&lt;Area&gt;Conventions</code>
      <span class="gate-arrow">→</span>
      <span class="gate-desc">opt into new names</span>
    </div>
    <div v-click="2" class="gate-pair">
      <code class="gate">&lt;kind&gt;.&lt;id&gt;.DontEmitV0&lt;Area&gt;Conventions</code>
      <span class="gate-arrow">→</span>
      <span class="gate-desc">stop emitting old names</span>
    </div>
  </div>
  <div class="lifecycle-steps">
    <div v-click="3" class="lifecycle-step step-alpha">
      <div class="step-badge">Alpha</div>
      <div class="step-desc">v0 names only. Users opt in to v1 or both.</div>
    </div>
    <span v-click="4" class="lifecycle-arrow">→</span>
    <div v-click="4" class="lifecycle-step step-beta">
      <div class="step-badge">Beta</div>
      <div class="step-desc">v1 only (opt-in: double-publish). Triggered when semconv reaches stable.</div>
    </div>
    <span v-click="5" class="lifecycle-arrow">→</span>
    <div v-click="5" class="lifecycle-step step-stable">
      <div class="step-badge">Stable</div>
      <div class="step-desc">v1 names only.</div>
    </div>
    <span v-click="6" class="lifecycle-arrow">→</span>
    <div v-click="6" class="lifecycle-step step-removed">
      <div class="step-badge">Removed</div>
      <div class="step-desc">After 4 more minor releases.</div>
    </div>
  </div>
  <p v-click="7" class="migration-note">
    RFC: <a href="https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/rfcs/semconv-feature-gates.md">semconv-feature-gates.md</a>
  </p>
</div>

<!-- CHRISTOS

The HTTP semconv migration showed us that a global env var wasn't enough. So we designed a proper, per-component mechanism.

Each component gets two paired feature gates. The first lets you opt into the new names early. The second lets you turn off the old names when you're ready. You control each independently — you can run both in parallel during your migration window.

The lifecycle has four stages:

Alpha: nothing changes by default. You can opt in to test the new names.

Beta: triggered automatically once the associated semconv area reaches stable. The default flips — new names emitted by default. You can still emit both.

Stable: old names are gone. Trying to enable them results in an error.

Removed: the gates themselves are removed after 4 more releases.

Minimum warning window across all stages: 8 minor releases. That's a real runway for users to migrate safely.

-->

---

# K8s SemConv SIG

<div class="icon-grid">
  <carbon-kubernetes-ip-address v-click="1" class="icon" />
  <span v-click="1">Described all K8s metrics from the Collector in Semantic Conventions.</span>
  <carbon-group v-click="2" class="icon" />
  <span v-click="2">KubeCon NA 2025: Collector SIG + K8s SIG aligned on stabilization priorities.</span>
  <carbon-idea v-click="3" class="icon" />
  <span v-click="3">Key insight: stabilizing K8s semconv directly <strong>benefits</strong> <code>k8sattributes</code> processor stability.</span>
  <carbon-checkmark v-click="4" class="icon" />
  <span v-click="4">First target: Stabilize K8s <strong>attributes</strong>.</span>
</div>

<!-- CHRISTOS

On the schema side: the K8s SemConv SIG had just finished formally defining all K8s metrics into the spec.

At KubeCon NA 2025, we aligned with the Collector SIG on priorities. We realized early: stabilize K8s semantic conventions first, and we directly unblock k8sattributes — one of the seven priority components.

K8s attributes became our first target. Get that to stable, and k8sattributes can ship as v1.

-->

---

# System SemConv SIG

<div class="icon-grid">
  <carbon-function v-click="1" class="icon" />
  <span v-click="1">SIG had been working on system metrics stabilization already.</span>
  <carbon-collaborate v-click="2" class="icon" />
  <span v-click="2">More focused now: tightly aligned with the <code>hostmetrics</code> receiver stability goal.</span>
</div>

<!-- CHRISTOS

The System SemConv SIG had already been working on stabilizing system metrics for over a year.

After our alignment with the Collector SIG, the effort became more focused: everything we do here is coordinated with the hostmetrics receiver stability goal.

-->

---

# Semantic Conventions: Progress Report

<div class="icon-grid">
  <carbon-checkmark-filled v-click="1" class="icon icon-stable" />
  <span v-click="1">K8s attributes → <strong>stable</strong> in <a href="https://github.com/open-telemetry/semantic-conventions/releases/tag/v1.42.0">semconv v1.42.0</a> (June 2026)</span>
  <carbon-in-progress v-click="2" class="icon icon-rc" />
  <span v-click="2"><code>process</code> namespace → Release Candidate (<a href="https://github.com/open-telemetry/semantic-conventions/pull/3758">PR #3758</a>, <a href="https://github.com/open-telemetry/semantic-conventions/pull/3564">#3564</a>)</span>
  <carbon-in-progress v-click="3" class="icon icon-rc" />
  <span v-click="3"><code>system</code> metrics → RC in progress (<a href="https://github.com/open-telemetry/semantic-conventions/pull/4055">PR #4055</a>)</span>
  <carbon-progress-bar v-click="4" class="icon icon-progress" />
  <span v-click="4">K8s and container metrics → RC, <strong>33 metrics</strong> already promoted</span>
</div>

<!-- CHRISTOS

Here's the current state of the SemConv work.

K8s attributes: done — stable since June in semconv v1.42.0.

Process metrics: Release Candidate.

System metrics: on their way to RC.

33 K8s and container metrics already promoted to RC.

-->

---

# <span style="color: #22c55e">✓</span> k8sattributes Processor → Stable

<div class="icon-grid">
  <carbon-list-checked v-click="1" class="icon" />
  <span v-click="1">Required two checklists: Collector component stability criteria <strong>and</strong> K8s semconv compatibility.</span>
  <carbon-plug v-click="2" class="icon" />
  <span v-click="2">Feature gates shipped in <code>v0.147.0</code>:<br><code>processor.k8sattributes.EmitV1K8sConventions</code><br><code>processor.k8sattributes.DontEmitV0K8sConventions</code></span>
  <carbon-rocket v-click="3" class="icon icon-stable" />
  <span v-click="3">Shipped as <strong>v1</strong> on September 15th — part of <code>v0.161.0</code> contrib distro.</span>
  <carbon-link v-click="4" class="icon" />
  <span v-click="4">Tracking issue: <a href="https://github.com/open-telemetry/opentelemetry-collector-contrib/issues/44483">#44483</a></span>
</div>

<!-- CHRISTOS

And we have our first finish line.

The k8sattributes processor shipped as v1 — stable — on September 15th, as part of the v0.161.0 Collector contrib release.

It required satisfying two checklists: the standard Collector component stability criteria, and a new K8s SemConv compatibility checklist. First component to complete both.

If you're on v0.161.0 or later, you have access to stable Kubernetes attribute enrichment — guaranteed not to break silently on the next upgrade.

-->

---

# Timeline

<Timeline :items="[
  { year: 'Oct 2025', desc: '<div class=tl-card-title>K8s Metrics in SemConv</div><p class=tl-body>K8s SIG completes K8s metrics in Semantic Conventions</p>' },
  { year: 'Nov 2025', desc: '<div class=tl-card-title>SIG Alignment at KubeCon NA</div><p class=tl-body>Collector SIG + K8s SIG align on stabilization priorities</p>' },
  { year: 'Apr 2026', desc: '<div class=tl-card-title>Migration Process</div><p class=tl-body>Feature gate pair RFC adopted; per-component migration process defined</p>' },
  { year: 'Jun 2026', desc: '<div class=tl-card-title>K8s Attributes Stable ✓</div><p class=tl-body>semconv v1.42.0 released</p>', highlight: true },
  { year: 'Sep 2026', desc: '<div class=tl-card-title>k8sattributes v1 ✓</div><p class=tl-body>First Collector component ships as v1</p>', highlight: true },
  { year: '2027', desc: '<div class=tl-card-title>More to Come</div><p class=tl-body>More components and stability updates coming!</p>' },
]" />

<!-- CHRISTOS

Here's the journey and where we're heading.

October 2025: K8s SIG finishes the K8s metrics spec work.

November 2025: After KubeCon NA, the two SIGs align on priorities.

April 2026: The feature gate pair RFC is adopted and the per-component migration process is defined.

June 2026: K8s attributes reach stable in semconv v1.42.0 — 33 metrics promoted.

September 2026 — this month — k8sattributes ships as v1. First Collector component to complete the full journey.

Target: remaining priority components at v1 by 2027.

Now, back to Pablo for what comes next.

-->

---

# What's Next and the End Goal

<div class="pablo-stub">
  <div class="pablo-stub-badge">PABLO</div>
  <ul>
    <li>6 remaining priority components on the path to v1 (target: March 2027)</li>
    <li>The end goal: "stable by default" — no configuration needed to get stable telemetry on upgrade</li>
    <li>What the world looks like when all critical Collector components are v1</li>
    <li>The roadmap for the next major OTel Collector release</li>
  </ul>
</div>

<!-- PABLO

Add slides covering the roadmap and end state:
- The remaining 6 components and expected timelines
- What "stable by default" looks like for end users
- How this connects to the next major Collector release
- The broader vision: every widely-used component is v1

-->

---

# Other Components Coming Up

<div class="pablo-stub">
  <div class="pablo-stub-badge">PABLO</div>
  <ul>
    <li>Which other Collector components are in the stabilization queue beyond the 7 priority ones</li>
    <li>Community effort: how other maintainers are adopting the same RFC and checklist pattern</li>
    <li>The template is open — any component team can use it</li>
  </ul>
</div>

<!-- PABLO

Brief slide covering the broader component ecosystem:
- Components beyond the 7 priority ones
- How the RFC and checklist pattern scales across the full contrib repo
- Invitation for other component maintainers to adopt the process

-->

---

# Telemetry Schemas

<div class="pablo-stub">
  <div class="pablo-stub-badge">PABLO</div>
  <ul>
    <li>The OTel Telemetry Schema spec — machine-readable migration definitions</li>
    <li>How schemas complement the feature gate approach for SemConv migrations</li>
    <li>Future direction: schema-driven automatic migration in the Collector</li>
  </ul>
</div>

<!-- PABLO

Brief slide on Telemetry Schemas:
- What they are and how they relate to SemConv stability
- Current state of the schema spec
- How they enable tooling to help users migrate automatically

-->

---
layout: center
---

<h1 class="qa-title">Q&A</h1>

<QrArrow />

<div class="kceu-logo-block">
  <img src="/observability-summit-eu-logo-color-2.svg" class="kceu-logo" />
  <span class="kceu-logo-year">2026</span>
</div>

<!-- BOTH

Thank you! Happy to take questions.

If you want to get involved:
- Collector SIG: every other Thursday (check the OTel community calendar)
- SemConv SIG: weekly on Fridays
- GitHub: open-telemetry/semantic-conventions and open-telemetry/opentelemetry-collector-contrib

The slides are available via the QR code — includes all links referenced today.

-->
