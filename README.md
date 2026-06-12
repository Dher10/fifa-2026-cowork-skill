# FIFA World Cup 2026 — Calendar Skill

Skill de Claude para gestionar el calendario del Mundial 2026 (México, USA, Canadá) en Google Calendar.

## Contenido

- `fifa-2026-calendar.skill` — paquete listo para instalar en Claude (Settings → Skills)
- `source/SKILL.md` — instrucciones del skill (formato de eventos, workflows)
- `source/references/matches.md` — los 104 partidos: fechas, horas ET/COT, equipos, sede
- `source/references/venues.md` — los 16 estadios: nombre, ciudad, datos de interés

## Cómo usarlo

1. Sube `fifa-2026-calendar.skill` en Claude → Settings → Skills
2. Crea un calendario dedicado en Google Calendar llamado "Mundial FIFA 2026"
3. En el chat o Cowork, pide: "Crea los partidos del Mundial 2026 en mi Google Calendar usando el skill fifa-2026-calendar"

## Barrido diario

El skill incluye un workflow de "barrido" para actualizar resultados, goles, tarjetas y jugador destacado conforme se juegan los partidos. Configúralo como tarea programada (Cowork → Scheduled) con el prompt documentado en `source/SKILL.md`.

## Fuente de datos

Resultados en vivo desde [openfootball/worldcup.json](https://github.com/openfootball/worldcup.json) — gratuito, sin API key.
