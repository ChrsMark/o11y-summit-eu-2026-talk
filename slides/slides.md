---
theme: dracula
background: https://cover.sli.dev
title: From Schema to Shipping Data
class: text-center
transition: slide-left
mdc: true
layout: intro
colorSchema: light
fonts:
  sans: 'Inter Tight'
  mono: 'IBM Plex Mono'
  weights: '400,500,700'
  custom: 'Inter'
---

# From Schema to Shipping Data

<p class="intro-subtitle">Making OpenTelemetry Stable by Default</p>

<div class="intro-meta">
  <p class="intro-speakers">Christos Markou <span class="intro-org">(Elastic)</span> · Pablo Baeyens <span class="intro-org">(Datadog)</span></p>
</div>

<QrArrow />

<div class="kceu-logo-block">
  <img src="/observability-summit-eu-logo-color-2-black.svg" class="kceu-logo" />
  <span class="kceu-logo-year">2026</span>
</div>

<!-- CHRISTOS

Thanks for joining. Use the QR code to follow along and open the links on the slides.

-->

---

# About us

<div class="speakers-grid">
  <div class="speaker">
    <img src="/chrismark.jpeg" class="speaker-pic" />
    <div class="speaker-name"><a href="https://github.com/ChrsMark">Christos Markou</a></div>
    <div class="speaker-org">Elastic</div>
    <div class="speaker-roles">
      <span>Principal Software Engineer I</span>
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

[PABLO] I'm Pablo, a Senior Software Engineer at Datadog. I maintain the OpenTelemetry Collector and I am currently a member on the Governance Committee. I have been contributing to the project since 2020.

[CHRISTOS] I'm Christos, a Principal Software Engineer at Elastic. I maintain Collector Contrib and I'm a Semantic Conventions Approver for system, Kubernetes, and container metrics. I'm also a CNCF Ambassador.

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

Most of you know OpenTelemetry, so this is a quick overview.

OpenTelemetry is the open standard for observability. It defines how applications emit telemetry and how that data gets to your observability tools.

It's the second-largest CNCF project, with contributions from most major vendors. It's a graduated project, so the CNCF considers it production-ready.

-->

---
clicks: 5
---

# The OpenTelemetry Ecosystem

<EcoSystem />

<!-- CHRISTOS

Your application emits telemetry through auto-instrumentation or through the OTel SDK and API.

<click> The data goes to the OTel Collector, a vendor-neutral pipeline that receives, processes, and exports telemetry.

<click> Collectors can also run close to the workload on each VM, pod, or Kubernetes node.

<click> The Collector forwards data to your observability backends.

<click> Underneath all of this are Semantic Conventions: standard metric, attribute and attribute values names that every tool agrees on.

<click> This talk focuses on the Collector and Semantic Conventions. Collector receivers and processors emit attributes using the names defined in Semantic Conventions. If a convention renames an attribute, every Collector component that emits it has to change too. If we don't handle that carefully, users see different data after an upgrade without noticing.

-->

---

# Story 1: Kubelet Stats

<div class="icon-grid">
  <carbon-warning-alt v-click="1" class="icon" />
  <span v-click="1"><code>k8s.node.cpu.utilization</code> > "utilization" in OTel semconv means a ratio (0–1). These were actually raw <strong>nanocore</strong> values. Fix: rename to <code>k8s.node.cpu.usage</code>.</span>
  <carbon-misuse v-click="2" class="icon" />
  <span v-click="2">Real user pain: Silent disappearance on upgrade</span>
  <carbon-time v-click="3" class="icon" />
  <span v-click="3">Multi-release migration/deprecation process. (Oct 2024): 10+ releases before gate reached beta. <a href="https://github.com/open-telemetry/opentelemetry-collector-contrib/issues/27885">#27885</a></span>
</div>

<!-- CHRISTOS

The kubeletstats receiver emitted metrics with "utilization" in their names. In semantic conventions, "utilization" means a ratio between 0 and 1, but these metrics were raw nanocore CPU values. The names were wrong.

The fix was to rename them to "usage."

When users upgraded their Collector, the old metric names disappeared without any warning or error.

