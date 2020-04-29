---
title: Gerir pontos finais VNet - Portal Azure - Base de Dados Azure para MariaDB
description: Crie e gerea Base de Dados Azure para os pontos finais e regras do serviço MariaDB VNet utilizando o portal Azure
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 61a8337536f55ceda9bef5b7eaa67a37644d2aca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530602"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Crie e gerea Base de Dados Azure para os pontos finais do serviço MariaDB VNet e as regras VNet utilizando o portal Azure

Os pontos finais e as regras dos serviços da Rede Virtual (VNet) expandem o espaço do endereço privado de uma Rede Virtual para o servidor do Azure Database for MariaDB. Para uma visão geral da Base de Dados Azure para os pontos finais do serviço MariaDB VNet, incluindo limitações, consulte a [Base de Dados Azure para os pontos finais](concepts-data-access-security-vnet.md)do serviço VNet do Servidor MariaDB . Os pontos finais do serviço VNet estão disponíveis em todas as regiões suportadas para a Base de Dados Azure para MariaDB.

> [!NOTE]
> O suporte para os pontos finais do serviço VNet destina-se apenas a servidores otimizados para fins gerais e memória.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Criar uma regra VNet e ativar pontos finais de serviço

1. Na página do servidor MariaDB, na rubrica Definições, clique em **Segurança de Ligação** para abrir o painel de Segurança de Ligação para base de dados Azure para MariaDB.

2. Certifique-se de que o controlo de serviços Do IA está definido para **OFF**.

> [!Important]
> Se o definir para ON, o seu servidor de base de dados Azure MariaDB aceita a comunicação de qualquer subrede. Deixar o controlo definido para on pode ser um acesso excessivo do ponto de vista de segurança. A funcionalidade de ponto final do serviço microsoft Azure Virtual Network, em coordenação com a funcionalidade de regra de rede virtual da Base de Dados Azure para O DYDB, em conjunto pode reduzir a sua área de superfície de segurança.

3. Em seguida, clique em **+ Adicionar rede virtual existente**. Se não tiver um VNet existente, pode clicar **+ Criar uma nova rede virtual** para criar uma. Ver [Quickstart: Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md)

   ![Portal Azure - clique em segurança de ligação](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Introduza um nome de regra VNet, selecione a subscrição, a rede virtual e o nome Subnet e, em seguida, clique em **Ativar**. Isto permite automaticamente pontos finais de serviço VNet na subnet utilizando a etiqueta de serviço **Microsoft.SQL.**

   ![Azure portal - configure VNet](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   A conta deve ter as permissões necessárias para criar uma rede virtual e o ponto final de serviço.

   Os pontos finais do serviço podem ser configurados em redes virtuais de forma independente, por um utilizador com acesso por escrito à rede virtual.
    
   Para garantir os recursos de serviço do Azure a um VNet, o utilizador deve ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" para que as subredes sejam adicionadas. Esta permissão está incluída por predefinição nas funções incorporadas de administrador de serviço e podem ser modificadas mediante a criação de funções personalizadas.
    
   Saiba mais sobre [funções incorporadas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e a atribuição de permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   As VNets e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Se os recursos de serviço VNet e Azure estiverem em subscrições diferentes, os recursos devem estar sob o mesmo inquilino de Diretório Ativo (AD). Certifique-se de que ambas as subscrições têm o fornecedor de recursos **Microsoft.Sql** registado. Para mais informações consulte o [registo de recursos-gestor][resource-manager-portal]

   > [!IMPORTANT]
   > É altamente recomendável ler este artigo sobre configurações e considerações de pontofinal de serviço antes de configurar pontos finais do serviço. Ponto final do serviço de **rede virtual:** Um ponto final de [serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma subnet cujos valores de propriedade incluem um ou mais nomes formais do tipo de serviço Azure. Os pontos finais dos serviços VNet utilizam o nome de tipo de serviço **Microsoft.Sql,** que se refere ao serviço Azure chamado Base de Dados SQL. Esta etiqueta de serviço também se aplica aos serviços Azure SQL Database, Azure Database para MariaDB, PostgreSQL e MySQL. É importante notar que ao aplicar a etiqueta de serviço **Microsoft.Sql** a um ponto final do serviço VNet, ele configura o tráfego de ponto final do serviço para todos os serviços da Base de Dados Azure, incluindo a Base de Dados Azure SQL, Base de Dados Azure para PostgreSQL, Base de Dados Azure para MariaDB e Base de Dados Azure para servidores MySQL na subnet.
   > 

5. Uma vez ativado, clique em **OK** e verá que os pontos finais do serviço VNet estão ativados juntamente com uma regra VNet.

   ![Pontos finais do serviço VNet ativados e regra VNet criada](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [configurar SSL na Base de Dados Azure para MariaDB](howto-configure-ssl.md)
- Da mesma forma, pode escrever para [ativar pontos finais de serviço VNet e criar uma regra VNET para A Base de Dados Azure para MariaDB usando o Azure CLI](howto-manage-vnet-cli.md).

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md