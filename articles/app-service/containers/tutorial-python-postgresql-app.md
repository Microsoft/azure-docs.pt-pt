---
title: 'Tutorial: Aplicativo Linux Python com Postgre'
description: Saiba como obter uma aplicação Linux Python a trabalhar no Azure App Service, com ligação a uma base de dados PostgreSQL em Azure. Django é usado neste tutorial.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523939"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Tutorial: Executar uma aplicação web Python (Django) com PostgreSQL no Azure App Service

O [Serviço de Aplicações do Azure](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra como ligar uma aplicação web python Django baseada em dados a uma base de dados Azure para base de dados PostgreSQL, e implementar e executar a aplicação no Azure App Service.

![App python Django no Serviço de Aplicações Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma Base de Dados Azure para base de dados PostgreSQL e ligue uma aplicação web a ela
> * Implemente a aplicação web para o Azure App Service
> * Ver registos de diagnóstico
> * Gerir a aplicação web no portal Azure

Pode seguir os passos deste artigo sobre macOS, Linux ou Windows. Os passos são semelhantes na maioria dos casos, embora as diferenças não sejam detalhadas neste tutorial. A maioria dos exemplos abaixo usam uma janela terminal `bash` em Linux. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Instale o [Git](https://git-scm.com/).
- Instale [python 3](https://www.python.org/downloads/).
- Instale e execute [postgreSQL](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Testar a instalação postgresQL e criar uma base de dados

Primeiro, ligue-se ao seu servidor Local PostgreSQL e crie uma base de dados: 

Numa janela de terminal local, execute `psql` para ligar ao seu servidor Local PostgreSQL como o utilizador `postgres` incorporado.

```bash
sudo su - postgres
psql
```
ou
```PowerShell
psql -U postgres
```

Se a ligação for bem-sucedida, a base de dados PostgreSQL está em execução. Caso contrário, certifique-se de que a base de dados PostgresQL local é iniciada conforme as instruções para o seu sistema operativo em [Transferências - Distribuição do PostgreSQL Core](https://www.postgresql.org/download/).

Crie uma nova base de dados chamada *pollsdb*, e crie um utilizador de base de dados nomeado *gestor* com senha *supersecretpass*:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Escreva `\q` para sair do cliente PostgreSQL.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Crie e execute a app python local

Em seguida, configurar e executar a aplicação web Python Django.

O repositório de [amostras djangoapp](https://github.com/Azure-Samples/djangoapp) contém a aplicação de sondagens [Django](https://www.djangoproject.com/) baseada em dados que obtém ao seguir a escrita da [sua primeira aplicação Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) na documentação django.

### <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Numa janela terminal, execute os seguintes comandos para clonar o repositório da aplicação da amostra e mude para o novo diretório de trabalho:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Configure o ambiente virtual python

Crie e ative um ambiente virtual Python para executar a sua aplicação.

```bash
python3 -m venv venv
source venv/bin/activate
```
ou
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

No ambiente `venv`, faça *env.sh* ou *env.ps1* para definir as variáveis ambientais que *o azuresite/configurações.py* utilizará para as definições de ligação da base de dados.

```bash
source ./env.sh
```
ou
```PowerShell
.\env.ps1
```

Instale os pacotes necessários a partir de *requisitos.txt,* executar [migrações Django,](https://docs.djangoproject.com/en/2.1/topics/migrations/)e [criar um utilizador administrativo:](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Executar a aplicação Web

Depois de criar o utilizador de administração, execute o servidor Django.

```bash
python manage.py runserver
```

Quando a aplicação web Django está totalmente carregada, devolve algo como a seguinte mensagem:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Visite *http:\//localhost:8000* em um navegador. Devia ver a mensagem **Não há sondagens disponíveis.** 

Vá a *http:\//localhost:8000/admin* e assine usando o utilizador administrativo que criou no último passo. Selecione **Adicionar** ao lado de **Perguntas**, e criar uma pergunta de sondagem com algumas escolhas.

![Executar app Python Django em Serviços de Aplicações localmente](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Vá a *http:\//localhost:8000* novamente para ver a pergunta da sondagem e responder à pergunta. A aplicação de amostra saquea local django escreve e armazena os dados dos utilizadores na base de dados local PostgreSQL.

Para parar o servidor Django, digite CTRL+C no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A maioria dos passos restantes neste artigo usam comandos Azure CLI na Casca de Nuvem Azure. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Criar e ligar à Base de Dados Azure para PostgreSQL

Nesta secção, cria uma Base de Dados Azure para servidor e base de dados PostgreSQL e liga a sua aplicação web a ela. Quando implementa a sua aplicação web para o Azure App Service, a aplicação utiliza esta base de dados em nuvem. 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Pode criar um novo grupo de recursos para a sua Base de Dados Azure para servidor PostgreSQL ou utilizar um grupo de recursos existente. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Criar uma Base de Dados do Azure para o servidor PostgreSQL

Cria um servidor PostgreSQL com o [servidor az postgres criar](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) comando na Cloud Shell.

> [!NOTE]
> Antes de criar uma Base de Dados Azure para servidor PostgreSQL, verifique qual a [geração](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) de cálculo disponível na sua região. Se a sua região não apoiar o hardware gen4, mude *o nome --sku-nome* na seguinte linha de comando para um valor que é suportado na sua região, como gen5. 

No comando seguinte, substitua *\<postgresql-name>* com um nome de servidor único. O nome do servidor faz parte do seu ponto final PostgreSQL *https://\<postgresql-name>.postgres.database.azure.com,* pelo que o nome tem de ser único em todos os servidores do Azure. 

Substitua\<nome do grupo de *recursos>* e *\<região>* com o nome e região do grupo de recursos que pretende utilizar. Para *\<nome de administrador&username>* e\<*admin-password>* Lembre-se do *\<nome de administração>* e *\<admin-password>* para utilizar mais tarde para iniciar sessão no servidor e bases de dados do PostgreSQL.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Quando a Base de Dados Azure para o servidor PostgreSQL é criada, o Azure CLI devolve o código JSON como o seguinte exemplo:

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Criar regras de firewall para a Base de Dados Azure para servidor PostgreSQL

Executar a regra de firewall do [servidor az postgres criar](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) comando para permitir o acesso à base de dados a partir de recursos Azure. Substitua a *\<postgresql-name>* e\<nome de *grupo de recursos>* espaços reservados com os seus valores.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> A definição anterior permite ligações de rede de todos os endereços IP dentro da rede Azure. Para utilização da produção, tente configurar as regras de firewall mais restritivas [possíveis, permitindo apenas os endereços IP](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)de saída que a sua aplicação utiliza .

Volte a executar o comando `firewall-rule create` para permitir o acesso ao computador local. Substitua *\<seu endereço ip>* com [o seu endereço IP IPv4 local](https://www.whatsmyip.org/). Substitua o *\<postgresql-name>* e *\<nome de grupo de recursos>* espaços reservados com os seus próprios valores.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Criar e ligar à Base de Dados Azure para base de dados PostgreSQL

Ligue-se à sua Base de Dados Azure para o servidor PostgreSQL executando o seguinte comando. Use o seu próprio *\<postgresql-name>* e *\<admin-username> e inscreva-se*com a palavra-passe que criou.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Tal como fez no seu servidor PostgreSQL local, crie uma base de dados e um utilizador na Base de Dados Azure para servidor PostgreSQL:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> É uma boa prática criar utilizadores de bases de dados com permissões restritas para aplicações específicas, em vez de usar o utilizador administrativo. O `manager` utilizador tem plenos privilégios *apenas* para a base de dados `pollsdb`.

Escreva `\q` para sair do cliente PostgreSQL.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Testar a conectividade da aplicação para a base de dados Azure PostgreSQL

Edite o seu ficheiro *local env.sh* ou *env.ps1* para apontar para a sua base de dados PostgreSQL em nuvem, substituindo *\<postgresql-name>* com a sua Base de Dados Azure para o nome do servidor PostgreSQL.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
ou
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

No ambiente `venv` na janela do terminal local, execute o *env.sh* editado ou *env.ps1*. 
```bash
source ./env.sh
```
ou
```PowerShell
.\env.ps1
```

Executar a migração django para a base de dados Azure, e criar um utilizador administrativo.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Assim que o utilizador de administração for criado, execute o servidor Django.

```bash
python manage.py runserver
```

Num browser, vá a *http:\//localhost:8000*, e você deve ver a mensagem **Não há sondagens disponíveis** novamente. 

Vá a *http:\//localhost:8000/admin,* inscreva-se usando o utilizador administrativo que criou, e crie uma pergunta de sondagem como antes.

![Executar app Python Django em Serviços de Aplicações localmente](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Vá a *http:\//localhost:8000* novamente, e veja a pergunta da sondagem exibida. A sua aplicação está agora a escrever dados para a Base de Dados Azure para base de dados PostgreSQL.

Para parar o servidor Django, digite CTRL+C no terminal.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Implemente a aplicação web para o Azure App Service

Neste passo, você implementa a base de dados Azure para postgresQL linha de base de dados Python para o Azure App Service.

### <a name="configure-repository"></a>Configurar o repositório

Como este tutorial utiliza uma amostra Django, é necessário alterar e adicionar algumas definições no seu *ficheiro djangoapp/azuresite/settings.py* para trabalhar com o Azure App Service. 

1. Django valida o cabeceamento `HTTP_HOST` nos pedidos de entrada. Para que a sua aplicação web Django funcione no App Service, precisa de adicionar o nome de domínio totalmente qualificado da app aos anfitriões permitidos. 
   
   Editar *o site/configurações.py* para alterar a linha `ALLOWED_HOSTS` da seguinte forma:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django não suporta [servir ficheiros estáticos em produção.](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/) Para este tutorial, utiliza o [WhiteNoise](https://whitenoise.evans.io/en/stable/) para permitir servir os ficheiros. O pacote WhiteNoise já estava instalado com *requisitos.txt*. 
   
   Para configurar o Django para utilizar o WhiteNoise, em *azuresite/configurações.py,* encontre a definição `MIDDLEWARE` e adicione `whitenoise.middleware.WhiteNoiseMiddleware` à lista, logo após a linha `django.middleware.security.SecurityMiddleware`. A sua definição `MIDDLEWARE` deve ser assim:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. No final do *azuresite/configurações.py,* adicione as seguintes linhas:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Para obter mais informações sobre a configuração do WhiteNoise, consulte a [documentação do WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> A secção de definições de bases de dados já segue as melhores práticas de segurança de utilização de variáveis ambientais. Para recomendações completas de implantação, consulte [a Documentação Django: lista de verificação de implementação](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Cometa as suas alterações na sua bifurcação do repositório *djangoapp:*

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Criar plano do App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

No início do tutorial, definiu as variáveis de ambiente para ligar à base de dados PostgreSQL.

No Serviço de Aplicações Azure, define variáveis ambientais como configurações de *aplicações,* utilizando o comando de definições de definição de aplicações de [config webapp az webapp.](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)

Na Nuvem Azure, execute o seguinte comando para especificar os detalhes da ligação da base de dados como definições de aplicação. Substitua *\<nome de aplicativo>* *\<nome de grupo de recursos>* e *\<postgresql-name>* com os seus próprios valores.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Para obter informações sobre como o seu código acede a estas definições de aplicações, consulte [as variáveis do ambiente de acesso](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

O servidor de implementação do Serviço de Aplicações vê *requisitos.txt* na raiz do repositório e executa a gestão do pacote Python automaticamente após `git push`.

### <a name="browse-to-the-azure-app"></a>Navegue na app Azure

Navegue na aplicação implementada com URL *http:\//\<app-name>.azurewebsites.net*. Demora algum tempo a começar, porque o contentor deve ser descarregado e executado quando a aplicação é solicitada pela primeira vez. Se a página exceder o limite de tempo ou apresentar uma mensagem de erro, aguarde alguns minutos e atualize a página.

Devia ver as perguntas das sondagens que criou anteriormente. 

O Serviço de Aplicações deteta um projeto Django no seu repositório, procurando um ficheiro *wsgi.py* em cada subdiretório, que `manage.py startproject` cria por padrão. Quando o Serviço de Aplicações encontra o ficheiro, carrega a aplicação web DaDjango. Para obter mais informações sobre como o App Service carrega aplicações Python, consulte a [imagem de Python incorporada configurada](how-to-configure-python.md).

Visite *http:\//\<app-name>.azurewebsites.net/admin e inscreva-se* usando o utilizador administrativo que criou. Se quiser, crie mais perguntas de sondagens.

![Executar app Python Django em App Services em Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Parabéns!** Você está executando uma aplicação web Python (Django) no Azure App Service para Linux.

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Gerencie a sua aplicação no portal Azure

No [portal Azure,](https://portal.azure.com)procure e selecione a app que criou.

![Navegue para a sua app Python Django no portal Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Por padrão, o portal mostra a página **de visão geral** da sua aplicação. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas básicas de gestão como navegar, parar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir.

![Gerencie a sua app Python Django na página overview no portal Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Vá ao próximo tutorial para aprender a mapear um nome DNS personalizado para a sua aplicação:

> [!div class="nextstepaction"]
> [Tutorial: Mapeie o nome dNS personalizado para a sua aplicação](../app-service-web-tutorial-custom-domain.md)

Ou verificar outros recursos:

> [!div class="nextstepaction"]
> [App Configure Python](how-to-configure-python.md)
