# Containerize the application

## Using Docker

Run the the command below to build a docker image

```
docker build . -t jcon-workshop-app -f Dockerfile.jvm
```

Run the container locally

```
docker run -p 9080:9080 -e HUGGING_FACE_API_KEY=$HUGGING_FACE_API_KEY jcon-workshop-app
```

Use the Web Browser, Open the App

[http://localhost:9080](http://localhost:9080)

Terminate the docker session. Now create an SBOM for the image

```
docker scout sbom --format list jcon-workshop-app
```

## Using Buildpacks 

Follow these instructions to install the pack CLI locally:

[Pack CLI Installation Instructions](https://buildpacks.io/docs/for-platform-operators/how-to/integrate-ci/pack/)

Install the latest version of buildpacks in the Cloud CLI

```
(curl -sSL "https://github.com/buildpacks/pack/releases/download/v0.33.0-rc1/pack-v0.33.0-rc1-linux.tgz" | sudo tar -C /usr/local/bin/ --no-same-owner -xzv pack)
```

Set the default builder

```
pack config default-builder paketobuildpacks/builder-jammy-base
```

Check out the project.toml file in the root dir. It should contain the following config:

```
[[build.env]]
    name = "BP_JAVA_APP_SERVER"
    value = "liberty"

[[build.env]]
    name = "BP_MAVEN_BUILT_ARTIFACT"
    value ="target/*.[ejw]ar src/main/liberty/config/*"

[[build.buildpacks]]
  uri = "docker://gcr.io/paketo-buildpacks/eclipse-openj9"

[[build.buildpacks]]
  uri = "docker://gcr.io/paketo-buildpacks/java"
```

Now build your OCI image

```
pack build jcon-workshop-app
```

We can use buildpacks to generate a Software Bill of Materials (SBOM) using the following command

```
pack sbom download jcon-workshop-app
```

