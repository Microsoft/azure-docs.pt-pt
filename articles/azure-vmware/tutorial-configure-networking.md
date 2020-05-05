---
title: Tutorial - Configure a rede para a sua nuvem privada VMWare em Azure
description: Aprenda a criar e configurar o networking necessário para implantar a sua nuvem privada em Azure
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: e8278eb3ab5e99d330e34fd9739ed2bc49aa7f08
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740214"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutorial: Configure a rede para a sua nuvem privada VMWare em Azure

Uma nuvem privada Azure VMware Solution (AVS) requer uma rede virtual. Como o AVS não suporta o seu vCenter no local durante a pré-visualização, são necessários passos adicionais para a integração com o seu ambiente no local. A criação de um circuito ExpressRoute e um Gateway de Rede Virtual também são necessários e serão abordados neste tutorial.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma Rede Virtual
> * Criar um Gateway de Rede Virtual
> * Ligue o circuito ExpressRoute ao portal
> * Localize os URLs para vCenter e NSX Manager

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://rc.portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Navegue para o grupo de recursos que criou no [tutorial anterior,](tutorial-create-private-cloud.md)e selecione **+ Adicione** para definir um novo recurso.

No tipo de caixa de texto **Do Mercado,** **Rede Virtual**. Encontre o recurso rede virtual e selecione-o.

Na página rede virtual, selecione **Create** para configurar a sua rede virtual para a sua nuvem privada.

Na página **Create Virtual Network,** introduza os detalhes relevantes para a sua rede virtual, uma descrição das propriedades é mostrada na tabela seguinte:

> [!IMPORTANT]
> Deve utilizar um espaço de endereço que **não** se sobreponha ao espaço de endereço que utilizou quando criou a sua nuvem privada no tutorial anterior.

No separador **Basics,** introduza um nome para a rede virtual e selecione a região apropriada e selecione **Seguinte : Endereços IP**

No separador **Endereços IP,** sob o espaço de **endereço IPv4,** introduza o espaço de endereço que criou no tutorial anterior.

Selecione **+ Adicionar sub-rede,** e na página **adicionar sub-rede,** dar à sub-rede um nome e uma gama de endereços apropriada. Quando terminar, selecione **Adicionar**.

Selecione **Review + Criar**

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="criar uma rede virtual" border="true":::

Verifique as informações e selecione **Criar**. Uma vez concluída a implementação, verá a sua rede virtual no grupo de recursos.

## <a name="create-a-virtual-network-gateway"></a>Criar um Gateway de Rede Virtual

Criou uma rede virtual na secção anterior, agora vai criar um Gateway de Rede Virtual.

No seu grupo de recursos, selecione **+ Adicione** para adicionar um novo recurso.

No tipo de caixa de texto **Do Mercado,** gateway de **rede virtual**. Encontre o recurso rede virtual e selecione-o.

Na página de gateway da **Rede Virtual,** selecione **Criar**.

No separador Basics da página de gateway da **rede virtual Create,** forneça valores para os campos. As descrições dos campos são apresentadas no quadro seguinte:

| Campo | Valor |
| --- | --- |
| **Subscrição** | Este valor já está preenchido com a Subscrição a que pertence o grupo de recursos. |
| **Grupo de recursos** | Este valor já está povoado para o grupo de recursos atuais. Este deve ser o grupo de recursos que criou num teste anterior. |
| **Nome** | Introduza um nome único para o portal da rede virtual. |
| **Região** | Selecione a localização geográfica do gateway da rede virtual. |
| **Tipo de gateway** | Selecione **ExpressRoute**. |
| **Tipo de VPN** | Selecione **baseado na Rota**. |
| **SKU** | Deixe o valor predefinido: **standard**. |
| **Rede virtual** | Selecione a rede virtual que criou anteriormente. Se não vir a rede virtual, certifique-se de que a região do portal corresponde à região da sua rede virtual. |
| **Gama de endereços de sub-rede gateway** | Este valor é povoado quando seleciona a rede virtual. Não altere o valor padrão. |
| **Endereço IP público** | Selecione **Criar novo**. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="criar uma porta de entrada" border="true":::

Selecione **Review + criar**, na página seguinte verifique se os detalhes estão corretos e selecione **Criar** para iniciar a implementação do gateway da rede virtual. Assim que a implementação estiver concluída, passe para a secção seguinte deste tutorial para ligar a ligação ExpressRoute à rede virtual que contém a sua nuvem privada.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Ligue a Rota Expresso ao Gateway da Rede Virtual

Esta secção acompanha-o através da adição de uma ligação entre a sua nuvem privada AVS e o portal de rede virtual que criou.

Navegue para a nuvem privada que criou no tutorial anterior e **selecione Conectividade** sob **Manage**, selecione o separador **ExpressRoute.**

Copie a chave de autorização. Se não houver uma chave de autorização, precisa de criar uma, para fazer essa selecione **+ Solicite uma chave** de autorização

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="solicitar uma chave de autorização" border="true":::

Navegue para o Gateway de Rede Virtual que criou no passo anterior e em **Definições,** selecione **Ligações**. Na página **Ligações,** **selecione + Adicionar**.

Na página de **ligação Adicionar,** forneça valores para os campos. As descrições dos campos são apresentadas na tabela seguinte:

| Campo | Valor |
| --- | --- |
| **Nome**  | Introduza um nome para a ligação.  |
| **Tipo de ligação**  | Selecione **ExpressRoute**.  |
| **Autorização de redentor**  | Certifique-se de que esta caixa está selecionada.  |
| **Gateway de rede virtual** | O portal de rede virtual que criou anteriormente  |
| **Chave de autorização**  | Copie e cole a chave de autorização do separador ExpressRoute para o seu Grupo de Recursos. |
| **Circuito de pares URI**  | Copie e cole o ID ExpressRoute do separador ExpressRoute para o seu Grupo de Recursos.  |

Selecione **OK**. Isto cria a ligação entre o circuito ExpressRoute e a sua rede virtual.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="adicionar uma ligação" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Localize os URLs para vCenter e NSX Manager

Para iniciar sessão no vVenter e no gestor nsx, você precisará dos urls para o cliente web vCenter e o site de gerente NSX-T. Para encontrar as urls:

Navegue para a sua nuvem privada AVS, sob **AGeçar,** selecione **Identidade,** aqui encontrará as informações necessárias.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="localizar os urls vCenter" border="true":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma Rede Virtual
> * Criar um Gateway de Rede Virtual
> * Ligue o circuito ExpressRoute ao portal
> * Localize os URLs para vCenter e NSX Manager

Continue para o próximo tutorial para aprender a criar uma caixa de salto que é usada para se conectar ao seu ambiente para que possa gerir a sua nuvem privada localmente.

> [!div class="nextstepaction"]
> [Aceder à Nuvem Privada](tutorial-access-private-cloud.md)