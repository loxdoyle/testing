# cert-manager/testing

This repository contains the configuration used for testing all jetstack projects.

It is used by [Prow](https://github.com/kubernetes-sigs/prow)
to provide GitHub automation to all of our repositories.

## Common tasks

### Linting files in this repository

We have certain requirements on files in these repository:

* boilerplate check - we require that all files in the repository have a valid
copyright notice at the top of the file.
* prowgen check - we require that all cert-manager job configuration files are
generated using `prowgen`.

You can run the lint checks with:

```bash
make verify
```

### Validating Prow configuration

In order to test the configuration is valid, you can run:

```bash
make local-checkconfig
```

This will use the test-infra 'checkconfig' tool to verify the configuration
files.

### Deploying a new version of Prow

Prow's deployment on our prow-untrusted cluster is done manually using a Makefile in ./prow/cluster.

See more detailed information about upgrading Prow in [./prow/cluster/README.md](./prow/cluster/README.md)

### Building an image and exporting to your local Docker daemon

Each directory under `images/` contains a configuration file that
define how each image should be built.

You can build these images and store them within your local docker daemon by
running:

```bash
$ ./images/image-builder-script/builder.sh images/golang-dind
./images/image-builder-script/builder.sh images/golang-aws
WARNING: GOOGLE_APPLICATION_CREDENTIALS not set
Executing builder...
2023/04/07 16:31:51 --confirm is set to false, not pushing images
...
```

This may take a few minutes depending on the state of your Docker cache.

### Pushing a docker image to the image repository

⚠️ WARNING: You're unlikely to have permissions to be able to push images to GCR locally. If you're simply
looking to update an image, a [workload](https://github.com/cert-manager/testing/blob/365d570125e751a7d9aac4148d8c0ef23e42168c/config/jobs/testing/testing-postsubmits-trusted.yaml#L76)
in prow will build and push the image for you when your PR with the changes is merged.

builder.sh can also be used to *push* built docker images to the remote registry.

This push target **will not** handle authentication with the remote registry for
you. You should ensure your Docker client is already authenticated using gcloud.

For example, to build and push the `images/golang-aws` image:

```bash
# Obtain credentials for the docker registry
$ gcloud docker -a
# Build (if required) and push the docker image
$ ./images/image-builder-script/builder.sh images/golang-aws --confirm=true
...
```

If you want to push to a custom repository, you can use the `--registry` flag.

---

## Structure

* [config/](config/): Adding or modifying CI jobs (presubmits, periodics or postsubmits)
* [prow/](prow/): Updating/upgrading Prow
* [images/](images/): Creating or modifying images used during CI

### hack/

This contains support scripts used to verify aspect
