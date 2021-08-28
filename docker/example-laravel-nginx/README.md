# Laravel

## Objetivo

Subir um ambiente de produção utilizando o Laravel.

## Problemas

Como o Laravel é um framework de desenvolvimento, muito do que é utilizado em desenvolvimento torna-se um peso para o ambiente de produção, além disso, em alguns contexto pode até gerar falhas de segurança. Além disso, garantir, ao menos em um estágio inical, a integridade da aplicação caso tenha mutiplos acessos simultâneos.

## Solução

Ao criar o Dockerfile da aplicação é necessário otimizar utilizando **Multistage Building**, com o intuito de pré compilar a aplicação em um estágio e depois criar de fato a imagem de produção em um próximo estágio. 

Para a estabilidade do serviço pode ser feita utilizando um proxy reverso, no caso o **nginx**, já que uma das finalidade de um proxy reverso é o banceamento de cargas (load balancer) podemos subir mais de uma instância do mesmo serviço.

## Executando

Para executar o ambiente basta utilizar o comando:

```bash
docker-compose up
```

Posteriormente acesse (template padrão do Laravel)[http://localhost:8080/] para ver o resultado 

