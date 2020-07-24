---
title: Tutorial - Configurar a rede para a sua nuvem privada VMware em Azure
description: Aprenda a criar e configurar a rede necessária para implantar a sua nuvem privada em Azure
ms.topic: tutorial
ms.date: 07/22/2020
ms.openlocfilehash: aa4247f60c3e1ec54bfcde336d1ae8c8f70ff7a8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079438"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutorial: Configurar a rede para a sua nuvem privada VMware em Azure

Uma nuvem privada Azure VMware Solution (AVS) requer uma Rede Virtual Azure. Como o AVS não suporta o seu vCenter no local durante a pré-visualização, são necessários passos adicionais para a integração com o seu ambiente no local. A criação de um circuito ExpressRoute e de uma porta de entrada de rede virtual também são necessárias e está coberta neste tutorial.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um gateway de rede virtual
> * Ligue o seu circuito ExpressRoute ao gateway
> * Localize os URLs para vCenter e NSX Manager

## <a name="prerequisites"></a>Pré-requisitos 
Antes de criar uma rede virtual, certifique-se de que criou uma [nuvem privada AVS.](tutorial-create-private-cloud.md) 

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

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="criar uma rede virtual" border="true":::

1. Verifique as informações e **selecione Criar**. Assim que a implementação estiver concluída, verá a sua rede virtual no grupo de recursos.

## <a name="create-a-virtual-network-gateway"></a>Criar um gateway de rede virtual

Agora que criou uma rede virtual, criará uma porta de entrada de rede virtual.

1. No seu grupo de recursos, selecione **+ Adicionar** para adicionar um novo recurso.

1. No tipo de caixa de texto **Do Mercado,** gateway **de rede virtual.** Encontre o recurso Rede Virtual e selecione-o.

1. Na página **de gateway da rede virtual,** selecione **Criar**.

1. No separador Básicos da página **de gateway de rede virtual,** forneça valores para os campos e, em seguida, selecione Review + **create**. 

   | Campo | Valor |
   | --- | --- |
   | **Subscrição** | Este valor já está preenchido com a Subscrição a que pertence o grupo de recursos. |
   | **Grupo de recursos** | Este valor já está povoado para o grupo de recursos atuais. Este deve ser o grupo de recursos que criou num teste anterior. |
   | **Nome** | Insira um nome único para o gateway de rede virtual. |
   | **Região** | Selecione a localização geográfica do gateway de rede virtual. |
   | **Tipo de gateway** | Selecione **ExpressRoute**. |
   | **SKU** | Deixe o valor predefinido: **padrão**. |
   | **Rede virtual** | Selecione a rede virtual que criou anteriormente. Se não vir a rede virtual, certifique-se de que a região do gateway corresponde à região da sua rede virtual. |
   | **Intervalo de endereço de sub-rede gateway** | Este valor é preenchido quando seleciona a rede virtual. Não altere o valor predefinido. |
   | **Endereço IP público** | Selecione **Criar novo**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="criar uma porta de entrada" border="true":::

1. Verifique se os detalhes estão corretos e selecione **Criar** para iniciar a implementação do seu gateway de rede virtual. 
1. Assim que a implementação estiver concluída, mova-se para a secção seguinte para ligar a sua ligação ExpressRoute ao gateway de rede virtual contendo a sua nuvem privada AVS.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Conecte o ExpressRoute ao gateway de rede virtual

Agora que implementou uma porta de entrada de rede virtual, irá adicionar uma ligação entre ele e a sua nuvem privada AVS.

1. Navegue para a nuvem privada que criou no tutorial anterior e selecione **Conectividade** em **Manage**, selecione o **separador ExpressRoute.**

1. Copie a chave de autorização. Se não houver uma chave de autorização, é necessário criar uma, para fazer esse select **+ Solicite uma chave de autorização**

   :::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="solicitar uma chave de autorização" border="true":::

1. Navegue para o Gateway de Rede Virtual que criou no passo anterior e em **Definições**, selecione **Connections**. Na página **'Ligações',** selecione **+ Adicionar**.

1. Na página **de ligação Adicionar,** forneça valores para os campos e selecione **OK**. 

   | Campo | Valor |
   | --- | --- |
   | **Nome**  | Introduza um nome para a ligação.  |
   | **Tipo de ligação**  | Selecione **ExpressRoute**.  |
   | **Autorização de redentor**  | Certifique-se de que esta caixa está selecionada.  |
   | **Gateway de rede virtual** | A porta de entrada da Rede Virtual que criou anteriormente.  |
   | **Chave de autorização**  | Copie e cole a chave de autorização do separador ExpressRoute para o seu Grupo de Recursos. |
   | **Circuito de pares URI**  | Copie e cole o ID ExpressRoute do separador ExpressRoute para o seu Grupo de Recursos.  |

   :::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="adicionar uma ligação" border="true":::

A ligação entre o seu circuito ExpressRoute e a sua Rede Virtual é criada.



## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Localize os URLs para vCenter e NSX Manager

Para iniciar sing para o gestor vCenter e NSX, você precisará dos URLs para o cliente web vCenter e o site de gerente NSX-T. 

Navegue para a sua nuvem privada AVS, em **Manage,** select **Identity,** aqui encontrará as informações necessárias.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="localizar os urls vCenter" border="true":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um gateway de rede virtual
> * Ligue o seu circuito ExpressRoute ao gateway
> * Localize os URLs para vCenter e NSX Manager

Continue até ao próximo tutorial para aprender a criar uma caixa de salto que é usada para se conectar ao seu ambiente para que possa gerir a sua nuvem privada localmente.

> [!div class="nextstepaction"]
> [Aceder à Cloud Privada](tutorial-access-private-cloud.md)
