<template>
  <div class="pa-4">
    <div class="mb-4">
      <h2 class="text-h5 font-weight-bold mb-1">Manifest</h2>
      <div class="text-body-2 text-medium-emphasis">
        Formular für template.json — compose, docker oder plugin.
      </div>
    </div>

    <v-tabs v-model="type" class="mb-4">
      <v-tab value="compose">Compose</v-tab>
      <v-tab value="docker">Docker</v-tab>
      <v-tab value="plugin">Plugin</v-tab>
    </v-tabs>

    <v-row>
      <!-- ───────────────── Formular ───────────────── -->
      <v-col cols="12" md="6">
        <v-window v-model="type">

          <!-- ===== Compose ===== -->
          <v-window-item value="compose">
            <v-text-field v-model="compose.name" label="Name" variant="outlined" density="comfortable" class="mb-2" />
            <v-combobox v-model="compose.category" :items="CATEGORY_OPTIONS" multiple chips closable-chips
                        label="Kategorien" variant="outlined" density="comfortable" class="mb-2" />
            <v-textarea v-model="compose.description" label="Beschreibung" variant="outlined" rows="3" class="mb-2" />
            <v-row dense>
              <v-col cols="12" sm="6"><v-text-field v-model="compose.website" label="Website" variant="outlined" density="comfortable" /></v-col>
              <v-col cols="12" sm="6"><v-text-field v-model="compose.icon" label="Icon-URL" variant="outlined" density="comfortable" /></v-col>
            </v-row>
            <v-row dense>
              <v-col cols="12" sm="6"><v-text-field v-model="compose.support" label="Support-URL" variant="outlined" density="comfortable" /></v-col>
              <v-col cols="12" sm="6">
                <v-text-field v-model="compose.webui" label="Web-UI-URL (optional)" placeholder="http://[ADDRESS]:[PORT:8080]/"
                              variant="outlined" density="comfortable" />
              </v-col>
            </v-row>

            <v-divider class="my-4" />
            <div class="text-subtitle-2 mb-1">compose.yaml</div>
            <div class="text-caption text-medium-emphasis mb-2">
              Wird nicht ins JSON übernommen, sondern separat als eigene Datei im Ordner benötigt — hier nur zum bequemen Kopieren.
            </div>
            <v-textarea v-model="compose.yaml" variant="outlined" rows="8"
                        placeholder="services:&#10;  app:&#10;    image: …"
                        style="font-family: ui-monospace, monospace; font-size: 12.5px;" />
          </v-window-item>

          <!-- ===== Docker ===== -->
          <v-window-item value="docker">
            <v-row dense>
              <v-col cols="12" sm="6"><v-text-field v-model="docker.name" label="Name" variant="outlined" density="comfortable" /></v-col>
              <v-col cols="12" sm="6"><v-text-field v-model="docker.repo" label="Image (repo)" placeholder="lscr.io/linuxserver/plex" variant="outlined" density="comfortable" /></v-col>
            </v-row>
            <v-row dense>
              <v-col cols="12" sm="6">
                <v-combobox v-model="docker.category" :items="CATEGORY_OPTIONS" multiple chips closable-chips
                            label="Kategorien" variant="outlined" density="comfortable" />
              </v-col>
              <v-col cols="12" sm="6"><v-text-field v-model="docker.registry" label="Registry-URL" variant="outlined" density="comfortable" /></v-col>
            </v-row>
            <v-row dense>
              <v-col cols="12" sm="4">
                <v-combobox v-model="docker.network" :items="NETWORK_OPTIONS" label="Netzwerk" variant="outlined" density="comfortable" />
              </v-col>
              <v-col cols="12" sm="4"><v-text-field v-model="docker.customIp" label="Custom IP" placeholder="leer = null" variant="outlined" density="comfortable" /></v-col>
              <v-col cols="12" sm="4">
                <v-combobox v-model="docker.shell" :items="SHELL_OPTIONS" label="Default Shell" variant="outlined" density="comfortable" />
              </v-col>
            </v-row>
            <v-checkbox v-model="docker.privileged" label="Privileged" density="compact" hide-details class="mb-2" />
            <v-row dense>
              <v-col cols="12" sm="6"><v-text-field v-model="docker.extra" label="Extra Parameters" placeholder="--memory=1G" variant="outlined" density="comfortable" /></v-col>
              <v-col cols="12" sm="6"><v-text-field v-model="docker.post" label="Post Parameters" variant="outlined" density="comfortable" /></v-col>
            </v-row>
            <v-text-field v-model="docker.webui" label="Web-UI-URL" placeholder="http://[IP]:[PORT:8080]/" variant="outlined" density="comfortable" />
            <v-row dense>
              <v-col cols="12" sm="6"><v-text-field v-model="docker.icon" label="Icon-URL" variant="outlined" density="comfortable" /></v-col>
              <v-col cols="12" sm="6"><v-text-field v-model="docker.project" label="Project-URL" variant="outlined" density="comfortable" /></v-col>
            </v-row>
            <v-text-field v-model="docker.support" label="Support-URL" variant="outlined" density="comfortable" />
            <v-textarea v-model="docker.description" label="Beschreibung" variant="outlined" rows="4" />

            <v-expansion-panels class="my-4" variant="accordion">
              <v-expansion-panel title="Erweitert: cpu_set, gpus, no_autoupdate">
                <v-expansion-panel-text>
                  <v-alert type="info" variant="tonal" density="compact" class="mb-3">
                    Diese drei Felder tauchen im Repo nur vereinzelt auf und werden nur ins JSON geschrieben, wenn hier aktiviert.
                  </v-alert>
                  <v-text-field v-model="docker.cpuSet" label="CPU-Set" placeholder="z. B. 0-3" variant="outlined" density="comfortable" class="mb-3" />
                  <v-checkbox v-model="docker.includeGpus" label="gpus-Feld einschließen" density="compact" hide-details />
                  <v-combobox v-model="docker.gpus" :items="[]" multiple chips closable-chips label="GPUs" placeholder="all"
                              variant="outlined" density="comfortable" class="mb-3" :disabled="!docker.includeGpus" />
                  <v-checkbox v-model="docker.includeNoAuto" label="no_autoupdate-Feld einschließen" density="compact" hide-details />
                  <v-checkbox v-model="docker.noAuto" label="no_autoupdate = true" density="compact" hide-details :disabled="!docker.includeNoAuto" />
                </v-expansion-panel-text>
              </v-expansion-panel>
            </v-expansion-panels>

            <!-- Pfade -->
            <v-divider class="my-4" />
            <div class="text-subtitle-2 mb-1">Pfade (paths)</div>
            <div class="text-caption text-medium-emphasis mb-2">Volumes — Host- zu Container-Pfad-Zuordnungen.</div>
            <v-sheet v-for="(row, i) in paths" :key="'p'+i" variant="outlined" class="pa-3 mb-2" rounded>
              <v-row dense align="center">
                <v-col cols="12" sm="3"><v-text-field v-model="row.name" label="Name" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="12" sm="3"><v-text-field v-model="row.host" label="Host-Pfad" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="12" sm="3"><v-text-field v-model="row.container" label="Container-Pfad" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="6" sm="2"><v-select v-model="row.mode" :items="['rw','ro']" label="Modus" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="6" sm="1" class="text-right"><v-btn icon="mdi-close" size="small" variant="text" @click="paths.splice(i,1)" /></v-col>
              </v-row>
              <v-row dense>
                <v-col cols="9"><v-text-field v-model="row.description" label="Beschreibung" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="3" class="d-flex align-center"><v-checkbox v-model="row.required" label="Pflicht" density="compact" hide-details /></v-col>
              </v-row>
            </v-sheet>
            <v-btn variant="text" size="small" prepend-icon="mdi-plus" @click="paths.push(newPathRow())">Zeile hinzufügen</v-btn>

            <!-- Ports -->
            <v-divider class="my-4" />
            <div class="text-subtitle-2 mb-1">Ports</div>
            <div class="text-caption text-medium-emphasis mb-2">Host- zu Container-Port-Zuordnungen.</div>
            <v-sheet v-for="(row, i) in ports" :key="'port'+i" variant="outlined" class="pa-3 mb-2" rounded>
              <v-row dense align="center">
                <v-col cols="12" sm="3"><v-text-field v-model="row.name" label="Name" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="6" sm="2"><v-text-field v-model="row.host" label="Host-Port" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="6" sm="2"><v-text-field v-model="row.container" label="Container-Port" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="6" sm="2"><v-select v-model="row.protocol" :items="['tcp','udp']" label="Protokoll" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="6" sm="1" class="text-right"><v-btn icon="mdi-close" size="small" variant="text" @click="ports.splice(i,1)" /></v-col>
              </v-row>
              <v-row dense>
                <v-col cols="7"><v-text-field v-model="row.description" label="Beschreibung" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="3" class="d-flex align-center"><v-checkbox v-model="row.required" label="Pflicht" density="compact" hide-details /></v-col>
                <v-col cols="2" class="d-flex align-center"><v-checkbox v-model="row.mask" label="Mask." density="compact" hide-details /></v-col>
              </v-row>
            </v-sheet>
            <v-btn variant="text" size="small" prepend-icon="mdi-plus" @click="ports.push(newPortRow())">Zeile hinzufügen</v-btn>

            <!-- Variablen -->
            <v-divider class="my-4" />
            <div class="text-subtitle-2 mb-1">Umgebungsvariablen (variables)</div>
            <div class="text-caption text-medium-emphasis mb-2">Environment-Variablen des Containers.</div>
            <v-sheet v-for="(row, i) in variables" :key="'v'+i" variant="outlined" class="pa-3 mb-2" rounded>
              <v-row dense align="center">
                <v-col cols="12" sm="3"><v-text-field v-model="row.name" label="Anzeigename" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="12" sm="3"><v-text-field v-model="row.key" label="Key" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="12" sm="4"><v-text-field v-model="row.value" label="Wert" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="12" sm="1" class="text-right"><v-btn icon="mdi-close" size="small" variant="text" @click="variables.splice(i,1)" /></v-col>
              </v-row>
              <v-row dense>
                <v-col cols="7"><v-text-field v-model="row.description" label="Beschreibung" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="3" class="d-flex align-center"><v-checkbox v-model="row.required" label="Pflicht" density="compact" hide-details /></v-col>
                <v-col cols="2" class="d-flex align-center"><v-checkbox v-model="row.mask" label="Mask." density="compact" hide-details /></v-col>
              </v-row>
            </v-sheet>
            <v-btn variant="text" size="small" prepend-icon="mdi-plus" @click="variables.push(newVarRow())">Zeile hinzufügen</v-btn>

            <!-- Geräte -->
            <v-divider class="my-4" />
            <div class="text-subtitle-2 mb-1">Geräte (devices)</div>
            <v-alert type="info" variant="tonal" density="compact" class="mb-2">
              Struktur ist im Repo nicht mit Beispielen belegt — hier analog zu „Pfade" nachgebildet (Host-Gerät → Container-Gerät).
              Bei Bedarf an das tatsächliche Schema anpassen.
            </v-alert>
            <v-sheet v-for="(row, i) in devices" :key="'d'+i" variant="outlined" class="pa-3 mb-2" rounded>
              <v-row dense align="center">
                <v-col cols="12" sm="3"><v-text-field v-model="row.name" label="Name" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="12" sm="4"><v-text-field v-model="row.host" label="Host-Gerät" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="12" sm="4"><v-text-field v-model="row.container" label="Container-Gerät" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="12" sm="1" class="text-right"><v-btn icon="mdi-close" size="small" variant="text" @click="devices.splice(i,1)" /></v-col>
              </v-row>
              <v-row dense>
                <v-col cols="9"><v-text-field v-model="row.description" label="Beschreibung" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="3" class="d-flex align-center"><v-checkbox v-model="row.required" label="Pflicht" density="compact" hide-details /></v-col>
              </v-row>
            </v-sheet>
            <v-btn variant="text" size="small" prepend-icon="mdi-plus" @click="devices.push(newDeviceRow())">Zeile hinzufügen</v-btn>

            <!-- Labels -->
            <v-divider class="my-4" />
            <div class="text-subtitle-2 mb-1">Labels</div>
            <div class="text-caption text-medium-emphasis mb-2">Container-Labels, z. B. für Traefik-Routing.</div>
            <v-sheet v-for="(row, i) in labels" :key="'l'+i" variant="outlined" class="pa-3 mb-2" rounded>
              <v-row dense align="center">
                <v-col cols="12" sm="3"><v-text-field v-model="row.name" label="Anzeigename" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="12" sm="3"><v-text-field v-model="row.key" label="Key" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="12" sm="4"><v-text-field v-model="row.value" label="Wert" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="12" sm="1" class="text-right"><v-btn icon="mdi-close" size="small" variant="text" @click="labels.splice(i,1)" /></v-col>
              </v-row>
              <v-row dense>
                <v-col cols="7"><v-text-field v-model="row.description" label="Beschreibung" density="compact" variant="outlined" hide-details /></v-col>
                <v-col cols="3" class="d-flex align-center"><v-checkbox v-model="row.required" label="Pflicht" density="compact" hide-details /></v-col>
                <v-col cols="2" class="d-flex align-center"><v-checkbox v-model="row.mask" label="Mask." density="compact" hide-details /></v-col>
              </v-row>
            </v-sheet>
            <v-btn variant="text" size="small" prepend-icon="mdi-plus" @click="labels.push(newLabelRow())">Zeile hinzufügen</v-btn>
          </v-window-item>

          <!-- ===== Plugin ===== -->
          <v-window-item value="plugin">
            <v-row dense>
              <v-col cols="12" sm="6"><v-text-field v-model="plug.name" label="Name" placeholder="MOS htop" variant="outlined" density="comfortable" /></v-col>
              <v-col cols="12" sm="6"><v-text-field v-model="plug.author" label="Autor" placeholder="s3ppo" variant="outlined" density="comfortable" /></v-col>
            </v-row>
            <v-textarea v-model="plug.description" label="Beschreibung" variant="outlined" rows="3" />
            <v-row dense>
              <v-col cols="12" sm="6">
                <v-combobox v-model="plug.category" :items="CATEGORY_OPTIONS" multiple chips closable-chips
                            label="Kategorien" variant="outlined" density="comfortable" />
              </v-col>
              <v-col cols="12" sm="6">
                <v-combobox v-model="plug.arch" :items="['amd64','arm64']" multiple chips closable-chips
                            label="Architektur" variant="outlined" density="comfortable" />
              </v-col>
            </v-row>
            <v-checkbox v-model="plug.driver" label="Ist ein Treiber (driver)" density="compact" hide-details class="mb-2" />
            <v-row dense>
              <v-col cols="12" sm="6"><v-text-field v-model="plug.repo" label="Repository-URL" variant="outlined" density="comfortable" /></v-col>
              <v-col cols="12" sm="6"><v-text-field v-model="plug.homepage" label="Homepage-URL" variant="outlined" density="comfortable" /></v-col>
            </v-row>
            <v-row dense>
              <v-col cols="12" sm="6"><v-text-field v-model="plug.icon" label="Icon-URL" variant="outlined" density="comfortable" /></v-col>
              <v-col cols="12" sm="6"><v-text-field v-model="plug.support" label="Support-URL" variant="outlined" density="comfortable" /></v-col>
            </v-row>
            <v-text-field v-model="plug.donate" label="Donate (optional)" placeholder='leer = ""' variant="outlined" density="comfortable" />
          </v-window-item>

        </v-window>
      </v-col>

      <!-- ───────────────── Ausgabe ───────────────── -->
      <v-col cols="12" md="6">
        <v-card variant="outlined" style="position: sticky; top: 16px;">
          <v-card-title class="d-flex align-center flex-wrap gap-2">
            <span class="text-body-2 text-medium-emphasis">template.json</span>
            <v-spacer />
            <v-btn size="small" variant="text" prepend-icon="mdi-download" @click="downloadJson">Herunterladen</v-btn>
            <v-btn size="small" :color="copied ? 'success' : 'primary'" @click="copyJson">
              {{ copied ? 'Kopiert' : 'Kopieren' }}
            </v-btn>
          </v-card-title>
          <v-divider />
          <v-card-text class="pa-0">
            <pre style="font-family: ui-monospace, monospace; font-size: 12.5px; line-height: 1.6; white-space: pre-wrap; word-break: break-word; max-height: 60vh; overflow-y: auto; margin: 0; padding: 16px;">{{ outputText }}</pre>
          </v-card-text>

          <template v-if="type === 'compose'">
            <v-divider />
            <v-card-title class="d-flex align-center">
              <span class="text-body-2 text-medium-emphasis">compose.yaml — separate Datei, kein JSON</span>
              <v-spacer />
              <v-btn size="small" :color="copiedYaml ? 'success' : undefined" variant="text" @click="copyYaml">
                {{ copiedYaml ? 'Kopiert' : 'Kopieren' }}
              </v-btn>
            </v-card-title>
            <v-divider />
            <v-card-text class="pa-0">
              <pre style="font-family: ui-monospace, monospace; font-size: 12.5px; line-height: 1.6; white-space: pre-wrap; word-break: break-word; max-height: 30vh; overflow-y: auto; margin: 0; padding: 16px;">{{ compose.yaml }}</pre>
            </v-card-text>
          </template>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue';

