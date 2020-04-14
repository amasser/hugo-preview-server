# Hugo Preview Server

**This is in an early stage:** It has only been tested with a single demo site and misses some features.

## Summary

A piece of glue code that runs Hugo for serving previews for CMS systems.

It uses a git client to get access to assets/layouts of the site and compiles pages based on content passed in via HTTP.

### DEMO

There is a demo setup at: https://hugo-preview-server.netlify.com/demo/admin/

Log in via GitHub and select any entry from the "Posts" collection to see it in action.

## Features

- Uses GitHub as a virtual filesystem to get layouts
- Renders pages on-demand, taking `path` and `data` via `POST`
- Supports partial re-renders (just like `hugo server`)
- Supports extended mode

## Roadmap

- Build & package a solid integration for [NetlifyCMS](https://www.netlifycms.org/)
- Support custom source branches (only default right now)
- Provide tooling for easy usage (e.g. `npx hugo-preview-server setup:function ./functions`)
- Run as a standalone http server process (for use in docker)

## Usage

There is a binary available that can be deployed to AWS Lambda or any compatible platforms like [Netlify Functions](https://www.netlify.com/products/functions/).

### Download in CI

Use this command in your build to get the latest release:

```
curl -L -s https://github.com/mraerino/hugo-preview-server/releases/latest/download/preview-lambda -o <destination>

# Example for Netlify Functions
mkdir -p functions
curl -L -s https://github.com/mraerino/hugo-preview-server/releases/latest/download/preview-lambda -o functions/preview
chmod +x functions/preview
```

See [this `Netlify.toml`](demo/netlify.toml) for an example.
In the future there will be tooling that will make this even easier.

### Configuration

The functions needs the following environment variables:

```bash
HUGO_PREVIEW_GITHUB_REPO=owner/repo # path to the repo where your hugo site is located
HUGO_PREVIEW_GITHUB_TOKEN=<token>   # a personal or oauth token that allows read access to the repo
HUGO_PREVIEW_BASE=<path>            # optional, use when your hugo site is not based in the repo root
```

## Run locally

Requirements:

- Docker
- [SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)

While developing, use the one of the make commands to get a binary:

- `make build` - if you are on linux
- `make in-docker` - any other system

Put your config vars into `env.json`, like this:

```
{
  "PreviewFunction": {
    "HUGO_PREVIEW_GITHUB_TOKEN": "<token>",
    "HUGO_PREVIEW_GITHUB_REPO": "owner/repo",
    "HUGO_PREVIEW_BASE": ""
  }
}
```

Run this to start the local API gateway:

```bash
sam local start-api --env-vars env.json
```

This is how the API request should look like:

```js
POST http://127.0.0.1:3000/render

{
    "path": "content/my-page.md",
    "data": {
        "body": "markdown body",
        "title": "title frontmatter field",
        "other": "frontmatter"
    }
}
```

## License

See [LICENSE file](LICENSE.md)
