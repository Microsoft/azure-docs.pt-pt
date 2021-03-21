---
title: 'Tutorial: Criar base de dados Azure para PostgreSQL - Servidor Flexível e App Azure App Web app na mesma rede virtual'
description: Quickstart guia para criar base de dados Azure para PostgreSQL - Servidor Flexível com Web App em uma rede virtual
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ff9af90ca0b6b80ffece5ccd7d919c1d93e210c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657591"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Tutorial: Criar uma base de dados Azure para PostgreSQL - Servidor Flexível com App Services Web App em rede virtual

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Este tutorial mostra-lhe como criar uma aplicação Web do Azure App Service com Base de Dados Azure para PostgreSQL - Servidor Flexível (Pré-visualização) dentro de uma [rede Virtual](../../virtual-network/virtual-networks-overview.md).

Neste tutorial, vai aprender a:
>[!div class="checklist"]
> * Criar um servidor flexível PostgreSQL numa rede virtual
> * Criar uma sub-rede para delegar no Serviço de Aplicações
> * Criar uma aplicação Web
> * Adicione a aplicação web à rede virtual
> * Conecte-se a Postgres a partir da aplicação web 

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo requer que esteja a executar a versão 2.0 do Azure CLI ou mais tarde localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Terá de iniciar sessão na sua conta utilizando o comando [de login az.](/cli/azure/authenticate-azure-cli) Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.

```azurecli
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](/cli/azure/account). Substitua a propriedade de **ID** por subscrição da saída **de login az** para a sua subscrição no espaço reservado de iD de subscrição.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Criar um Servidor Flexível PostgreSQL numa nova rede virtual

Criar um servidor flexível privado dentro de uma rede virtual (VNET) utilizando o seguinte comando:
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
Este comando executa as seguintes ações, que podem demorar alguns minutos:

- Crie o grupo de recursos se já não existir.
- Gera um nome de servidor se não for fornecido.
- Crie uma nova rede virtual para o seu novo servidor postgreSQL. Tome nota do nome da rede virtual e nome da sub-rede criado para o seu servidor, uma vez que precisa de adicionar a aplicação web à mesma rede virtual.
- Cria nome de utilizador de administração, palavra-passe para o seu servidor se não for fornecido.
- Cria uma base de dados vazia chamada **postgres**

> [!NOTE]
> - Tome nota da sua palavra-passe que será gerada para si se não for fornecida. Se esquecer a palavra-passe, terá de redefinir a palavra-passe usando o ``` az postgres flexible-server update``` comando
> - Se não estiver a utilizar o App Service Environment, terá de ativar o acesso a partir de quaisquer IPs Azure que utilizem este comando. 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```

## <a name="create-subnet-for-app-service-endpoint"></a>Criar sub-rede para ponto final de serviço de aplicações
Precisamos agora de ter uma sub-rede que seja delegada no ponto final da App Service Web App. Executar o seguinte comando para criar uma nova sub-rede na mesma rede virtual que o servidor de base de dados foi criado. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
Tome nota do nome da rede virtual e nome da sub-rede após este comando, pois precisaria de adicionar a regra de integração VNET para a aplicação web após a sua criação. 

## <a name="create-a-web-app"></a>Criar uma aplicação Web
Nesta secção, cria-se um anfitrião de aplicações na aplicação App Service, liga esta aplicação à base de dados postgres e, em seguida, implementa o seu código para esse anfitrião. Certifique-se de que está na raiz do repositório do seu código de aplicação no terminal. Nota Plano Básico não suporta a integração do VNET. Por favor, use Standard ou Premium. 

Criar uma aplicação de Serviço de Aplicações (o processo de anfitrião) com o comando az webapp up

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - Para o argumento da localização, utilize o mesmo local que fez para a base de dados na secção anterior.
> - Substitua <> de nome de aplicações por um nome único em todo o Azure (o ponto final do servidor é https:// \<app-name> .azurewebsites.net). Os caracteres permitidos para <> de nome de aplicações são A-Z, 0-9 e -. Um bom padrão é usar uma combinação do nome da sua empresa e um identificador de aplicativos.

Este comando executa as seguintes ações, que podem demorar alguns minutos:

- Crie o grupo de recursos se já não existir. (Neste comando utiliza-se o mesmo grupo de recursos em que criou a base de dados anteriormente.)
- Crie a aplicação De Serviço de Aplicações se não existir.
- Ativar o registo predefinido para a aplicação, se ainda não estiver ativado.
- Faça o upload do repositório utilizando a implementação ZIP com automatização de construção ativada.

## <a name="add-the-web-app-to-the-virtual-network"></a>Adicione a Web App à rede virtual
Use o comando **de integração de vnets az webapp** para adicionar uma integração regional de rede virtual a um webapp. Substitua <> de nome vnet e <> pelo nome de rede virtual e nome da sub-rede que o servidor flexível está a utilizar.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Configure variáveis ambientais para ligar a base de dados
Com o código agora implementado para o Serviço de Aplicações, o próximo passo é ligar a aplicação ao servidor flexível em Azure. O código da aplicação espera encontrar informações sobre bases de dados em várias variáveis ambientais. Para definir variáveis ambientais no Serviço de Aplicações, cria "definições de aplicações" com o ```az webapp config appsettings``` comando definido.

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- Substitua ```postgres-server-name``` , ```username``` para o ```password``` recém-criado comando de servidor flexível.
- Substitua <username> e <password> pelas credenciais que o comando também gerou para si.
- O grupo de recursos e o nome da aplicação são extraídos dos valores em cache no ficheiro .azure/config.
- O comando cria configurações chamadas ```DBHOST``` ```DBNAME``` , , e ```DBUSER``` ```DBPASS``` . Se o seu código de aplicação estiver a utilizar um nome diferente para as informações da base de dados, utilize esses nomes para as definições da aplicação, conforme mencionado no código.

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe todos os recursos que criou no tutorial usando o seguinte comando. Este comando elimina todos os recursos deste grupo de recursos.

```azurecli
az group delete -n myresourcegroup
```


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Mapeie um nome DNS personalizado existente para o Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
