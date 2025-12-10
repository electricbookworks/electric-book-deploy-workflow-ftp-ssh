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

Replace `{release-version}` with the latest release (e.g. `v2.0.0`), unless you want to use an older version.

You can also add the following optional inputs:

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
      setup-command: 'npm run setup' # optional - defaults to 'npm run setup'
      build-command: 'npm run eb -- output --baseurl="${BASEURL}" --configs="${FULL_CONFIGS}" --dontserve=true --deploy=true' # optional - defaults to 'npm run eb -- output --baseurl="${BASEURL}" --configs="${FULL_CONFIGS}" --dontserve=true --deploy=true'
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
      "web-root": "public_html",
      "path": ""
    },
    "staging": {
      "type": "ssh",
      "web-root": "public_html",
      "path": ""
    },
    "media": {
      "repo": "owner-name/repo-name",
      "dirs": [
        "assets/images/web"
      ]
    }
  }
}
```

## Configuring GitHub Secrets

This workflow depends on the follwoing secrets being defined either at the organisation level or on the consuming repo:

1. `DEPLOY_SSH_PRIVATE_KEY` - the corresponding public key needs to be added to the web server's `authorized_keys`.
2. `DEPLOY_FTP_PASSWORD` 
3. `DEPLOY_HOST_LIVE`
4. `DEPLOY_HOST_STAGING`
4. `DEPLOY_PORT_LIVE`
4. `DEPLOY_PORT_STAGING`
4. `DEPLOY_USERNAME_LIVE`
4. `DEPLOY_USERNAME_STAGING`