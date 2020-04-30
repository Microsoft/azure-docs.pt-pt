---
title: Gerir pontos finais VNet - Portal Azure - Base de Dados Azure para MySQL
description: Crie e gerea Base de Dados Azure para pontos finais de serviço MySQL VNet e regras usando o portal Azure
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ba63c65c2b0d7abda089c3dfd315bfe499632cc0
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509232"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Crie e gerea Base de Dados Azure para os pontos finais do serviço MySQL VNet e as regras VNet utilizando o portal Azure
Os pontos finais e as regras de serviços da Rede Virtual (VNet) expandem o espaço do endereço privado de uma Rede Virtual ao seu servidor da Base de Dados do Azure para MySQL. Para uma visão geral da Base de Dados Azure para os pontos finais do serviço MySQL VNet, incluindo limitações, consulte a [Base de Dados Azure para os pontos finais do serviço MySQL Server VNet](concepts-data-access-and-security-vnet.md). Os pontos finais do serviço VNet estão disponíveis em todas as regiões suportadas para a Base de Dados Azure para mySQL.

> [!NOTE]
> O suporte para os pontos finais do serviço VNet destina-se apenas a servidores otimizados para fins gerais e memória.
> No caso de vNet espreitar, se o tráfego estiver fluindo através de um VNet Gateway comum com pontos finais de serviço e é suposto fluir para o par, por favor crie uma regra ACL/VNet para permitir que as Máquinas Virtuais Azure no Gateway VNet acedam à Base de Dados Azure para o servidor MySQL.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Criar uma regra VNet e ativar pontos finais de serviço no portal Azure

1. Na página do servidor MySQL, na rubrica Definições, clique em **Segurança de Ligação** para abrir o painel de segurança de ligação para base de dados Azure para MySQL. 

2. Certifique-se de que o controlo de serviços Do IA está definido para **OFF**.

> [!Important]
> Se deixar o conjunto de controlo definido para ON, o seu servidor de base de dados Azure MySQL aceita a comunicação de qualquer sub-rede. Deixar o controlo definido para on pode ser um acesso excessivo do ponto de vista de segurança. A funcionalidade de ponto final do serviço microsoft Azure Virtual Network, em coordenação com a funcionalidade de regra de rede virtual da Base de Dados Azure para o MySQL, em conjunto pode reduzir a sua área de superfície de segurança.

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
- Da mesma forma, pode escrever para [ativar pontos finais de serviço VNet e criar uma regra VNET para A Base de Dados Azure para MySQL utilizando o Azure CLI](howto-manage-vnet-using-cli.md).
- Para obter ajuda na ligação a uma Base de Dados Azure para servidor MySQL, consulte bibliotecas de ligação para base de [dados Azure para MySQL](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md