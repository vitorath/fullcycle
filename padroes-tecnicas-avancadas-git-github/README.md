# Padrões e técnicas avançadas do Git e GitHub 

## Git flow

A [Atlassian](https://www.atlassian.com/br/git/tutorials/comparing-workflows/gitflow-workflow) contém um conteúdo bem explicativo de como funciona o **git flow**.

Além disso, o [gitflow-avh](https://github.com/petervanderdoes/gitflow-avh/wiki/Installation) eh uma boa ferramenta que auxilia no desenvolvimento utilizando o **git flow**, contudo, esta ferramenta tem um contra de que faz merge direto na main e/ou develop dando assim a possibilidade de criar complicações o trabalho quando estamos trabalhando com **pull requests**.

## Chave GPG
 
A **chave GPG** é utilizada para verificar os commits. Isto é, identificar de fato que você é o autor do commit e não outra pessoa se passando por você, já que é possível modificar o usuário e o email que são, normalmente, registrados nos commits.
 
### Passo a passo para criar chave GPG
 
Vamos começar listando todas as chaves sua máquina (O **key-id** está localizado na frente do tipo de criptografia, por exemplo**rsa4096/<key-id>** )
 
```bash
gpg --list-secret-key --keyid-form LONG
```
 
Posteriormente criaremos uma chave. Siga os passos solicitados para criar a chave
 
```bash
gpg --full-generate-key
```

## Criar um commit assinado

Agora iremos obter o endereço da public key para adicionar nas configurações do seus usuário do GitHub (Settings > SSH and GPG keys > New GPG key)
 
```bash
gpg --armor --export <key-id>
```
Ao finalizar a criação e configuração da chave GPG no github, é necessário identificar em sua máquina qual chave irá utilizar para isso adicionar a chave às configurações do git. Posteriormente adicione a variável de ambiente **GRG_TTY** (recomendado adicionar no arquivo de bash)
 
```bash
git config --global user.signingkey <key-id>
export GPG_TTY=$(tty)
```
 
Podemos prosseguir de duas formas:
 
1. Realizar a assinatura manualmente adicionado o -S no comando de commit
```bash
git commit -S -m '...'
```
2. Configurar o git local/global para sempre commitar com aquela chave
```bash
git config commit.gpgsign true
git config tag.gpgSign true
```

Verificamos se o commit foi assinado por meio do comando
```bash
git log --show-signature -1
```

Caso o agente do GPG não esteja ativo
```bash
vim ~/.gnupg/gpp.conf
gpcconf --launch gpg-agent
```

### Editar chave GPG

Caso queira adicionar mais um email ou usuário a sua chave execute

```bash
gpg --edit-key <key-id>
```
Para adicionar um usuario, execute o comando e preencha os requisitos

```bash
adduid
```

Caso queiramos adicionar modificar o grau de confiança do usuário

```bash
uid <idenficador>
trust # Escolha o grau de confiança
save
```