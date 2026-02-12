# Newcomers Guide to Prometheus

Welcome! This guide will help you understand the Prometheus codebase, build it locally, and make your first contribution.

## Table of Contents

1. [About Prometheus](#about-prometheus)
2. [Understanding the Codebase](#understanding-the-codebase)
3. [Setting Up Your Development Environment](#setting-up-your-development-environment)
4. [Building Prometheus Locally](#building-prometheus-locally)
5. [Running Tests](#running-tests)
6. [Making Your First Contribution](#making-your-first-contribution)
7. [Common Issues and Troubleshooting](#common-issues-and-troubleshooting)
8. [Additional Resources](#additional-resources)

## About Prometheus

Prometheus is an open-source systems monitoring and alerting toolkit originally built at SoundCloud. It's now a Cloud Native Computing Foundation (CNCF) graduated project.

### Key Features

- **Multi-dimensional data model**: Time series are identified by metric name and key/value pairs
- **PromQL**: A powerful query language for slicing and dicing data
- **Pull-based model**: Prometheus scrapes metrics from instrumented jobs
- **Service discovery**: Automatic target discovery via various mechanisms
- **No distributed storage dependency**: Single server nodes are autonomous

## Understanding the Codebase

### High-Level Architecture

```
┌─────────────┐
│  Prometheus │
│   Server    │
└──────┬──────┘
       │
       ├─> Scrape targets (pull metrics)
       ├─> Evaluate rules
       ├─> Store time series data
       ├─> Serve queries via API
       └─> Send alerts
```

### Directory Structure

Here's an overview of the main directories in the Prometheus codebase:

```
prometheus/
├── cmd/                    # Command-line tools
│   ├── prometheus/        # Main Prometheus server
│   └── promtool/         # Prometheus CLI tool for validation
├── config/                # Configuration parsing and validation
├── discovery/             # Service discovery mechanisms
│   ├── kubernetes/       # Kubernetes SD
│   ├── consul/          # Consul SD
│   ├── dns/             # DNS SD
│   └── ...              # Other service discoveries
├── docs/                  # Documentation
├── model/                 # Data model definitions
│   ├── labels/           # Label handling
│   ├── timestamp/        # Timestamp utilities
│   └── value/            # Value types
├── notifier/              # Alert notification handling
├── promql/                # PromQL query engine
│   ├── parser/           # PromQL parser
│   └── ...               # Query execution
├── rules/                 # Recording and alerting rules
├── scrape/                # Target scraping logic
├── storage/               # Storage interfaces
│   └── remote/           # Remote storage
├── tsdb/                  # Time series database
│   ├── agent/            # Agent mode
│   ├── chunkenc/         # Chunk encoding
│   └── wal/              # Write-ahead log
├── util/                  # Utility packages
└── web/                   # Web UI and API
    ├── api/              # HTTP API
    └── ui/               # React-based web interface
```

### Key Components

#### 1. **Scraper (`scrape/`)**
- Pulls metrics from configured targets
- Handles target lifecycle and metadata
- Implements metric relabeling

#### 2. **Storage (`tsdb/`)**
- Time Series Database (TSDB) for efficient data storage
- Write-Ahead Log (WAL) for durability
- Compaction and retention management

#### 3. **Query Engine (`promql/`)**
- Parses and executes PromQL queries
- Optimizes query execution
- Handles range queries and instant queries

#### 4. **Rules Engine (`rules/`)**
- Evaluates recording and alerting rules
- Manages rule groups

#### 5. **Service Discovery (`discovery/`)**
- Discovers targets dynamically
- Supports multiple SD mechanisms (Kubernetes, Consul, DNS, etc.)

#### 6. **Notifier (`notifier/`)**
- Sends alerts to Alertmanager
- Handles alert queuing and retry logic

#### 7. **Web UI and API (`web/`)**
- Provides HTTP API for queries and management
- Serves the React-based web interface

### Code Flow Example: Scraping Metrics

1. **Target Discovery**: Service discovery finds targets
2. **Scrape Target**: HTTP GET to target's `/metrics` endpoint
3. **Parse Metrics**: Text-based Prometheus exposition format
4. **Store Data**: Write to TSDB with WAL
5. **Query Data**: PromQL queries retrieve stored metrics

## Setting Up Your Development Environment

### Prerequisites

1. **Go**: Version specified in [go.mod](../go.mod) (currently Go 1.25.5+)
   ```bash
   go version
   ```

2. **Node.js**: For building the web UI (check [web/ui/.nvmrc](../web/ui/.nvmrc))
   ```bash
   node --version  # Should be v18+
   npm --version   # Should be v10+
   ```

3. **Git**: For version control
   ```bash
   git --version
   ```

4. **Make**: Build automation tool (usually pre-installed on Linux/Mac)
   ```bash
   make --version
   ```

### Clone the Repository

```bash
# Clone the official repository
git clone https://github.com/prometheus/prometheus.git
cd prometheus

# Or if you've forked it (recommended for contributions)
git clone https://github.com/YOUR_USERNAME/prometheus.git
cd prometheus

# Add upstream remote
git remote add upstream https://github.com/prometheus/prometheus.git
```

## Building Prometheus Locally

### Quick Build

The simplest way to build Prometheus:

```bash
# Build Prometheus and promtool binaries
make build

# This creates binaries in the current directory:
# - ./prometheus
# - ./promtool
```

### Alternative: Build with Go Directly

```bash
# Quick build without web assets (for testing backend changes)
go build ./cmd/prometheus
go build ./cmd/promtool

# Run Prometheus
./prometheus --config.file=documentation/examples/prometheus.yml
```

**Note**: When using `go build` directly, the web UI won't be fully functional unless you've pre-built the assets.

### Building with Web Assets

For a complete build including the React UI:

```bash
# Install npm dependencies and build UI
make assets

# Build everything
make build
```

### Running Prometheus

```bash
# Run with a configuration file
./prometheus --config.file=documentation/examples/prometheus.yml

# Prometheus will start on http://localhost:9090
```

Visit `http://localhost:9090` in your browser to access the web UI.

### Build Targets Reference

The Makefile provides several useful targets:

- `make build` - Build Prometheus and promtool with web assets
- `make test` - Run all tests
- `make test-short` - Run short tests (faster)
- `make lint` - Run code linters
- `make format` - Format source code
- `make assets` - Build web UI assets
- `make clean` - Clean build artifacts

## Running Tests

### Running All Tests

```bash
# Run the complete test suite
make test

# This includes:
# - Go tests
# - UI tests
# - Linting
```

### Running Specific Tests

```bash
# Run only Go tests
make test GO_ONLY=1

# Run tests for a specific package
go test ./tsdb/...

# Run a specific test
go test ./tsdb/ -run TestSpecificFunction

# Run tests with verbose output
go test -v ./promql/...

# Run tests with race detector
go test -race ./scrape/...
```

### Running Short Tests

For quick iterations during development:

```bash
# Run short tests (excludes longer integration tests)
make test-short
```

### Code Quality Checks

```bash
# Run linter
make lint

# Format code
make format

# Check for common mistakes
make vet
```

## Making Your First Contribution

### Finding Issues to Work On

1. **Browse "good first issue" labels**:
   - Visit: https://github.com/prometheus/prometheus/labels/good%20first%20issue
   - These are beginner-friendly issues

2. **Look for "low hanging fruit"**:
   - Visit: https://github.com/prometheus/prometheus/labels/low%20hanging%20fruit
   - These are relatively simple tasks

3. **Browse the codebase**:
   - Look for TODOs in the code
   - Improve documentation
   - Add missing tests

### Before You Start

1. **Claim the issue**: Comment on the GitHub issue that you want to work on it
2. **Discuss your approach**: For larger changes, discuss on the issue or mailing list
3. **Check for existing work**: Make sure no one else is already working on it

### Development Workflow

1. **Create a branch**:
   ```bash
   git checkout -b fix-issue-1234
   ```

2. **Make your changes**:
   - Keep commits small and focused
   - Write clear commit messages
   - Follow the existing code style

3. **Test your changes**:
   ```bash
   # Run tests
   make test-short
   
   # Run linter
   make lint
   
   # Test manually
   ./prometheus --config.file=your-test-config.yml
   ```

4. **Commit your changes**:
   ```bash
   git add .
   git commit -m "Fix issue #1234: Brief description of the fix"
   ```

5. **Push and create a pull request**:
   ```bash
   git push origin fix-issue-1234
   ```
   Then visit GitHub to create a pull request.

### Pull Request Guidelines

- **Title**: Clear and concise (e.g., "Fix memory leak in scrape manager")
- **Description**: Explain what and why, not just how
- **Link to issue**: Reference the issue number (e.g., "Fixes #1234")
- **Tests**: Add or update tests for your changes
- **Documentation**: Update docs if needed
- **DCO**: Sign-off on commits ([Developer Certificate of Origin](https://github.com/probot/dco#how-it-works))

### Code Review Process

1. Maintainers will review your PR
2. Address feedback by pushing new commits
3. Once approved, a maintainer will merge your PR
4. Celebrate your contribution! 🎉

### Sign-off Commits (DCO)

Prometheus uses the Developer Certificate of Origin (DCO). Add a sign-off to your commits:

```bash
git commit -s -m "Your commit message"
```

Or add it to an existing commit:

```bash
git commit --amend -s
```

## Common Issues and Troubleshooting

### Build Issues

**Problem**: `make build` fails with npm errors

**Solution**:
```bash
# Clear npm cache
cd web/ui && rm -rf node_modules package-lock.json
npm cache clean --force
npm install
```

**Problem**: Go module errors

**Solution**:
```bash
# Update dependencies
go mod tidy
go mod download
```

### Test Issues

**Problem**: Tests fail on your machine but pass on CI

**Solution**:
- Ensure you're using the correct Go version (check `go.mod`)
- Clean build artifacts: `make clean`
- Check for cached test results: `go clean -testcache`

**Problem**: Linter errors

**Solution**:
```bash
# Auto-fix many issues
make format

# Install golangci-lint locally
go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest
```

### Runtime Issues

**Problem**: Prometheus fails to start

**Solution**:
- Check your configuration file: `./promtool check config your-config.yml`
- Look at the error messages
- Check file permissions

**Problem**: Web UI doesn't load

**Solution**:
- Ensure assets were built: `make assets`
- Check browser console for errors
- Try a full rebuild: `make clean && make build`

## Additional Resources

### Documentation

- [Official Prometheus Documentation](https://prometheus.io/docs/)
- [Getting Started Guide](https://prometheus.io/docs/introduction/first_steps/)
- [PromQL Basics](https://prometheus.io/docs/prometheus/latest/querying/basics/)
- [Configuration Reference](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)

### Community

- [Prometheus Mailing List](https://groups.google.com/forum/#!forum/prometheus-developers)
- [CNCF Slack #prometheus channel](https://slack.cncf.io/)
- [IRC: #prometheus-dev on Libera.Chat](https://web.libera.chat/?channels=#prometheus-dev)

### Code

- [Main Repository](https://github.com/prometheus/prometheus)
- [Contributing Guidelines](../CONTRIBUTING.md)
- [Code of Conduct](../CODE_OF_CONDUCT.md)
- [Maintainers](../MAINTAINERS.md)

### Learning Resources

1. **Understanding Prometheus Internals**:
   - Read the [TSDB design doc](https://github.com/prometheus/prometheus/tree/main/tsdb/docs)
   - Explore the [PromQL parser](../promql/parser/)
   - Study [scrape implementation](../scrape/)

2. **Best Practices**:
   - Follow [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments)
   - Read [Effective Go](https://go.dev/doc/effective_go)
   - Study existing code in the repository

3. **Debugging Tips**:
   - Use `promtool` for configuration validation
   - Enable debug logging: `./prometheus --log.level=debug`
   - Use Go debugging tools: `dlv` (Delve debugger)

## Next Steps

Now that you understand the basics:

1. ✅ Build Prometheus locally
2. ✅ Run the tests
3. ✅ Explore the codebase
4. 📝 Find a "good first issue"
5. 💻 Make your first contribution
6. 🎉 Join the Prometheus community!

**Questions?** Don't hesitate to ask in the GitHub issues, on the mailing list, or in the CNCF Slack!

---

**Happy Contributing!** 🚀

The Prometheus community welcomes developers of all skill levels. Your fresh perspective is valuable, and we're here to help you succeed.
