## custom_image_pipeline

Example of how you can image manage using gitops.

### Using the image

You can build the image via:

```bash
$ podman image build . --build-arg now="$(date +%Y%m%d)" --build-arg ubi_version=9
```

### Build Args

`now` : Date in the '+%Y%m%d' format

`ubi_version` : 8 or 9 - Currently. These define the base image that will be used to build the datascience image.
