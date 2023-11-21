# arty
`arty` is a simple tool to maintain an artifactory server, it is using the standard `pyartifactory` module to list all repos and artifacts along info to quickly get what can be deleted.

## Configuration

The script requries to be configured before it can be used, run `arty --configure` to setup your username, password (or API token), and server URL. This will create a file inside the same repo called `.arty.conf` with those detail stored, which the script will then use to connect to artifactory.

## Usage

The script can list or get information about each repo/artifact. To just list, use the `-l` argument.

Without any argument the script will get all repos information about last update (for repos) or last download date/time (for artifacts). With `-l`, it will do the same, without any additional information.

To get info (or list) specific repo, use `-p <reponame>`:

```
arty -r docker-local 
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
arty -r docker-local -a myartifact 
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
arty -r docker-local -a myartifact -c
```

This will create a file `clean-up.csv` with a list of the repos (full path) and the number of days from the **last download date/time**.
