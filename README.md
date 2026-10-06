# robonuke_project_template

The starting point for a research project built on
[robonuke_rl_core](https://github.com/RoboNuke/robonuke_rl_core). One paper = one repo made
from this template.

## Start a project

1. GitHub: **Use this template** -> name the new repo after the project.
2. Rename `my_project/` to your project's name and fix the imports in `scripts/*.py`
   (`setup()` is the only place they appear).
3. Make sure the package is installed (once, for all projects):

   ```bash
   conda activate general
   pip install -e ~/robonuke_rl_core
   ```

   The package expects an Isaac Lab environment (built against Isaac Lab 0.47.1 /
   Isaac Sim 5.1.0 / Python 3.11) and brings its other dependencies itself.

## What lives where

| piece | where | registered by |
| --- | --- | --- |
| environments (env + task cfg + `gym.register`) | `my_project/tasks/` | importing the module in `setup()` |
| config sections (`@dataclass` + `validate`) | `my_project/cfg.py` | `register_section` in `setup()` |
| auxiliary losses (`AuxLoss` subclasses) | `my_project/losses.py` | `@register_loss`, imported in `setup()` |
| experiment YAMLs (`base:` chains, override only what differs) | `configs/` | `--config` / `--eval_config` |
| overlays / model architectures | same pattern: register in `setup()` | package CLAUDE.md recipes |

The demo task (`Isaac-Forge-DemoPegInsert-Direct-v0`) subclasses Isaac Lab's Forge peg
insert unchanged — "forge" in the task name is what lets the package's Forge wrappers
(controller, fragile peg, efficient reset, contact sensing) apply. Replace its `pass`
bodies with your observations, rewards and resets.

## Run

```bash
# train (3 agents, hybrid force/position control, fragile peg)
python scripts/train.py --config configs/experiments/hybrid_fragile.yaml --headless

# evaluate / record a trained run (downloads policy + config from the wandb run's files)
python scripts/eval.py --run hur/template_demo/<run_name> \
    --eval_config configs/eval/quick.yaml --headless [--record]

# watch it live (j pause / k reset / l quit), or inspect the reset distribution
python scripts/debug.py --run hur/template_demo/<run_name>
python scripts/debug.py --run hur/template_demo/<run_name> --resets --hold_seconds 2
```

`configs/experiments/match_fragile.yaml` is the MATCH variant: the selection-conditioned
distribution plus the supervised selection loss.

## Config sections this project adds

| field | type | default | what it does |
| --- | --- | --- | --- |
| `demo.reward_scale` | float | `1.0` | example field of the example section — replace with your project's |
