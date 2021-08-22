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

## Boas práticas
 
1. Adicionar proteções as branches **main** e **develop**, como por exemplo push direto e/ou exigir que os commits sejam assinados. Além disso, é possível bloquear commits forcados.
2. Trabalhar utilizando **pull request**. Isto e, para realizar merges nas branches master e develop somente por **pull request** (tambem e possivel bloquear as prancha para receber merges somente desta forma)
3. Criar um template de **pull request**, similar ao do (embeddedartistry)[https://embeddedartistry.com/blog/2017/08/11/a-github-pull-request-template-for-the-ccc-process/]. Para adicionar o template ao projeto criar um arquivo **.github/PULL_REQUEST_TEMPLATE.md** (funciona somente com o github)
4. Adicionar os code owners sempre como revisores de códigos específicos. Criando um arquivos .**github/CODEOWNERS** e adicionando:
>  *.js @usuario2
> 
>  .github/ @usuario1
> 
>  *.go @usuairo1 @grupo-x @grupo-y
5. Utilizar [semantical version](https://semver.org/lang/pt-BR/) para identificar as versoes de sua aplicacoes. Por exemplo:
> MAJOR.MINOR.PATH = 2.1.4
> 
> MAJOR - API PÚBLICA
> 
> MINOR - Adicionados funcionalidades, mas compatível com a API
> 
> PATCH - Bugs, ajustes
> PS. MAJOR = 0 significa API Instável. Pode mudar a qualquer momento.
> 
> 2.1.4-alpha < 2.1.4 "alpha" (pode ser qualquer nome) para indicar uma mesma versao mais estavel que a release
6. Utilizar [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/) e para nos auxiliar podemos utilizar [commitlint](https://commitlint.js.org/#/) configurado no projeto para que os usuário não realizar commits fora do padrão do **conventional commits**, [commitsat](https://commitsar.aevea.ee/) para verificar no servidor se todos os commits estão seguindo o padrão do **conventional commits** e [commitizen](https://github.com/commitizen/cz-cli) ccom o intuito de facilitar a criação de commits no padrão de **conventional commits** pelo terminal
```bash
git cz # Para executar o commitizen
```
