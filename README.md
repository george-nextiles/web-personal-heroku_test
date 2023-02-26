# web-personal-heroku_test

## Setup

- Ask admin to be invited to Heroku teams (sandbox, nextiles)
- Install heroku CLI
- Download heroku multi-app buildpack: `heroku create --buildpack https://github.com/heroku/heroku-buildpack-multi-procfile.git`

## Steps

Following heroku multi-procfile [documentation](https://elements.heroku.com/buildpacks/heroku/heroku-buildpack-multi-procfile)

1. Create git app with `client/` and `server/` folders
2. Create 2 remotes using the --remote and --org flag
```bash
heroku create sandbox-gs-client --remote heroku-client --org sandbox
heroku create sandbox-gs-server --remote heroku-server --org sandbox
```
3. Create `Procfile` for both folders
```bash
web: node server.js # or whatever to starrt
```
4. Deploy both two separate heroku remotes
```bash
heroku buildpacks:add -a sandbox-gs-client heroku-community/multi-procfile --remote heroku-client
heroku buildpacks:add -a sandbox-gs-server heroku-community/multi-procfile --remote heroku-server
```
5. Link `Procfiles`
```bash
heroku config:set -a sandbox-gs-client PROCFILE=client/Procfile
heroku config:set -a sandbox-gs-server PROCFILE=server/Procfile
```
6. Redeploy local git to heroku remote
```bash
git push heroku-client HEAD:master
git push heroku-server HEAD:master
```

## Commands

heroku commands

specify team

```bash
specify the Enterprise Team name with the --org flag on the heroku create command
```

## Resources
- https://elements.heroku.com/buildpacks/heroku/heroku-buildpack-multi-procfile
- https://devcenter.heroku.com/articles/heroku-cli-commands
- https://michaellin.me/deploy-multiple-apps-in-monorepo-to-heroku/