const type = ref('compose');

const NETWORK_OPTIONS = ['bridge', 'host', 'br0', 'none'];
const SHELL_OPTIONS = ['bash', 'sh'];
const CATEGORY_OPTIONS = ['media', 'network', 'system', 'monitoring', 'backup', 'productivity', 'utilities', 'finance', 'downloaders', 'security', 'misc'];

/* ---------- Compose ---------- */
const compose = reactive({
  name: '', category: [], description: '', website: '', icon: '', support: '', webui: '', yaml: '',
});

/* ---------- Docker ---------- */
const docker = reactive({
  name: '', repo: '', category: [], registry: '', network: 'bridge', customIp: '',
  shell: 'bash', privileged: false, extra: '', post: '', webui: '', icon: '', project: '', support: '',
  description: '', cpuSet: '', includeGpus: false, gpus: [], includeNoAuto: false, noAuto: false,
});

function newPathRow()   { return { name: '', host: '', container: '', mode: 'rw', description: '', required: false }; }
function newPortRow()   { return { name: '', host: '', container: '', protocol: 'tcp', description: '', required: false, mask: false }; }
function newVarRow()    { return { name: '', key: '', value: '', description: '', required: false, mask: false }; }
function newDeviceRow() { return { name: '', host: '', container: '', description: '', required: false }; }
function newLabelRow()  { return { name: '', key: '', value: '', description: '', required: false, mask: false }; }

