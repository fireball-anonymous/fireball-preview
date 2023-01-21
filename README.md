# FIREBALL Preview

This repo contains a single combat instance in the same format found in the full release of FIREBALL.

`raw.jsonl` consists of all the events in the combat instance, with one event per line.

`filtered_triples.jsonl` consists of the results of running the raw events through the distillation process specified in
the paper, with each line containing a (before, commands, after) triple along with associated metadata such as the
combat states.

All user IDs and usernames have been randomized (by way of a hash function) to preserve anonymity.

FIREBALL-preview is released for research purposes only under a CC-BY-4.0 license.

## Event Schema (raw.jsonl)

Each line contains a recorded event, which must be one of the following:

```text
- message
- alias_resolution
- snippet_resolution
- command
- button_press
- automation_run
- combat_state_update
```

The meanings of these events are discussed in Appendix A of the FIREBALL paper. Each event contains at least the
following keys:

- `combat_id`: Used for data partitioning, should be the same across all events in a gameplay instance.
- `event_type`: One of the above. Defines the other keys available in this event body.
- `timestamp`: The time this event was generated, as a UNIX timestamp.

The remaining event schema is defined in `event_definitions.py`.

## Filtered Triples Schema (filtered_triples.jsonl)

Each line contains a filtered triple, each of which includes the following keys:

```text
{
    "speaker_id": The anonymized user ID of the user who sent the commands in the triple.
    "before_utterances": A list of strings corresponding to the "preceding" utterances in the triple.
    "combat_state_before": A list of normalized actor states (see below) for each actor in the combat instance at the instant before the command was run.
    "current_actor": (nullable) The normalized actor state of the actor whose turn it currently is.
    "commands_norm": A list of strings corresponding to the "commands" portion of the triple.
    "automation_results": A mechanically generated list of strings representing the results of running the action in the Avrae engine.
    "caster_after": The normalized actor state of the actor who ran the action(s), which may or may not be the current actor.
    "targets_after": A list of normalized actor states for each actor who was targeted by the action.
    "combat_state_after": A list of normalized actor states for each actor in the combat instance at the instant after the command was run.
    "after_utterances": A list of strings corresponding to the "folllowing" utterances in the triple.
    "utterance_history": The last 5 messages in the chat history before the command was run.
    "before_idxs": A list of integers corresponding to the index of the "message" events containing the "preceding" utterances in the raw event file.
    "before_state_idx": The index of the "combat_state_update" event in the raw event file that was used to derive "combat_state_before".
    "command_idxs": The indexes of the "command" events corresponding to the "commands_norm" key.
    "after_state_idx": The index of the "combat_state_update" event corresponding to the "combat_state_after" key.
    "after_idxs": The indexes of the "message" events corresponding to the "after_utterances" key.
    "embed_idxs": (nullable, same length as "automation_results") The indexes of "message" events corresponding to rich results shown to players on Discord for each result in the "automation_results" key.
}
```

### Normalized Actor State

The normalized actor state is only a subset of the available actor information, corresponding to the information we
used for our engineering experiments for the FIREBALL paper. For a full list of available actor information, see table 1
in the FIREBALL paper.

```text
{
    "name": The name of the actor.
    "hp": The numerical and narrative hit points (e.g. "<12/34; Bloodied>").
    "class": The actor's class(es) and level(s), if applicable (e.g. "Fighter 3")
    "race": The actor's race, if applicable (e.g. "Mountain Dwarf", "Adult Red Dragon").
    "attacks": A list of the actor's available attack names.
    "spells": A list of the actor's available spells.
    "actions": A list of the actor's available special abilities.
    "effects": A list of any temporary effects on the actor (e.g. "Stunned").
    "description": The actor's narrative description (if available).
    "controller_id": The anonymized user ID of this actor's controller.
}
```
