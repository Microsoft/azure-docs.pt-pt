---
title: Tutorial - Configurar a rede para a sua nuvem privada VMware em Azure
description: Aprenda a criar e configurar a rede necessária para implantar a sua nuvem privada em Azure
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 6aff39284f3ea786080055552ac001ac5dd7b394
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578360"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutorial: Configurar a rede para a sua nuvem privada VMware em Azure

Uma nuvem privada Azure VMware Solution requer uma Rede Virtual Azure. Como a Azure VMware Solution não suporta o seu vCenter no local, são necessários passos adicionais para a integração com o seu ambiente no local. A criação de um circuito ExpressRoute e de uma porta de entrada de rede virtual também são necessárias.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um gateway de rede virtual
> * Ligue o seu circuito ExpressRoute ao gateway
> * Localize os URLs para vCenter e NSX Manager

## <a name="prerequisites"></a>Pré-requisitos 
Uma rede virtual que criou uma [nuvem privada Azure VMware Solution](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Navegue para o grupo de recursos que criou no [criar um tutorial de nuvem privada](tutorial-create-private-cloud.md) e selecione + **Adicionar** para definir um novo recurso. 

1. Na caixa de texto **do Marketplace,** digite **Rede Virtual.** Encontre o recurso Rede Virtual e selecione-o.

1. Na página **'Rede Virtual',** selecione **Criar** para configurar a sua rede virtual para a sua nuvem privada.

1. Na página **'Criar Rede Virtual',** insira os detalhes da sua rede virtual.

1. No **separador Básicos, insira** um nome para a rede virtual e selecione a região apropriada e selecione **Seguinte : Endereços IP**.

1. No separador **endereços IP,** no **espaço de endereço IPv4,** insira o espaço de endereço que criou no tutorial anterior.

   > [!IMPORTANT]
   > Tem de utilizar um espaço de endereço que **não** se sobreponha ao espaço de endereço que utilizou quando criou a sua nuvem privada no tutorial anterior.

1. **Selecione + Adicionar a sub-rede**, e na página de **sub-rede Adicionar,** dar à sub-rede um nome e intervalo de endereço apropriado. Quando terminar, selecione **Adicionar**.

1. Selecione **Rever + criar**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Selecione 'Rever + criar'." border="true":::

1. Verifique as informações e **selecione Criar**. Assim que a implementação estiver concluída, verá a sua rede virtual no grupo de recursos.

## <a name="create-a-virtual-network-gateway"></a>Criar um gateway de rede virtual

Agora que criou uma rede virtual, criará uma porta de entrada de rede virtual.

1. No seu grupo de recursos, selecione **+ Adicionar** para adicionar um novo recurso.

1. No tipo de caixa de texto **Do Mercado,** gateway **de rede virtual.** Encontre o recurso Rede Virtual e selecione-o.

1. Na página **de gateway da rede virtual,** selecione **Criar**.

1. No separador Básicos da página **de gateway de rede virtual,** forneça valores para os campos e, em seguida, selecione Review + **create**. 

   | Campo | Valor |
   | --- | --- |
   | **Subscrição** | Valor pré-povoado com a Subscrição a que pertence o grupo de recursos. |
   | **Grupo de recursos** | Valor pré-povoado para o grupo de recursos atuais. O valor deve ser o grupo de recursos que criou num teste anterior. |
   | **Nome** | Insira um nome único para o gateway de rede virtual. |
   | **Região** | Selecione a localização geográfica do gateway de rede virtual. |
   | **Tipo de gateway** | Selecione **ExpressRoute**. |
   | **SKU** | Deixe o valor predefinido: **padrão**. |
   | **Rede virtual** | Selecione a rede virtual que criou anteriormente. Se não vir a rede virtual, certifique-se de que a região do gateway corresponde à região da sua rede virtual. |
   | **Intervalo de endereço de sub-rede gateway** | Este valor é preenchido quando seleciona a rede virtual. Não altere o valor predefinido. |
   | **Endereço IP público** | Selecione **Criar novo**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Selecione 'Rever + criar'." border="true":::

1. Verifique se os detalhes estão corretos e selecione **Criar** para iniciar a implementação do seu gateway de rede virtual. 
1. Assim que a implementação estiver concluída, mova-se para a secção seguinte para ligar a sua ligação ExpressRoute ao gateway de rede virtual contendo a sua nuvem privada Azure VMware Solution.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Conecte o ExpressRoute ao gateway de rede virtual

Agora que implementou um gateway de rede virtual, irá adicionar uma ligação entre ele e a sua nuvem privada Azure VMware Solution.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Localize os URLs para vCenter e NSX Manager

Para iniciar sing para o gestor vCenter e NSX, você precisará dos URLs para o cliente web vCenter e o site de gerente NSX-T. 

Navegue para a sua nuvem privada Azure VMware Solution, em **Manage**, select **Identity**, aqui encontrará as informações necessárias.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Selecione 'Rever + criar'." border="true":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um gateway de rede virtual
> * Ligue o seu circuito ExpressRoute ao gateway
> * Localize os URLs para vCenter e NSX Manager

Continue até ao próximo tutorial para aprender a criar os segmentos de rede NSX-T que são usados para VMs em vCenter.

> [!div class="nextstepaction"]
> [Criar um segmento de rede NSX-T](tutorial-nsx-t-network-segment.md)