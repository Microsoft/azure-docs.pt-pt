---
title: Criar e gerenciar pontos de extremidade e regras de serviço VNet do banco de dados do Azure para MySQL usando o CLI do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerenciar pontos de extremidade e regras de serviço VNet do banco de dados do Azure para MySQL usando CLI do Azure linha de comando.
author: bolzmj
ms.author: mbolz
manager: jhubbard
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: d08b99c0a668286d9e9b94f2229915be8ff106f0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610399"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Criar e gerenciar pontos de extremidade de serviço VNet do banco de dados do Azure para MySQL usando o CLI do Azure
Os pontos finais e as regras de serviços da Rede Virtual (VNet) expandem o espaço do endereço privado de uma Rede Virtual ao seu servidor da Base de Dados do Azure para MySQL. Usando comandos convenientes da CLI (interface de linha de comando) do Azure, você pode criar, atualizar, excluir, listar e mostrar os pontos de extremidade e as regras do serviço VNet para gerenciar o servidor. Para obter uma visão geral dos pontos de extremidade do serviço VNet do banco de dados do Azure para MySQL, incluindo limitações, consulte [pontos de extremidade do serviço vnet do banco de dados do Azure para MySQL Server](concepts-data-access-and-security-vnet.md). Pontos de extremidade de serviço de VNet estão disponíveis em todas as regiões com suporte para o banco de dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- Instale [o CLI do Azure](/cli/azure/install-azure-cli) ou use o Azure cloud Shell no navegador.
- Uma [base de dados do Azure para servidor MySQL e base de dados](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Suporte para pontos finais de serviço da VNet é apenas para fins gerais e memória otimizada de servidores.
> No caso de emparelhamento VNet, se o tráfego estiver fluindo por um gateway de VNet comum com pontos de extremidade de serviço e for supostamente fluir para o par, crie uma regra de ACL/VNet para permitir que as máquinas virtuais do Azure na VNet do gateway acessem o servidor de banco de dados do Azure para MySQL.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Configurar pontos de extremidade de serviço de vnet para o banco de dados do Azure para MySQL
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
> É altamente recomendável ler este artigo sobre as configurações e considerações do ponto de extremidade de serviço antes de executar o script de exemplo abaixo ou configurar pontos de extremidades de serviço. **Ponto de extremidade de serviço de rede virtual:** Um [ponto de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Os pontos de extremidade dos serviços de VNet usam o nome do tipo de serviço **Microsoft. SQL**, que se refere ao serviço do Azure denominado Banco de dados SQL. Essa marca de serviço também se aplica ao banco de dados SQL do Azure, ao banco de dados do Azure para PostgreSQL e aos serviços MySQL. É importante observar ao aplicar a marca de serviço **Microsoft. SQL** a um ponto de extremidade de serviço VNet que configura o tráfego de ponto de extremidade de serviço para todos os serviços de banco de dados do Azure, incluindo o banco de dados SQL do Azure, banco de dados do Azure para PostgreSQL e banco de dados Servidores MySQL na sub-rede. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Script de exemplo para criar um banco de dados do Azure para MySQL, criar uma VNet, um ponto de extremidade de serviço de VNet e proteger o servidor para a sub-rede com uma regra de VNet
Neste script de exemplo, altere as linhas realçadas para personalizar o nome de utilizador administrador e a palavra-passe. Substitua a SubscriptionId usada no `az account set --subscription` comando pelo seu próprio identificador de assinatura.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md

