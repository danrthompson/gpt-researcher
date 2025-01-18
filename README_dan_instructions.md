## Set up the environment:
poetry install?

Once env is created, run:
source .venv/bin/activate

If you run into issues, let me know

## Set API keys as environment variables:
OPENAI_API_KEY
TAVILY_API_KEY
EXA_API_KEY

## Run multi agent researcher:
Set up the `multi_agent/task.json` file with the task you want to research.

(After activating the environment)
python multi_agent/main.py

Check the `multi_agent/output` folder for the research report

## Run single agent researcher
Use notebook/my-docs/research.ipynb

