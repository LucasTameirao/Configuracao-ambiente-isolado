version: '3.9'

services:
  dev:
    container_name: dev
    image: maven:3.9-eclipse-temurin-21
    volumes:
      - ..:/workspace
    working_dir: /workspace
    command: sleep infinity
    depends_on:
      mysql:
        condition: service_healthy
    networks:
      - condosmart-net

  mysql:
    container_name: mysql
    image: mysql:8
    ports:
      - "3307:3306"
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: condosmart
    volumes:
      - mysql-data:/var/lib/mysql
    networks:
      - condosmart-net
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-uroot", "-proot"]
      interval: 5s
      timeout: 5s
      retries: 10
      start_period: 20s

volumes:
  mysql-data:

networks:
  condosmart-net:
    driver: bridge
