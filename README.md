# Heroku/Dokku Environment File Buildpack

This buildpack allows you to include environment variables in your Heroku/Dokku deployments using a simple environment file. It automatically detects and loads environment variables from specified files during the build process.

## Why?

I like keeping project deployment configurations within the codebase. While `.buildpacks` files are helpful, some buildpacks require environment variables (like `NGINX_ROOT` for [heroku-buildpack-nginx](https://github.com/jackcannon/heroku-buildpack-nginx)), forcing server-side configuration. This adds complexity and prevents configuration updates through simple code changes.
This "pre-buildpack" approach aims to maximize codebase-based deployment configuration. Deploying to a new server becomes streamlined: create the app and push the code.

## Supported Files

The buildpack looks for the following files in your repository (in order):
- `.dokku.env`
- `.env.dokku`
- `.heroku.env`
- `.env.heroku`

The first matching file found will be used to load environment variables.

## File Format

The environment file should follow these conventions:
```
VARIABLE_NAME='value'
# Comments are ignored
ANOTHER_VARIABLE='another value'
```

## Installation

This buildpack should be the first buildpack, and should have other buildpacks running afterwards.

### .buildpacks
Add a `.buildpacks` file to your repo
```
https://github.com/jackcannon/heroku-buildpack-env
https://github.com/jackcannon/heroku-buildpack-nginx
```

### Command

Not recommended, but here if you must.

#### For dokku
```
dokku buildpacks:add --index 1 https://github.com/jackcannon/heroku-buildpack-env
```

#### For Heroku
```
heroku buildpacks:add --index 1 https://github.com/jackcannon/heroku-buildpack-env
```

## Usage

1. Create one of the supported environment files in your project root
2. Add your environment variables to the file
3. Commit the file to your repository
4. Deploy your application

## Example

This example will setup an NGINX server, serving the contents of 'sites/foo'.

- `.dokku.env`
```
NGINX_ROOT='sites/bar'
```

- `.buildpacks`
```
https://github.com/jackcannon/heroku-buildpack-env
https://github.com/jackcannon/heroku-buildpack-nginx
```

- `.static`
```
(can be empty. Required by heroku-buildpack-nginx)
```

### Example repo

An example of a full implementation can be found here: https://github.com/jackcannon/env-buildpack-example

## Security Notice

⚠️ **Important**: This buildpack is intended for non-sensitive configuration variables. Do not store sensitive information (passwords, API keys, etc.) in these files as they will be committed to your repository. For sensitive data, use your platform's secure environment variable interface:

- Dokku: `dokku config:set your-app KEY=VALUE`
- Heroku: `heroku config:set KEY=VALUE`

## Environment Variable Load Order

There are two ways to set environment variables, and they load at different times:

1. **Build Time Variables** (via `.dokku.env` file)
   - These load during the build process
   - Used when your app is being built
   - Persist if not overwritten

2. **Runtime Variables** (via `config:set`)
   - These load just before your app starts
   - Override any matching variables from `.dokku.env`

So if you set the same variable both ways:
```bash
# In .dokku.env
MY_VAR='build-time-value'

# Via command line
dokku config:set your-app MY_VAR='runtime-value'
```

Your app will see:
- `MY_VAR='build-time-value'` during build
- `MY_VAR='runtime-value'` when running

> **Tip:** Unless you really need values to be different between build and runtime, just use `.dokku.env` files.


## How It Works

The buildpack:
1. Detects if any of the supported environment files exist
2. Reads the first matching file found
3. Ignores comments and empty lines
4. Exports each valid environment variable
5. Makes the variables available to your application during runtime

## License

MIT License

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
