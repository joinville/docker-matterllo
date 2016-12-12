# docker-matterllo

Docker image for Matterllo (Mattermost + Trello integration).

Note: For this image, I have used the latest matterllo version ([commit from 2016-07-08](https://github.com/Lujeni/matterllo/tree/85ef4d2fb654499079a33326166146dbcd134f37)) before the project decided to use a database instead of the good old YAML file.

## Build

```shell
# Behind corporate proxy
docker build -t joinville/matterllo --build-arg "https_proxy=$http_proxy" .

# No proxy
docker build -t joinville/matterllo .
```

## Run

Create a `config.yaml` file ([documentation](https://github.com/Lujeni/matterllo/tree/85ef4d2fb654499079a33326166146dbcd134f37/docs)) on the current folder with your matterllo settings and run:

```shell
docker run -d --name matterllo \
    -p 8080:8080 \
    -v $PWD/config.yaml:/usr/src/app/config.yaml \
    joinville/matterllo
```

Matterllo is now listening on port 8080 for Trello notification requests.

## Webhooks setup

To create the webhooks on the Trello side, you need to run a command inside the running container, because Trello will send back a request to validate the setup.

First make sure this container is running and listening on the URL configured in `callback_url` at the `config.yaml` file.

Now run the following command to wipe any existing webhooks in your Trello account and create the new ones:

```shell
docker exec matterllo python scripts/helper.py --init
```

Rerun this command everytime you update your config.
