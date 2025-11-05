# ServerMind MCP Server

<div align="center">

![ServerMind Banner](https://img.shields.io/badge/ServerMind-MCP%20Server-blue?style=for-the-badge)
[![Python Version](https://img.shields.io/badge/python-3.8%2B-blue)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![MCP Compatible](https://img.shields.io/badge/MCP-Compatible-orange)](https://github.com/modelcontextprotocol)

**A powerful Model Context Protocol (MCP) server that bridges AI assistants with enterprise observability platforms**

[Features](#features) • [Installation](#installation) • [Quick Start](#quick-start) • [Documentation](#documentation) • [Contributing](#contributing)

</div>

---

## 🎯 Overview

**ServerMind MCP Server** is an enterprise-grade integration layer that connects AI assistants (like Claude, ChatGPT, and other MCP-compatible clients) with Splunk for intelligent log analysis, automated issue detection, and proactive system monitoring. Built on the Model Context Protocol (MCP), it enables AI-powered observability workflows that transform how teams interact with their infrastructure.

### What Problems Does It Solve?

- 🔍 **Log Analysis Complexity**: Navigate millions of log entries with AI-powered queries
- 🚨 **Alert Fatigue**: Automatically analyze and prioritize critical issues
- 🔗 **Tool Fragmentation**: Single interface for Splunk, JIRA, and GitHub workflows
- ⚡ **Manual Troubleshooting**: Automate root cause analysis and issue creation
- 📊 **Data Silos**: Correlate logs, traces, and errors across systems

### Key Differentiators

- **AI-Native Design**: Purpose-built for AI assistant integration
- **Enterprise-Ready**: Production-grade security, monitoring, and error handling
- **Extensible Architecture**: Plugin-based tool system for custom workflows
- **Automated Workflows**: Chain multiple operations with intelligent orchestration
- **Real-time Monitoring**: Continuous log streaming with buffered analysis

---

## ✨ Features

### 🔎 Splunk Integration

#### Core Search & Analysis
- **Advanced SPL Queries**: Execute complex Search Processing Language queries with validation
- **Smart Query Optimization**: Automatic query analysis with performance suggestions
- **Index Discovery**: Comprehensive index listing with size, event counts, and time ranges
- **Multi-format Export**: JSON, CSV, and XML export with field filtering
- **Real-time Monitoring**: Continuous log streaming with configurable intervals

#### Intelligent Log Analysis
- **Trace-based Search**: Find all logs associated with specific trace IDs
- **Error Pattern Detection**: Automatic identification of recurring error patterns
- **Log Correlation**: Group related logs by trace ID, session, or custom fields
- **Time-series Analysis**: Analyze log trends over time with statistical insights

### 🤖 AI-Powered Workflows

#### Automated Issue Creation
- **Smart Error Analysis**: AI-driven analysis of Splunk errors with severity classification
- **Auto-categorization**: Intelligent grouping of similar errors to reduce noise
- **Multi-platform Support**: Create issues in JIRA or GitHub via external MCP servers
- **Rich Context**: Include log snippets, traces, and analysis in issue descriptions
- **Custom Labels & Assignment**: Automatic tagging and assignee selection

#### Prompt-Driven Operations
- **Chain-based Processing**: Multi-stage workflows for complex analysis tasks
- **Template System**: Reusable prompt templates for common operations
- **Root Cause Analysis**: AI-guided investigation with structured prompts
- **Log Evaluation**: Automated log quality and completeness assessment
- **Query Generation**: Natural language to SPL query conversion

### 🏗️ Advanced Capabilities

#### Development Tools
- **Index Discovery**: Find Splunk indexes referenced in codebases
- **Environment Resolution**: Automatic Splunk instance and index identification
- **Debug Workflows**: Structured debugging processes with guided prompts

#### Integration & Extensibility
- **External MCP Servers**: Leverage Atlassian and GitHub MCP servers for issue management
- **API-First Design**: RESTful architecture with SSE transport
- **Event Streaming**: Server-Sent Events (SSE) for real-time updates
- **Health Monitoring**: Built-in health checks and status endpoints

---

## 🚀 Quick Start

### Prerequisites

- **Python**: 3.8 or higher
- **Splunk Instance**: Access to a Splunk deployment with API enabled
- **Credentials**: Valid Splunk username and password or API token
- **Optional**: GitHub token (for GitHub integration), JIRA credentials (for JIRA integration)

### Installation

#### Method 1: Standard Installation

```bash
# Clone the repository
git clone https://github.com/ChathuRaaksha/Server-Mind.git
cd Server-Mind

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

#### Method 2: Development Installation

```bash
# Clone and install in editable mode
git clone https://github.com/ChathuRaaksha/Server-Mind.git
cd Server-Mind
pip install -e .
```

#### Method 3: Docker Deployment

```bash
# Build the Docker image
docker build -t servermind-mcp-server .

# Run with environment variables
docker run -p 9090:9090 \
  -e SPLUNK_HOST=your-splunk-host \
  -e SPLUNK_USERNAME=your-username \
  -e SPLUNK_PASSWORD=your-password \
  servermind-mcp-server

# Or use docker-compose (see docker-compose.yml)
docker-compose up -d
```

### Configuration

1. **Copy the example environment file**:
```bash
cp .env.example .env
```

2. **Edit `.env` with your credentials**:
```bash
# Required: Splunk Configuration
SPLUNK_HOST=splunk.example.com
SPLUNK_PORT=8089
SPLUNK_USERNAME=admin
SPLUNK_PASSWORD=your-secure-password
SPLUNK_SCHEME=https
SPLUNK_VERIFY_SSL=true

# Optional: Advanced Settings
MCP_SERVER_NAME=servermind-mcp-server
LOG_LEVEL=INFO
MCP_MAX_RESULTS_DEFAULT=100
MCP_SEARCH_TIMEOUT=300
```

3. **Start the server**:
```bash
python src/server.py

# Or specify a custom port
python src/server.py 8756
```

### Connecting to MCP Clients

#### For Cline (SSE Transport)

Add to your `~/.config/Code/User/globalStorage/rooveterinaryinc.roo-cline/settings/cline_mcp_settings.json`:

```json
{
  "mcpServers": {
    "servermind": {
      "type": "sse",
      "url": "http://127.0.0.1:8756/",
      "timeout": 60,
      "disabled": false
    }
  }
}
```

#### For Claude Desktop (stdio)

Add to your `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "servermind": {
      "command": "python",
      "args": ["/path/to/servermind-mcp-server/src/server.py"],
      "env": {
        "SPLUNK_HOST": "splunk.example.com",
        "SPLUNK_USERNAME": "admin",
        "SPLUNK_PASSWORD": "your-password"
      }
    }
  }
}
```

---

## 📚 Documentation

### Available Tools

#### 1. splunk_search
Execute Splunk search queries with intelligent validation and formatting.

**Parameters:**
- `query` (string, required): SPL search query
- `earliest_time` (string): Start time (default: "-24h")
- `latest_time` (string): End time (default: "now")
- `max_results` (integer): Maximum results (default: 100, max: 10000)
- `timeout` (integer): Search timeout in seconds (default: 300)

**Example:**
```json
{
  "query": "index=web_logs status=500 | timechart span=1h count by host",
  "earliest_time": "-7d",
  "max_results": 500
}
```

**Returns:**
- Formatted search results with metadata
- Performance analysis and optimization suggestions
- Result statistics and patterns

#### 2. splunk_indexes
List and analyze available Splunk indexes.

**Parameters:**
- `filter_pattern` (string): Filter index names (case-insensitive)
- `include_disabled` (boolean): Include disabled indexes (default: true)
- `sort_by` (string): Sort field - 'name', 'size', 'events', 'earliest', 'latest' (default: 'name')
- `sort_order` (string): 'asc' or 'desc' (default: 'asc')

**Example:**
```json
{
  "filter_pattern": "web",
  "sort_by": "size",
  "sort_order": "desc"
}
```

#### 3. splunk_export
Export search results in multiple formats.

**Parameters:**
- `query` (string, required): SPL search query
- `format` (string): Export format - 'json', 'csv', 'xml' (default: 'json')
- `earliest_time` (string): Start time (default: "-24h")
- `latest_time` (string): End time (default: "now")
- `max_results` (integer): Maximum results (default: 1000, max: 50000)
- `fields` (array): Specific fields to export (optional)

**Example:**
```json
{
  "query": "index=security failed_login | stats count by src_ip",
  "format": "csv",
  "fields": ["src_ip", "count", "_time"]
}
```

#### 4. splunk_monitor
Continuous log monitoring with buffered results.

**Parameters:**
- `action` (string, required): 'start', 'stop', 'status', 'get_results'
- `query` (string): SPL query to monitor (required for 'start')
- `interval` (integer): Check interval in seconds (default: 60)
- `max_results` (integer): Results per check (default: 1000)
- `clear_buffer` (boolean): Clear buffer after retrieval (default: true)

**Example:**
```json
{
  "action": "start",
  "query": "index=app_logs level=ERROR | head 100",
  "interval": 30
}
```

#### 5. splunk_trace_search_by_ids
Search logs by specific trace IDs.

**Parameters:**
- `trace_ids` (array, required): List of trace IDs to search
- `indexes` (array): Specific indexes to search (optional)
- `trace_id_field` (string): Field name for trace ID (default: "traceId")
- `earliest_time` (string): Start time (default: "-24h")
- `max_results` (integer): Maximum results (default: 1000)
- `include_raw` (boolean): Include raw log data (default: true)
- `sort_by_time` (boolean): Sort results chronologically (default: true)

**Example:**
```json
{
  "trace_ids": ["abc123", "def456"],
  "indexes": ["app_logs", "api_logs"],
  "earliest_time": "-1h"
}
```

#### 6. automated_issue_creation
AI-powered error analysis and automated issue creation.

**Parameters:**
- `splunk_query` (string, required): Query to find errors
- `platform` (string): 'github', 'jira', 'both', or 'auto' (default: 'auto')
- `github_repo` (string): GitHub repository (format: 'owner/repo')
- `jira_project` (string): JIRA project key
- `earliest_time` (string): Start time (default: "-24h")
- `max_results` (integer): Results to analyze (default: 100)
- `severity_threshold` (string): 'low', 'medium', 'high', 'critical' (default: 'medium')
- `group_similar_errors` (boolean): Group similar errors (default: true)
- `auto_assign` (string): Auto-assign username (optional)
- `custom_labels` (array): Additional labels (optional)

**Example:**
```json
{
  "splunk_query": "index=production ERROR | head 50",
  "platform": "github",
  "github_repo": "company/backend-api",
  "severity_threshold": "high",
  "custom_labels": ["production", "urgent"]
}
```

#### 7. Prompt-Based Tools

These tools provide AI-ready prompts for common workflows:

- **get_chain_1_prompt**: Initial log analysis prompt
- **get_chain_2_prompt**: Deep dive analysis prompt
- **get_chain_3_prompt**: Root cause investigation prompt
- **get_logs_evaluation_prompt**: Log quality assessment
- **get_root_cause_identification_prompt**: Structured RCA prompt
- **get_splunk_query_prompt**: Natural language to SPL conversion

#### 8. Utility Tools

- **group_results_by_traceid**: Group logs by trace ID
- **expand_fetch_by_traceids**: Recursively fetch related traces
- **find_splunk_index_in_repo**: Search codebase for index references
- **resolve_splunk_index**: Determine correct Splunk environment
- **logs_debug_entry**: Entry point for debugging workflows
- **error_logs**: Process and analyze error logs

### Use Cases & Examples

#### Use Case 1: Investigating Production Errors

```bash
# Step 1: Search for recent errors
Tool: splunk_search
Query: "index=production level=ERROR | stats count by error_message | sort -count"
Time: Last 1 hour

# Step 2: Get details for top error
Tool: splunk_trace_search_by_ids
Trace IDs: ["trace-id-from-step-1"]

# Step 3: Create issue automatically
Tool: automated_issue_creation
Query: 'index=production error_message="Database connection timeout"'
Platform: github
Repo: company/api-service
```

#### Use Case 2: Continuous Monitoring

```bash
# Start monitoring critical errors
Tool: splunk_monitor
Action: start
Query: "index=app_logs level=CRITICAL"
Interval: 60

# Check status
Tool: splunk_monitor
Action: status

# Retrieve results
Tool: splunk_monitor
Action: get_results
```

#### Use Case 3: Performance Analysis

```bash
# Export slow query logs
Tool: splunk_export
Query: "index=db_logs query_time>1000 | stats avg(query_time) by query_type"
Format: csv
Time Range: Last 24 hours

# Analyze in spreadsheet or BI tool
```

---

## 🏗️ Architecture

### System Design

```
┌─────────────────────────────────────────────────────────────┐
│                     MCP Client (Claude/Cline)               │
│                                                             │
│  • Natural language queries                                 │
│  • Tool selection                                           │
│  • Result interpretation                                    │
└──────────────────────┬──────────────────────────────────────┘
                       │ MCP Protocol (SSE/stdio)
                       ▼
┌─────────────────────────────────────────────────────────────┐
│              ServerMind MCP Server                          │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  FastMCP Server                                     │   │
│  │  • Tool registration                                │   │
│  │  • Request routing                                  │   │
│  │  • Response formatting                              │   │
│  └─────────────────────────────────────────────────────┘   │
│                       │                                     │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Tool Layer                                         │   │
│  │  • splunk_search    • splunk_monitor                │   │
│  │  • splunk_indexes   • automated_issue_creation      │   │
│  │  • splunk_export    • trace_search                  │   │
│  └─────────────────────────────────────────────────────┘   │
│                       │                                     │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Service Layer                                      │   │
│  │  • Validation       • Caching                       │   │
│  │  • Error handling   • Rate limiting                 │   │
│  │  • Logging          • Metrics                       │   │
│  └─────────────────────────────────────────────────────┘   │
└──────────────────────┬──────────────────────────────────────┘
                       │
        ┌──────────────┴──────────────┐
        ▼                             ▼
┌──────────────────┐          ┌──────────────────┐
│  Splunk API      │          │  External MCP    │
│                  │          │  Servers         │
│  • Search API    │          │  • GitHub MCP    │
│  • Export API    │          │  • Atlassian MCP │
│  • Index API     │          │                  │
└──────────────────┘          └──────────────────┘
```

### Technology Stack

- **Framework**: FastMCP (MCP implementation)
- **Web Server**: Uvicorn with Starlette
- **Transport**: SSE (Server-Sent Events) and stdio
- **Splunk SDK**: splunk-sdk for Python
- **Data Validation**: Pydantic
- **Logging**: Structlog
- **Configuration**: python-dotenv

### Project Structure

```
servermind-mcp-server/
├── src/
│   ├── __init__.py
│   ├── server.py              # Main server entry point
│   ├── config.py              # Configuration management
│   │
│   ├── splunk/                # Splunk integration layer
│   │   ├── __init__.py
│   │   ├── client.py          # Splunk API client
│   │   ├── search.py          # Search utilities
│   │   └── utils.py           # Helper functions
│   │
│   ├── tools/                 # MCP tool implementations
│   │   ├── __init__.py
│   │   ├── search.py          # Search tools
│   │   ├── indexes.py         # Index management
│   │   ├── export.py          # Export functionality
│   │   ├── monitor.py         # Monitoring tools
│   │   ├── automated_issue_creation.py
│   │   ├── splunk_trace_search_by_ids.py
│   │   ├── group_results_by_traceid.py
│   │   └── [other tools]
│   │
│   └── prompts/               # Prompt templates
│       ├── analyze_traces_narrative.txt
│       ├── root_cause_identification_prompt.txt
│       └── [other prompts]
│
├── tests/
│   ├── unit/                  # Unit tests
│   ├── integration/           # Integration tests
│   └── __init__.py
│
├── .env.example              # Example environment variables
├── .gitignore
├── Dockerfile                # Docker configuration
├── docker-compose.yml
├── requirements.txt          # Python dependencies
├── pyproject.toml           # Project metadata
├── README.md               # This file
└── LICENSE
```

---

## 🔒 Security Best Practices

### Credential Management

1. **Never commit credentials**: Use `.env` files (excluded in `.gitignore`)
2. **Use API tokens**: Prefer tokens over passwords when available
3. **Rotate credentials**: Regularly update API tokens and passwords
4. **Least privilege**: Grant minimum required permissions
5. **Environment isolation**: Use separate credentials per environment

### Network Security

```bash
# Use HTTPS for Splunk connections
SPLUNK_SCHEME=https
SPLUNK_VERIFY_SSL=true

# Restrict server binding (production)
python src/server.py --host 127.0.0.1 --port 8756

# Use firewall rules to limit access
sudo ufw allow from 10.0.0.0/8 to any port 8756
```

### Query Validation

The server includes built-in validation to prevent:
- Dangerous SPL commands (delete, drop, etc.)
- SQL injection patterns
- Excessive resource consumption
- Unauthorized index access

### Docker Security

```dockerfile
# Run as non-root user
USER mcpuser

# Read-only filesystem (where possible)
docker run --read-only servermind-mcp-server

# Secrets management
docker run --secret splunk_password servermind-mcp-server
```

---

## 🐛 Troubleshooting

### Common Issues

#### 1. Connection Refused

**Symptom**: Cannot connect to Splunk server

**Solutions**:
```bash
# Test Splunk connectivity
curl -k https://$SPLUNK_HOST:$SPLUNK_PORT/services/auth/login

# Check firewall rules
telnet $SPLUNK_HOST $SPLUNK_PORT

# Verify credentials
python test_splunk_connection.py
```

#### 2. SSL Certificate Errors

**Symptom**: SSL verification failed

**Solutions**:
```bash
# Option 1: Install proper certificates
# Option 2: Disable SSL verification (not recommended for production)
SPLUNK_VERIFY_SSL=false

# Option 3: Provide custom CA bundle
SPLUNK_CA_BUNDLE=/path/to/ca-bundle.crt
```

#### 3. Query Timeout

**Symptom**: Search queries timing out

**Solutions**:
```python
# Increase timeout
{
  "query": "index=main",
  "timeout": 600  # 10 minutes
}

# Optimize query
# Use stats instead of raw events
# Limit time range
# Add index and sourcetype filters early
```

#### 4. Memory Issues

**Symptom**: Server running out of memory

**Solutions**:
```bash
# Reduce max_results
MCP_MAX_RESULTS_DEFAULT=100

# Clear monitor buffers more frequently
# Restart server periodically
# Use Docker memory limits
docker run -m 512m servermind-mcp-server
```

### Debug Mode

Enable detailed logging:

```bash
# Set log level
export LOG_LEVEL=DEBUG

# Run with debug output
python src/server.py --debug

# Check logs
tail -f /var/log/servermind-mcp-server.log
```

### Testing Connections

```bash
# Test Splunk connection
python test_splunk_connection.py

# Test MCP server
python test_mcp_server.py

# Test specific tool
python -c "from src.tools.search import get_search_tool; print(get_search_tool())"
```

---

## 🧪 Testing

### Running Tests

```bash
# Install dev dependencies
pip install -e ".[dev]"

# Run all tests
pytest

# Run specific test categories
pytest tests/unit/           # Unit tests only
pytest tests/integration/    # Integration tests only

# Run with coverage
pytest --cov=src --cov-report=html
open htmlcov/index.html

# Run specific test file
pytest tests/unit/test_search.py -v

# Run with markers
pytest -m "not slow"         # Skip slow tests
```

### Test Structure

```python
# Example unit test
def test_search_validation():
    """Test SPL query validation"""
    from src.tools.search import validate_spl_query
    
    # Valid query
    assert validate_spl_query("index=main error")
    
    # Invalid query
    with pytest.raises(ValueError):
        validate_spl_query("index=main | delete")

# Example integration test
@pytest.mark.integration
async def test_splunk_connection():
    """Test actual Splunk connection"""
    client = get_splunk_client()
    assert client.is_connected()
```

### Manual Testing

```bash
# Test with curl
curl -N http://localhost:8756/sse

# Test tool invocation
curl -X POST http://localhost:8756/messages/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "tools/call",
    "params": {
      "name": "splunk_search",
      "arguments": {"query": "index=main | head 10"}
    }
  }'
```

---

## 🤝 Contributing

We welcome contributions! Here's how to get started:

### Development Setup

```bash
# Fork and clone
git clone https://github.com/ChathuRaaksha/Server-Mind.git
cd Server-Mind

# Create virtual environment
python -m venv venv
source venv/bin/activate

# Install in development mode
pip install -e ".[dev]"

# Install pre-commit hooks
pre-commit install
```

### Code Standards

```bash
# Format code
black src/ tests/

# Check style
flake8 src/ tests/

# Type checking
mypy src/

# Run all checks
pre-commit run --all-files
```

### Contribution Workflow

1. **Create an issue**: Describe the feature or bug
2. **Fork the repository**: Create your own copy
3. **Create a branch**: `git checkout -b feature/amazing-feature`
4. **Make changes**: Implement your feature
5. **Add tests**: Ensure code coverage
6. **Commit**: `git commit -m 'Add amazing feature'`
7. **Push**: `git push origin feature/amazing-feature`
8. **Pull Request**: Open a PR with description

### Pull Request Guidelines

- Include tests for new features
- Update documentation
- Follow existing code style
- Keep PRs focused and small
- Link related issues
- Add changelog entry

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

```
MIT License

Copyright (c) 2024 ServerMind Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## 📞 Support & Community

### Getting Help

- **Documentation**: [GitHub Wiki](https://github.com/ChathuRaaksha/Server-Mind/wiki)
- **Issues**: [GitHub Issues](https://github.com/ChathuRaaksha/Server-Mind/issues)
- **Discussions**: [GitHub Discussions](https://github.com/ChathuRaaksha/Server-Mind/discussions)

### Reporting Issues

When reporting bugs, please include:

1. **Environment**: OS, Python version, dependency versions
2. **Configuration**: Relevant .env settings (sanitized)
3. **Steps to reproduce**: Detailed reproduction steps
4. **Expected behavior**: What should happen
5. **Actual behavior**: What actually happens
6. **Logs**: Relevant log output (with DEBUG level)
7. **Screenshots**: If applicable

### Feature Requests

Feature requests are welcome! Please include:

- **Use case**: What problem does it solve?
- **Proposed solution**: How should it work?
- **Alternatives considered**: Other approaches
- **Additional context**: Examples, mockups, etc.

---

## 🗺️ Roadmap

### Version 1.1 (Q2 2024)

- [ ] Enhanced trace correlation across multiple services
- [ ] Machine learning-based anomaly detection
- [ ] Custom dashboard creation and management
- [ ] Webhook support for external integrations
- [ ] Performance metrics and optimization tools

### Version 1.2 (Q3 2024)

- [ ] Multi-tenancy support
- [ ] Advanced RBAC (Role-Based Access Control)
- [ ] Kubernetes operator for easy deployment
- [ ] GraphQL API support
- [ ] Real-time collaboration features

### Version 2.0 (Q4 2024)

- [ ] Support for additional observability platforms (Datadog, New Relic)
- [ ] Advanced AI-powered root cause analysis
- [ ] Automated remediation workflows
- [ ] Enterprise SSO integration
- [ ] Compliance and audit logging

---

## 🙏 Acknowledgments

- **Model Context Protocol**: [Anthropic's MCP](https://github.com/modelcontextprotocol)
- **Splunk SDK**: [Splunk SDK for Python](https://github.com/splunk/splunk-sdk-python)
- **FastMCP**: Efficient MCP server implementation
- **Contributors**: All the amazing people who contribute to this project

---

## 📊 Stats & Metrics

![GitHub stars](https://img.shields.io/github/stars/ChathuRaaksha/Server-Mind?style=social)
![GitHub forks](https://img.shields.io/github/forks/ChathuRaaksha/Server-Mind?style=social)
![GitHub issues](https://img.shields.io/github/issues/ChathuRaaksha/Server-Mind)
![GitHub pull requests](https://img.shields.io/github/issues-pr/ChathuRaaksha/Server-Mind)

---

<div align="center">

**Built with ❤️ by the ServerMind Team**

[⬆ Back to Top](#servermind-mcp-server)

</div>
