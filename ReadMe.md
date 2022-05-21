## FastAPI application template for docker.

![fastapi-docker](cover.png)

### Requirements

`Python 3.6+`

### Create a folder `app/` inside create following files.

* Create folder **fastapi-docker-workflow** and `cd fastapi-docker-workflow`

* Issue following commands
    - `pip install fastapi`
    - `pip install "uvicorn[standard]"`

* `main.py`

```python
from typing import Union

from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def read_root():
    return {"Hello": "World"}
```

* Create a `requirement.txt` in the root folder.
```text
fastapi
uvicorn
```

* start the server with `uvicorn app.main:app --reload`

### Dockerfile

```Dockerfile
# Docker base image.
FROM python:3.9

# Working directory inside the container.
WORKDIR /code

# Copy the requirements.txt inside the image.
COPY ./requirements.txt /code/requirements.txt

# Install project dependencies.
RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt

# Copy project files to the image.
COPY ./app /code/app

# Run the contianer - force port
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "80"]

```

### Workflow (GitHub Actions)

```yaml
name: Fast API docker build push
on:
  push:
    branches: [ master ]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Build the FastAPI Docker image
      run: docker build -t '${{secrets.DOCKER_LOGIN}}'/fastapi-docker-workflow:'${{github.sha}}' .
      
    - name: Login to docker
      run: docker login --username '${{secrets.DOCKER_LOGIN}}' --password '${{secrets.DOCKER_PASSWORD}}'

    - name: Push the docker image
      run: docker push '${{secrets.DOCKER_LOGIN}}'/fastapi-docker-workflow:'${{github.sha}}'
```
⚠️ Add the `DOCKER_LOGIN` and `DOCKER_PASSWORD` to the github repository secrets.

