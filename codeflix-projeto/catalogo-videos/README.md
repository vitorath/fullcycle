# Catalogo de vídeos

## Objetivo

Desenvolver catalogo administrativo

## Glossário / Linhuagem Ubíqua

- Catalog = Catálogo de vídeos
- Category = Define o tipo de categorização de um video (ex: fil,e. documentario, infantil)
- Genre = Define o gênero do video (ex: terrror, comedia, acao)
- Cast members = atores, diretores e outr5as pessoas responsaveis pela cracao do video
- Featured Video = Video em destaque na plataforma
- My Videos = Lista de viodeos favoritos indicados explicitamente pelo usuario. Normalmente essa funcao tem o objetivo do usuario assis ao video depois
- Subscription = Assinatura que o cliente realiza para acessar a plataforma
- Plan = Plano que o usuario podera contratar em uma assinatura para acessar a plataforma
- Client = Cliente que contrata o acesso na plataforma de videos
- User = Qualquer pessoa que tenha acesso ao sistema, independete de permissao ou funcao

## Modelagem estrategica

Dominio Principal - Catalog (yellow)
Dominio Generico - Payment
Dominio Generico - Subscription
Dominio Auxiliar - Auth (red)
Dominio Generico - My List

## Context Map

Auth tem camada anti-corrupcao
---- Nucleo Compatilhado ----
Catalog --U--<cliente-fornecedor>--D-- Subscription
Catalog --U--<cliente-fornecedor>--D-- My List
Subscription --D--<conformista>--U-- Payment

Paymente tem uma camada de anti-corrupcao

## Arquitetura contextual