Some users were mid-migration: they had removed the old dashboard panels but hadn't added the new ones yet, so they had no data.

Doing this fix properly took over 10 releases. It became one of the main examples of why we needed a proper migration system.

-->

---

# Story 2: HTTP Semantic Conventions

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

The HTTP semantic convention migration was harder because of its scope.

Renaming http.method, http.url, and http.status_code touched HTTP instrumentation libraries and Collector components, and broke dashboards users had built.

That experience shaped what we'll show next.

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

No test or alert catches this.

A user upgrades their Collector, and their dashboards start showing different data or nothing at all.

That's what we set out to fix. First, Pablo will explain the framework we work within.

-->

---

# Users want stability

<div class="comparison-grid">
  <div v-click="1" class="info-box">
    <h3>User Surveys</h3>
    <p class="survey-stat">
      On the <a href="https://opentelemetry.io/blog/2026/otel-collector-follow-up-survey-analysis/" target="_blank">2025 Collector survey</a>,
      <span class="survey-stat-number">~52%</span>
      of users have stability as a top concern.
    </p>
  </div>
  <div v-click="2" class="info-box">
    <h3>OTel Graduation Process</h3>
    <blockquote class="info-quote">
      "Initial adopter interviews highlighted confusion regarding stability expectations of OTel distributions."
      <a href="https://github.com/cncf/toc/blob/main/projects/open-telemetry/otel-graduation-dd.md" target="_blank">— CNCF TOC OpenTelemetry Due Diligence</a>
    </blockquote>
  </div>
</div>

<!-- PABLO

We know this matters to users from a few sources.

First, our community Collector surveys. In both the 2024 and 2025 surveys, 52% of users list stability as a top concern.

The surveys also tell us which components are most used, so we know where to focus for the most impact.

Second, during OpenTelemetry's graduation, the CNCF interviewed adopters and we had access to their feedback.
Users are generally happy with the Collector, but they raised concerns about beta stability.

With so many moving pieces, how do we define stability?

-->
---

# Defining Stability: Specifications &amp; SemConv

<p class="stable-namespaces-text">Some namespaces are already stable or release candidate:</p>

<ul class="stable-namespaces">
  <li><code>jvm</code></li>
  <li><code>db</code></li>
  <li><code>cicd</code></li>
  <li><code>http</code></li>
  <li><code>exception</code></li>
  <li><code>k8s</code></li>
</ul>

<p class="stable-namespaces-text">The names and values of attributes and metrics of stable conventions won't change.</p>

<!-- PABLO

First, there's stability of the semantics. This is independent of any implementation, and roughly means "the names and well-known values of attributes and metrics won't change".

Users care about this a lot. Many namespaces are stable today, but many important ones are still unstable.

-->

---
class: top-aligned
---

# Defining Stability: Collector Components

<p class="stable-namespaces-text">Collector component stability considers:</p>

<ul class="stability-areas">
  <li>Configuration</li>
  <li>Testing</li>
  <li>Documentation</li>
  <li>Observability</li>
  <li>Maintenance</li>
  <li>Go API</li>
  <li>Adoption</li>
</ul>

<!-- PABLO

For Collector components, stability covers more than telemetry.

-->

---

# Coming up with a plan

<div class="icon-grid">
  <carbon-function class="icon" />
  <span>Since 2024, the Collector SIG has worked on stabilizing core libraries and APIs.</span>
  <carbon-idea v-click="1" class="icon" />
  <span v-click="1">But what about specific components?</span>
</div>

<div class="flex justify-center mt-8">
  <img src="/collector-v1-roadmap.png" class="max-h-40" />
</div>

<!-- PABLO

So how do we get to stability, and what do we focus on first?

Historically, the Collector focused on stabilizing its foundational pieces, including the libraries developers use to build components.

Last year, based on the graduation feedback, we decided to refocus on specific widely used components to have a more direct impact.
-->

---

# The 7 Highest-Priority Components

