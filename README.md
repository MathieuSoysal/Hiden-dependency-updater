# Hiden-dependency-updater
[![Test Actions](https://github.com/MathieuSoysal/hiden-dependency-updater/actions/workflows/test-action-final.yml/badge.svg)](https://github.com/MathieuSoysal/hiden-dependency-updater/actions/workflows/test-action-final.yml)*(Tested on Ubuntu, Macos, Windows)*


Update automatically dependency that Dependabot can't check.

## Requirements
- You need to give permission to your GitHub Actions to create a pull request in your GitHub repo settings *(Settings -> Actions -> General)*.   

OR


- Instead of use `${{ secrets.GITHUB_TOKEN }}` in GitHub Actions use a GitHub [Personnal Acces Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-fine-grained-personal-access-token) like : `${{ secrets.PAT }}`.

## Usage

### With pull request

The workflow, usually declared in `.github/workflows/dependencies.yml`, looks like:

<details open>

<summary>.github/workflows/dependencies.yml</summary>

```YAML
name: Update files

on:
  release:
    types: [published]

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - name: Update files
        uses: MathieuSoysal/hiden-dependency-updater@v1.1.0
        with:
          files: action.yml # List of files to update
          prefix: "com.google.cloud.tools:jib-maven-plugin:" # Prefix before the version, default is: ""
          repository: "GoogleContainerTools/jib"

      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }} # You need to create your own token with pull request rights
          commit-message: update jib
          title: Update jib
          body: Update jib to reflect release changes
          branch: update-jib
          base: main
```
</details>

### With directly commit

The workflow, usually declared in `.github/workflows/dependencies.yml`, looks like:

<details open>
<summary>.github/workflows/dependencies.yml</summary>



```YAML
name: Update files with commit

on:
  release:
    types: [published]

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:

      - name: Checkout
        uses: actions/checkout@v3
        with:
          fetch-depth: 0
          token: ${{ secrets.GITHUB_TOKEN }} # You need to create your own token with commit rights
          ref: main # The branch you want to commit to


      - uses: MathieuSoysal/hiden-dependency-updater@v1.1.0
        with:
          files: action.yml # List of files to update
          prefix: "com.google.cloud.tools:jib-maven-plugin:" # Prefix before the version, default is: ""
          repository: "GoogleContainerTools/jib"
      
      - name: Push changes
        uses: EndBug/add-and-commit@v9
        with:
          committer_name: GitHub Actions
          committer_email: actions@github.com
          add: .
          message: 'update jib'
```
</details>

## Contributing

Want to contribute to File Updater? Awesome! Check out [the contributing guidelines](CONTRIBUTING.md) to get involved.

### Requirements to your environment to test in locally

- Install [nektos/act](https://github.com/nektos/act) & clone the repo `git clone git@github.com:MathieuSoysal/hiden-dependency-updater.git`  
OR
- Use the devcontainer of the repo: with [GitHub Codespaces](https://github.com/codespaces/new?hide_repo_select=true&ref=main&repo=601249707)

### Command to test your changes

```bash
act workflow_dispatch -W .github/workflows/test-action-local.yml
```

### Stars 🎇

If you like or use this project, don't forget to give it a star ⭐️.

## License
The Dockerfile and associated scripts and documentation in this project are released under the [Apache 2.0 License](https://github.com/MathieuSoysal/hiden-dependency-updater/blob/main/LICENSE).
