# MOS Manifest

Manifest ist ein **MOS-Plugin**, das ein Formular für `template.json`-Einträge
aus [docker_json_templates](https://github.com/s3ppo/docker_json_templates)
direkt in die MOS-Oberfläche bringt — für `compose`, `docker` und `plugin`.

---

## Unterschied zu anderen MOS-Plugins

Dieses Plugin hat **keinen Treiber** und keinen Backend-Prozess. Es installiert
nichts auf dem System, ruft keine Query-Skripte auf und braucht daher weder
`functions`, `install.sh` noch einen Eintrag unter `/usr/bin/plugins/`.
Es liefert ausschließlich das statische Vue-Frontend — die gesamte Logik läuft
im Browser.

`driver` im MOS-Hub-Katalogeintrag (`plugins/*.json` im `docker_json_templates`-Repo)
ist entsprechend `false`.

## Aufbau

- `page/` — Vue-3-App (Vite + Module Federation), analog zu `mos-htop`/`mos-nethogs`.
  `Plugin.vue` wird von MOS als Remote-Komponente geladen und läuft im
  Vue-Kontext des Hosts — **Vuetify wird deshalb nicht als eigene Dependency
  geführt**, sondern vom MOS-Host bereitgestellt (gleiches Muster wie bei den
  bestehenden Plugins).
- `.github/workflows/build-plugin.yml` — baut das Frontend und packt es als
  `.deb` (ohne Backend-Anteil).

## Offene Punkte vor dem ersten Release

- **Icon** fehlt noch (`page/plugin.config.js` → `icon: ''`). Am besten unter
  `page/assets/` ablegen und die rohe GitHub-URL eintragen, wie bei den
  anderen Plugins.
- **Lokale Vorschau** (`npm run dev`) zeigt die Vuetify-Komponenten (`v-*`)
  nicht korrekt an, da Vuetify lokal nicht installiert ist — das ist bei
  `mos-htop`/`mos-nethogs` genauso. Ein echter Test ist erst innerhalb von
  MOS selbst möglich, bzw. für die lokale Vorschau müsste man Vuetify
  vorübergehend als Dev-Dependency ergänzen.
- **Hub-Katalogeintrag**: Damit das Plugin im MosHub erscheint, braucht es
  zusätzlich einen Eintrag unter `plugins/` im `docker_json_templates`-Repo
  (Kategorie, Architektur, Repository-URL, `driver: false` usw.) — diesen
  Eintrag kann man mit dem Manifest-Generator selbst erzeugen (Reiter „Plugin").

## Lizenz

GPL-3.0, wie die übrigen MOS-Plugins in diesem Ökosystem.
