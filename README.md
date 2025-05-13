# caddy-anubis

## Known Issues

- One major issue is the very first request after a Caddy start or restart, takes like 5 seconds till anubis kicks in. All subsequent requests, even after clearing cookies, are near instant.

## Current usage

Just add an `anubis` to your caddyfile in the block you want the protection. currently I have not seen it work properly inside a `route` or `handler` block. But it works outside of those.

There is an optional `target` field you can set if you want to force the redirect elsewhere. It does a 302 redirect.

Example (also check the caddyfile in this repo, it is used for testing):

```caddy
:80 {
  anubis

  # or

  anubis {
    target https://qwant.com
  }
}
```

## Testing locally

```bash
xcaddy -- run --config Caddyfile
```

## Usage on nixos

```nix
  services.caddy = {
    enable = true;
    package = pkgs.caddy.withPlugins {
      plugins = [
        "github.com/BatteredBunny/caddy-anubis@v1.0.3"
      ];
      hash = "sha256-RZgkwdKAeWTUg9iQwR9PSVmWx9v1bLVLR+dfqSpuvv8=";
    };

    virtualHosts."example.com".extraConfig = ''
      request_header +X-Real-IP {remote_host}
      request_header +X-Forwarded-For {remote_host}

      anubis

      reverse_proxy :8080
    '';
  };
```

## Credits

- [anubis](https://github.com/TecharoHQ/anubis) - the project that started all of this.
