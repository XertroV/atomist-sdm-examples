# atomist-sdm-examples

Atomist need examples for the SDM devs, here are some I've compiled

#### Requests for Examples

Some things I don't know how to do and want examples of: ([drive-by-contribution thread](https://github.com/XertroV/atomist-sdm-examples/issues/1))

* using artifacts
  - is there an ArtifactProvider type thing? e.g. so artifacts could be pushed to S3 or any other custom location?
    > answer: Artifacts are being phased out, but they're being replaced with caches (what's an artifact store besides a cache?). One should use a `ProjectListener` to add a hook after relevant goals (i.e. build) which will have access to the project directory and can upload specified files, etc. An example: `S3GoalCacheArchiveStore` in sdm-pack-s3. Source: deprecation of Artifact object - see history of sdm-pack-build
 - how do we access available ~artifacts~ cached outputs? query graph?

## Adding GitHub Checks to Goals

* doing it manually: https://github.com/XertroV/sdm-everything/blob/0923f23e83a9a7d28e4b81f8a6c740e40b12128b/lib/machine.ts#L90
* a GoalExecutionListener to automatically add github checks to goals:
  * impl: https://github.com/XertroV/sdm-everything/blob/0923f23e83a9a7d28e4b81f8a6c740e40b12128b/lib/listeners/GithubChecks.ts
  * usage: https://github.com/XertroV/sdm-everything/blob/0923f23e83a9a7d28e4b81f8a6c740e40b12128b/index.ts
* incomplete ProjectExecutionListener to automatically do github checks
  * impl: https://github.com/XertroV/sdm-everything/blob/8b205d6f8bbda1992b233fb5c9567cb485540942/lib/listeners/GithubChecks.ts

## Using SpawnBuilder to replace shell scripts

* starting a script: https://github.com/XertroV/sdm-everything/blob/0923f23e83a9a7d28e4b81f8a6c740e40b12128b/lib/machine.ts#L171

## S3 Integration

* https://github.com/XertroV/sdm-everything/blob/d5c44d6364cb47b55b9e2de623ccee0ce4611a77/lib/machine.ts#L209

# Avoid using cortex if that's your thing (or Atomist goes down)

Idea generally is to:

* run SDM in local mode
* use a custom ProgressLog to log to wherever (e.g. cloudtrail)
* use atomist-cli to clone projects (or you could set up hooks manually)
* trigger SDM via `atomist replay post-commit` at appropriate time
  - github webhook -> local HTTP server -> `git pull` in repo
  - cron job to `git pull` every 1 min or so
  - other ways?
* for integration w/ any particular service you'll obvs need to set up credentials in your environment or one of the `.atomist/client.config.json` files
* obvs any functions provided by cortex won't be available anymore; e.g. slack msgs, automatic github credentials
