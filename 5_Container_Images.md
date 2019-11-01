# Container Images

## What's in an image (And What isn't)

- App binaries and dependencies
- Metadata about the image data and how to run the image
- Official definition: "An image is an ordered collection of root file system
changes and the corresponding execution parameters for use within a container
runtime"
- Not a complete OS, No kernel, No kernel modules (drivers, etc..)

## Image Layers

Image Layer is actually transparent completely when you are using Docker

- `docker image history nginx:latest`

When creating an image we start with a layer called stratch, every layer
has his own unike SHA id. Every changes to an image is a layer.
All the layer together forms the image.

If we create a new image that has some layer in common with the old one
we don't need to re-download this layer (**image layers chaching**)

## Useful Commands

- `docker image history [image]` - Show layer of changes made in an image
- `docker image inspect [image]` - Show details about the image in JSON


# Image Tagging

A tag is a pointer to a specific image commit

## Useful Commands

`docker image tag SOURCE_IMG[:TAG] TARGET_IMAGE[:TAG]`