# To build one auto-instrumentation image for Ruby, please:
# - Ensure the packages are installed in the `/instrumentation` directory. This is required as when instrumenting the pod,
#   one init container will be created to copy all the content in `/instrumentation` directory to your app's container.
# - Grant the necessary access to `/instrumentation` directory. `chmod -R go+r /instrumentation`

FROM ruby:latest@sha256:3acb1a3f2351748d7ffd4408b179adb96682a93a4a76f30abcb9eb001d95443f AS build
WORKDIR /operator-build

ARG AGENT_VERSION

# Install gems from Gemfile into the `workspace` directory
ADD Gemfile .
RUN BUNDLE_PATH=workspace bundle install

FROM busybox:latest@sha256:9ae97d36d26566ff84e8893c64a6dc4fe8ca6d1144bf5b87b2b85a32def253c7

# Copy build artifacts from previous
COPY --from=build /operator-build /operator-build
WORKDIR /operator-build

# Shell command to search inside the `workspace/ruby` directory for a directory that starts with `newrelic_rpm`.
# Example of a found result: workspace/ruby/3.2.0/gems/newrelic_rpm-9.9.0
# Copy the result into a new directory called `/instrumentation`.
RUN sh -c "find 'workspace/ruby/' -type d -name newrelic_rpm\\* -exec cp -r '{}' /instrumentation \\;"

# Adjust permissions of `instrumentation` directory to readable by everyone
RUN chmod -R go+r /instrumentation

# Clean up build artifacts
RUN rm -rf /operator-build
