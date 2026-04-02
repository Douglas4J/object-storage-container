# 🧱 Estrutura do Container

### Arquivo de configuração do garage
- garage/garage-config/garage.toml

Chave secreta usada para autenticacao entre nodes (cluster). Deve ter 32 bytes em HEX (64 caracteres)
``` bash
  rpc_secret = "secret_hex_gerar"
```

**comando para gerar o HEX:** <br>
``` bash
powershell -Command "[guid]::NewGuid().ToString('N') + [guid]::NewGuid().ToString('N')"
```

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

------------------------------------------------------------------------

# 📦 Setup do Garage

## 🔰 1. Inicializar o cluster

#### 1.1 Pegar o Node ID

``` bash
docker exec -it garage /garage status
```

#### 1.2 Registrar o node no cluster (assign)

``` bash
docker exec -it garage /garage layout assign <NODE_ID> -z dc1 -c 1024
```

-   `-z dc1` → zona (qualquer nome)
-   `-c 1024` → capacidade (valor simbólico em dev)

#### 1.3 Verificar layout

``` bash
docker exec -it garage /garage layout show
```

#### 1.4 Aplicar layout

``` bash
docker exec -it garage /garage layout apply --version 1
```

#### 1.5 Validar

``` bash
docker exec -it garage /garage layout show
```

------------------------------------------------------------------------

## 🔑 2. Criar credenciais (Key)

#### 2.1 Criar key

``` bash
docker exec -it garage /garage key create
```

⚠️ Salve a Secret Key!

#### 2.2 Listar keys

``` bash
docker exec -it garage /garage key list
```

#### 2.3 Ativar a key

``` bash
docker exec -it garage /garage key allow <KEY_ID>
```

------------------------------------------------------------------------

## 🪣 3. Criar Bucket

``` bash
docker exec -it garage /garage bucket create arquivos-bucket
```

------------------------------------------------------------------------

## 🔗 4. Associar key ao bucket

``` bash
docker exec -it garage /garage bucket allow arquivos-bucket --key <KEY_ID>
```

------------------------------------------------------------------------

## ✅ 5. Validar configuração

``` bash
docker exec -it garage /garage bucket info arquivos-bucket
```

------------------------------------------------------------------------

## 🧠 Modelo

-   Cluster → infraestrutura\
-   Key → autenticação\
-   Bucket → armazenamento\
-   Bucket allow → autorização

------------------------------------------------------------------------

## 📦 Configuração da API

``` properties
aws.s3.endpoint=http://localhost:3900
aws.s3.access-key=<KEY_ID>
aws.s3.secret-key=<SECRET_KEY>
aws.s3.bucket=arquivos-bucket
aws.s3.region=us-east-1
```

### Pegar Credenciais
Access Key: aws.s3.access-key=GKd2c5851a1c3b4341e0718284
```
docker exec -it garage /garage key list
```

Secret Key: aws.s3.secret-key=135ed952e0fa...
```
docker exec -it garage /garage key create
```

Bucket: aws.s3.bucket=arquivos-bucket
```
docker exec -it garage /garage bucket create arquivos-bucket
```