const paths = reactive([newPathRow()]);
const ports = reactive([]);
const variables = reactive([newVarRow()]);
const devices = reactive([]);
const labels = reactive([]);

/* ---------- Plugin ---------- */
const plug = reactive({
  name: '', author: '', description: '', category: [], arch: ['amd64'], driver: false,
  repo: '', homepage: '', icon: '', support: '', donate: '',
});

/* ---------- JSON-Aufbau ---------- */
const orNull = (v) => (v === '' || v === null || v === undefined) ? null : v;

const rowOut = (row, fields, boolFields) => {
  const o = {};
  fields.forEach((f) => { o[f] = row[f] === '' ? null : row[f]; });
  boolFields.forEach((f) => { o[f] = !!row[f]; });
  return o;
};

const composeJson = computed(() => {
  const o = {
    name: compose.name,
    category: compose.category,
    description: compose.description,
    website: orNull(compose.website),
    icon: orNull(compose.icon),
    support: orNull(compose.support),
  };
  if (compose.webui) o.web_ui_url = compose.webui;
  return o;
});

const dockerJson = computed(() => {
  const o = {
    name: docker.name,
    repo: docker.repo,
    category: docker.category,
    registry: orNull(docker.registry),
    network: orNull(docker.network),
    custom_ip: orNull(docker.customIp),
    default_shell: docker.shell || 'sh',
    privileged: docker.privileged,
    extra_parameters: orNull(docker.extra),
    post_parameters: orNull(docker.post),
    cpu_set: orNull(docker.cpuSet),
    web_ui_url: orNull(docker.webui),
    icon: orNull(docker.icon),
    project: orNull(docker.project),
    support: orNull(docker.support),
    description: docker.description,
    paths: paths.map((r) => rowOut(r, ['name', 'host', 'container', 'mode', 'description'], ['required'])),
    ports: ports.map((r) => rowOut(r, ['name', 'host', 'container', 'protocol', 'description'], ['required', 'mask'])),
    variables: variables.map((r) => rowOut(r, ['name', 'key', 'value', 'description'], ['required', 'mask'])),
    devices: devices.map((r) => rowOut(r, ['name', 'host', 'container', 'description'], ['required'])),
    labels: labels.map((r) => rowOut(r, ['name', 'key', 'value', 'description'], ['required', 'mask'])),
  };
  if (docker.includeGpus) o.gpus = docker.gpus;
  if (docker.includeNoAuto) o.no_autoupdate = docker.noAuto;
  return o;
});

const pluginJson = computed(() => ({
  name: plug.name,
  description: plug.description,
  category: plug.category,
  repository: orNull(plug.repo),
  architecture: plug.arch,
  driver: plug.driver,
  icon: orNull(plug.icon),
  author: plug.author,
  homepage: orNull(plug.homepage),
  support: orNull(plug.support),
  donate: plug.donate || '',
}));

const outputObj = computed(() => {
  if (type.value === 'docker') return dockerJson.value;
  if (type.value === 'plugin') return pluginJson.value;
  return composeJson.value;
});
const outputText = computed(() => JSON.stringify(outputObj.value, null, 2));

/* ---------- Aktionen ---------- */
const copied = ref(false);
async function copyJson() {
  await navigator.clipboard.writeText(outputText.value);
  copied.value = true;
  setTimeout(() => { copied.value = false; }, 1400);
}

const copiedYaml = ref(false);
async function copyYaml() {
  await navigator.clipboard.writeText(compose.yaml);
  copiedYaml.value = true;
  setTimeout(() => { copiedYaml.value = false; }, 1400);
}

function downloadJson() {
  const blob = new Blob([outputText.value], { type: 'application/json' });
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = 'template.json';
  a.click();
}
</script>
