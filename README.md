# Docker Images

To push a new docker image:

```
# Change the directory and version tag name accordingly

docker build -t blacktangent/circleci-ruby:2.5.1-stretch-pgclient10.5-node10.9-browsers circleci-ruby/2.5.1-stretch-pgclient10.5-node10.9-browsers/

docker push blacktangent/circleci-ruby:2.5.1-stretch-pgclient10.5-node10.9-browsers
```

## TODO 

- Add a docker build and release process
