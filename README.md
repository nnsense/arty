# arty
`arty` is a simple tool to maintain an artifactory server, it is using the standard `pyartifactory` module to list all repos and artifacts along info to quickly get what can be deleted.

## Configuration

The script reads Artifactory credentials from `.arty.conf` if that file exists, otherwise it falls back to the `ARTIFACTORY_USERNAME` and `ARTIFACTORY_APIKEY` environment variables. The Artifactory URL is passed explicitly with `--url`.

## Usage

The script can list or get information about each repo/artifact. To just list, use the `-l` argument. Repository listings also include the description, used space, and item count when that storage summary is available from Artifactory. When listing all repositories with `-l`, the output is formatted as a table when `prettytable` is installed.

Without any argument the script will get all repos information about last update (for repos) or last download date/time (for artifacts). With `-l`, it will do the same, without any additional information.

To get info (or list) specific repo, use `-r <reponame>`:

```
arty --url https://artifactory.example.com/artifactory -r docker-local
docker-local
  /myartifact - Last update by someuser on 2020-06-08 13:35
   /aws-alb-ingress-controller - Last update by someuser on 2020-06-08 13:35
    /v1.1.6 - Last update by someuser on 2020-06-08 13:35
     └ Last artifact download on 2020-10-01 09:06 (1146 days old) by someotheruser
    /v2.4.5 - Last update by someotheruser on 2023-02-23 09:12
     └ ZERO DOWNLOADS
```

To get info about a specific artifact, use `-a`:

```
arty --url https://artifactory.example.com/artifactory -r docker-local -a myartifact
docker-local
  /myartifact - Last update by someuser on 2020-06-08 13:35
   /aws-alb-ingress-controller - Last update by someuser on 2020-06-08 13:35
    /v1.1.6 - Last update by someuser on 2020-06-08 13:35
     └ Last artifact download on 2020-10-01 09:06 (1146 days old) by someotheruser
    /v2.4.5 - Last update by someotheruser on 2023-02-23 09:12
     └ ZERO DOWNLOADS
```

A repo is considered "stale" if older than 365 days, the number can be set with `--stale-days` or `-s`. 

To get a CSV with all the repos/artifact older than 365 days (or any other number set with `-s`):

```
arty --url https://artifactory.example.com/artifactory -e -r docker-local -a myartifact
```

This will create a file `clean-up.csv` with a list of the repos (full path), the number of days from the **last download date/time**, and the repository used space when Artifactory exposes it through the storage summary API.

CSV rows use one of these formats:

```
path;NONE;repo used space
path;age;last download;created;repo used space
```
