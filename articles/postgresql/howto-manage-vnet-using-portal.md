---
title: Utilize regras de rede virtuais - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Crie e gerencie pontos finais de serviço VNet e regras Azure Database para PostgreSQL - Servidor Único utilizando o portal Azure
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 413c3a7b6fdcda996d3db548fb53f358eb8c71e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978282"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>Crie e gerencie pontos finais de serviço VNet e as regras VNet na Base de Dados Azure para PostgreSQL - Servidor Único utilizando o portal Azure
Os serviços de rede virtual (VNet) terminam os pontos finais e as regras estendem o espaço de endereço privado de uma Rede Virtual à sua Base de Dados Azure para servidor PostgreSQL. Para uma visão geral da Base de Dados Azure para pontos finais do serviço PostgreSQL VNet, incluindo limitações, consulte a [Base de Dados Azure para os pontos finais do serviço VNet do Servidor PostgreSQL](concepts-data-access-and-security-vnet.md). Os pontos finais do serviço VNet estão disponíveis em todas as regiões suportadas para a Base de Dados Azure para PostgreSQL.

> [!NOTE]
> O suporte para os pontos finais do serviço VNet destina-se apenas a servidores otimizados para fins gerais e memória.
> No caso de vNet espreitar, se o tráfego estiver fluindo através de um VNet Gateway comum com pontos finais de serviço e é suposto fluir para o par, por favor crie uma regra ACL/VNet para permitir que as Máquinas Virtuais Azure no Gateway VNet acedam à Base de Dados Azure para servidor PostgreSQL.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Criar uma regra VNet e ativar pontos finais de serviço no portal Azure

1. Na página do servidor PostgreSQL, na rubrica Definições, clique em **Segurança de Ligação** para abrir o painel de segurança de ligação para base de dados Azure para PostgreSQL. 

2. Certifique-se de que o controlo de serviços Do IA está definido para **OFF**.

> [!Important]
> Se deixar o conjunto de controlo definido para ON, o seu servidor de base de dados Azure PostgreSQL aceita a comunicação de qualquer sub-rede. Deixar o controlo definido para on pode ser um acesso excessivo do ponto de vista de segurança. A funcionalidade de ponto final do serviço microsoft Azure Virtual Network, em coordenação com a função de regra de rede virtual da Base de Dados Azure para PostgreSQL, em conjunto pode reduzir a sua área de superfície de segurança.

3. Em seguida, clique em **+ Adicionar rede virtual existente**. Se não tiver um VNet existente, pode clicar **+ Criar uma nova rede virtual** para criar uma. Ver [Quickstart: Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md)

   ![Portal Azure - clique em segurança de ligação](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Introduza um nome de regra VNet, selecione a subscrição, a rede virtual e o nome Subnet e, em seguida, clique em **Ativar**. Isto permite automaticamente pontos finais de serviço VNet na subnet utilizando a etiqueta de serviço **Microsoft.SQL.**

   ![Azure portal - configure VNet](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

    A conta deve ter as permissões necessárias para criar uma rede virtual e o ponto final de serviço.

    Os pontos finais do serviço podem ser configurados em redes virtuais de forma independente, por um utilizador com acesso por escrito à rede virtual.
    
    Para garantir os recursos de serviço do Azure a um VNet, o utilizador deve ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" para que as subredes sejam adicionadas. Esta permissão está incluída por predefinição nas funções incorporadas de administrador de serviço e podem ser modificadas mediante a criação de funções personalizadas.
    
    Saiba mais sobre [funções incorporadas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e a atribuição de permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
    As VNets e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Se os recursos de serviço VNet e Azure estiverem em subscrições diferentes, os recursos devem estar sob o mesmo inquilino de Diretório Ativo (AD). Certifique-se de que ambas as subscrições têm o fornecedor de recursos **Microsoft.Sql** registado. Para mais informações consulte o [registo de recursos-gestor][resource-manager-portal]

   > [!IMPORTANT]
   > É altamente recomendável ler este artigo sobre configurações e considerações de pontofinal de serviço antes de configurar pontos finais do serviço. Ponto final do serviço de **rede virtual:** Um ponto final de [serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma subnet cujos valores de propriedade incluem um ou mais nomes formais do tipo de serviço Azure. Os pontos finais dos serviços VNet utilizam o nome de tipo de serviço **Microsoft.Sql,** que se refere ao serviço Azure chamado Base de Dados SQL. Esta etiqueta de serviço também se aplica à Base de Dados Azure SQL, Base de Dados Azure para serviços PostgreSQL e MySQL. É importante notar que ao aplicar a etiqueta de serviço **Microsoft.Sql** a um ponto final do serviço VNet, ele configura o tráfego final do serviço para todos os serviços da Base de Dados Azure, incluindo a Base de Dados Azure SQL, base de dados Azure para PostgreSQL e Base de Dados Azure para servidores MySQL na subnet. 
   > 

5. Uma vez ativado, clique em **OK** e verá que os pontos finais do serviço VNet estão ativados juntamente com uma regra VNet.

   ![Pontos finais do serviço VNet ativados e regra VNet criada](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Passos seguintes
- Da mesma forma, pode escrever para [ativar pontos finais de serviço VNet e criar uma regra VNET para A Base de Dados Azure para PostgreSQL utilizando o Azure CLI](howto-manage-vnet-using-cli.md).
- Para obter ajuda na ligação a uma Base de Dados Azure para servidor PostgreSQL, consulte bibliotecas de [ligação para base de dados Azure para PostgreSQL](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md