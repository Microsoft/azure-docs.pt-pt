---
title: 'Tutorial: Implementar Python (Django) com Postgres'
description: Saiba como criar uma aplicação Python com uma base de dados PostgreSQL e implemente-a no Azure App Service no Linux. O tutorial usa uma aplicação de amostra de Django para demonstração.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- tracking-python
ms.openlocfilehash: 4a2f80ea30fc68ae1dfea72983fd2b229d40c711
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559290"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Tutorial: Implementar uma aplicação web Python (Django) com PostgreSQL no Azure App Service

Este tutorial mostra como implementar uma aplicação web Python (Django) orientada por dados para o [Azure App Service](app-service-linux-intro.md) e conectá-la a uma base de dados Azure Database for PostgreSQL. O App Service fornece um serviço de hospedagem web altamente escalável e auto-remendado.

![Implementar app web Python Django para o Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma base de dados Azure para base de dados PostgreSQL
> * Implementar código para o Serviço de Aplicações Azure e ligar-se a Postgres
> * Atualize o seu código e reimplante
> * Ver registos de diagnóstico
> * Gerir a aplicação web no portal Azure

Pode seguir os passos deste artigo sobre macOS, Linux ou Windows.

## <a name="install-dependencies"></a>Instalar dependências

Antes de iniciar este tutorial:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Instale [o Azure CLI](/cli/azure/install-azure-cli).
- Instale [git](https://git-scm.com/).
- Instale [python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Numa janela terminal, executar os seguintes comandos para clonar o repositório da aplicação da amostra e alterar para a raiz do repositório:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

O repositório de amostras de djangoapp contém a aplicação de sondagens [Django](https://www.djangoproject.com/) orientada por dados que obtém depois de [escrever a sua primeira aplicação Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) na documentação do Django. Está aqui para sua conveniência.

## <a name="prepare-app-for-app-service"></a>Preparar aplicativo para o Serviço de Aplicações

Como muitas estruturas web python, Django [requer certas mudanças antes de poderem ser executados num servidor](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)de produção , e não é diferente com o App Service. É necessário alterar e adicionar algumas definições no ficheiro *azuresite/definições.py* predefinido para que a aplicação funcione depois de ser implantada no Serviço de Aplicações. 

Veja o *azuresite/production.py,* que faz a configuração necessária para o Serviço de Aplicações. Resumidamente, faz o seguinte:

- Herdar todas as definições de *azuresite/configurações.py*.
- Adicione o nome de domínio totalmente qualificado da aplicação App Service aos anfitriões autorizados. 
- Use [o WhiteNoise](https://whitenoise.evans.io/en/stable/) para permitir o serviço de ficheiros estáticos na produção, porque o Django por defeito não serve ficheiros estáticos na produção. O pacote WhiteNoise já está incluído nos *requisitos.txt*.
- Adicione a configuração para a base de dados PostgreSQL. Por padrão, Django usa o Sqlite3 como base de dados, mas não é adequado para aplicações de produção. O pacote [psycopg2-binário](https://pypi.org/project/psycopg2-binary/) já está incluído nos *requisitos.txt*.
- A configuração postgres usa variáveis ambientais. Mais tarde, você vai descobrir como definir variáveis ambientais no Serviço de Aplicações.

*azuresite/production.py* está incluído no repositório por conveniência, mas ainda não é usado pela app. Para se certificar de que as suas definições são utilizadas no Serviço de Aplicações, é necessário configurar dois ficheiros, *manage.py* e *azuresite/wsgi.py,* para aceder ao mesmo.

- Em *manage.py,* altere a seguinte linha:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    Para o seguinte código:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    Definirá a variável ambiental `DJANGO_ENV` mais tarde quando configurar a sua aplicação De Serviço de Aplicações.

- Em *azuresite/wsgi.py,* faça a mesma alteração que acima.

    No Serviço de Aplicações, *utiliza-se manage.py* para executar migrações de bases de dados, e o Serviço de Aplicações utiliza *azuresite/wsgi.py* para executar a sua app Django em produção. Esta alteração em ambos os ficheiros garante que as definições de produção são utilizadas em ambos os casos.

## <a name="sign-in-to-azure-cli"></a>Iniciar sessão na CLI do Azure

Já deve ter o Azure CLI instalado. [O Azure CLI](/cli/azure/what-is-azure-cli) permite-lhe trabalhar com os recursos da Azure a partir do terminal da linha de comando. 

Para entrar em Azure, executar o [`az login`](/cli/azure/reference-index#az-login) comando:

```azurecli
az login
```

Siga as instruções no terminal para iniciar súm na sua conta Azure. Quando terminar, as suas subscrições são apresentadas no formato JSON na saída do terminal.

## <a name="create-postgres-database-in-azure"></a>Criar base de dados postgres em Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

Nesta secção, cria-se uma Base de Dados Azure para servidor e base de dados PostgreSQL. Para iniciar, instale a `db-up` extensão com o seguinte comando:

```azurecli
az extension add --name db-up
```

Crie a base de dados Postgres em Azure com o [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) comando, como mostra o exemplo seguinte. *\<postgresql-name>* Substitua-o por um nome *único* (o ponto final do servidor é *https:// \<postgresql-name> .postgres.database.azure.com*). Para *\<admin-username>* e *\<admin-password>* , especificar credenciais para criar um utilizador de administrador para este servidor Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Este comando pode demorar um pouco porque está a fazer o seguinte:

- Cria um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) chamado , se não `myResourceGroup` existe. Todos os recursos do Azure precisam de estar num destes. `--resource-group` é opcional.
- Cria um servidor Postgres com o utilizador administrativo.
- Cria uma `pollsdb` base de dados.
- Permite o acesso a partir do seu endereço IP local.
- Permite o acesso a partir dos serviços Azure.
- Criar um utilizador de base de dados com acesso à `pollsdb` base de dados.

Podes fazer todos os passos separadamente com `az postgres` outros comandos `psql` e, mas `az postgres up` fá-los todos num só passo para ti.

Quando o comando terminar, encontre as linhas de saída com `Ran Database Query:` . Eles mostram o utilizador da base de dados que é criado para si, com o nome de utilizador `root` e a palavra-passe. `Pollsdb1` Irá usá-los mais tarde para ligar a sua aplicação à base de dados.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`, pode ser definido para qualquer uma das [regiões de Azure](https://azure.microsoft.com/global-infrastructure/regions/). Pode disponibilizar as regiões à sua subscrição com o [`az account list-locations`](/cli/azure/account#az-account-list-locations) comando. Para aplicações de produção, coloque a sua base de dados e a sua aplicação no mesmo local.

## <a name="deploy-the-app-service-app"></a>Implementar a aplicação de Serviço de Aplicações

Nesta secção, cria-se a aplicação Serviço de Aplicações. Irá ligar esta aplicação à base de dados Postgres que criou e implementar o seu código.

### <a name="create-the-app-service-app"></a>Criar a app Serviço de Aplicações

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Certifique-se de que está de volta à raiz do repositório , `djangoapp` porque a aplicação será implantada a partir deste diretório.

Crie uma aplicação de Serviço de Aplicações com o [`az webapp up`](/cli/azure/webapp#az-webapp-up) comando, como mostra o exemplo a seguir. *\<app-name>* Substitua-o por um nome *único* (o ponto final do servidor é *https:// \<app-name> .azurewebsites.net*). Personagens *\<app-name>* permitidos são `A` - `Z` `0` - `9` , e `-` .

```azurecli
az webapp up --plan myAppServicePlan --location westus2 --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Este comando pode demorar um pouco porque está a fazer o seguinte:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Gera um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) automaticamente.
- Cria o [plano de Serviço de Aplicações](../overview-hosting-plans.md) *myAppServicePlan* no nível básico de preços (B1), se não existir. `--plan`e `--sku` são opcionais.
- Cria a aplicação De Serviço de Aplicações se não existir.
- Permite a sessão por defeito para a aplicação, se ainda não ativada.
- Carrega o repositório utilizando a implementação ZIP com automatização de construção ativada.

Uma vez terminada a implantação, vê-se uma saída JSON como a seguinte:

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

Copiar o valor de *\<app-resource-group>* . Precisa dele para configurar a aplicação mais tarde. 

> [!TIP]
> As configurações pertinentes são guardadas num *diretório* escondido no seu repositório. Pode utilizar o comando simples mais tarde para recolocar quaisquer alterações e ativar imediatamente os registos de diagnóstico com:
> 
> ```azurecli
> az webapp up
> ```

O código de amostra já foi implementado, mas a aplicação ainda não se liga à base de dados dos Postgres em Azure. Vais fazer isto a seguir.

### <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

Quando executar a sua aplicação localmente, pode definir as variáveis ambientais na sessão terminal. No Serviço de Aplicações, fá-lo com *as definições*de apps, utilizando o comando de configuração de [appsettings az webapp.](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set)

Executar o seguinte comando para especificar os detalhes da ligação da base de dados como definições de aplicação. *\<app-name>* Substitua, e com os seus *\<app-resource-group>* *\<postgresql-name>* próprios valores. Lembre-se que as credenciais de utilizador `root` e `Pollsdb1` foram criadas para si por `az postgres up` .

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Para obter informações sobre como o seu código acede a estas definições de aplicações, consulte [variáveis do ambiente Access](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Executar migrações de base de dados

Para executar migrações de base de dados no Serviço de Aplicações, abra uma sessão SSH no navegador navegando para *https:// \<app-name> .scm.azurewebsites.net/webssh/host*:

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

Na sessão SSH, executar os seguintes comandos:

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Navegue pela app Azure

Navegue na aplicação implementada com URL *http: \/ / \<app-name> .azurewebsites.net* num browser. Deve ver a mensagem **Não há sondagens disponíveis**. 

Navegue para *http: \/ / \<app-name> .azurewebsites.net/admin* e iniciar sê-lo utilizando o utilizador de administração que criou no último passo. **Selecione Adicionar** ao Lado **das Perguntas**e crie uma pergunta de sondagem com algumas escolhas.

Navegue na aplicação implementada com URL *http: \/ / \<app-name> .azurewebsites.net/admin*, e crie algumas questões de pesquisa. Pode ver as perguntas em *http: \/ / \<app-name> .azurewebsites.net/*. 

![Executar app Python Django em App Services em Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Navegue na aplicação implementada com URL *http: \/ / \<app-name> .azurewebsites.net* novamente para ver a pergunta da sondagem e responder à pergunta.

O Serviço de Aplicações deteta um projeto Django no seu repositório procurando um ficheiro *wsgi.py* em cada subdiretório, o que `manage.py startproject` cria por padrão. Quando o Serviço de Aplicações encontra o ficheiro, carrega a aplicação web Do Django. Para obter mais informações sobre como o Serviço de Aplicações de Aplicações carrega aplicações Python, consulte [a imagem de Configure incorporada python](how-to-configure-python.md).

**Parabéns!** Você está executando uma aplicação web Python (Django) no Azure App Service para Linux.

## <a name="develop-app-locally-and-redeploy"></a>Desenvolver app localmente e reimplantar

Nesta secção, desenvolve a sua aplicação no ambiente local e reimplanta o seu código para o Serviço de Aplicações.

### <a name="set-up-locally-and-run"></a>Configurar localmente e correr

Para configurar o seu ambiente de desenvolvimento local e executar a aplicação de amostra pela primeira vez, executar os seguintes comandos:

# <a name="bash"></a>[festa](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Quando a aplicação web Django está totalmente carregada, retorna algo como a seguinte mensagem:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Vá a *http: \/ /localhost:8000* em um browser. Deve ver a mensagem **Não há sondagens disponíveis**. 

Vá a *http: \/ /localhost:8000/administrador* e inscreva-se usando o utilizador administrativo que criou no último passo. **Selecione Adicionar** ao Lado **das Perguntas**e crie uma pergunta de sondagem com algumas escolhas.

![Executar app Python Django em App Services localmente](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Vá a *http: \/ /localhost:8000* novamente para ver a pergunta da sondagem e responder à pergunta. A aplicação local de amostras de Django escreve e armazena dados de utilizadores para uma base de dados local do Sqlite3, por isso não precisa de se preocupar em estragar a sua base de dados de produção. Para que o seu ambiente de desenvolvimento corresponda ao ambiente Azure, considere usar uma base de dados postgres localmente.

Para parar o servidor Django, digite Ctrl+C.

### <a name="update-the-app"></a>Atualizar a aplicação

Apenas para ver como funciona a realização de atualizações de aplicações, faça uma pequena alteração em `polls/models.py` . Encontre a linha:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

E mudá-lo para:

```python
choice_text = models.CharField(max_length=100)
```

Ao alterar o modelo de dados, precisa de criar uma nova migração django. Faça-o com o seguinte comando:

```
python manage.py makemigrations
```

Pode testar as suas alterações localmente executando migrações, executando o servidor de desenvolvimento e navegando para *http: \/ /localhost:8000/administrador*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Código de recolocação para Azure

Para recolocar as alterações, executar o seguinte comando a partir da raiz do repositório:

```azurecli
az webapp up
```

O Serviço de Aplicações deteta que a aplicação existe e apenas implementa o código.

### <a name="rerun-migrations-in-azure"></a>Rerun migrações em Azure

Como fez alterações no modelo de dados, precisa de repetir as migrações na base de dados no Serviço de Aplicações. Abra uma sessão de SSH no navegador navegando para *https:// \<app-name> .scm.azurewebsites.net/webssh/host*. Execute os seguintes comandos:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>App de revisão em produção

Navegue para *http: \/ / \<app-name> .azurewebsites.net* e ver as mudanças em execução ao vivo na produção. 

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

Pode aceder aos registos de consola gerados a partir do interior do contentor.

> [!TIP]
> `az webapp up`liga o registo padrão para si. Por razões de desempenho, esta exploração se desliga após algum tempo, mas volta a funcionar sempre que `az webapp up` corre novamente. Para ligá-lo manualmente, executar o seguinte comando:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Executar o seguinte comando Azure CLI para ver o fluxo de registo:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

> [!NOTE]
> Também pode inspecionar os ficheiros de registo do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Para parar o streaming de registo a qualquer momento, escreva `Ctrl` + `C` .

## <a name="manage-your-app-in-the-azure-portal"></a>Gerencie a sua app no portal Azure

No [portal Azure,](https://portal.azure.com)procure e selecione a aplicação que criou.

![Navegue para a sua app Python Django no portal Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Por predefinição, o portal mostra a página **geral** da sua aplicação. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode executar tarefas básicas de gestão como navegar, parar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir.

![Gerencie a sua app Python Django na página Overview no portal Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não espera precisar destes recursos no futuro, elimine os grupos de recursos executando os seguintes comandos:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu:

> [!div class="checklist"]
> * Criar uma base de dados Azure para base de dados PostgreSQL
> * Implementar código para o Serviço de Aplicações Azure e ligar-se a Postgres
> * Atualize o seu código e reimplante
> * Ver registos de diagnóstico
> * Gerir a aplicação web no portal Azure

Vá ao próximo tutorial para aprender a mapear um nome DNS personalizado para a sua aplicação:

> [!div class="nextstepaction"]
> [Tutorial: Mapeie o nome DNS personalizado para a sua aplicação](../app-service-web-tutorial-custom-domain.md)

Ou confira outros recursos:

> [!div class="nextstepaction"]
> [Configure app Python](how-to-configure-python.md)
