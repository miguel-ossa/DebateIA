# Debate Crew - AI-Powered Debate System

## Project Overview

This project implements an AI-powered debate system using **CrewAI** framework. The system features two AI agents - a debater and a judge - that engage in a structured debate on any given motion (topic/mesión), followed by a final decision from the judge.

**Language**: Spanish (Español)
**Framework**: CrewAI (version 1.9.3)
**LLM Model**: OpenAI GPT-4o-mini

## Architecture

The system follows a hierarchical, sequential workflow:

```
┌─────────────────┐
│   Propose       │  Debater presents arguments IN FAVOR of the motion
└────────┬────────┘
         ↓
┌─────────────────┐
│   Oppose        │  Debater presents arguments AGAINST the motion
└────────┬────────┘
         ↓
┌─────────────────┐
│   Decide        │  Judge evaluates both arguments and makes final decision
└────────┬────────┘
         ↓
    output/*.md
```

## Technology Stack

- **Python**: >=3.10,<3.14
- **Dependency Manager**: UV (https://docs.astral.sh/uv/)
- **AI Framework**: CrewAI with tools support
- **LLM Provider**: OpenAI (GPT-4o-mini)
- **Configuration**: YAML-based configuration files

## Project Structure

```
debate/
├── src/
│   └── debate/
│       ├── __init__.py          # Package initialization
│       ├── config/
│       │   ├── agents.yaml      # Agent definitions (debater, judge)
│       │   └── tasks.yaml       # Task definitions (propose, oppose, decide)
│       ├── tools/
│       │   ├── __init__.py      # Tools module init
│       │   └── custom_tool.py   # Custom tool example (currently unused)
│       ├── crew.py              # Crew orchestration class using decorators
│       └── main.py              # CLI entry point with run/train/replay/test functions
├── output/                      # Generated debate reports
│   ├── propose.md
│   ├── oppose.md
│   └── decide.md
├── knowledge/                   # Knowledge base files
│   └── user_preference.txt
├── tests/                       # Test files
├── .env                         # Environment variables (API keys)
├── pyproject.toml               # Project dependencies and configuration
├── uv.lock                      # Dependency lock file
├── config.py                    # Environment variable loading
└── README.md                    # User-facing documentation
```

## Configuration Files

### `pyproject.toml`
Project metadata and dependencies. Defines CLI entry points:
- `debate` / `run_crew` - Run a debate
- `train` - Train the crew
- `replay` - Replay a task execution
- `test` - Test the crew
- `run_with_trigger` - Run with custom trigger payload

### `src/debate/config/agents.yaml`
Defines the two main agents:

**debater**:
- **Role**: Convincing debater
- **Goal**: Present clear arguments for or against the motion
- **LLM**: `openai/gpt-4o-mini`
- **Backstory**: Experienced debater with ability to craft concise but compelling arguments

**judge**:
- **Role**: Decide the winner based on arguments
- **Goal**: Evaluate both sides and determine which is more compelling
- **LLM**: `openai/gpt-4o-mini`
- **Backstory**: Impartial judge known for evaluating arguments without personal opinions

### `src/debate/config/tasks.yaml`
Defines the three sequential tasks:

**propose**:
- **Description**: Present arguments IN FAVOR of the motion
- **Agent**: `debater`
- **Output**: `output/propose.md`

**oppose**:
- **Description**: Present arguments AGAINST the motion
- **Agent**: `debater`
- **Output**: `output/oppose.md`

**decide**:
- **Description**: Evaluate both arguments and decide which side is more compelling
- **Agent**: `judge`
- **Output**: `output/decide.md`

## Usage

### Setup

1. Install UV: `pip install uv`
2. Install dependencies: `uv sync`
3. Add your API key to `.env`:
   ```
   OPENAI_API_KEY=your_api_key_here
   ```

### Running a Debate

**Basic usage:**
```bash
# Run with default motion
crewai run

# Run with custom motion
crewai run --motion "¿Deberían las IA ser reguladas por el gobierno?"
```

**Advanced usage:**
```bash
# Run with custom trigger payload
crewai run_with_trigger '{"motion": "custom topic here"}'
```

### Additional CrewAI Commands

```bash
# Train the crew (n_iterations, filename)
crewai train 10 results.json

# Test the crew (n_iterations, eval_llm)
crewai test 5 gpt-4

# Replay a specific task
crewai replay task_id
```

## Key Implementation Details

### Crew Orchestration (`src/debate/crew.py`)

The `Debate` class uses CrewAI's decorator-based API:
- `@agent` decorator for defining agents (debater, judge)
- `@task` decorator for defining tasks (propose, oppose, decide)
- `@crew` decorator for creating the crew with `Process.sequential` workflow
- Agents and tasks are auto-loaded from YAML configuration files via `self.agents_config` and `self.tasks_config`

### CLI Interface (`src/debate/main.py`)

Main entry points:
- **`run()`**: Executes debate with hardcoded motion from line 21
- **`train()`**: Runs crew training for specified iterations with sample data
- **`replay()`**: Replays execution from a specific task ID
- **`test()`**: Tests crew execution with evaluation
- **`run_with_trigger()`**: Executes with JSON payload argument for external triggers

## Coding Conventions

1. **YAML Configuration**: Always use YAML files (`agents.yaml`, `tasks.yaml`) for configuration. Avoid hardcoding agents/tasks in Python code.

2. **Spanish Language**: The entire application is in Spanish - maintain this language in all messages, configurations, and outputs.

3. **Process Flow**: Maintain the sequential workflow (propose → oppose → decide). Do not change the order without good reason.

4. **Output Files**: Debate results are always saved to `output/*.md` with clear file naming (`propose.md`, `oppose.md`, `decide.md`).

5. **Agent Configuration**: When adding new agents:
   - Add definition to `agents.yaml`
   - Update task `agent` field in `tasks.yaml`
   - Add agent method to `crew.py` using `@agent` decorator

6. **Task Configuration**: When adding new tasks:
   - Add definition to `tasks.yaml` with description, expected_output, agent, and output_file
   - Add task method to `crew.py` using `@task` decorator

7. **Environment Variables**: Load `.env` file via `config.py` at module import time. All API keys should be in `.env`, not hardcoded.

8. **LLM Selection**: Currently using `openai/gpt-4o-mini` for both agents. Change this globally in `agents.yaml` for all agents or individually per agent.

## Development Notes

- The `custom_tool.py` file contains an example custom tool but is not currently used in the crew
- Knowledge files in `knowledge/` directory are available for agents but not actively configured in the current implementation
- CrewAI's `@before_kickoff` and `@after_kickoff` decorators are available for hooking into execution flow (see crew.py comments)
- The project supports hierarchical process (`Process.hierarchical`) as commented out in `crew.py` but currently uses sequential

## Common Tasks

### Adding a New Argument

To add more argument types or modify existing ones:
1. Edit `src/debate/config/tasks.yaml` to add or modify task definitions
2. The debater agent will automatically handle the arguments based on task descriptions

### Modifying Agent Behavior

To change how agents behave:
1. Edit `src/debate/config/agents.yaml`
2. Modify `role`, `goal`, or `backstory` fields
3. Change `llm` if needed

### Adding Custom Tools

1. Edit `src/debate/tools/custom_tool.py` to implement your tool
2. Import and register the tool in `crew.py` (not yet implemented in current version)

## License

MIT