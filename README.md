# Estrutura do Container

#### garage/garage-config/garage.toml (arquivo de configuração do garage)

#
  
#### docker-compose.yml

  - ports:
    - "3900:3900" (Porta da API S3 (onde a aplicacao vai conectar))
    - "3901:3901" (Porta RPC (comunicacao interna entre nodes do Garage))

  - volumes:
    - ./garage-data:/var/lib/garage (Persistencia dos dados (arquivos e metadados))
    - ./garage-config/garage.toml:/etc/garage.toml (Arquivo de configuracao do Garage)

#

#### ao subir o container se não conter a pasta garage-data, ele irá criar automaticamente 
  - /garage-data
    - /data
    - /meta
