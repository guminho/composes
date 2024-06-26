# docker-compose to run common backend services

## usage
```bash
# common
docker-compose up -d redis mongo 

# kafka-kraft
docker-compose -f k2.yml up -d

# elasticsearch
docker-compose -f e2.yml up -d
```

## ref
- https://github.com/confluentinc/cp-all-in-one
- https://github.com/bitnami/containers/blob/main/bitnami/kafka/docker-compose.yml