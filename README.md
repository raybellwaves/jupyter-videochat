# jupyter-videochat

> Video Chat with JupyterHub peers (or the world) inside JupyterLab, powered by
> [Jitsi].

[![Extension status](https://img.shields.io/badge/status-ready-success "ready to be used")](https://jupyterlab-contrib.github.io/)
[![install from pypi][pypi-badge]][pypi]
[![install from conda-forge][conda-forge-badge]][conda-forge]
[![reuse from npm][npm-badge]][npm]
[![continuous integration][workflow-badge]][workflow]
[![interactive demo][binder-badge]][binder] [![][changelog-badge]][changelog]
[![][contributing-badge]][contributing]

This extension is composed of:

- a Python package named `jupyter_videochat`, which offers:
  - a `jupyter_server` extension which provides convenient, configurable
    defaults for rooms on a [JupyterHub]
  - a JupyterLab _federated extension_ named `jupyter-videochat`
    - also distributed on [npm]
    - for more about the TypeScript/JS API, see [CONTRIBUTING]

[npm]: https://www.npmjs.com/package/jupyterlab-videochat
[jupyterhub]: https://github.com/jupyterhub/jupyterhub

![jupyter-videochat screenshot][lab-screenshot]

[lab-screenshot]:
  https://user-images.githubusercontent.com/45380/106391412-312d0400-63bb-11eb-9ed9-af3c4fe85ee4.png

## Requirements

- `python >=3.6`
- `jupyterlab ==3.*`

## Install

Install the server extension and JupyterLab extension with `pip`:

```bash
pip install -U jupyter-videochat
```

...or `conda`:

```bash
conda install -c conda-forge jupyter-videochat
```

## Troubleshoot

If you are seeing the frontend extension but it is not working, check that the
server extension is enabled:

```bash
jupyter server extension list
jupyter server extension enable --sys-prefix --py jupyter_videochat
```

> If you launch your Jupyter server with `jupyter notebook`, as Binder does, the
> equivalent commands are:
>
> ```bash
> jupyter serverextension list
> jupyter serverextension enable --sys-prefix --py jupyter_videochat
> ```

If the server extension is installed and enabled but you are not seeing the
frontend, check the frontend is installed:

```bash
jupyter labextension list
```

If you do not see `jupyterlab-videochat`, the best course of action is to
[uninstall](#uninstall) and [reinstall](#install), and carefully watch the log
output.

## Uninstall

```bash
pip uninstall jupyter-videochat
```

## Configuration

### Server Configuration

In your `jupyter_server_config.json` (or equivalent `.py` or `conf.d/*.json`),
you can configure the `VideoChat`:

- `room_prefix`, a prefix used for your group, by default a URL-frieldy version
  of your JupyterHub's hostname
  - can be overriden with the `JUPYTER_VIDEOCHAT_ROOM_PREFIX` environment
    variable
- `jitsi_server`, an HTTPS host that serves the Jitsi web application, by
  default `meet.jit.si`
- `rooms`, a list of Room descriptions that everyone on your Hub will be able to
  join

#### Example

```json
{
  "VideoChat": {
    "room_prefix": "our-spiffy-room-prefix",
    "rooms": [
      {
        "id": "stand-up",
        "displayName": "Stand-Up",
        "description": "Daily room for meeting with the team"
      },
      {
        "id": "all-hands",
        "displayName": "All-Hands",
        "description": "A weekly room for the whole team"
      }
    ],
    "jitsi_server": "jitsi.example.com"
  }
}
```

### Client Configuration

In the JupyterLab _Advanced Settings_ panel, the _Video Chat_ settings can be
further configured, as can a user's default `displayName` and `email`. The
defaults provided are generally pretty conservative, and disable as many
third-party services as possible. Additionally, access to
**globally-accessible** public rooms may be enabled.

#### Binder Client Example

For example, to enable all third-party features, public rooms, and open in the
`main` area by default:

- create an `overrides.json`

  ```json
  {
    "jupyter-videochat:plugin": {
      "interfaceConfigOverwrite": null,
      "configOverwrite": null,
      "disablePublicRooms": false,
      "area": "main"
    }
  }
  ```

- Copy it to the JupyterLab settings directory

  ```bash
  # postBuild
  mkdir -p ${NB_PYTHON_PREFIX}/share/jupyter/lab/settings
  cp overrides.json ${NB_PYTHON_PREFIX}/share/jupyter/lab/settings
  ```

### Start a Meet by URL

Appending `?jvc=room-name` to a JupyterLab URL will automatically open the Meet
(but not _fully_ start it, as browsers require a user gesture to start
audio/video).

#### Binder URL Example

On [Binder](https://mybinder.org), use the `urlpath` to append the argument,
ensuring the arguments get properly URL-encoded.

```
https://mybinder.org/v2/gh/jupyterlab-contrib/jupyter-videochat/demo?urlpath=tree%3Fjvc%3DStand-Up
                                                         # URL-encoded  [? ] [=  ]
```

#### nbgitpuller

If you have two repos (or branches) that contain:

- content that changes frequently
- a stable environment

...you can use [nbgitpuller](https://jupyterhub.github.io/nbgitpuller/link) to
have fast-building, (almost) single-click URLs that launch right into JupyterLab
showing your meeting and content. For example, to use...

- the [Python Data Science Handbook] as `master`
- this project's repo, at `demo` (_not recommended, as it's pretty
  [minimal][binder-reqs]_)

...and launch directly into JupyterLab showing

- the _Preface_ notebook
- the _Office Hours_ room

...the doubly-escaped URL would be something like:

```
https://mybinder.org/v2/gh/jupyterlab-contrib/jupyter-videochat/demo?
urlpath=git-pull
  %3Frepo%3Dhttps%253A%252F%252Fgithub.com%252Fjakevdp%252FPythonDataScienceHandbook
  %26branch%3Dmaster
  %26urlpath%3Dlab%252Ftree%252FPythonDataScienceHandbook%252Fnotebooks%252F00.00-Preface.ipynb
  %253Fjvc%253DOffice%2BHours
```

[workflow]:
  https://github.com/jupyterlab-contrib/jupyter-videochat/actions?query=workflow%3ACI+branch%3Amaster
[workflow-badge]:
  https://github.com/jupyterlab-contrib/jupyter-videochat/workflows/CI/badge.svg
[binder]:
  https://mybinder.org/v2/gh/jupyterlab-contrib/jupyter-videochat/demo?urlpath=lab
[binder-reqs]:
  https://github.com/jupyterlab-contrib/jupyter-videochat/blob/master/binder/requirements.txt
[binder-badge]: https://mybinder.org/badge_logo.svg
[pypi-badge]: https://img.shields.io/pypi/v/jupyter-videochat
[pypi]: https://pypi.org/project/jupyter-videochat/
[conda-forge-badge]:
  https://img.shields.io/conda/vn/conda-forge/jupyter-videochat
[conda-forge]: https://anaconda.org/conda-forge/jupyter-videochat
[npm-badge]: https://img.shields.io/npm/v/jupyterlab-videochat
[changelog]:
  https://github.com/jupyterlab-contrib/jupyter-videochat/blob/master/CHANGELOG.md
[changelog-badge]: https://img.shields.io/badge/CHANGELOG-md-000
[contributing-badge]: https://img.shields.io/badge/CONTRIBUTING-md-000
[contributing]:
  https://github.com/jupyterlab-contrib/jupyter-videochat/blob/master/CONTRIBUTING.md
[jitsi]: https://jitsi.org
[python data science handbook]:
  https://github.com/jakevdp/PythonDataScienceHandbook
