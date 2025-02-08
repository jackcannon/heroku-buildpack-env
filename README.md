# Heroku/Dokku Environment File Buildpack

This buildpack allows you to include environment variables in your Heroku/Dokku deployments using a simple environment file. It automatically detects and loads environment variables from specified files during the build process.

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

## Security Notice

⚠️ **Important**: This buildpack is intended for non-sensitive configuration variables. Do not store sensitive information (passwords, API keys, etc.) in these files as they will be committed to your repository. For sensitive data, use your platform's secure environment variable interface:

- Dokku: `dokku config:set your-app KEY=VALUE`
- Heroku: `heroku config:set KEY=VALUE`

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
