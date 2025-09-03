Dockerized gitlabce:

it contained:
redis,postgres,gitlab 

Create named volumes:
```bash
mkdir -p /Projects/gitlab/volumes/gitlab/{config,logs,data}
mkdir -p /Projects/gitlab/volumes/postgres/data
mkdir -p /Projects/gitlab/volumes/redis/data

# Set proper ownership (WSL may need different user)
chown -R 1000:1000 /Projects/gitlab/volumes/gitlab
chown -R 999:999 /Projects/gitlab/volumes/postgres
chown -R 999:999 /Projects/gitlab/volumes/redis

# Create volumes with bind mounts
docker volume create --driver local \
  --opt type=none \
  --opt device=/Projects/gitlab/volumes/config \
  --opt o=bind \
  gitlab-config

docker volume create --driver local \
  --opt type=none \
  --opt device=/Projects/gitlab/volumes/logs \
  --opt o=bind \
  gitlab-logs

docker volume create --driver local \
  --opt type=none \
  --opt device=/Projects/gitlab/volumes/data \
  --opt o=bind \
  gitlab-data

docker volume create --driver local \
  --opt type=none \
  --opt device=/Projects/gitlab/volumes/postgres/data \
  --opt o=bind \
  postgres-data

docker volume create --driver local \
  --opt type=none \
  --opt device=/Projects/gitlab/volumes/redis/data \
  --opt o=bind \
  redis-data

```

Compose file:

```bash
ram@gitlab:~$ cat /Projects/gitlab/compose.yml
services:
  postgres:
    image: postgres:17.6-alpine3.22
    restart: always
    env_file: .env
    environment:
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DB=${POSTGRES_DB}
    volumes:
      - postgres-data:/var/lib/postgresql/data
    networks:
      - gitlab-network

  redis:
    image: redis:8.2.0-alpine
    restart: always
    command: redis-server --requirepass ${REDIS_PASSWORD}
    env_file: .env
    volumes:
      - redis-data:/data
    networks:
      - gitlab-network

  gitlab:
    image: gitlab/gitlab-ce:18.3.1-ce.0
    restart: always
    hostname: ${GITLAB_HOST}
    env_file: .env
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://${GITLAB_HOST}'
        gitlab_rails['db_adapter'] = 'postgresql'
        gitlab_rails['db_encoding'] = 'unicode'
        gitlab_rails['db_host'] = 'postgres'
        gitlab_rails['db_database'] = '${POSTGRES_DB}'
        gitlab_rails['db_username'] = '${POSTGRES_USER}'
        gitlab_rails['db_password'] = '${POSTGRES_PASSWORD}'
        gitlab_rails['redis_host'] = 'redis'
        gitlab_rails['redis_password'] = '${REDIS_PASSWORD}'
        gitlab_rails['initial_root_password'] = '${GITLAB_ROOT_PASSWORD}'
    ports:
      - "8929:80"
      - "443:443"
      - "${GITLAB_SSH_PORT}:22"
    volumes:
      - gitlab-config:/etc/gitlab
      - gitlab-logs:/var/log/gitlab
      - gitlab-data:/var/opt/gitlab
    networks:
      - gitlab-network
    depends_on:
      - postgres
      - redis

volumes:
  postgres-data:
    external: true
  redis-data:
    external: true
  gitlab-config:
    external: true
  gitlab-logs:
    external: true
  gitlab-data:
    external: true

networks:
  gitlab-network:
    driver: bridge
```


sample env:

```bash
# PostgreSQL configuration
POSTGRES_USER=gitlab
POSTGRES_PASSWORD=eHNo4SveNZCx
POSTGRES_DB=gitlab

# Redis configuration
REDIS_PASSWORD=bqn9C5DsJzzd

# GitLab configuration
GITLAB_HOST=gitlab.local
GITLAB_SSH_PORT=2222
GITLAB_ROOT_PASSWORD=kX1PP9heAXtE_T

```
