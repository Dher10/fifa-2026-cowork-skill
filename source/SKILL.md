---
name: fifa-2026-calendar
description: >
  Manages FIFA World Cup 2026 calendar events in Google Calendar. Use this skill whenever the user wants to:
  add, update, view or search World Cup 2026 matches in their calendar; get game schedules, match info,
  or venue details for the Mundial 2026; update knockout-round fixtures as teams advance; run the daily
  morning sweep ("barrido") to update finished match results; or enrich events with odds, goals, cards,
  and standout player info. Triggers on any mention of "mundial", "world cup 2026", "partidos",
  "fixture", "calendario mundial", "clasificados", "barrido", "resultados", or similar.
---

# FIFA World Cup 2026 — Calendar Management Skill

## Core references (read before any action)
- `references/matches.md` — All 104 matches: numbers, dates, times ET+COT, teams, venue FIFA names
- `references/venues.md` — All 16 stadiums: real names, key facts, capacity

Always read both files before creating or updating events.

---

## Data source for results (free, no API key)

```
https://raw.githubusercontent.com/openfootball/worldcup.json/master/2026/worldcup.json
```

Use `web_fetch` on this URL to get live match data. Response structure:
```json
{
  "name": "World Cup 2026",
  "matches": [
    {
      "round": "Matchday 1",
      "date": "2026-06-11",
      "time": "13:00 UTC-6",
      "team1": "Mexico",
      "team2": "South Africa",
      "group": "Group A",
      "ground": "Mexico City",
      "score": { "ft": [2, 1] },
      "goals1": [
        { "name": "Lozano", "minute": "23" }
      ],
      "goals2": [],
      "cards1": [
        { "name": "Herrera", "minute": "45", "card": "yellow card" }
      ],
      "cards2": []
    }
  ]
}
```

A match is **finished** when it has a `score.ft` field.
A match is **pending** when there is no `score` field.

---

## Event Format

### Title

**Pending match:**
```
🏳️ PAIS1 🆚 PAIS2 🏴 | Grupo X / Fase | Estadio FIFA Name
```

**Finished match:**
```
🏳️ PAIS1 N–N PAIS2 🏴 ✅ | Grupo X / Fase | Estadio FIFA Name
```

**Rules:**
- Leading emoji = flag of first team
- Trailing emoji = flag of second team
- Group stage: `Grupo A` through `Grupo L`
- Knockout: `R32`, `R16`, `Cuartos`, `Semifinal`, `3er Puesto`, `🏆 FINAL`
- Estadio: use FIFA name from matches.md
- For knockout TBD teams: `TBD ❓`

**Examples:**
```
🇲🇽 México 🆚 Sudáfrica 🇿🇦 | Grupo A | Mexico City Stadium
🇲🇽 México 2–1 Sudáfrica 🇿🇦 ✅ | Grupo A | Mexico City Stadium
🇨🇴 Colombia 🆚 Portugal 🇵🇹 | Grupo K | Miami Stadium
🏆 TBD ❓ 🆚 TBD ❓ | 🏆 FINAL | New York New Jersey Stadium
```

### Location
Use the **real stadium name + city** so Google Maps resolves it automatically. Never use a long address. Format:
```
[Real stadium name], [City]
```

Reference:
```
Estadio Azteca, Ciudad de México
Estadio Akron, Zapopan
Estadio BBVA, Guadalupe, Monterrey
Mercedes-Benz Stadium, Atlanta
AT&T Stadium, Arlington
NRG Stadium, Houston
SoFi Stadium, Inglewood
GEHA Field at Arrowhead Stadium, Kansas City
MetLife Stadium, East Rutherford
Lincoln Financial Field, Philadelphia
Levi's Stadium, Santa Clara
Lumen Field, Seattle
Hard Rock Stadium, Miami Gardens
Gillette Stadium, Foxborough
BMO Field, Toronto
BC Place, Vancouver
```

### Time & Duration
- Schedule in **COT (Colombia Time, UTC-5)**
- Duration: **2 hours**
- Reminder: **15 minutes** popup

### Calendar
- `calendarId`: use the **"Mundial FIFA 2026"** calendar, NOT primary
- `timeZone`: `America/Bogota`
- `reminders`: `{ useDefault: false, overrides: [{ method: "popup", minutes: 15 }] }`

---

## Description Templates

### Grupo (M01–M72) — Partido pendiente
```
⚽ PARTIDO: [Team1 vs Team2]
📅 FASE: [Grupo X — Jornada N]
🏟️ ESTADIO: [Real stadium name] — [City, Country]
🕐 HORA COT: [HH:MM AM/PM]

🏟️ SOBRE EL ESTADIO:
[Key fact from venues.md]

📊 ODDS (actualizado: [fecha]):
• [Team1]: [odd]
• Empate: [odd]
• [Team2]: [odd]
Fuente: web search

🌟 DATO DE INTERÉS:
[Historical matchup, rivalry, player to watch, record at stake]

🔗 Match ID: [M##]
```

