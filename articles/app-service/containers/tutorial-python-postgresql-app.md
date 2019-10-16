---
title: 'Tutorial: aplicativo Web Python (Django) com PostgreSQL no serviço Linux-Azure App'
description: Saiba como executar um aplicativo Web Django (Python controlado por dados) no Azure, com conexão a um banco de dados PostgreSQL.
services: app-service\web
documentationcenter: python
author: cephalin
manager: gwallace
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom:
- mvc
- seodec18
- seo-python-october2019
ms.openlocfilehash: c816d2ee76002f60963415b1027579eb6db94089
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329980"
---
# <a name="build-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Compilar um aplicativo Web Python (Django) com PostgreSQL no serviço Azure App

[O Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra como criar um aplicativo Web Django (Python controlado por dados) usando PostgreSQL como o back-end do banco de dado. Quando terminar, você terá um aplicativo Web Django em execução no serviço Azure App no Linux.

![Aplicativo Web python django no serviço de aplicativo no Linux](./media/tutorial-python-postgresql-app/django-admin-azure.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma base de dados PostgreSQL no Azure
> * Conectar um aplicativo Web Python ao PostgreSQL
> * Implantar o aplicativo Web Python no Azure
> * Ver registos de diagnóstico
> * Gerenciar o aplicativo Web Python no portal do Azure

> [!NOTE]
> Antes de criar um banco de dados do Azure para PostgreSQL, verifique [qual geração de computação está disponível em sua região](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores).

Você pode seguir as etapas neste artigo sobre as instruções do macOS, Linux e Windows são as mesmas na maioria dos casos, mas as diferenças não são detalhadas neste tutorial.

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

Crie um banco de dados chamado *pollsdb* e configure um usuário de banco de dados separado chamado *gerente* com a senha *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Escreva `\q` para sair do cliente PostgreSQL.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Criar uma aplicação Python local

Nesta etapa, você configura o projeto Django do Python local.

### <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Abra a janela de terminal e `CD` num diretório de trabalho.

Execute os seguintes comandos para clonar o repositório de exemplo.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

Este repositório de exemplo contém um aplicativo [Django](https://www.djangoproject.com/) . É o mesmo aplicativo controlado por dados que você obteria seguindo o [tutorial de introdução na documentação do Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/). Este tutorial não ensina a Django, mas mostra como fazer a implantação e executar um aplicativo Web Django (ou outro aplicativo Python controlado por dados) para Azure App serviço.

### <a name="configure-environment"></a>Configurar o ambiente

Crie um ambiente virtual Python e use um script para definir as configurações de conexão do banco de dados.

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

As variáveis de ambiente definidas em *env.sh* e *env. ps1* são usadas em _azuresite/Settings. py_ para definir as configurações do banco de dados.

### <a name="run-app-locally"></a>Executar a aplicação localmente

Instale os pacotes necessários, [Execute migrações Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) e [crie um usuário administrador](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

Depois que o usuário administrador for criado, execute o servidor Django.

```bash
python manage.py runserver
```

Quando o aplicativo Web Django estiver totalmente carregado, você verá algo semelhante à seguinte mensagem:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Vá para `http://localhost:8000` em um navegador. Você deverá ver a mensagem `No polls are available.`. 

Vá para `http://localhost:8000/admin` e entre usando o usuário administrador que você criou na última etapa. Selecione **Adicionar** ao lado de **perguntas** e criar uma pergunta de sondagem com algumas opções.

![Aplicativo python django em execução localmente](./media/tutorial-python-postgresql-app/django-admin-local.png)

Vá para `http://localhost:8000` novamente e veja a pergunta de sondagem exibida.

O aplicativo de exemplo Django armazena dados do usuário no banco de dado. Se você tiver êxito ao adicionar uma pergunta de pesquisa, seu aplicativo está gravando dados no banco de dado PostgreSQL local.

Para interromper o servidor Django a qualquer momento, digite CTRL + C no terminal.

## <a name="create-a-production-postgresql-database"></a>Criar uma base de dados PostgreSQL de produção

Neste passo, vai criar uma base de dados PostgreSQL no Azure. Quando a aplicação for implementada no Azure, utiliza esta base de dados na cloud.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Criar uma Base de Dados do Azure para o servidor PostgreSQL

Crie um servidor PostgreSQL com o comando [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) no Cloud Shell.

No comando de exemplo a seguir, substitua *\<postgresql-name >* por um nome de servidor exclusivo e substitua *\<admin-username >* e *\<admin-password >* pelas credenciais de usuário desejadas. As credenciais de utilizador são para a conta de administrador da base de dados. O nome do servidor é utilizado como parte do ponto final do PostgreSQL (`https://<postgresql-name>.postgres.database.azure.com`), por isso, o nome tem de ser exclusivo em todos os servidores no Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql-name> --location "West Europe" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen4_1
```

Após criar o servidor da Base de Dados do Azure para PostgreSQL, a CLI do Azure mostra informações semelhantes ao exemplo seguinte:

```json
{
  "administratorLogin": "<admin-username>",
  "fullyQualifiedDomainName": "<postgresql-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql-name>",
  "location": "westus",
  "name": "<postgresql-name>",
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
> Lembre-se de que \<admin-username > e \<admin-password > para mais tarde. Precisará deles para iniciar sessão no servidor e bases de dados Postgre.

### <a name="create-firewall-rules-for-the-postgresql-server"></a>Criar regras de firewall para o servidor PostgreSQL

No Cloud Shell, execute o seguinte comando da CLI do Azure, para permitir o acesso à base de dados a partir dos recursos do Azure.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Esta definição permite ligações de rede de todos os IPs na rede do Azure. Para utilização em produção, experimente configurar as regras de firewall o mais restritivas possível ao [utilizar apenas os endereços IP de saída que a aplicação utiliza](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

No Cloud Shell, execute o comando novamente para permitir o acesso do seu computador local, substituindo *\<your-IP-address >* pelo [seu endereço IP IPv4 local](https://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Ligar a aplicação Python base de dados de produção

Nesta etapa, você conecta seu aplicativo Web Django ao banco de dados do Azure para o servidor PostgreSQL criado.

### <a name="create-empty-database-and-user-access"></a>Criar base de dados vazia e acesso de utilizador

No Cloud Shell, conecte-se ao banco de dados executando o comando a seguir. Quando a sua palavra-passe de administrador lhe for pedida, utilize a mesma palavra-passe que especificou em [Criar uma Base de Dados do Azure para servidor PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
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

Na janela do terminal local, altere as variáveis de ambiente do banco de dados (que você configurou anteriormente executando *env.sh* ou *env. ps1*):

```bash
# Bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
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

Vá para `http://localhost:8000` novamente. Você deverá ver a mensagem `No polls are available.` novamente. 

Vá para `http://localhost:8000/admin` e entre usando o usuário administrador que você criou e crie uma pergunta de pesquisa como antes.

![Aplicativo python django em execução no local](./media/tutorial-python-postgresql-app/django-admin-local.png)

Vá para `http://localhost:8000` novamente e veja a pergunta de sondagem exibida. Agora, seu aplicativo está gravando dados no banco de dado no Azure.

## <a name="deploy-to-azure"></a>Implementar no Azure

Neste passo, vai implementar a aplicação Python ligada ao Postgres no Serviço de Aplicações do Azure.

### <a name="configure-repository"></a>Configurar o repositório

Django valida o cabeçalho `HTTP_HOST` em solicitações de entrada. Para que seu aplicativo Web Django funcione no serviço de aplicativo, você precisa adicionar o nome de domínio totalmente qualificado do aplicativo aos hosts permitidos. Abra _azuresite/Settings. py_ e localize a configuração `ALLOWED_HOSTS`. Altere a linha para:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

Em seguida, Django não dá suporte para a [manutenção de arquivos estáticos em produção](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/), portanto, você precisa habilitar isso manualmente. Para este tutorial, você usa [WhiteNoise](https://whitenoise.evans.io/en/stable/). O pacote WhiteNoise já está incluído em _requirements. txt_. Você só precisa configurar o Django para usá-lo. 

Em _azuresite/Settings. py_, localize a configuração `MIDDLEWARE` e adicione o middleware `whitenoise.middleware.WhiteNoiseMiddleware` à lista, logo abaixo do middleware `django.middleware.security.SecurityMiddleware`. A configuração `MIDDLEWARE` deve ter a seguinte aparência:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

No final de _azuresite/Settings. py_, adicione as linhas a seguir.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Para obter mais informações sobre como configurar o WhiteNoise, consulte a [documentação do WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> A seção Configurações de banco de dados já segue a prática recomendada de segurança de usar variáveis de ambiente. Para obter as recomendações completas de implantação, consulte a [documentação do Django: lista de verificação de implantação](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Confirme suas alterações no repositório.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-deployment-user"></a>Configurar usuário de implantação

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Criar plano do App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

No início do tutorial, definiu as variáveis de ambiente para ligar à base de dados PostgreSQL.

No Serviço de Aplicações, as variáveis de ambiente são definidas como _definições da aplicação_ com o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell.

O exemplo seguinte especifica os detalhes de ligação de base de dados, como as definições da aplicação. 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Para obter informações sobre como essas configurações de aplicativo são acessadas em seu código, consulte [acessar variáveis de ambiente](how-to-configure-python.md#access-environment-variables).

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
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
   06b6df4..6520eea  master -> master
```  

O servidor de implantação do serviço de aplicativo vê _requirements. txt_ na raiz do repositório e executa o gerenciamento de pacotes do Python automaticamente após `git push`.

### <a name="browse-to-the-azure-app"></a>Navegue até o aplicativo do Azure

Navegue até o aplicativo implantado. Demora algum tempo a iniciar porque o contentor precisa de ser transferido e executado quando a aplicação é solicitada pela primeira vez. Se a página exceder o limite de tempo ou apresentar uma mensagem de erro, aguarde alguns minutos e atualize a página.

```bash
http://<app-name>.azurewebsites.net
```

Você deve ver a pergunta de pesquisa que você criou anteriormente. 

O serviço de aplicativo detecta um projeto Django em seu repositório procurando um _WSGI.py_ em cada subdiretório, que é criado pelo `manage.py startproject` por padrão. Quando ele encontra o arquivo, ele carrega o aplicativo Web Django. Para obter mais informações sobre como o serviço de aplicativo carrega aplicativos Python, consulte [Configurar imagem interna do Python](how-to-configure-python.md).

Acesse `<app-name>.azurewebsites.net` e entre usando o mesmo usuário administrador que você criou. Se desejar, tente criar mais algumas perguntas de pesquisa.

![Aplicativo python django em execução no local](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Parabéns!** Você está executando um aplicativo Web Python (Django) no serviço Azure App para Linux.

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Gerenciar seu aplicativo no portal do Azure

Vá para a [portal do Azure](https://portal.azure.com) para ver o aplicativo que você criou.

No menu à esquerda, selecione **serviços de aplicativos**e, em seguida, selecione o nome do seu aplicativo do Azure.

![Navegação do portal para a aplicação do Azure](./media/tutorial-python-postgresql-app/app-resource.png)

Por padrão, o portal mostra a página de **visão geral** do aplicativo. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir.

![Página Serviço de Aplicações no portal do Azure](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma base de dados PostgreSQL no Azure
> * Conectar um aplicativo Web Python ao PostgreSQL
> * Implantar o aplicativo Web Python no Azure
> * Ver registos de diagnóstico
> * Gerenciar o aplicativo Web Python no portal do Azure

Avance para o próximo tutorial para saber como mapear um nome DNS personalizado para seu aplicativo.

> [!div class="nextstepaction"]
> [Tutorial: mapear o nome DNS personalizado para seu aplicativo](../app-service-web-tutorial-custom-domain.md)

Ou então, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar o aplicativo Python](how-to-configure-python.md)
