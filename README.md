# Self-Hosted GitHub Actions Runner Setup Guide

A comprehensive guide for setting up and managing a self-hosted GitHub Actions runner on an Ubuntu VM.

## Project Structure

```plaintext
GitHub Repository:
└── .github/
    └── workflows/
        └── custom-runner.yml

Ubuntu VM:
└── actions-runner/
    ├── run.sh         # Starts the runner
    ├── config.sh      # Configures the runner
    └── .credentials   # Runner credentials
```

## Setup Instructions

### 1. Repository Setup
1. Go to repository Settings > Actions > Runners
2. Click "New self-hosted runner"
3. Select Linux/x64 ( if any )

### 2. VM Setup
Run these commands on your Ubuntu VM:
```bash
# Create and enter directory
mkdir actions-runner && cd actions-runner

# Download runner
curl -o actions-runner-linux-x64-2.320.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.320.0/actions-runner-linux-x64-2.320.0.tar.gz

# Extract
tar xzf ./actions-runner-linux-x64-2.320.0.tar.gz

# Configure runner
./config.sh --url https://github.com/[USERNAME]/[REPO] --token [TOKEN]

# Start runner
./run.sh
```

### 3. Workflow Configuration
Create `.github/workflows/custom-runner.yml` in your repository:

```yaml
name: Custom Runner Workflow
on: push

jobs:
  build:
    runs-on: self-hosted
    steps:
      - uses: actions/checkout@v2
      - name: Test Runner
        run: |
          echo "Running on: $HOSTNAME"
          echo "Working Directory: $PWD"
```

## How It Works

1. **Architecture**
   - Runner service on VM polls GitHub for jobs
   - All communication is outbound (HTTPS)
   - Jobs execute in isolated processes
   - Results report back to GitHub
  
![Architecture Diagram](https://raw.githubusercontent.com/Raihan-009/needs-custom-runner/refs/heads/main/custom-runner.png)

2. **Security**
   - HTTPS communication only
   - Token-based authentication
   - Isolated job execution
   - Automatic workspace cleanup

## Management Commands

```bash
# Start runner
./run.sh

# Stop runner
Ctrl+C

# Remove runner
./config.sh remove --token [REMOVE-TOKEN]
```

## Troubleshooting

1. **Runner Won't Connect**
   - Check network connectivity
   - Verify token validity
   - Ensure proper permissions

2. **Jobs Not Starting**
   - Confirm runner is online
   - Check workflow's `runs-on: self-hosted`
   - Verify repository permissions

## Best Practices

1. Regular runner updates
2. Monitor runner logs
3. Set job timeouts
4. Maintain backup runners

Need help? Contact your GitHub admin or refer to [GitHub Actions documentation](https://docs.github.com/en/actions).
