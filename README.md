# hello-world-static

A minimal example static site for [boffyn](https://boffyn.dev/) - a plain static site
(`index.html` and an image), with no build step, no containers, no boffyn config.

It is served by the [boffyn-static](https://github.com/boffyn/boffyn-static) app,
which runs a single nginx container shared between any number of static sites.

For full boffyn documentation, see [boffyn.dev](https://boffyn.dev/).


## Quick start

1. Install boffyn - see [boffyn.dev](https://boffyn.dev/) for instructions.

2. Create a host manifest, eg `myserver.yml` - see the sample below.

3. Set it as your default host manifest:

   ```bash
   boff use myserver.yml
   ```

4. If this is a fresh server, bootstrap it:

   ```bash
   boff bootstrap
   ```

5. Deploy the ingress, static server and site:

   ```bash
   boff deploy
   ```

6. Visit your site at the hostname you set under `ingress`.


## Sample host manifest

```yaml
host:
  name: myserver
  address: 192.168.56.10
  # use boffyn defaults for server
apps:
  boffyn-static:
    extends: gh:boffyn/boffyn-static

  hello-world:
    extends: gh:boffyn/hello-world-static
    ingress:
      - web: "hello.example.com"
        # Serve using boffyn-static's nginx
        service: boffyn-static:nginx
```

Replace `address` and the hostnames with your own. Make sure the hostnames resolve
to your server's address.

## Building your own

The site's files are served from the root of this repository, but this can be changed
the the `static_path` config setting.

For example, to serve files from a `public/` dir in your repo, you can set
`static_path` in your host manifest config:

```yaml
apps:
  ...
  hello-world:
    extends: gh:example/my-hello-world
    config:
      static_path: public
    ingress:
      - web: "hello.example.com"
        service: boffyn-static:nginx
```

or a more portable option is to create a `boffyn.yml` in the root of your project and set it there:

```yaml
name: my-hello-world
app:
  config:
    static_path: public
```

The path is relative to the root of the repository.

If your site needs a build step, see
[hello-world-build](https://github.com/boffyn/hello-world-build), which runs a
`pre-deploy` hook to generate the site and serves the output from `dist`.
