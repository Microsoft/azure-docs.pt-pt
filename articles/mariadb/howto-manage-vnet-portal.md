---
title: Criar e gerir a base de dados para pontos finais de serviço de MariaDB VNet e regras no portal do Azure | Documentos da Microsoft
description: Criar e gerir a base de dados para pontos finais de serviço de MariaDB VNet e regras no portal do Azure
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: a1d92f324c30c498b1a42f6155a478cf131ecc96
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961963"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Criar e gerir a base de dados para pontos finais de serviço de MariaDB VNet e regras de VNet com o portal do Azure

Pontos finais de serviços de rede (VNet) virtual e regras de estendem o espaço de endereços privados de uma rede Virtual para a base de dados do Azure para MariaDB server. Para uma visão geral da base de dados do Azure para MariaDB VNet pontos finais de serviço, incluindo as limitações, consulte [base de dados do Azure para pontos finais de serviço de VNet do servidor de MariaDB](concepts-data-access-security-vnet.md). Pontos finais de serviço de VNet estão disponíveis em todas as regiões suportadas para a base de dados do Azure para MariaDB.

> [!NOTE]
> Suporte para pontos finais de serviço da VNet é apenas para fins gerais e memória otimizada de servidores.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Criar uma regra de VNet e ativar os pontos finais de serviço

1. Na página de servidor MariaDB, sob as configurações, clique em **segurança de ligação** para abrir o painel de segurança da ligação da base de dados do Azure para MariaDB. Em seguida, clique em **+ adicionar rede virtual existente**. Se não tiver uma VNet existente pode clicar **+ criar nova rede virtual** para criar um. Consulte [início rápido: Criar uma rede virtual com o portal do Azure](../virtual-network/quick-create-portal.md)

   ![Portal do Azure - clique em segurança de ligação](./media/howto-manage-vnet-portal/1-connection-security.png)

2. Introduza um nome de regra de VNet, selecione a subscrição, a rede Virtual e o nome da sub-rede e, em seguida, clique em **ativar**. Isto permite automaticamente pontos finais de serviço de VNet no sub-rede com o **Microsoft. SQL** etiqueta de serviço.

   ![Portal do Azure - configurar VNet](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   A conta tem de ter as permissões necessárias para criar uma rede virtual e o ponto final de serviço.

   Pontos finais de serviço podem ser configurados em redes virtuais de forma independente por um utilizador com acesso de escrita para a rede virtual.
    
   Para proteger os recursos de serviço do Azure a uma VNet, o utilizador tem de ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" para as sub-redes que está a ser adicionadas. Esta permissão está incluída por predefinição nas funções incorporadas de administrador de serviço e podem ser modificadas mediante a criação de funções personalizadas.
    
   Saiba mais sobre [funções incorporadas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e a atribuição de permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   As VNets e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Se os recursos de serviço de VNet e o Azure estão em subscrições diferentes, os recursos devem existir no mesmo inquilino do Active Directory (AD).

   > [!IMPORTANT]
   > É altamente recomendado para ler este artigo sobre considerações e configurações de ponto final de serviço antes de configurar pontos finais de serviço. **Endpoint de serviço de rede virtual:** R [ponto final de serviço de rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes de tipo de serviço do Azure formal. Pontos de extremidade de serviços de VNet utilizam o nome do tipo de serviço **Microsoft. SQL**, que faz referência ao serviço do Azure com o nome da base de dados SQL. Esta etiqueta de serviço também se aplica a SQL Database do Azure, base de dados do Azure para serviços MariaDB, PostgreSQL e MySQL. É importante ter em conta ao aplicar a **Microsoft. SQL** etiqueta de serviço para um ponto de extremidade do serviço de VNet configura o tráfego de ponto final de serviço para todos os serviços de base de dados do Azure, incluindo o SQL Database do Azure, base de dados do Azure para PostgreSQL, Base de dados do Azure para MariaDB e base de dados do Azure para MySQL servidores na sub-rede.
   > 

3. Uma vez ativada, clique em **OK** e verá que os pontos finais de serviço de VNet estão ativados, juntamente com uma regra de VNet.

   ![Pontos finais de serviço de VNet ativados e a regra de VNet criada](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [configuração do SSL na base de dados do Azure para MariaDB](howto-configure-ssl.md)
- Da mesma forma, pode criar scripts para [VNet ativar pontos finais de serviço e criar uma regra VNET para a base de dados do Azure para MariaDB com a CLI do Azure](howto-manage-vnet-cli.md).