<div class="priority-components">
  <div class="priority-row row-2">
    <div class="component-box receiver"><code>filelog</code></div>
    <div class="component-box processor"><code>k8sattributes</code></div>
  </div>
  <div class="priority-row row-3">
    <div class="component-box receiver"><code>hostmetrics</code></div>
    <div class="component-box receiver"><code>prometheus</code></div>
    <div class="component-box processor"><code>resourcedetection</code></div>
  </div>
  <div class="priority-row row-2">
    <div class="component-box processor"><code>transform</code></div>
    <div class="component-box processor"><code>filter</code></div>
  </div>
</div>

<p class="priority-tracking">
  <carbon-link class="icon" />
  Tracking issue: <a href="https://github.com/open-telemetry/opentelemetry-collector-contrib/issues/44130">opentelemetry-collector-contrib#44130</a>
</p>

<!-- PABLO

We combined survey data with input from several vendors to pick 7 components to focus on.

Stabilizing some of these components meant breaking changes like the ones Christos described, so we had to agree on mechanisms to make the transition as smooth as possible.

-->

---

# From Schema to Shipping Data

<div class="migration-slide">
  <div class="migration-gates">
    <div class="gates-title">Two feature gates</div>
    <div class="gate-pair">
      <code class="gate">&lt;kind&gt;.&lt;id&gt;.EmitV1&lt;Area&gt;Conventions</code>
      <span class="gate-arrow">→</span>
      <span class="gate-desc">opt into new names</span>
    </div>
    <div class="gate-pair">
      <code class="gate">&lt;kind&gt;.&lt;id&gt;.DontEmitV0&lt;Area&gt;Conventions</code>
      <span class="gate-arrow">→</span>
      <span class="gate-desc">stop emitting old names</span>
    </div>
  </div>
  <div class="lifecycle-steps">
    <div v-click="1" class="lifecycle-step step-alpha">
      <div class="step-badge">Alpha</div>
      <div class="step-desc">
        <div class="step-line"><strong>Default:</strong> v0 names only</div>
        <div class="step-line"><strong>Opt-in:</strong> v1 or double-publish</div>
      </div>
    </div>
    <span v-click="2" class="lifecycle-arrow">→</span>
    <div v-click="2" class="lifecycle-step step-beta">
      <div class="step-badge">Beta</div>
      <div class="step-desc">
        <div class="step-line"><strong>Default:</strong> v1 names only</div>
        <div class="step-line"><strong>Opt-in:</strong> v0 or double-publish</div>
      </div>
    </div>
    <span v-click="3" class="lifecycle-arrow">→</span>
    <div v-click="3" class="lifecycle-step step-stable">
      <div class="step-badge">Stable</div>
      <div class="step-desc">
        <div class="step-line"><strong>Default:</strong> v1 names only</div>
        <div class="step-line">v0 support removed</div>
      </div>
    </div>
  </div>
  <p class="migration-note">
    RFC: <a href="https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/rfcs/semconv-feature-gates.md">semconv-feature-gates.md</a>
  </p>
</div>

<!-- PABLO

One of the first problems to solve to make the transition was to ensure a smooth migration.

While OpenTelemetry defined a migration strategy using environment variables, this did not feel native to the Collector and had some drawbacks such as not being customizable per component.

We designed a migration with two feature gates <describe stages>. As it happens with instrumentations, you can choose to double publish v0 and v1 conventions.

This way, users can migrate at their own pace, all at once or on a per component basis.

-->

---

# K8s SemConv SIG

<div class="icon-grid">
  <carbon-kubernetes-ip-address v-click="1" class="icon" />
  <span v-click="1">Described all K8s metrics from the Collector in Semantic Conventions.</span>
  <carbon-group v-click="2" class="icon" />
  <span v-click="2">KubeCon NA 2025: Collector SIG + K8s SIG aligned on priorities.</span>
  <carbon-idea v-click="3" class="icon" />
  <span v-click="3">Key insight: stabilizing K8s semconv <strong>benefits</strong> <code>k8sattributes</code> processor, <code>resourcedetection</code> processor and <code>filelog</code> receiver stability.</span>
  <carbon-checkmark v-click="4" class="icon" />
  <span v-click="4">First target: Stabilize K8s <strong>attributes</strong>.</span>
