---
title: 'Tutorial: Criar uma app multi-contentores'
description: Aprenda a usar a construção de uma aplicação multi-contentores no Azure App Service que contém uma aplicação WordPress e um recipiente MySQL, e configurar a aplicação WordPress.
keywords: serviço de aplicativos azure, web app, linux, docker, compose, multicontainer, multi-contentor, web app para contentores, múltiplos contentores, contentor, wordpress, azure db para mysql, base de dados de produção com contentores
author: msangapu-msft
ms.topic: tutorial
ms.date: 04/29/2019
ms.author: msangapu
ms.custom: cli-validate, devx-track-azurecli
ms.openlocfilehash: 7945c6c6f834de068665e3400440d2be5dd713ff
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743441"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>Tutorial: Criar uma aplicação com vários contentores (pré-visualização) na Aplicação Web para Contentores

> [!NOTE]
> O multi-contentor está em pré-visualização.

A [Aplicação Web para Contentores](overview.md#app-service-on-linux) proporciona uma forma flexível de utilizar imagens do Docker. Neste tutorial, irá aprender a criar uma aplicação com vários contentores utilizando o WordPress e MySQL. Este tutorial deverá ser concluído no Cloud Shell, mas também poderá executar estes comandos localmente com a ferramenta de linha de comandos [CLI do Azure](/cli/azure/install-azure-cli) (2.0.32 ou posterior).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Converter uma configuração do Docker Compose para funcionar com a Aplicação Web para Contentores
> * Implementar uma aplicação com vários contentores no Azure
> * Adicionar definições da aplicação
> * Utilizar o armazenamento persistente para os contentores
> * Ligar à Base de Dados do Azure para MySQL
> * Resolver erros

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa de experiência com [Docker Compose.](https://docs.docker.com/compose/)

## <a name="download-the-sample"></a>Transferir o exemplo

Para este tutorial, você usa o ficheiro composo de [Docker,](https://docs.docker.com/compose/wordpress/#define-the-project)mas irá modificá-lo para incluir Azure Database para MySQL, armazenamento persistente e Redis. O ficheiro de configuração pode ser encontrado em [Exemplos do Azure](https://github.com/Azure-Samples/multicontainerwordpress). Para opções de configuração suportadas, consulte [as opções de Composição de Docker](configure-custom-container.md#docker-compose-options).

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

No Cloud Shell, crie um diretório de tutorial e, em seguida, altere-o.

```bash
mkdir tutorial

cd tutorial
```

Em seguida, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu diretório de tutorial. Em seguida, mude para o diretório `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Em Cloud Shell, crie um grupo de recursos com o [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) comando. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *E.U.A. Centro-Sul* . Para ver todas as localizações suportadas para o Serviço de Aplicações no Linux no escalão **Standard** , execute o comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Geralmente, o grupo de recursos e os recursos são criados numa região perto de si.

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.

## <a name="create-an-azure-app-service-plan"></a>Criar um plano do Serviço de Aplicações do Azure

Na Cloud Shell, crie um plano de Serviço de Aplicações no grupo de recursos com o [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) comando.

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

O exemplo seguinte cria um Plano do Serviço de Aplicações com o nome `myAppServicePlan`, no escalão de preços **Standard** (`--sku S1`) e num contentor do Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Quando o plano do Serviço de Aplicações tiver sido criado, o Cloud Shell mostra informações semelhantes ao seguinte exemplo:

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>Docker Compose com contentores do WordPress e MySQL

## <a name="create-a-docker-compose-app"></a>Criar uma aplicação Docker Compose

No seu Cloud Shell, crie uma [aplicação Web](overview.md) com vários contentores no plano do Serviço de Aplicações `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Não se esqueça de substituir _\<app-name>_ por um nome de aplicação único.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Quando a aplicação Web tiver sido criada, o Cloud Shell mostra informações semelhantes ao seguinte exemplo:

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>Navegar para a aplicação

Navegue para a aplicação implementada em (`http://<app-name>.azurewebsites.net`). A aplicação pode demorar alguns minutos a carregar. Se receber uma mensagem de erro, aguarde mais alguns minutos e, em seguida, atualize o browser. Se estiver a ter problemas e os quiser resolver, consulte os [registos de contentor](#find-docker-container-logs).

![Aplicação com vários contentores de exemplo na Aplicação Web para Contentores][1]

**Parabéns,** criou uma aplicação multi-contentores na Web App para Contentores. Em seguida, irá configurar a sua aplicação para utilizar a Base de Dados do Azure para MySQL. Não instale o WordPress neste momento.

## <a name="connect-to-production-database"></a>Ligar à base de dados de produção

Não é recomendado utilizar os contentores da base de dados num ambiente de produção. Os contentores locais não são dimensionáveis. Em alternativa, irá utilizar a Base de Dados do Azure para MySQL que pode ser dimensionada.

### <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL

Crie uma Base de Dados Azure para o servidor MySQL com o [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) comando.

No comando seguinte, substitua o nome do servidor MySQL onde vê o _&lt; nome do mysql-servidor>_ espaço reservado (caracteres válidos são , e `a-z` `0-9` `-` ). Este nome faz parte do nome de anfitrião do servidor MySQL (`<mysql-server-name>.database.windows.net`); tem de ser globalmente exclusivo.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

Criar o servidor pode demorar alguns minutos a concluir. Quando o servidor MySQL tiver sido criado, o Cloud Shell mostra informações semelhantes às do exemplo abaixo:

<pre>
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "southcentralus",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
</pre>

### <a name="configure-server-firewall"></a>Configurar a firewall do servidor

Crie uma regra de firewall para o seu servidor MySQL para permitir ligações ao cliente utilizando o [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) comando. Quando os IPs inicial e final estão definidos como 0.0.0.0, a firewall apenas é aberta para outros recursos do Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Pode ser ainda mais restritivo na sua regra de firewall ao [utilizar apenas os endereços IP de saída que a aplicação utiliza](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Criar a base de dados do WordPress

```azurecli-interactive
az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name wordpress
```

Quando a base de dados tiver sido criada, o Cloud Shell mostra informações semelhantes às do exemplo abaixo:

<pre>
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
</pre>

### <a name="configure-database-variables-in-wordpress"></a>Configurar variáveis de base de dados no WordPress

Para ligar a aplicação WordPress a este novo servidor MySQL, irá configurar algumas variáveis de ambiente específico do WordPress, incluindo o caminho de AC de SSL definido por `MYSQL_SSL_CA`. O [Baltimore CyberTrust Root](https://www.digicert.com/digicert-root-certificates.htm) da [DigiCert](https://www.digicert.com/) é fornecida na [imagem personalizada](#use-a-custom-image-for-mysql-ssl-and-other-configurations) abaixo.

Para fazer estas alterações, utilize o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell. As definições da aplicação são sensíveis a maiúsculas e minúsculas e são separadas por espaços.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WORDPRESS_DB_HOST="<mysql-server-name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql-server-name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Quando a definição de aplicação tiver sido criada, o Cloud Shell mostra informações semelhantes ao seguinte exemplo:

<pre>
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "&lt;mysql-server-name&gt;.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@&lt;mysql-server-name&gt;"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
</pre>

Para obter mais informações sobre variáveis ambientais, consulte [variáveis ambientais configure.](configure-custom-container.md#configure-environment-variables)

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>Utilizar uma imagem personalizada para SSL do MySQL e outras configurações

Por predefinição, o SSL é utilizado pela Base de Dados do Azure para MySQL. O WordPress exige uma configuração adicional para utilizar o SSL com o MySQL. A 'imagem oficial' do WordPress não fornece a configuração adicional, mas foi preparada uma [imagem personalizada](https://github.com/Azure-Samples/multicontainerwordpress) para a sua conveniência. Na prática, estaria a adicionar as alterações pretendidas à sua própria imagem.

A imagem personalizada baseia-se na "imagem oficial" do [WordPress do Hub do Docker](https://hub.docker.com/_/wordpress/). Foram feitas as seguintes alterações nesta imagem personalizada para a Base de Dados do Azure para MySQL:

* [Adiciona o ficheiro de certificado Baltimore Cyber Trust Root para SSL ao MySQL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [Utiliza a Definição da Aplicação para o Certificado SSL do MySQL da Autoridade de certificação no wp-config.php do WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [Adiciona a definição do WordPress para MYSQL_CLIENT_FLAGS necessária para o SSL do MySQL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

Foram feitas as seguintes alterações para o Redis (a ser utilizado numa secção posterior):

* [Adiciona a extensão PHP para o Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Adiciona o comando unzip necessário para a extração de ficheiros.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Adiciona o plug-in Redis Object Cache 1.3.8 do WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Utiliza a Definição da Aplicação para o nome do anfitrião do Redis no wp-config.php do WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Para utilizar a imagem personalizada, deverá atualizar o ficheiro docker-compose-wordpress.yml. No Cloud Shell, escreva `nano docker-compose-wordpress.yml` para abrir o editor de texto nano. Alterar a `image: wordpress` para utilizar `image: microsoft/multicontainerwordpress`. Já não precisa do contentor de base de dados. Remova as secções `db`, `environment`, `depends_on` e `volumes` do ficheiro de configuração. O seu ficheiro deverá ser semelhante ao seguinte código:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

Guarde as alterações e feche o nano. Utilize o comando `^O` para guardar e `^X` para sair.

### <a name="update-app-with-new-configuration"></a>Atualizar a aplicação com uma nova configuração

No Cloud Shell, reconfigure a [aplicação Web](overview.md) de vários contentores com o comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Não se esqueça de substituir _\<app-name>_ pelo nome da aplicação web que criou anteriormente.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Quando a aplicação tiver sido reconfigurada, o Cloud Shell mostra informações semelhantes ao seguinte exemplo:

<pre>
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Navegar para a aplicação

Navegue para a aplicação implementada em (`http://<app-name>.azurewebsites.net`). A aplicação está agora a utilizar a Base de Dados do Azure para MySQL.

![Aplicação com vários contentores de exemplo na Aplicação Web para Contentores][1]

## <a name="add-persistent-storage"></a>Adicionar armazenamento persistente

Os seus vários contentores estão agora em execução na Aplicação Web para Contentores. No entanto, se instalar o WordPress agora e reiniciar a aplicação mais tarde, irá ver que a sua instalação do WordPress foi removida. Isto acontece porque a configuração do Docker Compose está atualmente a apontar para uma localização de armazenamento no interior do contentor. Os ficheiros instalados no seu contentor não persistem para além do reinício da aplicação. Nesta secção, irá [adicionar armazenamento persistente](configure-custom-container.md#use-persistent-shared-storage) ao seu recipiente WordPress.

### <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

Para utilizar o armazenamento persistente, ativará esta definição dentro do Serviço de Aplicações. Para fazer esta alteração, utilize o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell. As definições da aplicação são sensíveis a maiúsculas e minúsculas e são separadas por espaços.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Quando a definição de aplicação tiver sido criada, o Cloud Shell mostra informações semelhantes ao seguinte exemplo:

<pre>
[
  &lt; JSON data removed for brevity. &gt;
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
</pre>

### <a name="modify-configuration-file"></a>Modificar o ficheiro de configuração

No Cloud Shell, escreva `nano docker-compose-wordpress.yml` para abrir o editor de texto nano.

A opção `volumes` mapeia o sistema de ficheiros para um diretório dentro do contentor. `${WEBAPP_STORAGE_HOME}` é uma variável de ambiente no Serviço de Aplicações, que está mapeado para um armazenamento persistente para a sua aplicação. Irá utilizar esta variável de ambiente na opção volumes para que os ficheiros do WordPress sejam instalados no armazenamento persistente em vez de no contentor. Efetue as seguintes alterações ao ficheiro:

Na secção `wordpress`, adicione uma opção de `volumes` para que se pareça com o seguinte código:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>Atualizar a aplicação com uma nova configuração

No Cloud Shell, reconfigure a [aplicação Web](overview.md) de vários contentores com o comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Não se esqueça de substituir _\<app-name>_ por um nome de aplicação único.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Após a execução do comando, mostra um resultado semelhante ao seguinte exemplo:

<pre>
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Navegar para a aplicação

Navegue para a aplicação implementada em (`http://<app-name>.azurewebsites.net`).

O contentor do WordPress está agora a utilizar a Base de Dados do Azure para MySQL e o armazenamento persistente.

## <a name="add-redis-container"></a>Adicionar o contentor do Redis

 A "imagem oficial" do WordPress não inclui as dependências para Redis. Estas dependências e a configuração adicional necessária para utilizar o Redis com o WordPress foram preparadas para si nesta [imagem personalizada](https://github.com/Azure-Samples/multicontainerwordpress). Na prática, estaria a adicionar as alterações pretendidas à sua própria imagem.

A imagem personalizada baseia-se na "imagem oficial" do [WordPress do Hub do Docker](https://hub.docker.com/_/wordpress/). Foram feitas as seguintes alterações nesta imagem personalizada para Redis:

* [Adiciona a extensão PHP para o Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Adiciona o comando unzip necessário para a extração de ficheiros.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Adiciona o plug-in Redis Object Cache 1.3.8 do WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Utiliza a Definição da Aplicação para o nome do anfitrião do Redis no wp-config.php do WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Adicione o contentor do Redis ao final do ficheiro de configuração para que tenha um aspeto semelhante ao do exemplo abaixo:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always

   redis:
     image: redis:3-alpine
     restart: always
```

### <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

Para utilizar o Redis, terá de ativar a definição `WP_REDIS_HOST` no Serviço de Aplicações. Esta é uma *definição necessária* para o WordPress comunicar com o anfitrião do Redis. Para fazer esta alteração, utilize o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell. As definições da aplicação são sensíveis a maiúsculas e minúsculas e são separadas por espaços.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WP_REDIS_HOST="redis"
```

Quando a definição de aplicação tiver sido criada, o Cloud Shell mostra informações semelhantes ao seguinte exemplo:

<pre>
[
  &lt; JSON data removed for brevity. &gt;
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@&lt;mysql-server-name&gt;"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
</pre>

### <a name="update-app-with-new-configuration"></a>Atualizar a aplicação com uma nova configuração

No Cloud Shell, reconfigure a [aplicação Web](overview.md) de vários contentores com o comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Não se esqueça de substituir _\<app-name>_ por um nome de aplicação único.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Após a execução do comando, mostra um resultado semelhante ao seguinte exemplo:

<pre>
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Navegar para a aplicação

Navegue para a aplicação implementada em (`http://<app-name>.azurewebsites.net`).

Conclua os passos e instale o WordPress.

### <a name="connect-wordpress-to-redis"></a>Ligar o WordPress ao Redis

Inscreva-se na administração WordPress. Na navegação à esquerda, selecione **Plugins** e, em seguida, **selecione Plugins instalados** .

![Selecionar os Plug-ins do WordPress][2]

Mostrar todos os plug-ins aqui

Na página de plug-ins, localize **Redis Object Cache** e clique em **Activate** (Ativar).

![Ativar o Redis][3]

Clique em **Definições** .

![Clicar em Definições][4]

Clique no botão **Cache de Objeto ativado.**

![Clicar no botão "Enable Object Cache" (Ativar a Cache de Objeto).][5]

O WordPress é ligado ao servidor do Redis. O **estado** da ligação aparece na mesma página.

![O WordPress é ligado ao servidor do Redis. O **estado** da ligação aparece na mesma página.][6]

**Parabéns** , ligou o WordPress ao Redis. A aplicação pronta para produção está agora a utilizar a **Base de Dados do Azure para MySQL, o armazenamento persistente e o Redis** . Pode agora aumentar o seu plano do Serviço de Aplicações para múltiplas instâncias.

## <a name="find-docker-container-logs"></a>Localizar registos de Contentor do Docker

Caso se depare com problemas ao utilizar vários contentores, pode aceder aos registos de contentor ao navegar até: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Irá ver um resultado semelhante ao do exemplo abaixo:

<pre>
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://&lt;app-name&gt;.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
</pre>

Pode ver um registo para cada contentor e um registo adicional para o processo principal. Copie o respetivo valor `href` para o browser para ver o registo.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> * Converter uma configuração do Docker Compose para funcionar com a Aplicação Web para Contentores
> * Implementar uma aplicação com vários contentores no Azure
> * Adicionar definições da aplicação
> * Utilizar o armazenamento persistente para os contentores
> * Ligar à Base de Dados do Azure para MySQL
> * Resolver erros

Avance para o próximo tutorial para aprender a mapear um nome DNS personalizado para a sua aplicação.

> [!div class="nextstepaction"]
> [Tutorial: Mapeie o nome DNS personalizado para a sua aplicação](app-service-web-tutorial-custom-domain.md)

Ou, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar um contentor personalizado](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
