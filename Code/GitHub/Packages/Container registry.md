#github #docker #containers #code 
## About the Container registry

The Container registry stores container images within your organization or personal account, and allows you to associate an image with a repository. You can choose whether to inherit permissions from a repository, or set granular permissions independently of a repository. You can also access public container images anonymously.

## Authenticating to the Container registry

> [!NOTE]
> GitHub Packages only supports authentication using a personal access token (classic). For more information, see [Managing your personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).

You need an access token to publish, install, and delete private, internal, and public packages.

You can use a personal access token (classic) to authenticate to GitHub Packages or the GitHub API. When you create a personal access token (classic), you can assign the token different scopes depending on your needs. For more information about packages-related scopes for a personal access token (classic), see [About permissions for GitHub Packages](https://docs.github.com/en/packages/learn-github-packages/about-permissions-for-github-packages#about-scopes-and-permissions-for-package-registries).

To authenticate to a GitHub Packages registry within a GitHub Actions workflow, you can use:

- `GITHUB_TOKEN` to publish packages associated with the workflow repository.
- A personal access token (classic) with at least `read:packages` scope to install packages associated with other private repositories (`GITHUB_TOKEN` can be used if the repository is granted read access to the package. See [Configuring a package's access control and visibility](https://docs.github.com/en/packages/learn-github-packages/configuring-a-packages-access-control-and-visibility)).

### Authenticating in a GitHub Actions workflow

This registry supports granular permissions. For registries that support granular permissions, if your GitHub Actions workflow is using a personal access token to authenticate to a registry, we highly recommend you update your workflow to use the `GITHUB_TOKEN`. For guidance on updating your workflows that authenticate to a registry with a personal access token, see [Publishing and installing a package with GitHub Actions](https://docs.github.com/en/packages/managing-github-packages-using-github-actions-workflows/publishing-and-installing-a-package-with-github-actions#upgrading-a-workflow-that-accesses-a-registry-using-a-personal-access-token).

You can use a `GITHUB_TOKEN` in a GitHub Actions workflow to delete or restore a package using the REST API, if the token has `admin` permission to the package. Repositories that publish packages using a workflow, and repositories that you have explicitly connected to packages, are automatically granted `admin` permission to packages in the repository.

### Authenticating with a personal access token (classic)

> [!NOTE]
> GitHub Packages only supports authentication using a personal access token (classic). For more information, see [Managing your personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).

1. Create a new personal access token (classic) with the appropriate scopes for the tasks you want to accomplish. If your organization requires SSO, you must enable SSO for your new token.
   > [!NOTE]
   > By default, when you select the `write:packages` scope for your personal access token (classic) in the user interface, the `repo` scope will also be selected. The `repo` scope offers unnecessary and broad access, which we recommend you avoid using for GitHub Actions workflows in particular. For more information, see [Security hardening for GitHub Actions](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#considering-cross-repository-access). As a workaround, you can select just the `write:packages` scope for your personal access token (classic) in the user interface with this url: https://github.com/settings/tokens/new?scopes=write:packages.
   
   - Select the `read:packages` scope to download container images and read their metadata.
   - Select the `write:packages` scope to download and upload container images and read and write their metadata.
   - Select the `delete:packages` scope to delete container images.
    
   For more information, see [Managing your personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).
2. Using the CLI for your container type, sign in to the Container registry service at `ghcr.io`.
    
    ```shell
    echo YOUR_TOKEN | docker login ghcr.io -u USERNAME --password-stdin
    ```
	Replace `YOUR_TOKEN` with the actual PAT, and `USERNAME` with your GitHub username.

## Pushing container images

This example pushes the latest version of `IMAGE_NAME`.

```shell
docker push ghcr.io/NAMESPACE/IMAGE_NAME:latest
```

Replace `NAMESPACE` with the name of the personal account or organization to which you want the image to be scoped.

This example pushes the `2.5` version of the image.

```shell
docker push ghcr.io/NAMESPACE/IMAGE_NAME:2.5
```

When you first publish a package, the default visibility is private. To change the visibility or set access permissions, see [Configuring a package's access control and visibility](https://docs.github.com/en/packages/learn-github-packages/configuring-a-packages-access-control-and-visibility). You can link a published package to a repository using the user interface or command line. For more information, see [Connecting a repository to a package](https://docs.github.com/en/packages/learn-github-packages/connecting-a-repository-to-a-package).

When you push a container image from the command line, the image is not linked to a repository by default. This is the case even if you tag the image with a namespace that matches the name of the repository, such as `ghcr.io/octocat/my-repo:latest`.

To connect a repository when publishing an image from the command line, and to ensure your `GITHUB_TOKEN` has appropriate permissions when using a GitHub Actions workflow, we recommend adding the label `org.opencontainers.image.source` to your `Dockerfile`. For more information, see [Labelling container images](#labelling-container-images) in this article and [Publishing and installing a package with GitHub Actions](https://docs.github.com/en/packages/managing-github-packages-using-github-actions-workflows/publishing-and-installing-a-package-with-github-actions).

## Pulling container images

### Pull by digest

To ensure you're always using the same image, you can specify the exact container image version you want to pull by the `digest` SHA value.

1. To find the digest SHA value, use `docker inspect` or `docker pull` and copy the SHA value after `Digest:`
    
    ```shell
    docker inspect ghcr.io/NAMESPACE/IMAGE_NAME
    ```
    
    Replace `NAMESPACE` with the name of the personal account or organization to which the image is scoped.
    
2. Remove image locally as needed.
    
    ```shell
    docker rmi ghcr.io/NAMESPACE/IMAGE_NAME:latest
    ```
    
3. Pull the container image with `@YOUR_SHA_VALUE` after the image name.
    
    ```shell
    docker pull ghcr.io/NAMESPACE/IMAGE_NAME@sha256:82jf9a84u29hiasldj289498uhois8498hjs29hkuhs
    ```
    

### Pull by name

```shell
docker pull ghcr.io/NAMESPACE/IMAGE_NAME
```

Replace `NAMESPACE` with the name of the personal account or organization to which the image is scoped.

### Pull by name and version

```shell
$ docker pull ghcr.io/NAMESPACE/IMAGE_NAME:1.14.1
```

Replace `NAMESPACE` with the name of the personal account or organization to which the image is scoped.
### Pull by name and latest version

```shell
$ docker pull ghcr.io/NAMESPACE/IMAGE_NAME:latest
```

> [!IMPORTANT]
> If the image wasn't pushed explicitly with the tag `latest`, this won't work because there's no default version if using `latest`.

## Building container images

This example builds the `hello_docker` image:

```shell
docker build -t hello_docker .
```

## Tagging container images

An image can be tagged by its local name (the `-t` option in `docker build`) or by its ID (`docker images`):

```shell
docker tag 38f737a91f39 ghcr.io/NAMESPACE/NEW_IMAGE_NAME:latest
```

Replace `NAMESPACE` with the name of the personal account or organization to which you want the image to be scoped.

## Labelling container images

You can use pre-defined annotation keys to add metadata including a description, a license, and a source repository to your container image. Values for supported keys will appear on the package page for the image.

For most images, you can use Docker labels to add the annotation keys to an image. For more information, see [LABEL](https://docs.docker.com/engine/reference/builder/#label) in the official Docker documentation and [Pre-Defined Annotation Keys](https://github.com/opencontainers/image-spec/blob/main/annotations.md#pre-defined-annotation-keys) in the `opencontainers/image-spec` repository.

For multi-arch images, you can add a description to the image by adding the appropriate annotation key to the `annotations` field in the image's manifest. For more information, see [Adding a description to multi-arch images](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry#adding-a-description-to-multi-arch-images).

The following annotation keys are supported in the Container registry.

| Key                                    | Description                                                                                                                                                                                                                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `org.opencontainers.image.source`      | The URL of the repository associated with the package. For more information, see [Connecting a repository to a package](https://docs.github.com/en/packages/learn-github-packages/connecting-a-repository-to-a-package#connecting-a-repository-to-a-container-image-using-the-command-line). |
| `org.opencontainers.image.description` | A text-only description limited to 512 characters. This description will appear on the package page, below the name of the package.                                                                                                                                                          |
| `org.opencontainers.image.licenses`    | An SPDX license identifier such as "MIT," limited to 256 characters. The license will appear on the package page, in the "Details" sidebar. For more information, see [SPDX License List](https://spdx.org/licenses/).                                                                       |

To add a key as a Docker label, we recommend using the `LABEL` instruction in your `Dockerfile`. For example, if you're the user `octocat` and you own `my-repo`, and your image is distributed under the terms of the MIT license, you would add the following lines to your `Dockerfile`:

```dockerfile
LABEL org.opencontainers.image.source=https://github.com/octocat/my-repo
LABEL org.opencontainers.image.description="My container image"
LABEL org.opencontainers.image.licenses=MIT
```

> [!NOTE]
> If you publish a package that is linked to a repository, the package automatically inherits the access permissions of the linked repository, and GitHub Actions workflows in the linked repository automatically get access to the package, unless your organization has disabled automatic inheritance of access permissions. For more information, see [Configuring a package's access control and visibility](https://docs.github.com/en/packages/learn-github-packages/configuring-a-packages-access-control-and-visibility#about-inheritance-of-access-permissions).

Alternatively, you can add labels to an image at build time with the `docker build` command.

```shell
$ docker build \
 --label "org.opencontainers.image.source=https://github.com/octocat/my-repo" \
 --label "org.opencontainers.image.description=My container image" \
 --label "org.opencontainers.image.licenses=MIT"
```

### Building multi-arch images

For example, the following GitHub Actions workflow step builds and pushes a multi-arch image. The `outputs` parameter sets the description for the image.

```yaml
# This workflow uses actions that are not certified by GitHub.
# They are provided by a third-party and are governed by
# separate terms of service, privacy policy, and support
# documentation.

- name: Build and push Docker image
  uses: docker/build-push-action@f2a1d5e99d037542a71f64918e516c093c6f3fc4
  with:
    context: .
    file: ./Dockerfile
    platforms: ${{ matrix.platforms }}
    push: true
    outputs: type=image,name=target,annotation-index.org.opencontainers.image.description=My multi-arch image
```

## Troubleshooting

- The Container registry has a 10 GB size limit for each layer.
- The Container registry has a 10 minute timeout limit for uploads.
