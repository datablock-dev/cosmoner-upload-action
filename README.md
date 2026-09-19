# Cosmoner Web Hosting Upload

Upload a folder to a [Cosmoner](https://cosmoner.com) web hosting site over SFTP
from a GitHub Actions workflow. The SFTP login is fetched with the API key, so
the key is the only secret you store.

It runs [`cosmoner upload`](https://github.com/datablock-dev/cosmoner-sdk/tree/main/cli),
so it behaves exactly as the CLI does on your own machine. The runner needs
Node.js 20 or later, which GitHub-hosted runners already have.

To deploy an image app, use
[cosmoner-deploy-action](https://github.com/datablock-dev/cosmoner-deploy-action).

## Usage

```yaml
- uses: actions/checkout@v7

- run: npm ci && npm run build

- uses: datablock-dev/cosmoner-upload-action@v1
  with:
    api-key: ${{ secrets.COSMONER_API_KEY }}
    project-id: ${{ vars.COSMONER_PROJECT_ID }}
    site: my-site
    path: dist
    delete: true
    host-key: SHA256:PfqYSl1pbMjfMKAbcmjzGZ0t1kpuCZ2mtymdyLu9HwA
```

Every file in `path` is uploaded into the folder the site's own hostname serves,
unless `remote-path` names another, such as `/shop.example.com/public_html`.
Existing files are overwritten. With `delete: true`, files on the site that are
not in `path` are removed once the upload has finished. `.git` folders and
symlinks are never uploaded, and an empty `path` is refused.

`host-key` pins the SFTP gateway's key: a server presenting any other key is
refused before the password is sent. The value above is the gateway's current
key.

The API key needs `hosting:read`.

## Inputs

| Input | Default | |
| --- | --- | --- |
| `api-key` | | Cosmoner API key. Pass it from a secret. |
| `project-id` | | Project the site is in. |
| `site` | | The hosting site's name or id. |
| `path` | | Local folder whose contents are uploaded. |
| `remote-path` | the site's own folder | Folder on the site to upload into. |
| `delete` | `false` | Remove files on the site that are not in `path`. |
| `dry-run` | `false` | List what would change without changing it. |
| `host-key` | | SHA256 fingerprint(s) of the gateway's host key, comma-separated. |
| `cli-version` | `0.3` | `@cosmoner/cli` version or range to run. |

The step fails when the upload fails, and when `site` or `path` is missing.

## License

[MIT](LICENSE)
