FROM golang:1.10.3-alpine3.8 as build

# copy operator-sdk executable
COPY operator/operator-sdk /usr/bin

# Install tools required for project
# Run `docker build --no-cache .` to update dependencies
RUN apk add --no-cache git
RUN go get github.com/golang/dep/cmd/dep

# List project dependencies with Gopkg.toml and Gopkg.lock
# These layers are only re-built when Gopkg files are updated
COPY operator/Gopkg.lock operator/Gopkg.toml /go/src/project/
WORKDIR /go/src/project/
# Install library dependencies
RUN dep ensure -vendor-only

# Copy the entire project and build it
# This layer is rebuilt when a file changes in the project directory
COPY operator /go/src/project/
CMD ["operator-sdk", "test", "-t", "./test/e2e", "-g", "deploy/global-init.yaml", "-n", "deploy/namespaced-init.yaml"]

# This results in a single layer image
FROM scratch
COPY --from=build /bin/project /bin/project
ENTRYPOINT ["/bin/project"]
CMD ["--help"]
