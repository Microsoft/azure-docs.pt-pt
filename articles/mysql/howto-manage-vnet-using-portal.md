---
title: Gerenciar pontos de extremidade de VNet-portal do Azure-banco de dados do Azure para MySQL
description: Criar e gerenciar pontos de extremidade e regras de serviço VNet do banco de dados do Azure para MySQL usando o portal do Azure
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 7479b16f2e1f14d8ebe611bf3121005af342ccb9
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764938"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Criar e gerenciar pontos de extremidade de serviço VNet do banco de dados do Azure para MySQL e regras de VNet usando o portal do Azure
Os pontos finais e as regras de serviços da Rede Virtual (VNet) expandem o espaço do endereço privado de uma Rede Virtual ao seu servidor da Base de Dados do Azure para MySQL. Para obter uma visão geral dos pontos de extremidade do serviço VNet do banco de dados do Azure para MySQL, incluindo limitações, consulte [pontos de extremidade do serviço vnet do banco de dados do Azure para MySQL Server](concepts-data-access-and-security-vnet.md). Pontos de extremidade de serviço de VNet estão disponíveis em todas as regiões com suporte para o banco de dados do Azure para MySQL.

> [!NOTE]
> O suporte para pontos de extremidade de serviço de VNet é apenas para servidores Uso Geral e com otimização de memória.
> No caso de emparelhamento VNet, se o tráfego estiver fluindo por um gateway de VNet comum com pontos de extremidade de serviço e for supostamente fluir para o par, crie uma regra de ACL/VNet para permitir que as máquinas virtuais do Azure na VNet do gateway acessem o servidor de banco de dados do Azure para MySQL.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Criar uma regra de VNet e habilitar pontos de extremidade de serviço no portal do Azure

1. Na página do MySQL Server, no título configurações, clique em **segurança de conexão** para abrir o painel segurança de conexão do banco de dados do Azure para MySQL. 

2. Verifique se o controle permitir acesso aos serviços do Azure está definido como **desativado**.

> [!Important]
> Se você deixar o controle definido como ON, seu servidor de banco de dados MySQL do Azure aceitará a comunicação de qualquer sub-rede. Deixar o controle definido como ON pode ser o acesso excessivo de um ponto de vista de segurança. O recurso de ponto de extremidade de serviço Rede Virtual do Microsoft Azure, em coordenação com o recurso de regra de rede virtual do banco de dados do Azure para MySQL, pode reduzir sua área de superfície de segurança.

3. Em seguida, clique em **+ Adicionar rede virtual existente**. Se você não tiver uma VNet existente, poderá clicar em **+ criar nova rede virtual** para criar uma. Consulte [início rápido: criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md)

   ![portal do Azure clique em segurança de conexão](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Insira um nome de regra de VNet, selecione a assinatura, a rede virtual e o nome da sub-rede e clique em **habilitar**. Isso habilita automaticamente os pontos de extremidade do serviço VNet na sub-rede usando a marca de serviço **Microsoft. SQL** .

   ![Portal do Azure-configurar VNet](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   A conta deve ter as permissões necessárias para criar uma rede virtual e um ponto de extremidade de serviço.

   Os pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente, por um usuário com acesso de gravação à rede virtual.
    
   Para proteger os recursos de serviço do Azure para uma VNet, o usuário deve ter permissão para "Microsoft. Network/virtualNetworks/sub-redes/joinViaServiceEndpoint/" para as sub-redes que estão sendo adicionadas. Esta permissão está incluída por predefinição nas funções incorporadas de administrador de serviço e podem ser modificadas mediante a criação de funções personalizadas.
    
   Saiba mais sobre [funções incorporadas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e a atribuição de permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   As VNets e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Se os recursos de serviço da VNet e do Azure estiverem em assinaturas diferentes, os recursos deverão estar no mesmo locatário do Active Directory (AD). Certifique-se de que ambas as assinaturas tenham o provedor de recursos **Microsoft. SQL** registrado. Para obter mais informações, consulte [Resource-Manager-Registration][resource-manager-portal]

   > [!IMPORTANT]
   > É altamente recomendável ler este artigo sobre as configurações e considerações do ponto de extremidade de serviço antes de configurar pontos de extremidades de serviço. **Ponto de extremidade de serviço de rede virtual:** Um [ponto de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Os pontos de extremidade dos serviços de VNet usam o nome do tipo de serviço **Microsoft. SQL**, que se refere ao serviço do Azure denominado Banco de dados SQL. Essa marca de serviço também se aplica ao banco de dados SQL do Azure, ao banco de dados do Azure para PostgreSQL e aos serviços MySQL. É importante observar ao aplicar a marca de serviço **Microsoft. SQL** a um ponto de extremidade de serviço VNet que configura o tráfego de ponto de extremidade de serviço para todos os serviços de banco de dados do Azure, incluindo banco de dados SQL do Azure, banco de dados do Azure para PostgreSQL e banco de dados do Azure para servidores MySQL na sub-rede. 
   > 

5. Uma vez habilitado, clique em **OK** e você verá que os pontos de extremidade do serviço VNet estão habilitados junto com uma regra de VNet.

   ![Pontos de extremidade de serviço de VNet habilitados e regras de VNet criadas](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Passos seguintes
- Da mesma forma, você pode [criar scripts para habilitar pontos de extremidade de serviço de vnet e crie uma regra de vnet para o banco de dados do Azure para MySQL usando CLI do Azure](howto-manage-vnet-using-cli.md).
- Para obter ajuda para se conectar a um servidor de banco de dados do Azure para MySQL, consulte [bibliotecas de conexões para o banco de dados do Azure para MySQL](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md