</div>

<!-- CHRISTOS

On the schema side, the K8s SemConv SIG had just finished defining all K8s metrics in the spec.

At KubeCon NA 2025, we aligned with the Collector SIG on priorities. Stabilizing K8s semantic conventions first would unblock k8sattributes, one of the seven priority components.

So K8s attributes became our first target. Once they're stable, k8sattributes can ship as v1.

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

The System SemConv SIG had been working on stabilizing system metrics for over a year.

After aligning with the Collector SIG, we coordinate this work with the goal of a stable hostmetrics receiver.

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
  <span v-click="4">K8s and container metrics → RC, <strong>34 metrics</strong> already promoted</span>
</div>

<!-- CHRISTOS

K8s attributes are stable since June, in semconv v1.42.0.

Process metrics are a Release Candidate.

System metrics are on their way to RC.

33 K8s and container metrics are already RC.

-->

---

# <span style="color: #22c55e">✓</span> k8sattributes Processor → Stable

<div class="icon-grid">
  <carbon-list-checked v-click="1" class="icon" />
  <span v-click="1">Required two checklists: Collector component stability criteria <strong>and</strong> K8s semconv compatibility.</span>
  <carbon-plug v-click="2" class="icon" />
  <span v-click="2">Feature gates shipped in <code>v0.147.0</code>:<br><code>processor.k8sattributes.EmitV1K8sConventions</code><br><code>processor.k8sattributes.DontEmitV0K8sConventions</code></span>
  <carbon-rocket v-click="3" class="icon icon-stable" />
  <span v-click="3">Shipped as <strong>v1</strong> on September 15th; part of <code>v0.161.0</code> contrib distro.</span>
  <carbon-link v-click="4" class="icon" />
  <span v-click="4">Tracking issue: <a href="https://github.com/open-telemetry/opentelemetry-collector-contrib/issues/44483">#44483</a></span>
</div>

<!-- CHRISTOS

The k8sattributes processor shipped as stable v1 on September 15th, in the v0.161.0 Collector contrib release.

It had to meet two checklists: the standard Collector component stability criteria and a new K8s SemConv compatibility checklist. It's the first component to complete both.

On v0.161.0 or later, Kubernetes attribute enrichment is stable.

-->

---

# Timeline

<Timeline :items="[
  { year: 'Oct 2025', desc: '<div class=tl-card-title>K8s Metrics in SemConv</div><p class=tl-body>SIG adds K8s metrics to Semantic Conventions</p>' },
  { year: 'Nov 2025', desc: '<div class=tl-card-title>SIG Alignment at KubeCon NA</div><p class=tl-body>Collector and K8s SIG align on priorities</p>' },
  { year: 'Apr 2026', desc: '<div class=tl-card-title>Migration Process</div><p class=tl-body>RFC on feature gate pair for migration approved</p>' },
  { year: 'Jun 2026', desc: '<div class=tl-card-title>K8s Attributes Stable</div><p class=tl-body>SemConv v1.42.0 released</p>', highlight: true },
  { year: 'Sep 2026', desc: '<div class=tl-card-title>k8sattributes v1</div><p class=tl-body>First contrib component to ship as v1</p>', highlight: true },
  { year: '2027', desc: '<div class=tl-card-title>More to Come</div><p class=tl-body>More components and stability updates coming!</p>' },
]" />

<!-- CHRISTOS

October 2025: K8s SIG finishes the K8s metrics spec.

November 2025: after KubeCon NA, the two SIGs align on priorities.

April 2026: the feature gate pair RFC is adopted.

June 2026: K8s attributes are stable in semconv v1.42.0, and 33 metrics are promoted.

September 2026: k8sattributes ships as v1, the first Collector component to go through the whole process.

Target: the remaining priority components at v1 by 2027.

Back to Pablo.

-->

---
clicks: 2
---

# What's next -- Other priority components

