It is a platform that lets u create container.
There are 2 components
Docker container and docker images
Container = app + dependencies
Images =

Irrespective of the operating system , these docker container works on different machines. Replicating the development environment.

Comands
docker pull image_name
docker start image_name

docker ps - shows all the currently running docker containenr
docker stop id 
docker run id

## 🚪 What Is Port Binding?

When you run a Docker container, it runs in an isolated network. To access services (like PostgreSQL) running **inside** the container **from your local machine (host)**, you must **bind container ports to host ports**.

---

### 🔄 Example:


`docker run -p 5432:5432 ...`

This means:

|Host (Your machine)|Container (PostgreSQL)|
|---|---|
|`localhost:5432`|`5432` (default Postgres port)|

So when you or your app connect to `localhost:5432`, Docker forwards that traffic to port `5432` inside the container — where PostgreSQL is running.

---

## 🧠 General Syntax



`docker run -p <host_port>:<container_port> ...`

You can even use different ports:


`docker run -p 9999:5432 ...`

- You would then connect from the host to `localhost:9999`
    
- Inside the container, Postgres still runs on `5432`
    

---

## ✅ Best Practice for PostgreSQL

bash

Copy code

`docker run --name my-postgres \   -e POSTGRES_PASSWORD=yourpassword \   -p 5432:5432 \   -d postgres`

### Meaning:

- `5432` on your machine (host) is connected to `5432` in the container.
    
- You or your apps can access PostgreSQL via `localhost:5432`.
    

---

## 🧪 Test It Works

Run:

bash

Copy code

`psql -h localhost -p 5432 -U postgres`

Then enter the password you set (e.g. `yourpassword`).

// Some other commands

docker run -d p:port:port

docker images



If we want to give the each image a name, then we can do that:
docker run -d -p:host:port_of_app --name actual_name redis

docker exec:

docker exec -it 095aa8644ebe /bin/bash








