# Zip Release [![License](https://img.shields.io/github/license/TheDoctor0/zip-release)](https://github.com/TheDoctor0/zip-release/blob/master/LICENSE)
GitHub action that can be used to create release archive using zip or tar.

It works on all platforms: **Linux**, **MacOS** and **Windows**.

## Usage
An example workflow config:
```yaml
name: Create Archive
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Archive Release
      uses: deep-soft/zip-release@v2
      with:
        type: 'zip'
        filename: 'release.zip'
        exclusions: '*.git* /*node_modules/* .editorconfig'
        verbose: 'yes'
```

The generated archive will be placed as specified by `directory`, `path` and `filename`.
If you want to attach it to the latest release use another action like [`ncipollo/release-action`](https://github.com/ncipollo/release-action):
```yaml
- name: Upload Release
  uses: ncipollo/release-action@v1.12.0
  with:
    artifacts: "release.zip"
    token: ${{ secrets.GITHUB_TOKEN }}
```

## Arguments

### `filename`
Default: `release.zip`

The filename for the generated archive, relative to `directory`.

If you use `type: tar` it's recommended to set the filename to a `tar.gz` (the tarball is always gzip compressed).

### `directory`
Default: `.`

The working directory where the zip or tar actually runs.

### `path`
Default: `.`

The path to the files or directory that should be archived, relative to `directory`.

### `type`
Default: `zip`

Either `zip` or `7z` or `tar` or `tar.gz` or `tar.xz`

Defines if either a zip file, 7z or tar file is created; tar archive compressed with gzip or xz.

On Windows platform 7zip is used to zip files as zip command is not available there.

### `exclusions`
Default: none

List of excluded files or directories.

Please note: this handles slightly differently, depending on if you use `type: zip` or `type: tar`.

ZIP requires you to specify wildcards or full filenames.

TAR allows you to specify only the filename, no matter if it's in a subdirectory.

### `recursive_exclusions`
Default: none

Alternative to `exclusions` that allows you to specify a list of [recursive wildcards](https://sevenzip.osdn.jp/chm/cmdline/switches/recurse.htm).
Only applies to `type: zip` on Windows where 7zip is used.

For example:

```exclusions: *.txt``` will only exclude files ending with `.txt`

```recursive_exclusions: *.txt``` will exclude files ending with `.txt` in any subdirectory.

### `ignore_git`
Default: yes

Default setting: ignore .git/ folder

Set ```ignore_git: 'no'``` to add to archive .git/ folder

### `volume_size:`
Default: ''

Set volume size for `type: 7z` in bytes or accepted suffix values: b|k|m|g

Example:</br>
 volume_size: 2g, means volume size is 2 GBytes</br>
 volume_size: 1024k, means volume size is 1 MByte</br>
 volume_size: 1073741824, means volume size is 1 GByte or 1024 MBytes</br>

### `env_variable`
Default: ZIP_RELEASE_ARCHIVE

env variable name to set after archive creation

### `verbose`
Default: no

set to no for quiet operations
set to yes to show output of filenames added to archive

### `custom`
Default: none

Provide any custom parameters to the command.

For example:

```custom: --ignore-failed-read``` option used with `tar` command, which allows to ignore and continue on unreadable files. 

### `outputs:`
```
  volumes_list_name:
    description: 'Name of file list, containing volumes filenames'
    value: ${{ steps.zip_release_run.outputs.volumes_list_name }}
  volumes_number:
    description: 'Number of volumes'
    value: ${{ steps.zip_release_run.outputs.volumes_number }}
  volumes_files:
    description: 'Names of volumes, concatenated with :'
    value: ${{ steps.zip_release_run.outputs.volumes_files }}
```
```example:
echo "VOLUMES_LIST_NAME=${{ steps.zip_release_action.outputs.VOLUMES_LIST_NAME }}";
echo "VOLUMES_NUMBER=${{ steps.zip_release_action.outputs.VOLUMES_NUMBER }}";
echo "VOLUMES_FILES=${{ steps.zip_release_action.outputs.VOLUMES_FILES }}";
```
