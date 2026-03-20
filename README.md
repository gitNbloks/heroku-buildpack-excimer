# heroku-buildpack-excimer

A Cloud Native Buildpack (CNB) that installs the [Excimer](https://www.mediawiki.org/wiki/Excimer) PHP extension on top of Heroku PHP 24.

## How it works

1. Runs **after** `heroku/php` so that `php` and `pecl` are already on `$PATH`.
2. Installs `excimer` via PECL into a dedicated CNB layer.
3. Enables the extension by prepending its `.ini` directory to `PHP_INI_SCAN_DIR`.
4. Caches the compiled `.so` by PHP version — reinstalls only when PHP is upgraded.

## Usage in your app

Add a `project.toml` to your app repo:

```toml
[_]
schema-version = "0.2"

[[io.buildpacks.group]]
uri = "heroku/php"

[[io.buildpacks.group]]
uri = "docker.io/gitnbloks/php-excimer"   # replace with your published image
```

> **Important:** `heroku/php` must be listed **before** this buildpack.

## Build locally with pack

```bash
# Build the buildpack image once
pack buildpack package heroku/php-excimer --config ./package.toml

# Build your app
pack build my-app --builder heroku/builder:24 --config ./project.toml
```

## Add to heroku project
```
heroku buildpacks:add https://github.com/teamtv/heroku-buildpack-excimer --index 2 -a [your app name]
```

## Requirements

- Heroku stack: `heroku-24`
- Builder: `heroku/builder:24`
- `heroku/php` buildpack must run before this one

## Files

| File | Purpose |
|---|---|
| `buildpack.toml` | Buildpack metadata (id, version, stack) |
| `bin/detect` | Applies to any app with `composer.json` / `composer.lock` |
| `bin/build` | Installs excimer via PECL, wires up `PHP_INI_SCAN_DIR` |
| `package.toml` | Used by `pack buildpack package` to publish as OCI image |
| `project.toml.example` | Example app-side config |
