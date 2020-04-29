---
title: Gerir pontos finais VNet - Azure CLI - Base de Dados Azure para MariaDB
description: Este artigo descreve como criar e gerir a Base de Dados Azure para os pontos finais do serviço MariaDB VNet e as regras utilizando a linha de comando Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 46bfab6935d08ac28ced7f392892ade6f68a0492
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530857"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Crie e gerea Base de Dados Azure para os pontos finais do serviço MariaDB VNet utilizando o Azure CLI

Os pontos finais e as regras dos serviços da Rede Virtual (VNet) expandem o espaço do endereço privado de uma Rede Virtual para o servidor do Azure Database for MariaDB. Utilizando comandos convenientes da Interface da Linha de Comando Azure (CLI), pode criar, atualizar, excluir, listar e mostrar pontos finais e regras de serviço VNet para gerir o servidor. Para uma visão geral da Base de Dados Azure para os pontos finais do serviço MariaDB VNet, incluindo limitações, consulte a [Base de Dados Azure para os pontos finais](concepts-data-access-security-vnet.md)do serviço VNet do Servidor MariaDB . Os pontos finais do serviço VNet estão disponíveis em todas as regiões suportadas para a Base de Dados Azure para MariaDB.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar, você precisa:
- Instale [o Azure CLI](/cli/azure/install-azure-cli) ou utilize a Cloud Shell Azure no navegador.
- Uma [Base de Dados Azure para servidor MariaDB e base de dados.](quickstart-create-mariadb-server-database-using-azure-cli.md)

> [!NOTE]
> O suporte para os pontos finais do serviço VNet destina-se apenas a servidores otimizados para fins gerais e memória.

## <a name="configure-vnet-service-endpoints"></a>Configure pontos finais do serviço VNet
Os comandos vnet da [rede Az](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) são usados para configurar redes virtuais.

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

Se estiver a executar a CLI localmente, tem de iniciar sessão na sua conta através do comando [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.
```azurecli-interactive
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Substitua a propriedade **id** da saída **az login** da sua subscrição no marcador de posição de id de subscrição.

- A conta deve ter as permissões necessárias para criar uma rede virtual e o ponto final de serviço.

Os pontos finais do serviço podem ser configurados em redes virtuais de forma independente, por um utilizador com acesso por escrito à rede virtual.

Para garantir os recursos de serviço do Azure a um VNet, o utilizador deve ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" para que as subredes sejam adicionadas. Esta permissão está incluída por predefinição nas funções incorporadas de administrador de serviço e podem ser modificadas mediante a criação de funções personalizadas.

Saiba mais sobre [funções incorporadas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e a atribuição de permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

As VNets e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Se os recursos de serviço VNet e Azure estiverem em subscrições diferentes, os recursos devem estar sob o mesmo inquilino de Diretório Ativo (AD). Certifique-se de que ambas as subscrições têm o fornecedor de recursos **Microsoft.Sql** registado. Para mais informações consulte o [registo de recursos-gestor][resource-manager-portal]

> [!IMPORTANT]
> É altamente recomendável ler este artigo sobre configurações e considerações de pontofinal de serviço antes de configurar pontos finais do serviço. Ponto final do serviço de **rede virtual:** Um ponto final de [serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma subnet cujos valores de propriedade incluem um ou mais nomes formais do tipo de serviço Azure. Os pontos finais dos serviços VNet utilizam o nome de tipo de serviço **Microsoft.Sql,** que se refere ao serviço Azure chamado Base de Dados SQL. Esta etiqueta de serviço também se aplica aos serviços Azure SQL Database, Azure Database para MariaDB, PostgreSQL e MySQL. É importante notar que ao aplicar a etiqueta de serviço **Microsoft.Sql** a um ponto final do serviço VNet, ele configura o tráfego de ponto final do serviço para todos os serviços da Base de Dados Azure, incluindo a Base de Dados Azure SQL, Base de Dados Azure para PostgreSQL, Base de Dados Azure para MariaDB e Base de Dados Azure para servidores MySQL na subnet.

### <a name="sample-script"></a>Script de exemplo

Este script de amostra é usado para criar uma Base de Dados Azure para o servidor MariaDB, criar um ponto final de serviço VNet, VNet e fixar o servidor na subnet com uma regra VNet. Neste script de amostra, altere o nome de utilizador e a palavra-passe do administrador. Substitua o ID `az account set --subscription` de subscrição utilizado no comando pelo seu próprio identificador de subscrição.

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