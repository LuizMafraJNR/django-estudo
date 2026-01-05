# Iniciando Django

## Comandos Básicos

### Listar todos os comandos
```bash
django-admin --help
```

### Criar um projeto
```bash
django-admin startproject nome_do_projeto .
```
O ponto é para criar o projeto na mesma pasta atual.

### Iniciar o servidor
```bash
python manage.py runserver
```

Caso tenha migrações pendentes ao utilizar esse comando, utilize:
```bash
python manage.py migrate
```

---

# Aplicação de Enquetes (`polls`)

## Visão Geral

Agora que seu ambiente – um "projeto" – está configurado, você está pronto para começar o trabalho. Cada aplicação que você escreve no Django consiste de um pacote Python que segue uma certa convenção. O Django vem com um utilitário que gera automaticamente a estrutura básica de diretório de uma aplicação, então você pode se concentrar apenas em escrever código em vez de ficar criando diretórios.

## Projetos versus Aplicações

**Um `app`** é uma aplicação web que executa algo — por exemplo:
- Um sistema de blog
- Uma base de dados de registros públicos
- Uma pequena aplicação de enquete

**Um `projeto`** é uma coleção de configurações e `app`s para um website específico. Um projeto pode conter múltiplos `app`s. Um `app` pode estar em múltiplos projetos.

## Criando a Aplicação

Suas apps podem ficar em qualquer lugar no `PYTHONPATH`. Neste tutorial, vamos criar a app de enquetes dentro da pasta do projeto.

Para criar sua aplicação, certifique-se de que esteja no mesmo diretório que `manage.py` e execute o seguinte comando:

```bash
python manage.py startapp polls
```

Isso criará a estrutura básica da aplicação `polls` com os arquivos necessários para começar o desenvolvimento.

