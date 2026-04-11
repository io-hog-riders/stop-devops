# stop-devops

## Dependencies
```
docker docker-compose
```
see: [Docker Install](https://docs.docker.com/engine/install/)

## Deployment
To deploy the project:
1. Run
```bash
git clone https://github.com/io-hog-riders/stop-front
git clone https://github.com/io-hog-riders/stop-back
```
2. Fill the values in `.env.example`,
3. Rename the file to `.env`
4. Run:
```bash
docker compose up --build
```