### Eliminación (M73–M104) — Partido pendiente
```
⚽ PARTIDO: [Team1 vs Team2]
📅 FASE: [R32 / R16 / Cuartos / Semifinal / 3er Puesto / Final]
🏟️ ESTADIO: [Real stadium name] — [City, Country]
🕐 HORA COT: [HH:MM AM/PM]

📊 ODDS (actualizado: [fecha]):
• [Team1]: [odd]
• [Team2]: [odd]
Fuente: web search

⚔️ HISTORIAL DE ENFRENTAMIENTOS:
[Web search "Team1 vs Team2 head to head" — balance W/D/L, último encuentro, dato memorable]

🌟 JUGADORES CLAVE:
• [Team1 emoji] [Team1]: [Jugador 1] — [rol/razón], [Jugador 2] — [rol/razón]
• [Team2 emoji] [Team2]: [Jugador 1] — [rol/razón], [Jugador 2] — [rol/razón]
[Web search goleadores y figuras del torneo a esa fecha]

🏆 EN JUEGO:
[Qué se disputa — cupo a semis, final, título, etc.]

🔗 Match ID: [M##]
```

### Partido finalizado (cualquier fase)
```
⚽ PARTIDO: [Team1] [score] [Team2]
📅 FASE: [Grupo X / Round name]
🏟️ ESTADIO: [Real stadium name] — [City, Country]
🕐 HORA COT: [HH:MM AM/PM]

📊 RESULTADO FINAL: [Team1] [score] [Team2]

⚽ GOLES:
• [minute]' [Player name] ([Team emoji])
• [minute]' [Player name] ([Team emoji]) — penalti/aut si aplica

🟨 TARJETAS AMARILLAS:
• [minute]' [Player name] ([Team emoji])

🟥 TARJETAS ROJAS:
• [minute]' [Player name] ([Team emoji])
← omitir secciones de tarjetas si no hubo

🏆 JUGADOR DESTACADO: [Name] ([Team emoji]) — [brief reason]

🔗 Match ID: [M##]
```

---

## Workflow 1: Crear los 104 eventos (setup inicial)

1. Read `references/matches.md` and `references/venues.md`
2. Fetch data source URL to check which matches already have results
3. For each match:
   - Group (M01–M72) pending → grupo template + odds via web search
   - Group (M01–M72) finished → finished template with results
   - Knockout (M73–M104) teams known → eliminación template + historial + jugadores clave
   - Knockout (M73–M104) teams TBD → eliminación template with TBD placeholders, skip historial
4. Use calendarId "Mundial FIFA 2026", location = stadium name + city, reminder 15 min
5. Go group by group (A→L) then knockouts. Confirm count with user after each group.

---

## Workflow 2: Barrido diario matutino

1. Fetch data source URL
2. Find matches with `score.ft` that haven't been updated yet (still have 🆚 in title)
3. For each finished match:
   a. Find event in "Mundial FIFA 2026" calendar by team names or M## in description
   b. Update title: add score + ✅, replace 🆚
   c. Update description: replace odds/dato block with resultado/goles/tarjetas/jugador destacado
   d. Keep location, time, reminder unchanged
4. For knockout events: if teams are now known, update TBD → real team names + flags + historial + jugadores clave
5. Report:
   - ✅ Partidos actualizados: [list]
   - ⏳ Partidos pendientes hoy: [list with COT times]
   - 🔄 Fixtures eliminación actualizados: [list if any]

**Jugador destacado cuando no está en la fuente:**
- 1 equipo anotó 2+ → destacar al goleador
- Hubo tarjeta roja → mencionarla como momento clave
- Empate 0-0 → "Partido muy cerrado, sin destacado claro"
- Web search: `"[Team1] vs [Team2] World Cup 2026 player of the match"`

---

## Workflow 3: Actualizar fixture eliminatorio

When bracket teams become known after group stage or previous round:

1. Identify M## matches with newly known opponents
2. Find those events in calendar
3. Update title with real team names + flags
4. Update description: add historial + jugadores clave + odds via web search
5. Keep location, time, reminder unchanged

---

## Colombia 🇨🇴 — Grupo K (priority reference)

| # | Fecha COT | Rival | Sede |
|---|-----------|-------|------|
| M62 | Mié 17 Jun, 9:00 PM COT | Uzbekistán | Mexico City Stadium |
| M64 | Mar 23 Jun, 9:00 PM COT | R. D. Congo | Guadalajara Stadium |
| M65 | Sáb 27 Jun, 6:30 PM COT | Portugal | Miami Stadium |

---

## Timezone edge cases

Algunos partidos tienen kickoff a medianoche ET → caen el día anterior en COT:
- ET 12:00 AM = COT 11:00 PM del día anterior
- Ejemplo: M20 Australia vs Turquía — Sáb 13 Jun 12:00 AM ET = Vie 12 Jun 11:00 PM COT

---

## Error handling

- **API no disponible**: reintentar; si falla, reportar y omitir barrido
- **Evento no encontrado**: reportar "no encontrado — puede necesitar creación manual"
- **Tarjetas/goles vacíos**: omitir esa sección, no dejar en blanco
- **Jugador destacado no identificable**: omitir o poner "Sin dato disponible"
- **Equipos TBD en knockouts**: mantener placeholder hasta que la fuente confirme
