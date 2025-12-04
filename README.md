# Electric Book Deploy Workflow FTP and SSH Plugin

A deploy workflow plugin used by Electric Book projects with automated deploys to a web server with FTP or SSH access.

## Adding the workflow to your project repository

### 1. Add the consuming workflow file

Create `.github/workflows/deploy-ftp-ssh.yml` in your Electric Book project and/or media repo:

```yaml
name: Deploy to web server with FTP or SSH

on:
  push: # Trigger on any push, but branch validation will filter
  workflow_dispatch: # Allow manual triggering

jobs:
  deploy:
    uses: electricbookworks/electric-book-deploy-workflow-ftp-ssh/.github/workflows/deploy.yml@{release-version}
    secrets: inherit
```

Replace `{release-version}` with the latest release (e.g. `v1.0.0`), unless you want to use an older version.

You can also add optional inputs to customise the Ruby and Node versions, as well as the config file location:

```yaml
name: Deploy to web server with FTP or SSH

on:
  push: # Trigger on any push, but branch validation will filter
  workflow_dispatch: # Allow manual triggering

jobs:
  deploy:
    uses: electricbookworks/electric-book-deploy-workflow-ftp-ssh/.github/workflows/deploy.yml@{release-version}
    secrets: inherit
    with:
      config-file: '.github/workflows/deploy.config.json' # optional - defaults to '.github/workflows/deploy.config.json'
      node-version: '22' # optional - defaults to 22
      ruby-version: '3.2' # optional - defaults to 3.2
```

### 2. Create your deployment configuration

Create `.github/workflows/deploy.config.json`:

```json
{
  "ftp-ssh": {
    "configs-comment": "All configs are relative to the _configs folder.",
    "configs": ["_config.live.yml"],
    "builds": [
      {
        "configs": [],
        "dir": "electric-book"
      },
      {
        "configs": ["_config.student.yml"],
        "dir": "test/student"
      },
      {
        "configs": ["_config.teacher.yml"],
        "dir": "test/teacher"
      }
    ],
    "live":{
      "type": "ftp", 
      "host": "host.com",
      "port": 21,
      "username": "username",
      "path": "/"
    },
    "staging": {
      "type": "ssh", 
      "host": "host.com",
      "port": 2222,
      "username": "username",
      "path": "~/public_html/host.com/staging/"
    }
  }
}

```

## Configuring GitHub Secrets

This workflow depends on the follwoing secrets being defined either at the organisation level or on the consuming repo:

1. `DEPLOY_SSH_PRIVATE_KEY` - the corresponding public key needs to be added to the web server's `authorized_keys`.
2. `DEPLOY_FTP_PASSWORD` 