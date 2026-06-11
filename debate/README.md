# Debate Crew

AI-powered debate system using [crewAI](https://docs.crewai.com/). Two agents (debater and judge) engage in a structured debate on any given motion, followed by a final decision from the judge.

## Features

- **Debater Agent**: Presents persuasive arguments for or against a given motion
- **Judge Agent**: Evaluates arguments and decides which side is more compelling
- **Sequential Process**: Propose → Oppose → Decide workflow
- **Spanish Language**: Fully localized interface
- **Configurable**: Easy YAML configuration for agents and tasks
- **Multiple Modes**: Run, train, test, and replay crew executions

## Installation

Ensure you have Python >=3.10 <3.14 installed on your system. This project uses [UV](https://docs.astral.sh/uv/) for dependency management.

```bash
# Install UV
pip install uv

# Install dependencies
uv sync
```

## Configuration

1. Add your `OPENAI_API_KEY` to the `.env` file
2. Configure agents in `src/debate/config/agents.yaml`
3. Configure tasks in `src/debate/config/tasks.yaml`

## Usage

### Run a Debate

```bash
# Run with default motion
crewai run

# Run with custom motion
crewai run --motion "¿Deberían las IA ser reguladas por el gobierno?"
```

The system executes three sequential tasks:
1. **Propose** (`propose.md`) - Arguments in favor of the motion
2. **Oppose** (`oppose.md`) - Arguments against the motion
3. **Decide** (`decide.md`) - Judge's final decision

Output files are saved to the `output/` directory.

### Additional Commands

```bash
# Train the crew (n_iterations, filename)
crewai train 10 results.json

# Test the crew (n_iterations, eval_llm)
crewai test 5 gpt-4

# Replay a specific task
crewai replay task_id

# Run with custom trigger payload
crewai run --trigger '{"motion": "custom topic here"}'
```

## Project Structure

```
debate/
├── src/debate/
│   ├── config/
│   │   ├── agents.yaml      # Agent definitions (debater, judge)
│   │   └── tasks.yaml       # Task definitions (propose, oppose, decide)
│   ├── crew.py              # Crew orchestration class
│   └── main.py              # Entry point with run/train/replay/test functions
├── output/                  # Generated debate reports
├── .env                     # Environment variables (API keys)
├── pyproject.toml           # Project dependencies
└── README.md               # This file
```

## Agent Configuration

### Debater Agent
- **Role**: Convincing debater
- **Goal**: Present clear arguments for or against the motion
- **LLM**: `openai/gpt-4o-mini`

### Judge Agent
- **Role**: Decide the winner based on arguments
- **Goal**: Evaluate both sides and determine which is more compelling
- **LLM**: `openai/gpt-4o-mini`

## Workflow

```
┌─────────────────┐
│   Propose       │  Debater presents arguments IN FAVOR
└────────┬────────┘
         ↓
┌─────────────────┐
│   Oppose        │  Debater presents arguments AGAINST
└────────┬────────┘
         ↓
┌─────────────────┐
│   Decide        │  Judge evaluates and makes final decision
└─────────────────┘
         ↓
    output/*.md
```

## License

MIT