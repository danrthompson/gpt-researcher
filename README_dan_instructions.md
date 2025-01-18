## Set up the environment:
poetry install?

Once env is created, run:
source .venv/bin/activate

If you run into issues, let me know

## Set API keys as environment variables:
OPENAI_API_KEY - Required for OpenAI LLM access
TAVILY_API_KEY - Required for web search functionality
EXA_API_KEY - Required for additional web search capabilities

## Core Configuration Options
You can customize the core behavior of GPTResearcher by modifying the configuration values. These can be set in three ways:
1. Environment variables
2. Custom config file (specified via `config_path` in task.json)
3. Default values in `gpt_researcher/config/variables/default.py`

### Available Configuration Options:

```python
# Search and Retrieval
RETRIEVER: str = "tavily"
# Possible values:
# - "tavily" (default) - Tavily search API
# - "exa" - Exa.ai search API
# - "tavily,exa" - Use both in sequence
# - "google" - Google Custom Search
# - "duckduckgo" - DuckDuckGo search
# - "searx" - SearX meta search

MAX_SEARCH_RESULTS_PER_QUERY: int = 15  # Any positive integer (1-50 recommended)
CURATE_SOURCES: bool = False  # true/false - Enable source validation

# Language Models
FAST_LLM: str = "openai:gpt-4o-mini"
# Possible providers:
# - "openai:" - OpenAI models
# - "anthropic:" - Anthropic models
# - "azure:" - Azure OpenAI models
# - "ollama:" - Local Ollama models
# Common models:
# - "openai:gpt-4-turbo"
# - "openai:gpt-3.5-turbo"
# - "anthropic:claude-3-opus"
# - "anthropic:claude-3-sonnet"

SMART_LLM: str = "openai:gpt-4o"  # Same providers/models as FAST_LLM
STRATEGIC_LLM: str = "openai:o1-mini"  # Same providers/models as FAST_LLM

TEMPERATURE: float = 0.4  # 0.0 to 1.0 (0.0 = focused, 1.0 = creative)
LLM_TEMPERATURE: float = 0.55  # 0.0 to 1.0 (0.0 = focused, 1.0 = creative)

# Token Management
FAST_TOKEN_LIMIT: int = 2000      # Any positive integer (2000-4000 recommended)
SMART_TOKEN_LIMIT: int = 4000     # Any positive integer (4000-8000 recommended)
STRATEGIC_TOKEN_LIMIT: int = 4000  # Any positive integer (4000-8000 recommended)
SUMMARY_TOKEN_LIMIT: int = 700    # Any positive integer (500-1000 recommended)
BROWSE_CHUNK_MAX_LENGTH: int = 8192  # Any positive integer (4096-16384 recommended)

# Embeddings
EMBEDDING: str = "openai:text-embedding-3-small"
# Possible providers/models:
# - "openai:text-embedding-3-small" (default)
# - "openai:text-embedding-3-large"
# - "openai:text-embedding-ada-002"
# - "huggingface:sentence-transformers/all-MiniLM-L6-v2"
# - "huggingface:BAAI/bge-large-en-v1.5"
# - "ollama:nomic-embed-text"

SIMILARITY_THRESHOLD: float = 0.42  # 0.0 to 1.0 (0.4 to 0.8 recommended)

# Research Control
MAX_ITERATIONS: int = 8  # Any positive integer (5-15 recommended)
TOTAL_WORDS: int = 1000  # Any positive integer
MAX_SUBTOPICS: int = 3   # Any positive integer (2-5 recommended)

# Output Configuration
REPORT_FORMAT: str = "APA"
# Possible values:
# - "APA" - APA style
# - "MLA" - MLA style
# - "Chicago" - Chicago style
# - "Harvard" - Harvard style
# - "IEEE" - IEEE style

LANGUAGE: str = "english"
# Any valid language code or name:
# - "english" (default)
# - "spanish"
# - "french"
# - "german"
# - "chinese"
# - etc.

REPORT_SOURCE: str = "web"
# Possible values:
# - "web" (default) - Internet search
# - "docs" - Local documents
# - "both" - Combined sources

DOC_PATH: str = "./my-docs"  # Any valid directory path

# Technical Settings
MEMORY_BACKEND: str = "local"
# Possible values:
# - "local" (default) - Local file storage
# - "redis" - Redis database
# - "postgres" - PostgreSQL database
# - "pinecone" - Pinecone vector database
# - "weaviate" - Weaviate vector database

SCRAPER: str = "bs"
# Possible values:
# - "bs" (default) - BeautifulSoup
# - "selenium" - Selenium WebDriver
# - "playwright" - Playwright
# - "trafilatura" - Trafilatura

USER_AGENT: str  # Any valid user agent string
```

### Configuration Format Examples:

1. Environment Variables:
```bash
export RETRIEVER="exa,tavily"  # Use multiple retrievers
export SMART_LLM="openai:gpt-4"
export MAX_ITERATIONS=12
```

