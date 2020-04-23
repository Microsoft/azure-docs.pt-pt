---
title: 'Tutorial: Deploy Python (Django) com Postgres'
description: Aprenda a criar uma aplicação Python com uma base de dados PostgreSQL e implemente-a para o Azure App Service no Linux. O tutorial usa uma aplicação de amostra django para demonstração.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 0c9329b46d096df1afab6f7e457d143f9c6504be
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085761"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Tutorial: Implemente uma aplicação web Python (Django) com PostgreSQL no Serviço de Aplicações Azure

Este tutorial mostra como implementar uma aplicação web python (Django) baseada em dados para [o Azure App Service](app-service-linux-intro.md) e ligá-la a uma base de dados Azure para base de dados PostgreSQL. O Serviço de Aplicações fornece um serviço de hospedagem web altamente escalável e auto-remendado.

![Implementar app web Python Django para o Serviço de Aplicações Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma base de dados Azure para base de dados PostgreSQL
> * Implementar código para o Serviço de Aplicações Azure e ligar-se a Postgres
> * Atualize o seu código e reimplemente
> * Ver registos de diagnóstico
> * Gerir a aplicação web no portal Azure

Pode seguir os passos deste artigo sobre macOS, Linux ou Windows.

## <a name="install-dependencies"></a>Instalar dependências

Antes de iniciar este tutorial:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Instale [o Azure CLI](/cli/azure/install-azure-cli).
- Instale [Git](https://git-scm.com/).
- Instale [python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Numa janela terminal, execute os seguintes comandos para clonar o repositório da aplicação da amostra e mude para a raiz do repositório:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

O repositório de amostras djangoapp contém a aplicação de sondagens [Django](https://www.djangoproject.com/) baseada em dados que obtém ao seguir a escrita da [sua primeira aplicação Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) na documentação django. É fornecido aqui para sua conveniência.

## <a name="prepare-app-for-app-service"></a>Prepare aplicativo para serviço de aplicações

Como muitos quadros web Python, Django requer certas mudanças antes de [poderem ser executados num servidor](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)de produção , e não é diferente com o App Service. É necessário alterar e adicionar algumas definições no ficheiro *azuresite/configurações padrão.py* para que a aplicação funcione depois de ser implementada no Serviço de Aplicações. 

Dê uma olhada no *azuresite/production.py,* o que torna a configuração necessária para o Serviço de Aplicações. Resumidamente, faz o seguinte:

- Herda todas as definições do *azuresite/configurações.py*.
- Adicione o nome de domínio totalmente qualificado da app App Service aos anfitriões permitidos. 
- Utilize [o WhiteNoise](https://whitenoise.evans.io/en/stable/) para permitir a utilização de ficheiros estáticos em produção, porque o Django por defeito não serve ficheiros estáticos na produção. O pacote WhiteNoise já está incluído nos *requisitos.txt*.
- Adicione a configuração para a base de dados PostgreSQL. Por padrão, a Django usa o Sqlite3 como base de dados, mas não é adequado para aplicações de produção. O pacote [psycopg2-binary](https://pypi.org/project/psycopg2-binary/) já está incluído nos *requisitos.txt*.
- A configuração Postgres utiliza variáveis ambientais. Mais tarde, você vai descobrir como definir variáveis ambientais no Serviço de Aplicações.

*azuresite/production.py* está incluído no repositório por conveniência, mas ainda não é usado pela app. Para se certificar de que as suas definições são utilizadas no Serviço de Aplicações, é necessário configurar dois ficheiros, *manage.py* e *azuresite/wsgi.py,* para aceder ao mesmo.

- Em *manage.py,* altere a seguinte linha:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    Ao seguinte código:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    Definirá a variável `DJANGO_ENV` ambiental mais tarde quando configurar a sua aplicação app Service.

- Em *azuresite/wsgi.py,* faça a mesma alteração que acima.

    No Serviço de Aplicações, utiliza *manage.py* para executar migrações de bases de dados, e o App Service utiliza *o azuresite/wsgi.py* para executar a sua aplicação Django em produção. Esta alteração em ambos os ficheiros garante que as definições de produção são utilizadas em ambos os casos.

## <a name="sign-in-to-azure-cli"></a>Iniciar sessão na CLI do Azure

Já deve ter o Azure CLI instalado. [O Azure CLI](/cli/azure/what-is-azure-cli) permite-lhe trabalhar com os recursos azure a partir do terminal de linha de comando. 

Para iniciar sessão no [`az login`](/cli/azure/reference-index#az-login) Azure, dirija o comando:

```azurecli
az login
```

Siga as instruções no terminal para assinar na sua conta Azure. Quando terminar, as suas subscrições são mostradas no formato JSON na saída do terminal.

## <a name="create-postgres-database-in-azure"></a>Criar base de dados Postgres em Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

Nesta secção, cria uma Base de Dados Azure para servidor e base de dados PostgreSQL. Para iniciar, `db-up` instale a extensão com o seguinte comando:

```azurecli
az extension add --name db-up
```

Crie a base de dados [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Postgres em Azure com o comando, como mostra o seguinte exemplo. Substitua * \<>de nome postgresql* por um nome *único* (o ponto final do servidor é *https://\<nome pós-gresql>.postgres.database.azure.com*). Para * \<* o nome de utilizador de administração>e * \<>* de senha de administração, especifique credenciais para criar um utilizador administrador para este servidor Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Este comando pode demorar algum tempo porque está a fazer o seguinte:

- Cria um grupo `myResourceGroup`de [recursos](../../azure-resource-manager/management/overview.md#terminology) chamado , se não existir. Todos os recursos do Azure têm de estar num destes. `--resource-group` é opcional.
- Cria um servidor Postgres com o utilizador administrativo.
- Cria `pollsdb` uma base de dados.
- Permite o acesso a partir do seu endereço IP local.
- Permite o acesso dos serviços Azure.
- Crie um utilizador de `pollsdb` base de dados com acesso à base de dados.

Podes fazer todos os passos separadamente com outros `az postgres` comandos e, `psql`mas `az postgres up` faz todos eles num só passo para ti.

Quando o comando terminar, encontre as `Ran Database Query:`linhas de saída com que está com . Mostram o utilizador da base de dados que `root` é `Pollsdb1`criado para si, com o nome de utilizador e a palavra-passe. Irá usá-los mais tarde para ligar a sua aplicação à base de dados.

<!-- not all locations support az postgres up -->
> [!TIP]
> Para especificar a localização do seu servidor `--location <location-name>`Postgres, inclua o argumento, onde `<location_name>` está uma das [regiões do Azure.](https://azure.microsoft.com/global-infrastructure/regions/) Pode obter as regiões disponíveis para [`az account list-locations`](/cli/azure/account#az-account-list-locations) a sua subscrição com o comando.

## <a name="deploy-the-app-service-app"></a>Implementar a app App Service

Nesta secção, cria a aplicação App Service. Irá ligar esta aplicação à base de dados postgres que criou e implementar o seu código.

### <a name="create-the-app-service-app"></a>Criar a app App Service

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Certifique-se de que está de volta`djangoapp`à raiz do repositório , porque a aplicação será implantada a partir deste diretório.

Crie uma aplicação [`az webapp up`](/cli/azure/webapp#az-webapp-up) de Serviço de Aplicações com o comando, como mostra o seguinte exemplo. Substitua * \<o nome da aplicação>* por um nome *único* (o ponto final do servidor é *https://\<nome de aplicação>.azurewebsites.net*). Os caracteres permitidos para `A` - `Z` `0` - `9` * \<>de nome de aplicativo* são , e `-`.

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Este comando pode demorar algum tempo porque está a fazer o seguinte:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Gera um grupo de [recursos](../../azure-resource-manager/management/overview.md#terminology) automaticamente.
- Cria o plano de serviço de [aplicações](../overview-hosting-plans.md) *myAppServicePlan* no nível de preços Básicos (B1), se não existir. `--plan`e `--sku` são opcionais.
- Cria a aplicação App Service se não existir.
- Permite o registo predefinido da aplicação, se ainda não estiver ativado.
- Carrega o repositório utilizando a implementação zip com automatização de construção ativada.

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

Copiar o valor do * \<grupo de recursos de aplicações>*. Precisa dele para configurar a aplicação mais tarde. 

> [!TIP]
> Pode utilizar o mesmo comando mais tarde para implementar quaisquer alterações e ativar imediatamente os registos de diagnóstico com:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

O código da amostra está agora implementado, mas a aplicação ainda não se liga à base de dados postgres em Azure. Vais fazer isto a seguir.

### <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

Ao executar a sua aplicação localmente, pode definir as variáveis ambientais na sessão terminal. No Serviço de Aplicações, fá-lo com as definições de *aplicações,* utilizando o comando de definições de definição de aplicações de config da [Webapp az webapp.](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set)

Executar o seguinte comando para especificar os dados de ligação da base de dados como definições da aplicação. Substitua * \<o nome da aplicação>*, * \<>do grupo de recursos de aplicações *e * \<>de nome pós-grésq* com os seus próprios valores. Lembre-se que `root` `Pollsdb1` as credenciais `az postgres up`de utilizador e foram criadas para si por .

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Para obter informações sobre como o seu código acede a estas definições de aplicações, consulte [as variáveis do ambiente de acesso](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Executar migrações de bases de dados

Para executar migrações de bases de dados no App Service, abra uma sessão SSH no navegador navegando para *https://\<nome de aplicação>.scm.azurewebsites.net/webssh/host*:

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

Na sessão sSH, executar os seguintes comandos:

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Install requirements in environment
pip install -r requirements.txt
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Navegue na app Azure

Navegue na aplicação implementada com URL *http:\//\<nome de aplicação>.azurewebsites.net* num browser. Devia ver a mensagem **Não há sondagens disponíveis.** 

Navegue para *http:\//\<nome de aplicação>.azurewebsites.net/admin e inscreva-se* usando o utilizador administrativo que criou no último passo. Selecione **Adicionar** ao lado de **Perguntas**, e criar uma pergunta de sondagem com algumas escolhas.

Navegue na aplicação implementada com URL *http:\//\<nome de aplicação>.azurewebsites.net/admin*, e crie algumas questões de sondagem. Pode ver as perguntas em *http:\//\<nome de aplicação>.azurewebsites.net/*. 

![Executar app Python Django em App Services em Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Navegue na aplicação implementada com URL *http:\//\<nome de aplicação>.azurewebsites.net* novamente para ver a pergunta da sondagem e responder à pergunta.

O Serviço de Aplicações deteta um projeto Django no seu repositório, procurando um ficheiro *wsgi.py* em cada subdiretório, o que `manage.py startproject` cria por padrão. Quando o Serviço de Aplicações encontra o ficheiro, carrega a aplicação web DaDjango. Para obter mais informações sobre como o App Service carrega aplicações Python, consulte a [imagem de Python incorporada configurada](how-to-configure-python.md).

**Parabéns!** Você está executando uma aplicação web Python (Django) no Azure App Service para Linux.

## <a name="develop-app-locally-and-redeploy"></a>Desenvolver app localmente e reimplantar

Nesta secção, desenvolve a sua aplicação no seu ambiente local e redistribui o seu código para o Serviço de Aplicações.

### <a name="set-up-locally-and-run"></a>Configurar localmente e correr

Para configurar o seu ambiente de desenvolvimento local e executar a aplicação de amostras pela primeira vez, execute os seguintes comandos:

# <a name="bash"></a>[bash](#tab/bash)

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

Quando a aplicação web Django está totalmente carregada, devolve algo como a seguinte mensagem:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Visite *http:\//localhost:8000* em um navegador. Devia ver a mensagem **Não há sondagens disponíveis.** 

Vá a *\/http: /localhost:8000/admin* e assine usando o utilizador administrativo que criou no último passo. Selecione **Adicionar** ao lado de **Perguntas**, e criar uma pergunta de sondagem com algumas escolhas.

![Executar app Python Django em Serviços de Aplicações localmente](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Vá a *http:\//localhost:8000* novamente para ver a pergunta da sondagem e responder à pergunta. A aplicação de amostras local Django escreve e armazena dados de utilizadores numa base de dados local do Sqlite3, pelo que não precisa de se preocupar em estragar a sua base de dados de produção. Para tornar o seu ambiente de desenvolvimento compatível com o ambiente Azure, considere utilizar uma base de dados postgres localmente.

Para parar o servidor Django, digite CTRL+C.

### <a name="update-the-app"></a>Atualizar a aplicação

Só para ver como funciona a atualização `polls/models.py`de aplicações, faça uma pequena alteração em . Encontre a linha:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

E mudá-lo para:

```python
choice_text = models.CharField(max_length=100)
```

Ao alterar o modelo de dados, é necessário criar uma nova migração Django. Faça-o com o seguinte comando:

```
python manage.py makemigrations
```

Pode testar as suas alterações localmente, executando migrações, executando o servidor de desenvolvimento, e navegando para *\/http: /localhost:8000/admin*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Reutilizar código para Azure

Para recolocar as alterações, execute o seguinte comando a partir da raiz do repositório:

```azurecli
az webapp up --name <app-name>
```

O Serviço de Aplicações deteta que a aplicação existe e apenas implementa o código.

### <a name="rerun-migrations-in-azure"></a>Reexecutar migrações em Azure

Como fez alterações no modelo de dados, precisa de refazer as migrações de bases de dados no Serviço de Aplicações. Abra uma sessão sSH no navegador navegando para *https://\<nome de aplicação>.scm.azurewebsites.net/webssh/host*. Execute os seguintes comandos:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>App de revisão em produção

Navegue para *\//\<http: nome de aplicativo>.azurewebsites.net* e veja as alterações em execução ao vivo na produção. 

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

Pode aceder aos registos de consolas gerados a partir do interior do recipiente.

> [!TIP]
> `az webapp up`liga a exploração de madeira padrão para si. Por razões de desempenho, esta exploração de madeira desliga-se passado algum tempo, mas volta a funcionar sempre que corre `az webapp up` novamente. Para ligá-lo manualmente, execute o seguinte comando:
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
> Também pode inspecionar os ficheiros `https://<app-name>.scm.azurewebsites.net/api/logs/docker`de registo do navegador em .

Para parar o streaming de `Ctrl` + `C`registo a qualquer momento, escreva .

## <a name="manage-your-app-in-the-azure-portal"></a>Gerencie a sua aplicação no portal Azure

No [portal Azure,](https://portal.azure.com)procure e selecione a app que criou.

![Navegue para a sua app Python Django no portal Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Por padrão, o portal mostra a página **de visão geral** da sua aplicação. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas básicas de gestão como navegar, parar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir.

![Gerencie a sua app Python Django na página overview no portal Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não espera precisar destes recursos no futuro, elimine os grupos de recursos executando os seguintes comandos:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeste:

> [!div class="checklist"]
> * Criar uma base de dados Azure para base de dados PostgreSQL
> * Implementar código para o Serviço de Aplicações Azure e ligar-se a Postgres
> * Atualize o seu código e reimplemente
> * Ver registos de diagnóstico
> * Gerir a aplicação web no portal Azure

Vá ao próximo tutorial para aprender a mapear um nome DNS personalizado para a sua aplicação:

> [!div class="nextstepaction"]
> [Tutorial: Mapeie o nome dNS personalizado para a sua aplicação](../app-service-web-tutorial-custom-domain.md)

Ou verificar outros recursos:

> [!div class="nextstepaction"]
> [App Configure Python](how-to-configure-python.md)
