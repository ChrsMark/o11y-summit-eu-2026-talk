<script setup>
import { useSlideContext } from '@slidev/client'
const { $clicks } = useSlideContext()
</script>

<template>
  <div class="eco-arch">
    <div class="eco-main-grid">
      <!-- Row 1: Applications → Gateway Collector → Backends -->
      <div v-click="1" class="eco-arch-block eco-arch-services eco-g-svc">
        <div class="eco-arch-title">Your Applications</div>
        <div class="eco-lang-icons">
          <logos-go class="eco-lang-icon" />
          <logos-python class="eco-lang-icon" />
          <logos-java class="eco-lang-icon" />
          <logos-rust class="eco-lang-icon" />
        </div>
        <div class="eco-arch-stack">
          <div class="eco-layer-box">Auto Instrumentation</div>
          <div class="eco-layer-box eco-layer-api">OTel API</div>
          <div class="eco-layer-box eco-layer-sdk">OTel SDK</div>
        </div>
      </div>

      <div v-click="2" class="eco-arch-arrow eco-g-ar-sg">
        <div class="eco-h-arrow-line"></div>
        <div class="eco-h-arrow-label">OTLP</div>
      </div>

      <div v-click="2" class="eco-arch-block eco-arch-collector eco-g-gw" :class="{ 'eco-focus-highlight': $clicks >= 6 }">
        <img src="/otel-icon.png" class="eco-otel-icon" />
        <div class="eco-arch-title">Gateway Collector</div>
        <div class="eco-pipeline">
          <div class="eco-pipe-seg">Receivers</div>
          <span class="eco-pipe-conn">→</span>
          <div class="eco-pipe-seg">Processors</div>
          <span class="eco-pipe-conn">→</span>
          <div class="eco-pipe-seg">Exporters</div>
        </div>
      </div>

      <div v-click="4" class="eco-arch-arrow eco-g-ar-gb">
        <div class="eco-h-arrow-line"></div>
        <div class="eco-h-arrow-label">OTLP</div>
      </div>

      <div v-click="4" class="eco-arch-block eco-arch-backends eco-g-be">
        <div class="eco-arch-title">Observability Backends</div>
        <div class="eco-vendor-grid">
          <div class="eco-vendor-item">
            <logos-prometheus class="eco-vendor-icon" />
            <span>Prometheus</span>
          </div>
          <div class="eco-vendor-item">
            <logos-datadog class="eco-vendor-icon" />
            <span>Datadog</span>
          </div>
          <div class="eco-vendor-item eco-vendor-elastic">
            <simple-icons-elastic class="eco-vendor-icon" />
            <span>Elastic</span>
          </div>
          <div class="eco-vendor-item">
            <logos-grafana class="eco-vendor-icon" />
            <span>Grafana</span>
          </div>
          <div class="eco-vendor-item eco-vendor-jaeger">
            <simple-icons-jaeger class="eco-vendor-icon" />
            <span>Jaeger</span>
          </div>
        </div>
      </div>

      <!-- Row 2: Agent Collector — directly below Gateway -->
      <div v-click="3" class="eco-arch-block eco-arch-edge-collector eco-g-agent" :class="{ 'eco-focus-highlight': $clicks >= 6 }">
        <!-- Connector lives here so it's anchored to this block, not the container -->
        <div class="eco-agw-connector">
          <div class="eco-agw-line"></div>
          <span class="eco-agw-label">OTLP</span>
        </div>
        <img src="/otel-icon.png" class="eco-otel-icon" />
        <div class="eco-arch-title">Agent Collector</div>
        <div class="eco-arch-subtitle">VM / Pod / k8s Node</div>
        <div class="eco-pipeline eco-pipeline-compact">
          <div class="eco-pipe-seg eco-pipe-seg-sm">Recv</div>
          <span class="eco-pipe-conn">→</span>
          <div class="eco-pipe-seg eco-pipe-seg-sm">Proc</div>
          <span class="eco-pipe-conn">→</span>
          <div class="eco-pipe-seg eco-pipe-seg-sm">Exp</div>
        </div>
      </div>

      <!-- Connectors: Agent→Backends L-shape (Agent→Gateway handled inside Agent block) -->
      <div v-click="4" class="eco-connections">
        <!-- Agent → Backends: horizontal right from Agent edge -->
        <div class="eco-cline eco-cline-abe-h">
          <span class="eco-clabel eco-clabel-h">OTLP</span>
        </div>
        <!-- Agent → Backends: vertical up into Backends block -->
        <div class="eco-cline eco-cline-abe-v"></div>
      </div>
    </div>

    <div v-click="5" class="eco-semconv" :class="{ 'eco-focus-highlight': $clicks >= 6 }">
      <span class="eco-semconv-title">Semantic Conventions</span>
      <code>system.cpu.time</code>
      <code>host.name</code>
      <code>k8s.pod.name</code>
      <code>http.request.method</code>
      <span class="eco-semconv-dots">···</span>
    </div>
  </div>
</template>
