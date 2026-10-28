# Unixplay manifest examples

## Bash application

```yaml
schema: playkeen/v1
id: my-cli
name: My CLI
version: 1.0.0
type: bash
architectures: [arm64, armv7]
install: app/install.sh
update: app/update.sh
remove: app/remove.sh
status: app/status.sh
terminal:
  enabled: true
  provider: ttyd
  command: /opt/bin/my-cli
  port: auto
```

## Native Entware package

```yaml
schema: playkeen/v1
id: transmission
name: Transmission
version: 4.0.6
type: opkg
architectures: [arm64, armv7]
package_name: transmission-daemon
package_file: transmission-daemon_4.0.6_{arch}.ipk
```

For S-UI, keep the existing `/opt/sbin/sui-update` and `/opt/etc/init.d/S90s-ui` behavior in the adapter. For 3X-UI, keep `/opt/sbin/x-ui` and its init script. The first adapter release should not silently change panel databases or credentials.
