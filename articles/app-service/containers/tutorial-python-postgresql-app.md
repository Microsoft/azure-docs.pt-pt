---
title: 'Tutorial: aplicativo Linux Python com Postgre'
description: Saiba como obter um aplicativo Python Linux trabalhando no serviço Azure App, com conexão a um banco de dados PostgreSQL no Azure. Django é usado neste tutorial.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
ms.openlocfilehash: 3aa5b5085a6120ca513f0aeba344e7f541f0fd72
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713420"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Tutorial: executar um aplicativo Web Python (Django) com PostgreSQL no serviço Azure App

O [Serviço de Aplicações do Azure](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra como conectar um aplicativo Web Django de Python controlado por dados a um banco de dados do Azure para PostgreSQL e implantar e executar o aplicativo no serviço Azure App.

![Aplicativo Web python django no serviço Azure App](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um banco de dados do Azure para PostgreSQL e conectar um aplicativo Web a ele
> * Implantar o aplicativo Web no serviço Azure App
> * Ver registos de diagnóstico
> * Gerenciar o aplicativo Web no portal do Azure

Você pode seguir as etapas neste artigo sobre macOS, Linux ou Windows. As etapas são semelhantes na maioria dos casos, embora as diferenças não sejam detalhadas neste tutorial. A maioria dos exemplos abaixo usa uma janela `bash` terminal no Linux. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Instale o [Git](https://git-scm.com/).
- Instale o [Python 3](https://www.python.org/downloads/).
- Instale e execute o [PostgreSQL](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Testar a instalação do PostgreSQL e criar um banco de dados

Primeiro, conecte-se ao seu servidor PostgreSQL local e crie um banco de dados: 

Em uma janela de terminal local, execute `psql` para se conectar ao servidor PostgreSQL local como o usuário interno `postgres`.

```bash
sudo su - postgres
psql
```
ou
```PowerShell
psql -U postgres
```

Se a ligação for bem-sucedida, a base de dados PostgreSQL está em execução. Caso contrário, certifique-se de que a base de dados PostgresQL local é iniciada conforme as instruções para o seu sistema operativo em [Transferências - Distribuição do PostgreSQL Core](https://www.postgresql.org/download/).

Crie um novo banco de dados chamado *pollsdb*e configure um usuário de banco de dados chamado *gerente* com a senha *supersecretpass*:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Escreva `\q` para sair do cliente PostgreSQL.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Criar e executar o aplicativo Python local

Em seguida, configure e execute o aplicativo Web python django de exemplo.

O repositório de exemplo [djangoapp](https://github.com/Azure-Samples/djangoapp) contém o aplicativo de sondagens do [Django](https://www.djangoproject.com/) controlado por dados que você obtém ao [escrever seu primeiro aplicativo Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) na documentação do Django.

### <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Em uma janela de terminal, execute os seguintes comandos para clonar o repositório de aplicativo de exemplo e altere para o novo diretório de trabalho:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Configurar o ambiente virtual do Python

Crie e ative um ambiente virtual Python para executar seu aplicativo.

```bash
python3 -m venv venv
source venv/bin/activate
```
ou
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

No ambiente de `venv`, execute *env.sh* ou *env. ps1* para definir as variáveis de ambiente que *azuresite/Settings. py* usará para as configurações de conexão do banco de dados.

```bash
source ./env.sh
```
ou
```PowerShell
.\env.ps1
```

Instale os pacotes necessários em *requirements. txt*, execute [migrações do Django](https://docs.djangoproject.com/en/2.1/topics/migrations/)e [crie um usuário administrador](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user):

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Executar a aplicação Web

Depois de criar o usuário administrador, execute o servidor Django.

```bash
python manage.py runserver
```

Quando o aplicativo Web Django é totalmente carregado, ele retorna algo semelhante à seguinte mensagem:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Vá para *http:\//localhost: 8000* em um navegador. Você deverá ver a mensagem **nenhuma sondagem está disponível**. 

Vá para *http:\//localhost: 8000/admin* e entre usando o usuário administrador que você criou na última etapa. Selecione **Adicionar** ao lado de **perguntas**e crie uma pergunta de sondagem com algumas opções.

![Executar o aplicativo python django nos serviços de aplicativos localmente](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Vá para *http:\//localhost: 8000* novamente para ver a pergunta da pesquisa e responder à pergunta. O aplicativo de exemplo Django local grava e armazena dados de usuário no banco de dado PostgreSQL local.

Para interromper o servidor Django, digite CTRL + C no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A maioria das etapas restantes neste artigo usa CLI do Azure comandos no Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Criar e conectar-se ao banco de dados do Azure para PostgreSQL

Nesta seção, você cria um banco de dados do Azure para servidor e banco de dados PostgreSQL e conecta seu aplicativo Web a ele. Quando você implanta seu aplicativo Web no serviço Azure App, o aplicativo usa esse banco de dados de nuvem. 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Você pode criar um novo grupo de recursos para o servidor do banco de dados do Azure para PostgreSQL ou usar um grupo de recursos existente. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Criar uma Base de Dados do Azure para o servidor PostgreSQL

Você cria um servidor PostgreSQL com o comando [AZ postgres Server CREATE](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) no Cloud Shell.

> [!NOTE]
> Antes de criar um banco de dados do Azure para o servidor PostgreSQL, verifique qual [geração de computação](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) está disponível em sua região. Se sua região não der suporte a hardware Gen4, altere *--SKU-Name* na linha de comando a seguir para um valor com suporte em sua região, como Gen5. 

No comando a seguir, substitua *\<PostgreSQL-name >* com um nome de servidor exclusivo. O nome do servidor faz parte do seu ponto de extremidade PostgreSQL *https://\<PostgreSQL-name >. Postgres. Database. Azure. com*, portanto, o nome precisa ser exclusivo em todos os servidores no Azure. 

Substitua *\<> de nome* de grupo de recursos e *\<> de região* com o nome e a região do grupo de recurso que você deseja usar. Para *\<admin-username >* e *\<> admin-password*, crie as credenciais do usuário para a conta de administrador do banco de dados. Lembre-se do *> de\<admin-username* e *\<> de senha de administrador* para uso posterior para entrar no servidor e nos bancos de dados PostgreSQL.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Quando o banco de dados do Azure para servidor PostgreSQL é criado, o CLI do Azure retorna um código JSON como o exemplo a seguir:

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

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Criar regras de firewall para o servidor do banco de dados do Azure para PostgreSQL

Execute o comando [AZ postgres Server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) para permitir o acesso ao banco de dados de recursos do Azure. Substitua os *\<PostgreSQL-name >* e *\<o nome do resourcegroup >* espaços reservados com seus valores.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> A configuração anterior permite conexões de rede de todos os endereços IP na rede do Azure. Para uso em produção, tente configurar as regras de firewall mais restritivas possíveis [permitindo apenas os endereços IP de saída que seu aplicativo usa](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Execute o comando `firewall-rule create` novamente para permitir o acesso do seu computador local. Substitua *\<seu endereço ip >* pelo [seu endereço IP IPv4 local](https://www.whatsmyip.org/). Substitua os *\<PostgreSQL-name >* e *\<o nome do resourcegroup >* espaços reservados com seus próprios valores.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Criar e conectar-se ao banco de dados do Azure para o banco de dados PostgreSQL

Conecte-se ao banco de dados do Azure para o servidor PostgreSQL executando o comando a seguir. Use seu próprio *\<PostgreSQL-name >* e *\<admin-username >* e entre com a senha que você criou.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Assim como você fez no servidor PostgreSQL local, crie um banco de dados e um usuário no banco de dados do Azure para o servidor PostgreSQL:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> É uma prática recomendada criar usuários de banco de dados com permissões restritas para aplicativos específicos, em vez de usar o usuário administrador. O usuário `manager` tem privilégios totais *apenas* para o banco de dados `pollsdb`.

Escreva `\q` para sair do cliente PostgreSQL.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Testar a conectividade do aplicativo com o banco de dados PostgreSQL do Azure

Edite seu arquivo *env.sh* ou *env. ps1* local para apontar para o banco de dados PostgreSQL de nuvem, substituindo *\<> do PostgreSQL* pelo nome do servidor do banco de dados do Azure para PostgreSQL.

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

No ambiente de `venv` na janela do terminal local, execute o *env.sh* ou *env. ps1*editado. 
```bash
source ./env.sh
```
ou
```PowerShell
.\env.ps1
```

Execute a migração do Django para o banco de dados do Azure e crie um usuário administrador.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Depois que o usuário administrador for criado, execute o servidor Django.

```bash
python manage.py runserver
```

Em um navegador, vá para *http:\//localhost: 8000*, e você deverá ver a mensagem **nenhuma sondagem está disponível** novamente. 

Vá para *http:\//localhost: 8000/admin*, entre usando o usuário administrador que você criou e crie uma pergunta de pesquisa como antes.

![Executar o aplicativo python django nos serviços de aplicativos localmente](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Vá para *http:\//localhost: 8000* novamente e veja a pergunta de sondagem exibida. Agora, seu aplicativo está gravando dados no Azure Database para o banco de dados PostgreSQL.

Para interromper o servidor Django, digite CTRL + C no terminal.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Implantar o aplicativo Web no serviço Azure App

Nesta etapa, você implantará o aplicativo Python conectado ao banco de dados do Azure para PostgreSQL para Azure App serviço.

### <a name="configure-repository"></a>Configurar o repositório

Como este tutorial usa um exemplo de Django, você precisa alterar e adicionar algumas configurações em seu arquivo *djangoapp/azuresite/Settings. py* para trabalhar com o serviço Azure app. 

1. Django valida o cabeceamento `HTTP_HOST` nos pedidos de entrada. Para que seu aplicativo Web Django funcione no serviço de aplicativo, você precisa adicionar o nome de domínio totalmente qualificado do aplicativo aos hosts permitidos. 
   
   Editar *o site/configurações.py* para alterar a linha `ALLOWED_HOSTS` da seguinte forma:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django não dá suporte para a [manutenção de arquivos estáticos em produção](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). Para este tutorial, use [WhiteNoise](https://whitenoise.evans.io/en/stable/) para habilitar o fornecimento dos arquivos. O pacote WhiteNoise já foi instalado com *requirements. txt*. 
   
   Para configurar o Django para usar WhiteNoise, em *azuresite/Settings. py*, localize a configuração de `MIDDLEWARE` e adicione `whitenoise.middleware.WhiteNoiseMiddleware` à lista, logo após a linha de `django.middleware.security.SecurityMiddleware`. Sua configuração de `MIDDLEWARE` deve ter a seguinte aparência:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. No final de *azuresite/Settings. py*, adicione as seguintes linhas:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Para obter mais informações sobre como configurar o WhiteNoise, consulte a [documentação do WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> A seção Configurações de banco de dados já segue a prática recomendada de segurança de usar variáveis de ambiente. Para obter recomendações completas de implantação, consulte a [documentação do Django: lista de verificação de implantação](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Confirme suas alterações na bifurcação do repositório *djangoapp* :

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

No Azure App Service, você define as variáveis de ambiente como *configurações do aplicativo*, usando o comando [AZ webapp config appSettings Set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) .

No Azure Cloud Shell, execute o comando a seguir para especificar os detalhes de conexão do banco de dados como configurações do aplicativo. Substitua *\<> do nome do aplicativo*, *\<> do nome do resourcegroup*e\<o nome do *PostgreSQL >* com seus próprios valores.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Para obter informações sobre como o seu código acessa essas configurações de aplicativo, consulte [acessar variáveis de ambiente](how-to-configure-python.md#access-environment-variables).

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

O servidor de implantação do serviço de aplicativo vê *requirements. txt* na raiz do repositório e executa o gerenciamento de pacotes do Python automaticamente após a `git push`.

### <a name="browse-to-the-azure-app"></a>Navegue até o aplicativo do Azure

Navegue até o aplicativo implantado com URL *http:\//\<nome-do-aplicativo >. azurewebsites. net*. Leva algum tempo para começar, porque o contêiner deve ser baixado e executado quando o aplicativo é solicitado pela primeira vez. Se a página exceder o limite de tempo ou apresentar uma mensagem de erro, aguarde alguns minutos e atualize a página.

Você deve ver as perguntas de pesquisa que você criou anteriormente. 

O serviço de aplicativo detecta um projeto Django em seu repositório procurando um arquivo *WSGI.py* em cada subdiretório, que `manage.py startproject` cria por padrão. Quando o serviço de aplicativo encontra o arquivo, ele carrega o aplicativo Web Django. Para obter mais informações sobre como o serviço de aplicativo carrega aplicativos Python, consulte [Configurar imagem interna do Python](how-to-configure-python.md).

Vá para *http:\//\<nome-do-aplicativo >. azurewebsites. net/admin* e entre usando o usuário administrador que você criou. Se desejar, crie mais algumas perguntas de pesquisa.

![Executar o aplicativo python django nos serviços de aplicativos no Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Parabéns!** Você está executando um aplicativo Web Python (Django) no serviço Azure App para Linux.

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Gerenciar seu aplicativo no portal do Azure

Na [portal do Azure](https://portal.azure.com), procure e selecione o aplicativo que você criou.

![Navegue até seu aplicativo python django no portal do Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Por padrão, o portal mostra a página de **visão geral** do aplicativo. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, você também pode executar tarefas básicas de gerenciamento, como procurar, parar, reiniciar e excluir. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir.

![Gerencie seu aplicativo python django na página Visão geral no portal do Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Vá para o próximo tutorial para saber como mapear um nome DNS personalizado para seu aplicativo:

> [!div class="nextstepaction"]
> [Tutorial: mapear o nome DNS personalizado para seu aplicativo](../app-service-web-tutorial-custom-domain.md)

Ou confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar o aplicativo Python](how-to-configure-python.md)
