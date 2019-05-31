---
title: Criar e gerir pontos finais de serviço de VNet e regras para base de dados do Azure para PostgreSQL - único servidor com a CLI do Azure
description: Este artigo descreve como criar e gerir pontos finais de serviço de VNet e regras para a base de dados do Azure para PostgreSQL com a linha de comandos da CLI do Azure.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: df2af0240852b23203e504d8233de4af48475438
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067574"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Criar e gerir pontos finais de serviço de VNet para a base de dados do Azure para PostgreSQL - único servidor com a CLI do Azure
Pontos finais de serviços de rede (VNet) virtual e regras de estendem o espaço de endereços privados de uma rede Virtual à sua base de dados do Azure para o servidor PostgreSQL. Utilizar comandos de Interface de linha de comandos (CLI do Azure) conveniente, pode criar, atualizar, eliminar, listar e Mostrar pontos finais de serviço de VNet e regras para gerir o seu servidor. Para uma visão geral da base de dados do Azure para PostgreSQL VNet pontos finais de serviço, incluindo as limitações, consulte [base de dados do Azure para pontos finais de serviço de VNet de servidor PostgreSQL](concepts-data-access-and-security-vnet.md). Pontos finais de serviço de VNet estão disponíveis em todas as regiões suportadas para a base de dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- Instale [a CLI do Azure](/cli/azure/install-azure-cli) ou utilizar o Azure Cloud Shell no browser.
- Uma [base de dados do Azure para PostgreSQL server e base de dados](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Suporte para pontos finais de serviço da VNet é apenas para fins gerais e memória otimizada de servidores.
> Em caso de VNet peering, se o tráfego flui através de um Gateway de VNet comuns com pontos finais de serviço e deve ser enviados para o elemento de rede, crie uma regra ACL/VNet para permitir a máquinas virtuais do Azure na VNet de Gateway para acessar o banco de dados do Azure para o servidor PostgreSQL.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Configurar pontos finais de serviço de Vnet para a base de dados do Azure para PostgreSQL
O [vnet de rede de az](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) comandos são utilizados para configurar redes virtuais.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

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
> É altamente recomendável para ler este artigo sobre considerações e configurações de ponto final de serviço antes de executar o script de exemplo abaixo, ou a configuração de pontos finais de serviço. **Endpoint de serviço de rede virtual:** R [ponto final de serviço de rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes de tipo de serviço do Azure formal. Pontos de extremidade de serviços de VNet utilizam o nome do tipo de serviço **Microsoft. SQL**, que faz referência ao serviço do Azure com o nome da base de dados SQL. Esta etiqueta de serviço também se aplica a SQL Database do Azure, base de dados do Azure para PostgreSQL e MySQL serviços. É importante ter em conta ao aplicar a **Microsoft. SQL** etiqueta de serviço para um ponto de extremidade do serviço de VNet configura o tráfego de ponto final de serviço para todos os serviços de base de dados do Azure, incluindo o SQL Database do Azure, base de dados do Azure para PostgreSQL e Base de dados do Azure para MySQL servidores na sub-rede. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Exemplo de script para criar uma base de dados do Azure para postgresql, a criar uma VNet, o ponto final de serviço de VNet e a proteger o servidor para a sub-rede com uma regra de VNet
Neste script de exemplo, altere as linhas realçadas para personalizar o nome de utilizador administrador e a palavra-passe. Substitua o SubscriptionID utilizado na `az account set --subscription` comando com o seu próprio identificador de subscrição.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]