<div class="priority-split">
  <ul class="priority-list">
    <li :class="{ active: $clicks === 0 }"><code>hostmetrics</code> and <code>resourcedetection</code></li>
    <li :class="{ active: $clicks === 1 }"><code>prometheus</code> receiver</li>
    <li :class="{ active: $clicks === 2 }"><code>transform</code> and <code>filter</code></li>
  </ul>
  <div v-if="$clicks === 0" class="component-details">
    <h2><code>hostmetrics</code> and <code>resourcedetection</code></h2>
    <p><code>process</code> namespace shipped as release candidate. Working on prioritization for <code>resourcedetection</code> detectors.</p>
  </div>
  <div v-if="$clicks === 1" class="component-details">
    <h3><code>prometheus</code> receiver</h3>
    <p>Prometheus interoperability SIG is working on the Prometheus to OpenTelemetry spec. Only one major question remains!</p>
  </div>
  <div v-if="$clicks === 2" class="component-details">
    <h3><code>transform</code> and <code>filter</code></h3>
    <p>OTTL, the DSL used by these components, is seeking feedback for reaching v1. Components will follow suit.</p>
  </div>
</div>

---

# What's Next -- RFC

<div class="flex justify-center items-center h-[80%]">
  <img src="/rfc.png" class="max-h-full max-w-full" />
</div>

<!-- PABLO

A couple of weeks ago we started discussing an RFC that would allow us to clarify the requirements to mark the Collector itself as v1 as well as what is the minimum feature baseline for these components.

Discussion is ongoing and appreciate your feedback on it (link at the end).

-->

---

# What's Next -- Minimum stability

<p class="stable-namespaces-text">We are discussing how to make the default Collector experience stable.</p>

<div class="absolute inset-0 flex justify-center items-center pointer-events-none">
  <span class="font-mono text-6xl font-bold">--stability-level<span style="color: rgb(var(--accent))">?</span></span>
</div>

<!-- PABLO

We're also discussing how to make component stability easy for users to understand.

We're discussing a mechanism such as a "--stability-level" CLI flag that prevents using components and features below your preferred stability level.

You can share your feedback on how this should work on the linked issue.

-->

---

# Telemetry Schemas

<div class="flex justify-center items-center h-[80%]">
  <img src="/telemetry-schemas.png" class="max-h-full max-w-full" />
</div>

<!-- PABLO

Outside the Collector, the community is working on telemetry schemas: a machine-readable manifest that declares a telemetry schema and lets you programmatically migrate between its versions.

Backends will need to support it to get the full benefit, but we hope it will also help with telemetry migrations.

-->
---

# How you can help

<div class="help-cols grid grid-cols-2 gap-12">
<div>

### Share your feedback:

- [v1 core distro RFC](https://github.com/open-telemetry/opentelemetry-collector/pull/16037)
- [Priority components for stabilization](https://github.com/open-telemetry/opentelemetry-collector-contrib/issues/44130)
- [Opt-in for unstable components](https://github.com/open-telemetry/opentelemetry-collector/issues/14064)

</div>
<div>

### Join the community:

- [Collector SIG](https://github.com/open-telemetry/community/blob/main/sigs.md#collector)
- [System SemConv SIG](https://github.com/open-telemetry/community/blob/main/sigs.md#semantic-conventions-system-metrics)
- [K8s SemConv SIG](https://github.com/open-telemetry/community/blob/main/sigs.md#semantic-conventions-k8s)

</div>
</div>

<style>
.help-cols {
  flex: 1;
  align-content: start;
  padding-top: 1.5rem;
}

.help-cols h3 {
  margin: 0 0 1.5rem;
  padding: 0;
}

.help-cols ul {
  margin-top: 0;
}

.help-cols li {
  padding-top: 1.5rem;
}
</style>

<!-- PABLO

It's very important for us to have feedback, specially from end users, on these items.

If you want to help or just give your opinion, here are some links.

-->

---
layout: center
class: qa
---

<h1 class="qa-title">Q&A</h1>

<QrArrow />

<div class="kceu-logo-block">
  <img src="/observability-summit-eu-logo-color-2-black.svg" class="kceu-logo" />
  <span class="kceu-logo-year">2026</span>
</div>

<!-- BOTH

The slides, with all the links, are available via the QR code.

-->
