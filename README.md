# Docker Source Hash GitHub Action

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Docker%20Source%20Hash-blue?logo=github)](https://github.com/marketplace/actions/docker-source-hash)

Create a hash based on Docker source files to determine if a rebuild is necessary. This action helps optimize Docker
image builds by detecting changes in relevant source files and generating a unique hash, which can be used for cache
keys or conditional steps in your CI/CD pipeline.

## Inputs

| Name          | Description                                                                                                  | Required | Default        |
|---------------|--------------------------------------------------------------------------------------------------------------|----------|----------------|
| ignore-files  | List of files containing patterns to exclude from the hash generation. Each pattern should be on a new line. | false    | .dockerignore  |
| exclude-paths | List of additional path patterns to exclude from the hash generation. Each path should be on a new line.     | false    | (empty string) |

## Outputs

| Name       | Description                                      |
|------------|--------------------------------------------------|
| hash       | The generated hash based on Docker source files. |
| file-list  | List of files considered for hash generation.    |
| file-count | Number of files considered for hash generation.  |

## Usage

```yaml
name: Build Docker Image
on: [ push ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Generate Docker source hash
        id: hash
        uses: Matchory/docker-source-hash-action@v1
        with:
          ignore-files: |
            .dockerignore
            .gitignore
          exclude-paths: |
            node_modules/
            dist/
      - name: Use hash output
        run: |
          echo "Hash: ${{ steps.hash.outputs.hash }}"
          echo "Files: ${{ steps.hash.outputs.file-list }}"
          echo "File count: ${{ steps.hash.outputs.file-count }}"
```

## Example Scenarios

This action is particularly useful in continuous integration pipelines where you want to avoid unnecessary Docker image
rebuilds. For example, you can use the generated hash as a cache key for Docker build steps, ensuring that the build
cache is only invalidated when relevant source files change.  
This can significantly speed up your workflows by reusing
previously built layers when possible. Additionally, you can use the hash to conditionally trigger downstream jobs or
deployment steps, making your automation more efficient and reliable by only acting when actual changes to the Docker
context occur.

## License

[MIT](LICENSE)
