# Deployment

Two main ways to deploy and run Data Caterer:

- Docker
- Helm

## Docker

To package up your class along with the Data Caterer base image, you can follow
the [Dockerfile that is created for you here](https://github.com/pflooky/data-caterer-example/blob/main/Dockerfile).

Then you can run the following:

```shell
./gradlew clean build
docker build -t <my_image_name>:<my_image_tag> .
```

## Helm

[Link to sample helm on GitHub here](https://github.com/pflooky/data-caterer-example/tree/main/helm/data-caterer)

Update
the [configuration](https://github.com/pflooky/data-caterer-example/blob/main/helm/data-caterer/templates/configuration.yaml)
to your own data connections and configuration or own image created from above.

```shell
git clone git@github.com:pflooky/data-caterer-example.git
helm install data-caterer ./data-caterer-example/helm/data-caterer
```
