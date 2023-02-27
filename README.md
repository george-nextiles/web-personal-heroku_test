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
# heroku config:set -a sandbox-gs-client PROCFILE=client/Procfile
# heroku config:set -a sandbox-gs-server PROCFILE=server/Procfile
heroku config:set -a sandbox-gs-client PROCFILE=Procfile
heroku config:set -a sandbox-gs-server PROCFILE=Procfile
```
6. Redeploy local git to heroku remote
```bash
# git push heroku-client HEAD:main
# git push heroku-server HEAD:main
git push heroku-client main
git push heroku-server main
```
7. Check deployment
```bash
heroku logs --tail --remote heroku-client
heroku logs --tail --remote heroku-server
```

You can also test locally by specifying the `Procfile`

```bash
heroku local -f client/Procfile
heroku local -f server/Procfile
```

Note that the server runs default on port 5000, use the `-p` flag to change it

If the above doesn't work, create a Procfile at the root and force the dyno to run

```bash
heroku ps:scale client=1 --remote heroku-client
heroku ps:scale server=1 --remote heroku-server
```

In trouble, remove buildpacks and redo

```bash
heroku buildpacks:clear --remote heroku-client
heroku buildpacks:clear --remote heroku-server
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
