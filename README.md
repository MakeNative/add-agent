# add-agent

Claude-Code-Skill von MakeNative: fügt einem Agenten-Kit (einem Claude-Code-Plugin-Repo mit `.claude-plugin/plugin.json` und `agents/`) einen neuen Agenten hinzu und verdrahtet ihn so, dass er auch wirklich gestartet wird.

## Installation

In Claude Code:

```
/plugin marketplace add MakeNative/add-agent
/plugin install add-agent@add-agent
```

Danach Claude Code einmal neu starten.

## `/add-agent:add-agent` – neuen Agenten ins Kit bringen

```
/add-agent:add-agent [Beschreibung oder Pfad zu einer Agenten-Datei]
```

Der Skill fragt Schritt für Schritt nach und schreibt erst nach deinem „go“:

| Schritt | Was passiert |
|---|---|
| Kit wählen | findet deine Kits auf der Platte oder legt ein neues Kit an |
| Agent festlegen | Beschreibung, vorhandene Datei importieren oder Lücken des Kits vorschlagen lassen |
| Platz finden | liest Agenten und Workflow-Skills des Kits und schlägt den besten Platz in der Pipeline vor (oder eine App, die den Agenten headless startet) |
| Vorschau | zeigt Name, Grenzen, Tools, Memory und alle Dateien, die sich ändern |
| Schreiben | Agenten-Datei nach den Konventionen des Kits, Verdrahtung in alle Workflows, README und Version |
| Bericht | was fertig ist und was noch offen ist |

Der Skill committet und pusht nie. Ein Agent, den weder ein Workflow noch eine App startet, gilt als nicht integriert, und der Skill sagt das auch so.

## MakeNative

Dieses Plugin ist Teil von [MakeNative](https://makenative.de). Wir arbeiten jeden Tag mit
Agenten-Pipelines und bringen anderen bei, sie für ihre eigenen Prozesse zu
bauen. Für Entwickler genauso wie für alle, die ihre Arbeit mit KI automatisieren wollen.

Du willst das für deine Arbeit lernen? Trag dich in die Liste ein:
[Bewerbung Starter-Kohorte](https://tally.so/r/5ByYj6?utm_source=github&utm_medium=readme&utm_campaign=starter-kohorte)

Christian Eichmüller · [LinkedIn](https://linkedin.com/in/christianeichmueller)

## Lizenz

MIT
