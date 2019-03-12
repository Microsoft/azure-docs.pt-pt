---
title: Criar uma aplicação de Python com PostgreSQL no Linux - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como executar uma aplicação Python condicionada por dados no Azure, com ligação a uma base de dados PostgreSQL.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: beverst;cephalin
ms.custom: seodec18
ms.openlocfilehash: 00fc92ebe8b43f16791adce1f1cb9a1d6da7fbde
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534145"
---
# <a name="build-a-python-and-postgresql-app-in-azure-app-service"></a>Criar uma aplicação Python e PostgreSQL no serviço de aplicações do Azure

[O Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra como criar uma aplicação de Python condicionada por dados, com o PostgreSQL como o back-end de base de dados. Quando tiver terminado, terá uma aplicação Django em execução no serviço de aplicações no Linux.

![Aplicação do Django Python no serviço de aplicações no Linux](./media/tutorial-python-postgresql-app/django-admin-azure.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma base de dados PostgreSQL no Azure
> * Ligar uma aplicação Python ao PostgreSQL
> * Implementar a aplicação no Azure
> * Ver registos de diagnóstico
> * Gerir a aplicação no portal do Azure

> [!NOTE]
> Antes de criar uma base de dados do Azure para PostgreSQL, verifique [geração de computação que está disponível na sua região](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores).

Pode seguir os passos neste artigo no macOS. As instruções do Linux e Windows são as mesmas na maioria dos casos, mas as diferenças não estão descritas neste tutorial.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar o Python](https://www.python.org/downloads/)
3. [Instalar e executar o PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testar a instalação do PostgreSQL local e criar uma base de dados

Numa janela do terminal local, execute `psql` para ligar ao seu servidor PostgreSQL local.

```bash
sudo -u postgres psql postgres
```

Se obtiver uma mensagem de erro semelhante a `unknown user: postgres`, a instalação do PostgreSQL pode ser configurada com o seu nome de utilizador com sessão iniciada. Em alternativa, experimente o comando seguinte.

```bash
psql postgres
```

Se a ligação for bem-sucedida, a base de dados PostgreSQL está em execução. Caso contrário, certifique-se de que a base de dados PostgresQL local é iniciada conforme as instruções para o seu sistema operativo em [Transferências - Distribuição do PostgreSQL Core](https://www.postgresql.org/download/).

Criar uma base de dados chamado *pollsdb* e defina um com o nome de utilizador de base de dados separada *manager* com palavra-passe *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Escreva `\q` para sair do cliente PostgreSQL.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Criar uma aplicação Python local

Neste passo, vai configurar o projeto de Python Django local.

### <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Abra a janela de terminal e `CD` num diretório de trabalho.

Execute os seguintes comandos para clonar o repositório de exemplo.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

Este repositório de exemplo contém um [Django](https://www.djangoproject.com/) aplicação. É a mesma aplicação orientada por dados obteria ao seguir a [tutorial de introdução na documentação do Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/). Este tutorial não ensiná-lo Django, mas mostra-lhe como tirar implementar e executar uma aplicação Django (ou outra aplicação de Python condicionada por dados) no serviço de aplicações.

### <a name="configure-environment"></a>Configurar o ambiente

Criar um ambiente virtual do Python e utilize um script para definir as definições de ligação de base de dados.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

As variáveis de ambiente definidas *env.sh* e *env.ps1* são utilizadas no _azuresite/settings.py_ para definir as definições de base de dados.

### <a name="run-app-locally"></a>Executar a aplicação localmente

Instalar os pacotes necessários, [execute migrações de Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) e [criar um utilizador administrador](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

Depois de criar o utilizador administrador, execute o servidor do Django.

```bash
python manage.py runserver
```

Quando a aplicação estiver totalmente carregada, verá algo semelhante à mensagem seguinte:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Navegue para `http://localhost:8000` num browser. Deverá ver a mensagem `No polls are available.`. 

Navegue para `http://localhost:8000/admin` e inicie sessão com o utilizador de administrador que criou no último passo. Clique em **Add** junto a **perguntas** e criar uma pergunta de inquérito com algumas opções.

![Aplicação Python Django em execução localmente](./media/tutorial-python-postgresql-app/django-admin-local.png)

Navegue para `http://localhost:8000` novamente e ver a pergunta de inquérito apresentada.

A aplicação de exemplo do Django armazena dados de utilizador na base de dados. Se for concluída com êxito ao adicionar uma pergunta de inquérito, a aplicação é escrever dados na base de dados do PostgreSQL local.

Para parar o servidor do Django a qualquer altura, escreva Ctrl + C no terminal.

## <a name="create-a-production-postgresql-database"></a>Criar uma base de dados PostgreSQL de produção

Neste passo, vai criar uma base de dados PostgreSQL no Azure. Quando a aplicação for implementada no Azure, utiliza esta base de dados na cloud.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Criar uma Base de Dados do Azure para o servidor PostgreSQL

Crie um servidor PostgreSQL com o comando [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) no Cloud Shell.

No seguinte exemplo de linha de comandos, substitua *\<postgresql_name>* por um nome de servidor exclusivo, e substitua *\<admin_username>* e *\<admin_password>* pelas credenciais de utilizador pretendidas. As credenciais de utilizador são para a conta de administrador da base de dados. O nome do servidor é utilizado como parte do ponto final do PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), por isso, o nome tem de ser exclusivo em todos os servidores no Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
```

Após criar o servidor da Base de Dados do Azure para PostgreSQL, a CLI do Azure mostra informações semelhantes ao exemplo seguinte:

```json
{
  "administratorLogin": "<admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Memorize \<admin_username> e \<admin_password> para utilizar mais tarde. Precisará deles para iniciar sessão no servidor e bases de dados Postgre.


### <a name="create-firewall-rules-for-the-postgresql-server"></a>Criar regras de firewall para o servidor PostgreSQL

No Cloud Shell, execute o seguinte comando da CLI do Azure, para permitir o acesso à base de dados a partir dos recursos do Azure.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Esta definição permite ligações de rede de todos os IPs na rede do Azure. Para utilização em produção, experimente configurar as regras de firewall o mais restritivas possível ao [utilizar apenas os endereços IP de saída que a aplicação utiliza](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

No Cloud Shell, execute o comando novamente para permitir o acesso a partir do seu computador local, substituindo *\<your_ip_address>* pelo [seu endereço IP IPv4 local](https://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Ligar a aplicação Python base de dados de produção

Neste passo, vai ligar a aplicação de exemplo do Django para a base de dados do Azure para o servidor PostgreSQL que criou.

### <a name="create-empty-database-and-user-access"></a>Criar base de dados vazia e acesso de utilizador

No Cloud Shell, ligue à base de dados ao executar o comando abaixo. Quando a sua palavra-passe de administrador lhe for pedida, utilize a mesma palavra-passe que especificou em [Criar uma Base de Dados do Azure para servidor PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Assim como no seu servidor Postgres local, crie a base de dados e um utilizador no servidor Postgres do Azure.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Escreva `\q` para sair do cliente PostgreSQL.

> [!NOTE]
> Uma prática recomendada é criar utilizadores de base de dados com permissões restritas para aplicações específicas, em vez de utilizar o utilizador administrador. Neste exemplo, o utilizador `manager` tem todos os privilégios _só_ para a base de dados `pollsdb`.

### <a name="test-app-connectivity-to-production-database"></a>Testar a conectividade da aplicação à base de dados de produção

Na janela de terminal local, alterar as variáveis de ambiente de base de dados (que configurou anteriormente, executando *env.sh* ou *env.ps1*):

```bash
# Bash
export DBHOST="<postgresql_name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql_name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql_name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql_name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Executar migração Django para a base de dados do Azure e criar um utilizador administrador.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Depois de criar o utilizador administrador, execute o servidor do Django.

```bash
python manage.py runserver
```

Navegue para `http://localhost:8000` no novamente. Deverá ver a mensagem `No polls are available.` novamente. 

Navegue para `http://localhost:8000/admin` e inicie sessão com o utilizador de administrador que criou e criar uma pergunta de inquérito como antes.

![Aplicação Python Django em execução no local](./media/tutorial-python-postgresql-app/django-admin-local.png)

Navegue para `http://localhost:8000` novamente e ver a pergunta de inquérito apresentada. A aplicação é agora escrever dados na base de dados no Azure.

## <a name="deploy-to-azure"></a>Implementar no Azure

Neste passo, vai implementar a aplicação Python ligada ao Postgres no Serviço de Aplicações do Azure.

### <a name="configure-repository"></a>Configurar o repositório

Django valida o `HTTP_HOST` cabeçalho em solicitações de entrada. Para a sua aplicação Django funcionar no serviço de aplicações, terá de adicionar o nome de domínio completamente qualificados da aplicação para os anfitriões permitidos. Open _azuresite/settings.py_ e localize o `ALLOWED_HOSTS` definição. Altere a linha para:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

Em seguida, não suporta a Django [servir ficheiros estáticos na produção](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/), por isso terá de ativar esta opção manualmente. Para este tutorial, utilize [WhiteNoise](https://whitenoise.evans.io/en/stable/). O pacote de WhiteNoise já está incluído no _Requirements. txt_. Terá de configurar o Django usá-lo. 

No _azuresite/settings.py_, localizar o `MIDDLEWARE` definição e adicionar o `whitenoise.middleware.WhiteNoiseMiddleware` middleware à lista, logo abaixo do `django.middleware.security.SecurityMiddleware` middleware. Sua `MIDDLEWARE` definição deve ter o seguinte aspeto:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

No final da _azuresite/settings.py_, adicione as seguintes linhas.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Para obter mais informações sobre como configurar WhiteNoise, consulte a [WhiteNoise documentação](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Secção de definições da base de dados já segue a prática recomendada de segurança do uso de variáveis de ambiente. Para obter recomendações de implantação completa, veja [documentação do Django: lista de verificação de implementação](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).


Consolide as suas alterações no repositório.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar uma aplicação Web 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

No início do tutorial, definiu as variáveis de ambiente para ligar à base de dados PostgreSQL.

No Serviço de Aplicações, as variáveis de ambiente são definidas como _definições da aplicação_ com o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell.

O exemplo seguinte especifica os detalhes de ligação de base de dados, como as definições da aplicação. 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git 
   06b6df4..6520eea  master -> master
```  

O servidor de implantação do serviço de aplicações vê _Requirements. txt_ na raiz do repositório e executa automaticamente após a gestão de pacotes de Python `git push`.

### <a name="browse-to-the-azure-app"></a>Navegue para a aplicação do Azure

Navegue para a aplicação implementada. Demora algum tempo a iniciar porque o contentor precisa de ser transferido e executado quando a aplicação é solicitada pela primeira vez. Se a página exceder o limite de tempo ou apresentar uma mensagem de erro, aguarde alguns minutos e atualize a página.

```bash
http://<app_name>.azurewebsites.net
```

Deverá ver a pergunta de consulta que criou anteriormente. 

Serviço de aplicações Deteta um projeto do Django no seu repositório para procurar um _wsgi.py_ em cada subdiretório, que é criado pela `manage.py startproject` por predefinição. Quando encontra o ficheiro, ele carrega a aplicação Django. Para obter mais informações sobre a forma como o serviço de aplicações carrega aplicações Python, veja [configurar imagem de Python incorporada](how-to-configure-python.md).

Navegue para `<app_name>.azurewebsites.net` e inicie sessão com o mesmo utilizador de administrador que criou. Se desejar, tente criar a algumas perguntas da pesquisa mais.

![Aplicação Python Django em execução no local](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Parabéns!** Está a executar uma aplicação Python no Serviço de Aplicações para Linux.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

No serviço de aplicações no Linux, aplicações são executadas dentro de um contentor a partir de uma imagem do Docker padrão. Pode acessar os registos de consola gerados a partir de dentro do contentor. Para obter os registos, primeiro ative o registo de contentor ao executar o seguinte comando no Cloud Shell:

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Assim que o registo de contentor está ativado, execute o seguinte comando para ver o fluxo de registo:

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

## <a name="manage-your-app-in-the-azure-portal"></a>Gerir a sua aplicação no Portal do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver a aplicação que criou.

No menu à esquerda, clique em **dos serviços de aplicações**, em seguida, clique no nome da sua aplicação do Azure.

![Navegação do portal para a aplicação do Azure](./media/tutorial-python-postgresql-app/app-resource.png)

Por predefinição, o portal mostra a sua aplicação **descrição geral** página. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir.

![Página Serviço de Aplicações no portal do Azure](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma base de dados PostgreSQL no Azure
> * Ligar uma aplicação Python ao PostgreSQL
> * Implementar a aplicação no Azure
> * Ver registos de diagnóstico
> * Gerir a aplicação no portal do Azure

Avance para o próximo tutorial para saber como mapear um nome DNS personalizado à sua aplicação.

> [!div class="nextstepaction"]
> [Mapear um nome DNS existente personalizado para o serviço de aplicações do Azure](../app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Configurar incorporadas Python de imagem e resolver problemas de erros](how-to-configure-python.md)

