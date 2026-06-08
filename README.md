# fleet-midi-modulation

_Modulation rate — vibrato, tremolo, and LFO speed._

_One of 16 ternary MIDI agents in the [Live Paradigm Fleet](https://github.com/SuperInstance/sailor-workspace)._

---

## Philosophy — Why Ternary?

The Live Paradigm treats musical gestures as ternary operations. Where binary logic
gives yes/no, ternary gives **approve/reject/observe** — a richer cognitive substrate
that maps naturally to music theory, emotional tension, and conversational flow.

This agent implements **ternary decomposition for modulation**.

## Architecture

Position in the fleet pipeline:

```
🎤 Voice → OpenSMILE (25 features) → Ghost Track (T-0..T-4 CR predictions)
  → tminus-dispatcher (cue scheduling) → Fleet Conductor (routing)
  → modulation (port 2168)
```

## API Reference

| Method | Path | Description |
|--------|------|-------------|
| GET | /health | Health check + agent identity |
| POST | /agent with `{"type":"probe"}` | Liveness probe for fleet-conductor |
| POST | /agent | Process musical data, return ternary analysis |
| POST | / | Direct query with JSON body |

### Response Format

```json
{
  "status": "ok",
  "agent": "fleet-midi-modulation",
  "port": 2168,
  "ternary_vector": [0, 0, 0],
  "ternary_invariant": 0,
  "closed_gesture": false
}
```

## Ternary Logic

| Position | +1 | 0 | -1 |
|----------|------|------|------|
| ternary[0] | fast/trill | off/steady | slow/drone |

## Educational Supplement

Modulation is periodic variation of a sound parameter. The most common types:

- **Vibrato**: Pitch modulation (CC#1, modulation wheel)
- **Tremolo**: Amplitude modulation
- **Filter modulation**: Timbre modulation (CC#74, cutoff frequency)

### Modulation Rate
- **Slow (-1)**: <1Hz — drone, slow pulse, hypnotic
- **Off (0)**: No modulation, static
- **Fast (+1)**: >5Hz — trill, wobble, vibrato

### Modulation Depth
How much the parameter changes. Deep modulation = wide sweep.

## Fleet Integration

- **Port**: 2168
- **Roles**: spatial
- **Conductor ID**: `modulation`
- **Protocol**: HTTP POST to `/2168/agent` with JSON body, 5s timeout
- **Conservation Law**: Σ(Δ_midi) = 4 × Σ(ternary) — closed gestures return to start

## Starting

Local development:

```bash
python3 engine.py --port 2168
```

Or via the fleet start script:

```bash
./scripts/start-fleet-agents.sh
```

## Credits

**Part of the Live Paradigm Fleet** — A ternary cognitive architecture for musical AI.
GitHub: github.com/SuperInstance
Fleet conductor: [sailor-workspace](https://github.com/SuperInstance/sailor-workspace)
