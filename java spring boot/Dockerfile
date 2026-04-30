services:

  dev:
    image: maven:3.9-eclipse-temurin-21
    volumes:
      - ..:/workspace
    command: sleep infinity
    depends_on:
      mysql:
        condition: service_healthy
      
    networks:
      - condosmart-net

  mysql:
    image: mysql:8
    networks:
      - condosmart-net
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: condosmart
    ports:
      - "3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "root", "-proot"]
      interval: 5s    # Verifica a cada 5 segundos
      timeout: 5s     # Se demorar 5s para responder, considera falha
      retries: 10     # Tenta 10 vezes antes de desistir
      start_period: 20s # Aguarda 20s antes de começar a testar (tempo de boot inicial)

volumes:
  mysql-data:

networks:
  condosmart-net:
    driver: bridge
