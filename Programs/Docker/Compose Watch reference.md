#programs #docker #docker-compose #code #reference

Watch automatically updates and previews your running Compose services as you edit and save your code.

## Usage

```
docker compose watch [services]
```

or

```
docker compose up --watch
```

## Example
Add a `develop.watch` section to your `docker-compose.yml` for the relevant service(s).  

```yaml
 services:
   web:
	 build: .
	 ports:
	   - "8000:5000"
	 develop:
	   watch:
		 - action: sync
		   path: .
		   target: /code
```

## Properties

| Property | Description                                                    |
| -------- | -------------------------------------------------------------- |
| `action` | The action to do when the changes are detected                 |
| `path`   | The path relative to the project root that needs to be watched |
| `target` | The path to update inside the container                        |
| `ignore` | The paths relative to the project root to ignore               |

## Actions

| Action         | Description                                   | Compose version |
| -------------- | --------------------------------------------- | --------------- |
| `sync`         | Sync files to the container.                  |                 |
| `rebuild`      | Rebuild the image and recreate the service    |                 |
| `restart`      | Restart the container                         | v2.32.0+        |
| `sync+restart` | Sync files and restart the container          | v2.23.0+        |
| `sync+exec`    | Sync files and run a command in the container | v2.32.0+        |

### `sync+exec` additional properties

| Property       | Description                           | Nessesary |
| -------------- | ------------------------------------- | :-------: |
| `exec.command` | The command to run after syncing      |     ✅     |
| `user`         | User to run the command as            |     ❌     |
| `privileged`   | Run with privileged access            |     ❌     |
| `working_dir`  | Directory to run the command in       |     ❌     |
| `environment`  | Environment variables for the command |     ❌     |

## Options

| Option    | Description                                   | Default |
| --------- | --------------------------------------------- | ------- |
| `--no-up` | Do not build & start services before watching | false   |
| `--prune` | Prune dangling images on rebuild              | true    |
| `--quiet` | Hide build output                             | false   |


> [!IMPORTANT]
> - Compose Watch can't watch  `Dockerfile` or `docker-compose.yml`.
> - It only works with services using the `build` attribute (not pre-built images).