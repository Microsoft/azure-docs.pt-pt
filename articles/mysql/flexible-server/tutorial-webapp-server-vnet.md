---
title: 'Tutorial: Criar base de dados Azure para o MySQL Flexible Server (Preview) e Azure App Web Service Na mesma rede virtual'
description: Quickstart guia para criar base de dados Azure para MySQL Flexible Server (Preview) com Web App em uma rede virtual
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3e334eda46e5e67a0fc0755f5e02a0724d34a4b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657642"
---
# <a name="tutorial-create-an-azure-database-for-mysql---flexible-server-preview-with-app-services-web-app-in-virtual-network"></a>Tutorial: Criar uma base de dados Azure para MySQL - Servidor Flexível (Pré-visualização) com App Services Web App em rede virtual

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este tutorial mostra-lhe como criar uma App Web do Azure App Service Web app com o MySQL Flexible Server (Preview) dentro de uma [rede Virtual](../../virtual-network/virtual-networks-overview.md).

Neste tutorial, vai aprender a:
>[!div class="checklist"]
> * Criar um servidor flexível MySQL numa rede virtual
> * Criar uma sub-rede para delegar no Serviço de Aplicações
> * Criar uma aplicação Web
> * Adicione a aplicação web à rede virtual
> * Conecte-se a Postgres a partir da aplicação web 

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo requer que esteja a executar a versão 2.0 do Azure CLI ou mais tarde localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Terá de iniciar sessão na sua conta utilizando o comando [de login az.](/cli/azure/reference-index#az-login) Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.

```azurecli
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](/cli/azure/account). Substitua a propriedade de **ID** por subscrição da saída **de login az** para a sua subscrição no espaço reservado de iD de subscrição.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Criar uma base de dados Azure para o MySQL Flexible Server

Criar um servidor flexível privado dentro de uma rede virtual (VNET) utilizando o seguinte comando:
```azurecli
az mysql flexible-server create --resource-group myresourcegroup --location westus2
```
Copie a cadeia de ligação e o nome da rede virtual recém-criada. Este comando executa as seguintes ações, que podem demorar alguns minutos:

- Crie o grupo de recursos se já não existir.
- Gera um nome de servidor se não for fornecido.
- Crie uma nova rede virtual para o seu novo servidor MySQL. Tome nota do nome da rede virtual e nome da sub-rede criado para o seu servidor, uma vez que precisa de adicionar a aplicação web à mesma rede virtual.
- Cria nome de utilizador de administração, palavra-passe para o seu servidor se não for fornecido.
- Cria uma base de dados vazia chamada **flexibleserverdb**

> [!NOTE]
> Tome nota da sua palavra-passe que será gerada para si se não for fornecida. Se esquecer a palavra-passe, terá de redefinir a palavra-passe usando o ``` az mysql flexible-server update``` comando

## <a name="create-subnet-for-app-service-endpoint"></a>Criar sub-rede para ponto final de serviço de aplicações
Precisamos agora de ter uma sub-rede que seja delegada no ponto final da App Service Web App. Executar o seguinte comando para criar uma nova sub-rede na mesma rede virtual que o servidor de base de dados foi criado. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
Tome nota do nome da rede virtual e nome da sub-rede após este comando, pois precisaria de adicionar a regra de integração VNET para a aplicação web após a sua criação. 

## <a name="create-a-web-app"></a>Criar uma aplicação Web

Nesta secção, cria-se um anfitrião de aplicações na aplicação App Service e liga esta aplicação à base de dados MySQL. Certifique-se de que está na raiz do repositório do seu código de aplicação no terminal.

Criar uma aplicação de Serviço de Aplicações (o processo de anfitrião) com o comando az webapp up

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - Para o argumento da localização, utilize o mesmo local que fez para a base de dados na secção anterior.
> - Substitua _&lt; o nome da aplicação>_ por um nome único em todo o Azure (o ponto final do servidor é https:// \<app-name> .azurewebsites.net). Os caracteres permitidos para <> de nome de aplicações são A-Z, 0-9 e -. Um bom padrão é usar uma combinação do nome da sua empresa e um identificador de aplicativos.
> - O nível básico do Serviço de Aplicações não suporta a integração do VNET. Por favor, use Standard ou Premium. 

Este comando executa as seguintes ações, que podem demorar alguns minutos:

- Crie o grupo de recursos se já não existir. (Neste comando utiliza-se o mesmo grupo de recursos em que criou a base de dados anteriormente.)
- Crie o plano de Serviço de Aplicações ```testappserviceplan``` no nível básico de preços (B1), se não existir. ...plano e sku são opcionais.
- Crie a aplicação De Serviço de Aplicações se não existir.
- Ativar o registo predefinido para a aplicação, se ainda não estiver ativado.
- Faça o upload do repositório utilizando a implementação ZIP com automatização de construção ativada.

## <a name="add-the-web-app-to-the-virtual-network"></a>Adicione a aplicação web à rede virtual

Use o comando **de integração de vnets az webapp** para adicionar uma integração regional de rede virtual a um webapp. Substitua _&lt; o nome vnet>_ e nome da _&lt; sub-rede_ pelo nome de rede virtual e sub-rede que o servidor flexível está a usar.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Configure variáveis ambientais para ligar a base de dados

Com o código agora implementado para o Serviço de Aplicações, o próximo passo é ligar a aplicação ao servidor flexível em Azure. O código da aplicação espera encontrar informações sobre bases de dados em várias variáveis ambientais. Para definir variáveis ambientais no Serviço de Aplicações, cria "definições de aplicações" com o ```az webapp config appsettings``` comando definido.

```azurecli
az webapp config appsettings set --settings DBHOST="<mysql-server-name>.mysql.database.azure.com" DBNAME="flexibleserverdb" DBUSER="<username>" DBPASS="<password>"
```

- Substitua _&lt; o nome do mysql-servidor>_, nome de utilizador _&lt;>_ e>de _&lt; palavra-passe_ para o comando do servidor flexível recentemente criado.
- Substitua _&lt; o nome de utilizador>_ e>de _&lt; palavra-passe_ pelas credenciais que o comando também gerou para si.
- O grupo de recursos e o nome da aplicação são extraídos dos valores em cache no ficheiro .azure/config.
- O comando cria definições chamadas DBHOST, DBNAME, DBUSER e DBPASS. Se o seu código de aplicação estiver a utilizar um nome diferente para as informações da base de dados, utilize esses nomes para as definições da aplicação, conforme mencionado no código.

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe todos os recursos que criou no tutorial usando o seguinte comando. Este comando elimina todos os recursos deste grupo de recursos.

```azurecli
az group delete -n myresourcegroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mapeie um nome DNS personalizado existente para o Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
