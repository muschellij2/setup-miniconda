# Setup Conda Action

This action sets up a Conda installation and creates a test environment by installing Miniconda3 on unix systems and windows (x86) systems and locating the bundled Miniconda on windows systems (could be overiden if a clean install is desired).

Miniconda `condabin/`, `bin/` and `scripts/` folders are added to PATH and conda is initialized across platforms.

The default `conda` version used is 4.7.

This action correctly handles activation of conda environments. See the **Important** notes.

# Usage

See [action.yml](action.yml)

## Unix systems:

```yaml
steps:
- uses: actions/checkout@v1
- uses: goanpeca/action-setup-conda@master
  with:
    miniconda-version: '4.7.12'          # optional. Version of miniconda installer to use.
    conda-build-version: '3.17'          # optional. If not provided conda build is not installed.
    environment-file: 'environment.yml'  # optional. Version of miniconda installer to use.
    activate-environment: 'base'         # optional. Version of miniconda installer to use.
- run: conda create --name test python=3.6
- name: Using bash
  shell: bash -l {0}  # IMPORTANT!
  run: |
    conda activate test
    conda info
    conda list
- name: Using sh
  shell: sh -l {0}  # IMPORTANT!
  run: |
    conda activate test
    conda info
    conda list
```

### IMPORTANT

- Bash shells do not use `~/.profile` or `~/.bashrc` so these shells need to be explicitely declared as `shell: bash -l {0}` on steps using `conda activate` commands. This is because bash shells are executed with `bash --noprofile --norc -eo pipefail {0}` thus ignoring updated on bash profile files made by `conda init bash`. See [Github Actions Documentation](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/workflow-syntax-for-github-actions#using-a-specific-shell) and [thread](https://github.community/t5/GitHub-Actions/How-to-share-shell-profile-between-steps-or-how-to-use-nvm-rvm/td-p/33185).
- Sh shells do not use `~/.profile` or `~/.bashrc` so these shells need to be explicitely declared as `shell: sh -l {0}` on steps using `conda activate` commands. This is because sh shells are executed with `sh -e {0}` thus ignoring updated on bash profile files made by `conda init bash`. See [Github Actions Documentation](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/workflow-syntax-for-github-actions#using-a-specific-shell).

## Windows systems:

```yaml
steps:
- uses: actions/checkout@v1
- uses: goanpeca/action-setup-conda@master
  with:
    conda-version: '4.7'         # optional. Version of conda to use for base evironment.
    conda-build-version: '3.17'  # optional. If not provided conda build is not installed.
- run: conda create --name test python=3.6
- name: Using PowerShell
  run: |
    conda activate test
    conda info
    conda list
- name: Using PowerShell Core
  shell: pwsh
  run: |
    conda activate test
    conda info
    conda list
- name: Using bash
  shell: bash -l {0}  # IMPORTANT!
  run: |
    conda activate test
    conda info
    conda list
- name: Using cmd.exe
  shell: cmd
  run: >-  # IMPORTANT!
    "%CONDA_BAT%" activate test &&
    conda info &&
    conda list &&
```

### IMPORTANT

- Bash shells do not use `~/.profile` or `~/.bashrc` so these shells need to be explicitely declared as `shell: bash -l {0}` on steps using `conda activate` commands. This is bacause bash shells are executed with `bash --noprofile --norc -eo pipefail {0}` thus ignoring updated on bash profile files made by `conda init bash`. See [Github Actions Documentation](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/workflow-syntax-for-github-actions#using-a-specific-shell).
- Cmd shells need to use `"%CONDA_BAT%"` instead of `conda` for steps using environment activation commands. This is because cmd shells are executed with `%ComSpec% /D /E:ON /V:OFF /S /C "CALL "{0}""` and the `/D` flag disabled execution of `Command Processor/Autorun` windows registry keys, which is what `conda init cmd.exe` sets. See [Github Actions Documentation](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/workflow-syntax-for-github-actions#using-a-specific-shell).
- Running `conda create ...` commands with PowerShell might fail and exit with `error code 1`, even if they are actually successful. Bash can be used under these circumstances.

# License

The scripts and documentation in this project are released under the [MIT License](LICENSE.txt)
