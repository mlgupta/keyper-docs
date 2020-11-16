# keyper-docs
Keyper is an SSH Key Based Authentication Manager. It standardizes and centralizes the storage of SSH public keys for all Linux users in your organization saving significant time and effort it takes to manage SSH public keys on each Linux Server. Keyper is a lightweight container taking less than 100MB. It is launched either using Docker or Podman. You can be up and running within minutes instead of days.

This is a Technical documentation repository for Keyper.

## Installation/Build
1. Clone this git repository
```console
$ git clone https://github.com/dbsentry/keyper-docs.git
```
2. Initialize python environment
```console
$ cd keyper-docs
$ rm -rf env/*
$ python3 -m venv env
$ . env/bin/activate
$ pip install -r requirements.txt
```
3. Build using ```make html```
```console
$ make html
```

## Related Projects
- [Keyper Docker](https://github.com/dbsentry/keyper-docker)
- [Keyper](https://github.com/dbsentry/keyper)
- [Keyper FE](https://github.com/dbsentry/keyper-fe)