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

---

# Escrevendo sua primeira aplicação Django, parte 2

## Visão Geral

Este tutorial inicia onde o Tutorial 1 termina. Vamos configurar uma base de dados, criar seu primeiro model e obter uma rápida introdução ao site de administração do Django.

## Configuração do Banco de Dados

Abra o arquivo `projeto/settings.py`. É um módulo normal do Python com variáveis de módulo representando as configurações do Django.

Por padrão, a configuração `DATABASES` usa **SQLite**. Se você é novo em bancos de dados, esta é a escolha mais fácil. SQLite é incluído no Python, então você não precisará instalar nada mais para suportar seu banco de dados.

> **Quando começar um projeto real**, você pode querer usar um banco de dados mais escalável como **PostgreSQL**, para evitar dores de cabeça com troca de banco de dados mais tarde.

### Configurações Importantes

Enquanto estiver editando `projeto/settings.py`:

1. **Mude `TIME_ZONE`** para o seu fuso horário
2. **Observe a configuração `INSTALLED_APPS`** na parte superior do arquivo

A configuração `INSTALLED_APPS` contém os nomes de todas as aplicações Django ativas para essa instância.

### Aplicações Padrão Incluídas

Por padrão, `INSTALLED_APPS` contém as seguintes aplicações:

- **`django.contrib.admin`** – O site de administração
- **`django.contrib.auth`** – Um sistema de autenticação
- **`django.contrib.contenttypes`** – Um framework para tipos de conteúdo
- **`django.contrib.sessions`** – Um framework de sessão
- **`django.contrib.messages`** – Um framework de envio de mensagem
- **`django.contrib.staticfiles`** – Um framework para gerenciamento de arquivos estáticos

### Criando as Tabelas do Banco de Dados

Algumas dessas aplicações fazem uso de pelo menos uma tabela no banco de dados. Para criar as tabelas necessárias, execute:

```bash
python manage.py migrate
```

O comando `migrate` analisa a configuração `INSTALLED_APPS` e cria as tabelas de banco de dados necessárias de acordo com as configurações em `projeto/settings.py`.

## Criando Modelos

Agora vamos definir seus modelos — essencialmente, o layout do banco de dados com metadados adicionais.

### Filosofia dos Modelos

> A model é a única e definitiva fonte de informação sobre seus dados. Ela contém os campos essenciais e comportamentos dos dados que você está armazenando. O Django segue o **Princípio DRY** (Don't Repeat Yourself).

As migrações são inteiramente derivadas do seu arquivo de modelos — são essencialmente apenas um histórico que o Django pode avançar para atualizar o esquema do banco de dados.

### Modelos da Aplicação de Enquetes

Em nossa aplicação de enquete, vamos criar dois modelos:

- **`Question`** – tem uma pergunta e uma data de publicação
- **`Choice`** – tem o texto da escolha e um totalizador de votos; está associada a uma `Question`

Edite o arquivo `polls/models.py`:

```python
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

### Explicação dos Modelos

- Cada modelo é uma classe derivada de `django.db.models.Model`
- Cada atributo de classe representa um campo no banco de dados
- `CharField` é para campos de texto com limite de caracteres
- `DateTimeField` é para data e hora
- `ForeignKey` define um relacionamento muitos-para-um com `Question`
- O argumento `on_delete=models.CASCADE` garante que quando uma pergunta é deletada, suas escolhas também sejam

## Ativando Modelos

Para que o Django reconheça seus modelos, você precisa adicionar a aplicação `polls` à configuração `INSTALLED_APPS`.

Edite `projeto/settings.py`:

```python
INSTALLED_APPS = [
    "polls.apps.PollsConfig",
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
]
```

### Criando Migrações

Execute o seguinte comando:

```bash
python manage.py makemigrations polls
```

Você verá algo como:

```
Migrations for 'polls':
  polls/migrations/0001_initial.py
    + Create model Question
    + Create model Choice
```

As migrações são arquivos que armazenam as alterações nos seus modelos.

### Visualizando o SQL

Para ver o SQL que será executado, use:

```bash
python manage.py sqlmigrate polls 0001
```
### Validando Modelos

Se você tiver interesse, você pode rodar 
```bash
python manage.py check
```
Ele checa por problemas no seu projeto sem criar migrations ou tocar seu banco de dados.

### Aplicando as Migrações

Agora aplique as migrações ao banco de dados:

```bash
python manage.py migrate
```

Você verá:

```
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying polls.0001_initial... OK
```

## Brincando com a API

Para interagir com seus modelos via Python, use o shell:

```bash
python manage.py shell
```

### Exemplos Básicos

```python
# Nenhuma pergunta no sistema ainda
>>> from polls.models import Question, Choice
>>> Question.objects.all()
<QuerySet []>

# Criar uma nova pergunta
>>> from django.utils import timezone
>>> q = Question(question_text="O que há de novo?", pub_date=timezone.now())

# Salvar no banco de dados
>>> q.save()

# Acessar os campos
>>> q.id
1
>>> q.question_text
"O que há de novo?"

# Alterar e salvar
>>> q.question_text = "E aí?"
>>> q.save()

# Listar todas as perguntas
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>
```

### Melhorando a Representação dos Objetos

Adicione o método `__str__()` aos seus modelos para uma melhor representação:

```python
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")
    
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
    
    def __str__(self):
        return self.choice_text
```

### Adicionando Métodos Personalizados

```python
import datetime
from django.db import models
from django.utils import timezone

class Question(models.Model):
    # ...
    
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```

### Usando a API Melhorada

```python
# Agora a representação é melhor
>>> Question.objects.all()
<QuerySet [<Question: E aí?>]>

# Filtrar perguntas
>>> Question.objects.filter(question_text__startswith="O que")
<QuerySet [<Question: E aí?>]>

# Adicionar escolhas
>>> q = Question.objects.get(pk=1)
>>> q.choice_set.create(choice_text="Nada demais", votes=0)
<Choice: Nada demais>
>>> q.choice_set.create(choice_text="O céu", votes=0)
<Choice: O céu>

# Contar escolhas
>>> q.choice_set.count()
2
```

## Administração do Django

### O Site de Administração

O Django automatiza totalmente a criação de uma interface de administração para os seus modelos.

### Criando um Superusuário

Execute:

```bash
python manage.py createsuperuser
```

Você será solicitado a fornecer:

```
Username: admin
Email address: admin@example.com
Password: **********
Password (again): *********
Superuser created successfully.
```

### Iniciando o Servidor

```bash
python manage.py runserver
```

Acesse: **`http://127.0.0.1:8000/admin/`**

### Registrando Modelos na Administração

Edite `polls/admin.py`:

```python
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```

Agora a aplicação `polls` aparecerá no site de administração! Você pode:

- ✅ Criar novas perguntas
- ✅ Editar perguntas existentes
- ✅ Deletar perguntas
- ✅ Ver o histórico de alterações

### Recursos do Site de Administração

- Formulários são gerados automaticamente para seus modelos
- Diferentes tipos de campos possuem componentes HTML apropriados
- Campos de data/hora ganham atalhos JavaScript (hoje, agora, calendário)
- A página oferece opções de salvar, continuar editando, deletar, etc.
- Histórico de alterações é mantido automaticamente

