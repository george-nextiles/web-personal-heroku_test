# web-personal-heroku_test

## Summary

Following this tutorial: https://towardsdev.com/deploying-a-monorepo-to-heroku-74c0d5a1f79e

Steps can be done on the command line, or directly through the heroku console

1. Add buildpacks
   1. https://github.com/heroku/heroku-buildpack-multi-procfile
   2. heroku/nodejs
2. Add Procfile's to client and server
   1. `web: cd server && npm start`
   2. `web: cd client && npm start`
3. Set PROCFILE env variable to point at client or server local Procfile
   1. `heroku config:set -a heroku-client PROCFILE=client/Procfile` 
   2. `heroku config:set -a heroku-server PROCFILE=server/Procfile` 
4. Set env variables in their respective heroku apps
   1. `CLIENT_ENV=true`
   2. `SERVER_ENV=true`
5. Create npm install scripts in root `package.json`
```json
    "scripts": {
       "postinstall": "if [ $CLIENT_ENV ]; then npm run postinstall-client; elif [ $SERVER_ENV ]; then npm run postinstall-server; else echo no environment detected, please set CLIENT_ENV or SERVER_ENV; fi",
       "postinstall-client": "cd packages/client && npm install && npm run build",
       "postinstall-server": "cd packages/server && npm install"
   }
```
6. Commit, and push to heroku remote
   1. `git push heroku-client main`
   2. `git push heroku-server main`
7. Make sure the web dynos are enabled on the heroku console

## Setup

- Ask admin to be invited to Heroku teams (sandbox, nextiles)
- Install heroku CLI
- Download heroku multi-app buildpack

```bash
heroku create --buildpack https://github.com/heroku/heroku-buildpack-multi-procfile.git
```

```bash
heroku create --buildpack https://github.com/lstoll/heroku-buildpack-monorepo.git
```

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
# remove buildpack
heroku buildpacks:clear --remote heroku-client
heroku buildpacks:clear --remote heroku-server

# add nodejs buildpack
heroku buildpacks:set heroku/nodejs --remote heroku-server
heroku buildpacks:set heroku/nodejs --remote heroku-server
```

## Commands

heroku commands

specify team

```bash
specify the Enterprise Team name with the --org flag on the heroku create command
```

## Resources
- https://towardsdev.com/deploying-a-monorepo-to-heroku-74c0d5a1f79e
- https://medium.com/inato/how-to-setup-heroku-with-yarn-workspaces-d8eac0db0256
- https://elements.heroku.com/buildpacks/heroku/heroku-buildpack-multi-procfile
- https://devcenter.heroku.com/articles/heroku-cli-commands
- https://michaellin.me/deploy-multiple-apps-in-monorepo-to-heroku/
