# Debate Crew

AI-powered debate system using crewAI. Two agents (debater and judge) engage in a structured debate on any given motion, followed by a final decision from the judge.

## Features

- **Debater Agent**: Presents persuasive arguments for or against a given motion
- **Judge Agent**: Evaluates arguments and decides which side is more compelling
- **Sequential Process**: Propose → Oppose → Decide workflow
- **Spanish Language**: Fully localized interface
- **Configurable**: Easy YAML configuration for agents and tasks

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

Run a debate on any topic:

```bash
crewai run --motion "¿La inteligencia artificial debería ser regulada?"
```

The system will:
1. **Propose**: Present arguments in favor of the motion
2. **Oppose**: Present arguments against the motion
3. **Decide**: Judge evaluates both sides and makes a final decision

Output files will be saved to the `output/` directory:
- `output/propose.md` - Arguments in favor
- `output/oppose.md` - Arguments against
- `output/decide.md` - Judge's decision

## Project Structure

```
debate/
├── src/debate/
│   ├── config/
│   │   ├── agents.yaml      # Agent definitions
│   │   └── tasks.yaml       # Task definitions
│   ├── crew.py              # Crew orchestration
│   └── main.py              # Entry point
├── output/                  # Generated reports
├── .env                     # Environment variables
├── pyproject.toml           # Project dependencies
└── README.md               # This file
```

## LLM Model

Currently configured to use `openai/gpt-4o-mini` for efficient and cost-effective AI-powered debates.

## Contributing

Feel free to customize the agents, tasks, or add tools to enhance the debate system.

## License

MIT