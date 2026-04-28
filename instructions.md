# BirdCLEF Kaggle Challenge

## Objective

Your overall goal is to win this competition by developing a machine learning framework capable of identitfying understudied species within continuous audio data.

Subgoal: Achieve a `padded cmAP` value of 0.97

## Setup

1. Read the README.md file
2. Read all the example notebooks from discussion/*. This will help you kick-start the entire experiment.
3. Read experiments.md and optionally also the notebooks in notebooks/* to understand what you've done so far.

Important considerations
- All else being equal, simpler is better
- Everything is fair game: change the architecture, the optimizer, the hyperparameters, the batch size, the model size. The only constraint is that the code runs without crashing and finishes within the time budget.
- VRAM is a soft constraint. Some increase is acceptable for meaningful gains, but it should not blow up dramatically.
- Don't submit anything to Kaggle. Keep it local.
- The goal is simple: get the highest `padded cmAP`. For some notebooks you're also given the public `padded cmAP` aka `Published Score`, which is the ultimate score (but we only can generate it for some notebooks). That is why `padded cmAP` is our best proxy.

Once you get confirmation, kick off the experimentation loop.

## Experiment Loop

LOOP FOREVER:

1. If it doesn't exist, create a file called 'experiments.md' where you add a new row with a very short description of the upcoming experiment, the name of the notebook, as well as the results
2. For each new experiment, create a new jupyter notebook
3. Decide on one type of experiment - you're free in deciding on what to focus on - everything is fair game (see above)
4. Run and evaluate the model. The goal is simple: get the highest `padded cmAP`.
5. Add the results to the markdown file from step 1

The idea is that you are a completely autonomous researcher trying things out. If they work, keep. If they don't, discard. And you're advancing the branch so that you can iterate. Try to improve the metrics, that is, `padded cmAP` over time. If you feel like you're getting stuck in some way, you can rewind but you should probably do this very very sparingly (if ever). One subgoal is to get a padded cmAP value of 0.97.

Timeout: Each experiment should take ~10 minutes total (+ a few seconds for startup and eval overhead). If a run exceeds 15 minutes, kill it and treat it as a failure (discard and revert).

Crashes: If a run crashes (OOM, or a bug, or etc.), use your judgment: If it's something dumb and easy to fix (e.g. a typo, a missing import), fix it and re-run. If the idea itself is fundamentally broken, just skip it, log "crash" as the status in the tsv, and move on.

NEVER STOP: Once the experiment loop has begun (after the initial setup), do NOT pause to ask the human if you should continue. Do NOT ask "should I keep going?" or "is this a good stopping point?". The human might be asleep, or gone from a computer and expects you to continue working indefinitely until you are manually stopped. You are autonomous. If you run out of ideas, think harder — read papers referenced in the code, re-read the in-scope files for new angles, try combining previous near-misses, try more radical architectural changes. The loop runs until the human interrupts you, period.

As an example use case, a user might leave you running while they sleep. If each experiment takes you ~5 minutes then you can run approx 12/hour, for a total of about 100 over the duration of the average human sleep. The user then wakes up to experimental results, all completed by you while they slept!