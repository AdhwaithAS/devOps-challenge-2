# Full Stack Flask CouchDB - project generator

[![Build Status](https://travis-ci.org/tiangolo/full-stack-flask-couchdb.svg?branch=master)](https://travis-ci.org/tiangolo/full-stack-flask-couchdb)

Generate a backend and frontend stack using Python, including interactive API documentation.

[![Screenshot](screenshot.png)](https://github.com/tiangolo/full-stack-flask-couchdb)

## Features

* Full Docker integration (Docker based)
* Docker Swarm Mode deployment
* Docker Compose integration and optimization for local development
* Production ready Python web server using Nginx and uWSGI
* Python Flask backend with:
  * Flask-apispec: Swagger live documentation generation
  * Marshmallow: model and data serialization (convert model objects to JSON)
  * Webargs: parse, validate and document inputs to the endpoint / route
  * Secure password hashing by default
  * JWT token authentication
  * Basic starting functionality for users and roles (modify and remove as you need)
  * CORS (Cross Origin Resource Sharing)
* Celery worker that can import and use code from the rest of the backend selectively (you don't have to install the complete app in each worker)
* NoSQL CouchDB database that supports direct synchronization for offline-first applications
* REST backend tests based on Pytest, integrated with Docker, so you can test the full API interaction, independent on the database. As it runs in Docker, it can build a new data store from scratch each time (so you can use ElasticSearch, MongoDB, or whatever you want, and just test that the API works).
* Easy Python integration with Jupyter Kernels for remote or in-Docker development with extensions like Atom Hydrogen or Visual Studio Code Jupyter
* Angular frontend with:
  * Docker server based on Nginx
  * Docker multi-stage building, so you don't need to save or commit compiled code
  * Docker building integrated tests with Chrome Headless
* Swagger-UI for live interactive documentation
* Flower for Celery jobs monitoring
* Load balancing between frontend and backend with Traefik, so you can have both under the same domain, separated by path, but served by different containers
* Traefik integration, including Let's Encrypt HTTPS certificates automatic generation
* GitLab CI (continuous integration), including frontend and backend testing

## How to use it

Go to the directoy where you want to create your project and run:

```bash
pip install cookiecutter
cookiecutter https://github.com/tiangolo/full-stack-flask-couchdb
```

### Generate passwords

You will be asked to provide passwords and secret keys for several components. Open another terminal and run:

```bash
openssl rand -hex 32
# Outputs something like: 99d3b1f01aa639e4a76f4fc281fc834747a543720ba4c8a8648ba755aef9be7f
```

Copy the contents and use that as password / secret key. And run that again to generate another secure key.


### Input variables

The generator (cookiecutter) will ask you for some data, you might want to have at hand before generating the project.

The input variables, with their default values (some auto generated) are:

* `project_name`: The name of the project
* `project_slug`: The development friendly name of the project. By default, based on the project name
* `domain_main`: The domain in where to deploy the project for production (from the branch `production`), used by the load balancer, backend, etc. By default, based on the project slug.
* `domain_staging`: The domain in where to deploy while staging (before production) (from the branch `master`). By default, based on the main domain.
* `domain_branch`: The domain in where to deploy the project while on another branch, probably a feature branch. By default, based on the main domain.
* `domain_dev`: The domain to use while developing. It won't be deployed, but you should use it by modifying your local `hosts` file.

* `docker_swarm_stack_name_main`: The name of the stack while deploying to Docker in Swarm mode for production. By default, based on the domain.
* `docker_swarm_stack_name_staging`: The name of the stack while deploying to Docker in Swarm mode for staging. By default, based on the domain.
* `docker_swarm_stack_name_branch`: The name of the stack while deploying to Docker in Swarm mode for feature branches. By default, based on the domain.
* `secret_key`: Backend server secret key. Use the method above to generate it.
* `first_superuser`: The first superuser generated, with it you will be able to create more users, etc. By default, based on the domain.
* `first_superuser_password`: First superuser password. Use the method above to generate it.
 
* `couchdb_user`: CouchDB main user to be used by the application (code). By default `admin`.
* `couchdb_password`: Password of the main user, for the backend code. Generate it with the method above.
* `couchdb_cors_origins`: List of origins that the database should allow to talk to directly.
 
* `traefik_constraint_tag`: The tag to be used by the internal Traefik load balancer (for example, to divide requests between backend and frontend) for production. Used to separate this stack from any other stack you might have. This should identify each stack in each environment (production, staging, etc).
* `traefik_constraint_tag_staging`: The Traefik tag to be used while on staging. 
* `traefik_constraint_tag_branch`: The Traefik tag to be used while on a feature branch.

* `traefik_public_network`: This assumes you have another separate publicly facing Traefik at the server / cluster level. This is the network that main Traefik lives in.
* `traefik_public_constraint_tag`: The tag that should be used by stack services that should communicate with the public.

* `flower_auth`: Basic HTTP authentication for flower, in the form`user:password`. By default: "`root:changethis`".

* `sentry_dsn`: Key URL (DSN) of Sentry, for live error reporting. If you are not using it yet, you should, is open source. E.g.: `https://1234abcd:5678ef@sentry.example.com/30`.

* `docker_image_prefix`: Prefix to use for Docker image names. If you are using GitLab Docker registry it would be based on your code repository. E.g.: `git.example.com/development-team/my-awesome-project/`.
* `docker_image_backend`: Docker image name for the backend. By default, it will be based on your Docker image prefix, e.g.: `git.example.com/development-team/my-awesome-project/backend`. And depending on your environment, a different tag will be appended ( `prod`, `stag`, `branch` ). So, the final image names used will be like: `git.example.com/development-team/my-awesome-project/backend:prod`.
* `docker_image_celeryworker`: Docker image for the celery worker. By default, based on your Docker image prefix.
* `docker_image_frontend`: Docker image for the frontend. By default, based on your Docker image prefix.

## How to deploy

This stack can be adjusted and used with several deployment options that are compatible with Docker Compose, but it is designed to be used in a cluster controlled with pure Docker in Swarm Mode with a Traefik main load balancer proxy.

Read the [**Guide to deploy a Docker Swarm Mode Cluster**](https://github.com/tiangolo/full-stack/blob/master/docker-swarm-cluster-deploy.md).

## More details

After using this generator, the new project will contain an extensive `README.md` with instructions for development, deployment, etc. You can read [the project `README.md` template here](./{{cookiecutter.project_slug}}/README.md).

## License

This project is licensed under the terms of the MIT license.
