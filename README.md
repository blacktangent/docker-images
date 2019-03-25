# Docker Images

To push a new docker image:

```
# Change the directory and version tag name accordingly

docker build -t blacktangent/circleci-ruby:2.6.2-stretch-pgclient11.1-node10.15.1-browsers circleci-ruby/2.6.2-stretch-pgclient11.1-node10.15.1-browsers/

docker push blacktangent/circleci-ruby:2.6.2-stretch-pgclient11.1-node10.15.1-browsers
```

## TODO 

- Add a docker build and release process
