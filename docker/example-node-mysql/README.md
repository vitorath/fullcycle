# Node Js + MySQL 

## Objetivo

Criar um ambiente de desenvolvimento com Node e MySQL.

## Problema

Ao criar um docker-compose com o banco de dados junto à aplicação nem sempre o banco é instânciado antes da Node.

## Solução

Para solucionar este problema é necesso algum tipo de aplicação intermediaria, no caso o **dockerize**, para retardar o tempo do Node e instância-lo após o MySQL estiver pronto. Além disso, utilizar o **depends_on** como um auxiliador do **dockerize**.

## Executando

Para executar o ambiente basta utilizar o comando:

```bash
docker-compose up
```