---
title: Criar e gerir a base de dados para pontos finais de serviço de MariaDB VNet e regras com a CLI do Azure | Documentos da Microsoft
description: Este artigo descreve como criar e gerir a base de dados para pontos finais de serviço de MariaDB VNet e regras utilizando a linha de comandos da CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: a86b755770dc59f196c57f1d86e7f29200ce25e3
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962065"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Criar e gerir a base de dados para pontos finais de serviço de VNet da MariaDB com a CLI do Azure

Pontos finais de serviços de rede (VNet) virtual e regras de estendem o espaço de endereços privados de uma rede Virtual para a base de dados do Azure para MariaDB server. Utilizar comandos de Interface de linha de comandos (CLI do Azure) conveniente, pode criar, atualizar, eliminar, listar e Mostrar pontos finais de serviço de VNet e regras para gerir o seu servidor. Para uma visão geral da base de dados do Azure para MariaDB VNet pontos finais de serviço, incluindo as limitações, consulte [base de dados do Azure para pontos finais de serviço de VNet do servidor de MariaDB](concepts-data-access-security-vnet.md). Pontos finais de serviço de VNet estão disponíveis em todas as regiões suportadas para a base de dados do Azure para MariaDB.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- Instale [a CLI do Azure](/cli/azure/install-azure-cli) ou utilizar o Azure Cloud Shell no browser.
- Uma [base de dados do Azure para MariaDB servidor e base de dados](quickstart-create-mariadb-server-database-using-azure-cli.md).

> [!NOTE]
> Suporte para pontos finais de serviço da VNet é apenas para fins gerais e memória otimizada de servidores.

## <a name="configure-vnet-service-endpoints"></a>Configurar pontos finais de serviço de VNet
O [vnet de rede de az](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) comandos são utilizados para configurar redes virtuais.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

Se estiver a executar a CLI localmente, tem de iniciar sessão na sua conta através do comando [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.
```azurecli-interactive
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Substitua a propriedade **id** da saída **az login** da sua subscrição no marcador de posição de id de subscrição.

- A conta tem de ter as permissões necessárias para criar uma rede virtual e o ponto final de serviço.

Pontos finais de serviço podem ser configurados em redes virtuais de forma independente por um utilizador com acesso de escrita para a rede virtual.

Para proteger os recursos de serviço do Azure a uma VNet, o utilizador tem de ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" para as sub-redes que está a ser adicionadas. Esta permissão está incluída por predefinição nas funções incorporadas de administrador de serviço e podem ser modificadas mediante a criação de funções personalizadas.

Saiba mais sobre [funções incorporadas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e a atribuição de permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

As VNets e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Se os recursos de serviço de VNet e o Azure estão em subscrições diferentes, os recursos devem existir no mesmo inquilino do Active Directory (AD).

> [!IMPORTANT]
> É altamente recomendado para ler este artigo sobre considerações e configurações de ponto final de serviço antes de configurar pontos finais de serviço. **Endpoint de serviço de rede virtual:** R [ponto final de serviço de rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes de tipo de serviço do Azure formal. Pontos de extremidade de serviços de VNet utilizam o nome do tipo de serviço **Microsoft. SQL**, que faz referência ao serviço do Azure com o nome da base de dados SQL. Esta etiqueta de serviço também se aplica a SQL Database do Azure, base de dados do Azure para serviços MariaDB, PostgreSQL e MySQL. É importante ter em conta ao aplicar a **Microsoft. SQL** etiqueta de serviço para um ponto de extremidade do serviço de VNet configura o tráfego de ponto final de serviço para todos os serviços de base de dados do Azure, incluindo o SQL Database do Azure, base de dados do Azure para PostgreSQL, Base de dados do Azure para MariaDB e base de dados do Azure para MySQL servidores na sub-rede.

### <a name="sample-script"></a>Script de exemplo

Este script de exemplo é utilizado para criar uma base de dados do Azure para MariaDB server, a criar uma VNet, o ponto final de serviço de VNet e a proteger o servidor para a sub-rede com uma regra de VNet. Este script de exemplo, altere o nome de utilizador administrador e a palavra-passe. Substitua o SubscriptionID utilizado na `az account set --subscription` comando com o seu próprio identificador de subscrição.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Limpar a implementação
Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->
