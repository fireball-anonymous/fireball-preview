# FIREBALL Preview

This repo contains a single combat instance in the same format found in the full release of FIREBALL.

`raw.jsonl` consists of all the events in the combat instance, with one event per line.

`filtered_triples.jsonl` consists of the results of running the raw events through the distillation process specified in
the paper, with each line containing a (before, commands, after) triple along with associated metadata such as the
combat states.

All user IDs and usernames have been randomized (by way of a hash function) to preserve anonymity.

FIREBALL-preview is released for research purposes only under a CC-BY-4.0 license.
