# My Helm Charts Repository

A collection of Helm charts with automated CI/CD using GitHub Actions and GitHub Pages.

## Available Charts

- **[Cassandra](charts/cassandra/README.md)** - Apache Cassandra with authentication and PVC storage

## Usage

Add this Helm repository:

```bash
helm repo add my-charts https://yourusername.github.io/my-helm-charts
helm repo update
```

Install charts:

```bash
# List available charts
helm search repo my-charts

# Install Cassandra
helm install my-cassandra my-charts/cassandra

# Install with custom values
helm install my-cassandra my-charts/cassandra \
  --set auth.superuser.password=mySecurePassword \
  --set persistence.size=20Gi
```

## Development

### Adding New Charts

1. Create a new directory under `charts/`
2. Add your chart files (Chart.yaml, values.yaml, templates/, etc.)
3. Include a README.md with documentation
4. Update the chart version in Chart.yaml
5. Commit and push to main branch

### Updating Existing Charts

1. Modify chart files as needed
2. **Increment version** in Chart.yaml (required for release)
3. Update README.md if needed
4. Commit and push to main branch

The GitHub Actions workflow will automatically:
- Package the charts
- Create GitHub releases
- Update the repository index
- Publish to GitHub Pages

## Repository Structure

```
my-helm-charts/
├── .github/
│   └── workflows/
│       └── release.yml
├── charts/
│   └── cassandra/
│       ├── Chart.yaml
│       ├── values.yaml
│       ├── templates/
│       └── README.md
├── README.md
└── .helmignore
```

## Contributing

1. Fork this repository
2. Create a feature branch
3. Add or modify charts
4. Ensure chart version is incremented
5. Submit a pull request

## License

This repository is licensed under the MIT License.