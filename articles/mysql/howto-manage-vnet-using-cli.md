---
title: Gerir pontos finais VNet - Azure CLI - Azure Database for MySQL
description: Este artigo descreve como criar e gerir a Base de Dados Azure para os pontos finais e regras do serviço MySQL VNet utilizando a linha de comando Azure CLI.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 3c4dced2177252daedc36c5a78707e47cf194d77
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118771"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Criar e gerir a Base de Dados Azure para os pontos finais do serviço MySQL VNet utilizando o Azure CLI
Os pontos finais e as regras de serviços da Rede Virtual (VNet) expandem o espaço do endereço privado de uma Rede Virtual ao seu servidor da Base de Dados do Azure para MySQL. Utilizando os convenientes comandos Azure Command Line Interface (CLI), pode criar, atualizar, eliminar, listar e mostrar pontos finais e regras de serviço VNet para gerir o seu servidor. Para obter uma visão geral da Base de Dados Azure para os pontos finais do serviço MySQL VNet, incluindo limitações, consulte [a Base de Dados Azure para os pontos finais do serviço VNet do MySQL Server](concepts-data-access-and-security-vnet.md). Os pontos finais do serviço VNet estão disponíveis em todas as regiões suportadas para a Base de Dados Azure para o MySQL.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- Instale [o Azure CLI](/cli/azure/install-azure-cli) ou utilize o Azure Cloud Shell no navegador.
- Uma [base de dados Azure para servidor e base de dados MySQL](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> O suporte para os pontos finais do serviço VNet é apenas para servidores otimizados para fins gerais e memória.
> Em caso de observação de VNet, se o tráfego estiver a fluir através de um VNet Gateway comum com pontos finais de serviço e for suposto fluir para o par, por favor crie uma regra ACL/VNet para permitir que as Máquinas Virtuais Azure no Gateway VNet acedam à Base de Dados Azure para o servidor MySQL.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Configurar pontos finais de serviço Vnet para Azure Database para MySQL
Os comandos [vnet da rede az](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) são utilizados para configurar redes virtuais.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

Se estiver a executar a CLI localmente, tem de iniciar sessão na sua conta através do comando [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.
```azurecli-interactive
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Substitua a propriedade **id** da saída **az login** da sua subscrição no marcador de posição de id de subscrição.

- A conta deve ter as permissões necessárias para criar uma rede virtual e o ponto final de serviço.

Os pontos finais de serviço podem ser configurados em redes virtuais de forma independente, por um utilizador com acesso por escrito à rede virtual.

Para garantir os recursos de serviço da Azure a um VNet, o utilizador deve ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" para a adição das sub-redes. Esta permissão está incluída por predefinição nas funções incorporadas de administrador de serviço e podem ser modificadas mediante a criação de funções personalizadas.

Saiba mais sobre [funções incorporadas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e a atribuição de permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

As VNets e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Se os recursos de serviço VNet e Azure estiverem em diferentes subscrições, os recursos devem estar sob o mesmo inquilino ative directy (AD). Certifique-se de que ambas as subscrições têm o fornecedor de recursos **Microsoft.Sql** registado. Para mais informações, consulte [o gestor de recursos-registo][resource-manager-portal]

> [!IMPORTANT]
> É altamente recomendado ler este artigo sobre configurações e considerações de ponto final de serviço antes de executar o script da amostra abaixo, ou configurar pontos finais de serviço. **Ponto final do serviço de rede virtual:** Um [ponto final de serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço Azure. Os pontos finais dos serviços VNet utilizam o nome de tipo de serviço **Microsoft.Sql,** que se refere ao serviço Azure denominado SQL Database. Esta etiqueta de serviço também se aplica à Base de Dados Azure SQL, Base de Dados Azure para os serviços PostgreSQL e MySQL. É importante notar que ao aplicar a etiqueta de serviço **Microsoft.Sql** a um ponto final do serviço VNet configura o tráfego de ponto final de serviço para todos os serviços da Azure Database, incluindo a Base de Dados Azure SQL, Base de Dados Azure para PostgreSQL e Base de Dados Azure para servidores MySQL na sub-rede. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Script de amostra para criar uma base de dados Azure para base de dados MySQL, criar um ponto final de serviço VNet, VNet e fixar o servidor à sub-rede com uma regra VNet
Neste script de exemplo, altere as linhas realçadas para personalizar o nome de utilizador administrador e a palavra-passe. Substitua o SubscriçãoID utilizado no `az account set --subscription` comando pelo seu próprio identificador de subscrição.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md

