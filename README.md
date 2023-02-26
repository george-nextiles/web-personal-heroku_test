# web-personal-heroku_test

## Steps

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

## Commands

heroku commands

specify team

```bash
specify the Enterprise Team name with the --org flag on the heroku create command
```

## Resources

- https://michaellin.me/deploy-multiple-apps-in-monorepo-to-heroku/