2. Custom Config File (config.json):
```json
{
  "RETRIEVER": "tavily",
  "MAX_SEARCH_RESULTS_PER_QUERY": 20,
  "SMART_LLM": "openai:gpt-4-turbo",
  "TEMPERATURE": 0.5
}
```

### Important Notes:
1. LLM Format: `provider:model` (e.g., "openai:gpt-4", "anthropic:claude-3")
2. Embedding Format: `provider:model` (e.g., "openai:text-embedding-3-small")
3. Token Limits: Higher limits = more context but higher costs
4. Temperature: Higher = more creative, Lower = more focused
5. Multiple Retrievers: Comma-separated list (e.g., "tavily,exa")

## Run multi agent researcher:
Set up the `multi_agent/task.json` file with the task you want to research.

### Task Configuration Options:
```json
{
  // Required fields
  "query": "Your research question here",  // The main research question to investigate

  // Optional fields with detailed explanations
  "guidelines": [
    // List of guidelines that control report formatting and content requirements
    // Each guideline is strictly followed if follow_guidelines is true
    "The report MUST be written in APA format",
    "Each sub section MUST include supporting sources"
  ],

  "max_sections": 3,  // Limits the number of main sections in the report (default: 5)

  "include_human_feedback": false,  // If true, pauses research after planning for human input

  "model": "gpt-4",  // LLM model selection
  // Available models:
  // - "gpt-4-turbo" (default)
  // - "gpt-4"
  // - "gpt-3.5-turbo"
  // - "claude-3-opus"
  // - "claude-3-sonnet"

  "follow_guidelines": true,  // Enforces strict adherence to provided guidelines

  "publish_formats": {
    // Controls which output formats to generate
    "markdown": true,  // Always generates markdown
    "pdf": true,      // Converts to PDF using md2pdf
    "docx": true      // Converts to DOCX using python-docx
  },

  "verbose": true,  // Enables detailed logging of the research process

  // GPT Researcher Advanced Parameters
  "gpt_researcher_params": {
    "source_urls": [
      // List of specific URLs to use as research sources
      // Example: ["https://example.com/article1", "https://example.com/article2"]
    ],

    "document_urls": [
      // List of document URLs to analyze (PDFs, DOCXs)
      // Example: ["https://example.com/paper.pdf"]
    ],

    "complement_source_urls": false,  // If true, finds additional sources beyond provided URLs

    "report_format": "markdown",  // Output format for the research report
    // Available formats:
    // - "markdown" (default)
    // - "pdf"
    // - "docx"

    "report_type": "research_report",  // Type of report to generate
    // Available types:
    // - "research_report" (default) - Comprehensive academic style
    // - "outline_report" - High-level overview
    // - "analysis_report" - Detailed analysis with pros/cons
    // - "comparison_report" - Comparative analysis
    // - "summary_report" - Brief summary
    // - "detailed_report" - In-depth investigation
    // - "basic_report" - Simple findings presentation

    "report_source": "web",  // Primary source of research information
    // Available sources:
    // - "web" (default) - Internet search
    // - "docs" - Local documents only
    // - "both" - Combines web and documents

    "max_subtopics": 5,  // Maximum number of subtopics to explore per section

    "tone": "objective",  // Writing style and perspective
    // Available tones:
    // - "objective" (default) - Neutral, fact-based
    // - "critical" - Analytical, questioning
    // - "optimistic" - Positive, opportunity-focused
    // - "balanced" - Pros and cons
    // - "academic" - Scholarly, formal
    // - "persuasive" - Convincing, argument-based

    "documents": [
      // List of local document paths to analyze
      // Example: ["./docs/paper1.pdf", "./docs/data.docx"]
    ],

    "vector_store": null,  // Custom vector store configuration for document retrieval
    // Can be configured with:
    // - null (default) - Uses internal storage
    // - Custom vector store instance
    // - Connection string to existing store

    "vector_store_filter": null,  // Query filters for vector store searches
    // Example: {"type": "academic", "year": "2023"}

    "config_path": null  // Path to custom configuration file
    // Example: "./config/custom_research_config.json"
  }
}
```

### Parameter Dependencies and Interactions:
1. When `include_human_feedback` is true:
   - Research pauses after planning phase
   - User can modify research direction
   - Affects overall research time

2. When `complement_source_urls` is true:
   - Additional sources are found even if `source_urls` provided
   - May increase research time
   - Improves source diversity

3. Report Type and Tone combinations:
   - "research_report" + "academic" = Traditional scholarly paper
   - "analysis_report" + "critical" = In-depth critique
   - "summary_report" + "objective" = Quick factual overview

4. Source Configuration Priority:
   - `source_urls` (highest priority)
   - `document_urls` and `documents`
   - Web search results (if no sources specified)

### Output Directory Structure:
```
multi_agent/output/
├── run_{timestamp}_{query}/
│   ├── report.md
│   ├── report.pdf (if pdf=true)
│   └── report.docx (if docx=true)
```

(After activating the environment)
python multi_agent/main.py

Check the `multi_agent/output` folder for the research report

## Run single agent researcher
Use notebook/my-docs/research.ipynb

