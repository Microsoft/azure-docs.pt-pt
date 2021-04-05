---
title: Gerir pontos finais VNet - Portal Azure - Base de Dados Azure para MariaDB
description: Criar e gerir a Base de Dados Azure para pontos finais de serviço MariaDB VNet e regras usando o portal Azure
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5eaa7821c61010b322d8f9032c439df28c297f3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665026"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Criar e gerir a Base de Dados Azure para pontos finais de serviço MariaDB VNet e regras VNet utilizando o portal Azure

Os pontos finais e as regras dos serviços da Rede Virtual (VNet) expandem o espaço do endereço privado de uma Rede Virtual para o servidor do Azure Database for MariaDB. Para obter uma visão geral da Base de Dados Azure para os pontos finais do serviço MariaDB VNet, incluindo limitações, consulte [a Base de Dados Azure para os pontos finais do serviço MariaDB Server VNet](concepts-data-access-security-vnet.md). Os pontos finais de serviço VNet estão disponíveis em todas as regiões suportadas para a Base de Dados Azure para MariaDB.

> [!NOTE]
> O suporte para os pontos finais do serviço VNet é apenas para servidores otimizados para fins gerais e memória.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Crie uma regra VNet e ative os pontos finais de serviço

1. Na página do servidor MariaDB, no título Definições, clique em **'Connection Security'** para abrir o painel de segurança de ligação para a base de dados Azure para MariaDB.

2. Certifique-se de que o controlo de serviços Azure está definido para **OFF**.

> [!Important]
> Se o configurar para ON, o seu servidor Azure MariaDB Database aceita a comunicação a partir de qualquer sub-rede. Deixar o controlo definido para ON pode ser um acesso excessivo do ponto de vista de segurança. A funcionalidade de ponto final do serviço de rede virtual Microsoft Azure, em coordenação com a funcionalidade de regra de rede virtual da Azure Database for MariaDB, em conjunto pode reduzir a sua área de superfície de segurança.

3. Em seguida, clique em **+ Adicionar rede virtual existente.** Se não tiver um VNet existente, pode clicar **+ Criar uma nova rede virtual** para criar uma. Ver [Quickstart: Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md)

   ![Portal Azure - clique na segurança de conexão](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Introduza um nome de regra VNet, selecione a subscrição, o nome da rede virtual e sub-rede e, em seguida, clique em **Enable**. Isto ativa automaticamente os pontos finais do serviço VNet na sub-rede utilizando a etiqueta de serviço **.SQL Microsoft.**

   ![Portal Azure - configurar vNet](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   A conta deve ter as permissões necessárias para criar uma rede virtual e o ponto final de serviço.

   Os pontos finais de serviço podem ser configurados em redes virtuais de forma independente, por um utilizador com acesso por escrito à rede virtual.
    
   Para garantir os recursos de serviço da Azure a um VNet, o utilizador deve ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" para a adição das sub-redes. Esta permissão está incluída por predefinição nas funções incorporadas de administrador de serviço e podem ser modificadas mediante a criação de funções personalizadas.
    
   Saiba mais sobre [funções incorporadas](../role-based-access-control/built-in-roles.md) e a atribuição de permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md).
    
   As VNets e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Se os recursos de serviço VNet e Azure estiverem em diferentes subscrições, os recursos devem estar sob o mesmo inquilino ative directy (AD). Certifique-se de que ambas as subscrições têm o fornecedor de recursos **Microsoft.Sql** registado. Para mais informações, consulte [o gestor de recursos-registo][resource-manager-portal]

   > [!IMPORTANT]
   > É altamente recomendado ler este artigo sobre configurações e considerações de ponto final de serviço antes de configurar os pontos finais do serviço. **Ponto final do serviço de rede virtual:** Um [ponto final de serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço Azure. Os pontos finais dos serviços VNet utilizam o nome de tipo de serviço **Microsoft.Sql,** que se refere ao serviço Azure denominado SQL Database. Esta etiqueta de serviço também se aplica aos serviços Azure SQL Database, Azure Database for MariaDB, PostgreSQL e MySQL. É importante notar que ao aplicar a etiqueta de serviço **Microsoft.Sql** a um ponto final do serviço VNet configura o tráfego de ponto final de serviço para todos os serviços da Azure Database, incluindo a Base de Dados Azure SQL, Base de Dados Azure para PostgreSQL, Base de Dados Azure para MariaDB e Azure Database para servidores MySQL na sub-rede.
   > 

5. Uma vez ativados, clique em **OK** e verá que os pontos finais do serviço VNet estão ativados juntamente com uma regra VNet.

   ![Pontos finais de serviço VNet ativados e regra VNet criada](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a configuração do SSL na Base de Dados Azure para MariaDB](howto-configure-ssl.md)
- Da mesma forma, pode escrever para [ativar pontos finais de serviço VNet e criar uma regra VNET para Azure Database for MariaDB usando Azure CLI](howto-manage-vnet-cli.md).

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md