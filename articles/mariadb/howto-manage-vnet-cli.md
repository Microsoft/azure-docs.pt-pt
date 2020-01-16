---
title: Gerenciar pontos de extremidade de VNet-CLI do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como criar e gerenciar os pontos de extremidade do serviço VNet MariaDB e as regras do banco de dados do Azure usando CLI do Azure linha de comando.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9f15c8230c2dad558e9a125ebe6874a7429f3488
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965912"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Criar e gerenciar pontos de extremidade de serviço VNet do banco de dados do Azure para MariaDB usando o CLI do Azure

Os pontos finais e as regras dos serviços da Rede Virtual (VNet) expandem o espaço do endereço privado de uma Rede Virtual para o servidor do Azure Database for MariaDB. Usando comandos convenientes da CLI (interface de linha de comando) do Azure, você pode criar, atualizar, excluir, listar e mostrar os pontos de extremidade e as regras do serviço VNet para gerenciar o servidor. Para obter uma visão geral do banco de dados do Azure para pontos de extremidade de serviço VNet do MariaDB, incluindo limitações, consulte [pontos de extremidade do serviço vnet do banco de dados do Azure para MariaDB Server](concepts-data-access-security-vnet.md). Pontos de extremidade de serviço de VNet estão disponíveis em todas as regiões com suporte para o banco de dados do Azure para MariaDB.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- Instale [o CLI do Azure](/cli/azure/install-azure-cli) ou use o Azure cloud Shell no navegador.
- Um [banco de dados do Azure para servidor MariaDB e banco de dados](quickstart-create-mariadb-server-database-using-azure-cli.md).

> [!NOTE]
> Suporte para pontos finais de serviço da VNet é apenas para fins gerais e memória otimizada de servidores.

## <a name="configure-vnet-service-endpoints"></a>Configurar pontos de extremidade de serviço de VNet
Os comandos [AZ Network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) são usados para configurar redes virtuais.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

Se estiver a executar a CLI localmente, tem de iniciar sessão na sua conta através do comando [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.
```azurecli-interactive
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Substitua a propriedade **id** da saída **az login** da sua subscrição no marcador de posição de id de subscrição.

- A conta deve ter as permissões necessárias para criar uma rede virtual e um ponto de extremidade de serviço.

Os pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente, por um usuário com acesso de gravação à rede virtual.

Para proteger os recursos de serviço do Azure para uma VNet, o usuário deve ter permissão para "Microsoft. Network/virtualNetworks/sub-redes/joinViaServiceEndpoint/" para as sub-redes que estão sendo adicionadas. Esta permissão está incluída por predefinição nas funções incorporadas de administrador de serviço e podem ser modificadas mediante a criação de funções personalizadas.

Saiba mais sobre [funções incorporadas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e a atribuição de permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

As VNets e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Se os recursos de serviço da VNet e do Azure estiverem em assinaturas diferentes, os recursos deverão estar no mesmo locatário do Active Directory (AD). Certifique-se de que ambas as assinaturas tenham o provedor de recursos **Microsoft. SQL** registrado. Para obter mais informações, consulte [Resource-Manager-Registration][resource-manager-portal]

> [!IMPORTANT]
> É altamente recomendável ler este artigo sobre as configurações e considerações do ponto de extremidade de serviço antes de configurar pontos de extremidades de serviço. **Ponto de extremidade de serviço de rede virtual:** Um [ponto de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Os pontos de extremidade dos serviços de VNet usam o nome do tipo de serviço **Microsoft. SQL**, que se refere ao serviço do Azure denominado Banco de dados SQL. Essa marca de serviço também se aplica ao banco de dados SQL do Azure, ao banco de dados do Azure para serviços MariaDB, PostgreSQL e MySQL. É importante observar ao aplicar a marca de serviço **Microsoft. SQL** a um ponto de extremidade de serviço VNet que configura o tráfego de ponto de extremidade de serviço para todos os serviços de banco de dados do Azure, incluindo o banco de dados SQL do Azure, banco de dados do Azure para PostgreSQL, banco de dados do Azure para MariaDB e servidores do banco de dados do Azure para MySQL

### <a name="sample-script"></a>Script de exemplo

Este script de exemplo é usado para criar um banco de dados do Azure para o MariaDB Server, criar uma VNet, um ponto de extremidade de serviço de VNet e proteger o servidor para a sub-rede com uma regra de VNet. Neste script de exemplo, altere o nome de usuário e a senha do administrador. Substitua a SubscriptionId usada no comando `az account set --subscription` pelo seu próprio identificador de assinatura.

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

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md