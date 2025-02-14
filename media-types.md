# OCI Image Media Types

The following media types identify the formats described here and their referenced resources:

- `application/vnd.oci.descriptor.v1+json`: [Content Descriptor](descriptor.md)
- `application/vnd.oci.layout.header.v1+json`: [OCI Layout](image-layout.md#oci-layout-file)
- `application/vnd.oci.image.index.v1+json`: [Image Index](image-index.md)
- `application/vnd.oci.image.manifest.v1+json`: [Image manifest](manifest.md#image-manifest)
- `application/vnd.oci.image.config.v1+json`: [Image config](config.md)
- `application/vnd.oci.image.layer.v1.tar`: ["Layer", as a tar archive](layer.md)
- `application/vnd.oci.image.layer.v1.tar+gzip`: ["Layer", as a tar archive](layer.md#gzip-media-types) compressed with [gzip][rfc1952]
- `application/vnd.oci.image.layer.v1.tar+zstd`: ["Layer", as a tar archive](layer.md#zstd-media-types) compressed with [zstd][rfc8478]
- `application/vnd.oci.scratch.v1+json`: [Scratch blob](manifest.md#example-of-a-scratch-config-or-layer-descriptor)
- `application/vnd.oci.artifact.manifest.v1+json`: [Artifact manifest](artifact.md)

The following media types identify a ["Layer" with distribution restrictions](layer.md#non-distributable-layers), but are **deprecated** and not recommended for future use:

- `application/vnd.oci.image.layer.nondistributable.v1.tar`: "Layer", as a tar archive
- `application/vnd.oci.image.layer.nondistributable.v1.tar+gzip`: ["Layer", as a tar archive with distribution restrictions](layer.md#gzip-media-types) compressed with [gzip][rfc1952]
- `application/vnd.oci.image.layer.nondistributable.v1.tar+zstd`: ["Layer", as a tar archive with distribution restrictions](layer.md#zstd-media-types) compressed with [zstd][rfc8478]

## Media Type Conflicts

[Blob](image-layout.md) retrieval methods MAY return media type metadata.
For example, a HTTP response might return a manifest with the Content-Type header set to `application/vnd.oci.image.manifest.v1+json`.
Implementations MAY also have expectations for the blob's media type and digest (e.g. from a [descriptor](descriptor.md) referencing the blob).

* Implementations that do not have an expected media type for the blob SHOULD respect the returned media type.
* Implementations that have an expected media type which matches the returned media type SHOULD respect the matched media type.
* Implementations that have an expected media type which does not match the returned media type SHOULD:
    * Respect the expected media type if the blob matches the expected digest.
      Implementations MAY warn about the media type mismatch.
    * Return an error if the blob does not match the expected digest (as [recommended for descriptors](descriptor.md#properties)).
    * Return an error if they do not have an expected digest.

## Compatibility Matrix

The OCI Image Specification strives to be backwards and forwards compatible when possible.
Breaking compatibility with existing systems creates a burden on users whether they are build systems, distribution systems, container engines, etc.
This section shows where the OCI Image Specification is compatible with formats external to the OCI Image and different versions of this specification.

### application/vnd.oci.image.index.v1+json

**Similar/related schema**

- [application/vnd.docker.distribution.manifest.list.v2+json](https://github.com/distribution/distribution/blob/master/docs/spec/manifest-v2-2.md#manifest-list)
  - `.annotations`: only present in OCI
  - `.[]manifests.annotations`: only present in OCI
  - `.[]manifests.urls`: only present in OCI

### application/vnd.oci.image.manifest.v1+json

**Similar/related schema**

- [application/vnd.docker.distribution.manifest.v2+json](https://github.com/distribution/distribution/blob/master/docs/spec/manifest-v2-2.md#image-manifest-field-descriptions)
  - `.annotations`: only present in OCI
  - `.config.annotations`: only present in OCI
  - `.config.urls`: only present in OCI
  - `.[]layers.annotations`: only present in OCI

### application/vnd.oci.image.layer.v1.tar+gzip

**Interchangeable and fully compatible mime-types**

- [application/vnd.docker.image.rootfs.diff.tar.gzip](https://github.com/moby/moby/blob/v20.10.8/image/spec/v1.2.md#creating-an-image-filesystem-changeset)

### application/vnd.oci.image.config.v1+json

**Similar/related schema**

- [application/vnd.docker.container.image.v1+json](https://github.com/moby/moby/blob/v20.10.8/image/spec/v1.2.md#image-json-description) (Docker Image Spec v1.2)
  - `.config.Memory`: only present in Docker, and reserved in OCI
  - `.config.MemorySwap`: only present in Docker, and reserved in OCI
  - `.config.CpuShares`: only present in Docker, and reserved in OCI
  - `.config.Healthcheck`: only present in Docker, and reserved in OCI
- [Moby/Docker](https://github.com/moby/moby)
  - `.config.ArgsEscaped`: Windows-specific Moby/Docker extension, deprecated in OCI, available for compatibility with older images.

`.config.StopSignal` and `.config.Labels` are accidentally undocumented in Docker Image Spec v1.2, but these fields are not OCI-specific concepts.

## Relations

The following figure shows how the above media types reference each other:

![](img/media-types.png)

[Descriptors](descriptor.md) are used for all references.
The image-index being a "fat manifest" references a list of image manifests per target platform. An image manifest references exactly one target configuration and possibly many layers.

[rfc1952]: https://tools.ietf.org/html/rfc1952
[rfc8478]: https://tools.ietf.org/html/rfc8478
