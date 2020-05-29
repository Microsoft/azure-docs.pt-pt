---
title: Tutorial - Configurar a rede para a sua nuvem privada VMware em Azure
description: Aprenda a criar e configurar a rede necessária para implantar a sua nuvem privada em Azure
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 6cac420fb77526746dbdbfef5a88b071c007d555
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148111"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutorial: Configurar a rede para a sua nuvem privada VMware em Azure

Uma nuvem privada Azure VMware Solution (AVS) requer uma rede virtual. Como o AVS não suporta o seu vCenter no local durante a pré-visualização, são necessários passos adicionais para a integração com o seu ambiente no local. A criação de um circuito ExpressRoute e um Gateway de Rede Virtual também são necessários e será abordado neste tutorial.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma Rede Virtual
> * Criar um Gateway de rede virtual
> * Ligue o seu circuito ExpressRoute ao gateway
> * Localize os URLs para vCenter e NSX Manager

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Navegue para o grupo de recursos que criou no [tutorial anterior,](tutorial-create-private-cloud.md)e selecione **+ Adicionar** para definir um novo recurso.

No tipo de caixa de texto **Do Mercado,** **Rede Virtual.** Encontre o recurso Rede Virtual e selecione-o.

Na página 'Rede Virtual', **selecione Criar** para configurar a sua rede virtual para a sua nuvem privada.

Na página **'Criar Rede Virtual',** insira os detalhes relevantes para a sua rede virtual, uma descrição das propriedades é mostrada na tabela seguinte:

> [!IMPORTANT]
> Tem de utilizar um espaço de endereço que **não** se sobreponha ao espaço de endereço que utilizou quando criou a sua nuvem privada no tutorial anterior.

No separador **Básicos, insira** um nome para a rede virtual e selecione a região apropriada e selecione **Seguinte : Endereços IP**

No separador **endereços IP,** no **espaço de endereço IPv4,** insira o espaço de endereço que criou no tutorial anterior.

**Selecione + Adicionar a sub-rede**, e na página de **sub-rede Adicionar,** dar à sub-rede um nome e intervalo de endereço apropriado. Quando terminar, selecione **Adicionar**.

**Selecione Review + Criar**

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="criar uma rede virtual" border="true":::

Verifique as informações e **selecione Criar**. Assim que a implementação estiver concluída, verá a sua rede virtual no grupo de recursos.

## <a name="create-a-virtual-network-gateway"></a>Criar um Gateway de rede virtual

Criou uma rede virtual na secção anterior, agora vai criar um Gateway de Rede Virtual.

No seu grupo de recursos, selecione **+ Adicionar** para adicionar um novo recurso.

No tipo de caixa de texto **Do Mercado,** gateway **de rede virtual.** Encontre o recurso Rede Virtual e selecione-o.

Na página **de gateway da rede virtual,** selecione **Criar**.

No separador Básicos da página **de gateway de rede virtual Create,** forneça valores para os campos. As descrições dos campos são apresentadas no quadro seguinte:

| Campo | Valor |
| --- | --- |
| **Subscrição** | Este valor já está preenchido com a Subscrição a que pertence o grupo de recursos. |
| **Grupo de recursos** | Este valor já está povoado para o grupo de recursos atuais. Este deve ser o grupo de recursos que criou num teste anterior. |
| **Nome** | Insira um nome único para o gateway de rede virtual. |
| **Região** | Selecione a localização geográfica do gateway de rede virtual. |
| **Tipo de gateway** | Selecione **ExpressRoute**. |
| **Tipo de VPN** | Selecione **Route-based**. |
| **SKU** | Deixe o valor predefinido: **padrão**. |
| **Rede virtual** | Selecione a rede virtual que criou anteriormente. Se não vir a rede virtual, certifique-se de que a região do gateway corresponde à região da sua rede virtual. |
| **Intervalo de endereço de sub-rede gateway** | Este valor é preenchido quando seleciona a rede virtual. Não altere o valor predefinido. |
| **Endereço IP público** | Selecione **Criar novo**. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="criar uma porta de entrada" border="true":::

Selecione **Rever + criar**, na página seguinte verifique se os detalhes estão corretos e selecione **Criar** para iniciar a implementação do seu gateway de rede virtual. Assim que a implementação estiver concluída, mova-se para a secção seguinte neste tutorial para ligar a sua ligação ExpressRoute à rede virtual que contém a sua nuvem privada.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Conecte o ExpressRoute ao Gateway de Rede Virtual

Esta secção acompanha-o através da adição de uma ligação entre a sua nuvem privada AVS e a porta de entrada de rede virtual que criou.

Navegue para a nuvem privada que criou no tutorial anterior e selecione **Conectividade** em **Manage**, selecione o **separador ExpressRoute.**

Copie a chave de autorização. Se não houver uma chave de autorização, é necessário criar uma, para fazer esse select **+ Solicite uma chave de autorização**

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="solicitar uma chave de autorização" border="true":::

Navegue para o Gateway de Rede Virtual que criou no passo anterior e em **Definições**, selecione **Connections**. Na página **'Ligações',** selecione **+ Adicionar**.

Na página **de ligação Adicionar,** forneça valores para os campos. As descrições dos campos são mostradas no quadro seguinte:

| Campo | Valor |
| --- | --- |
| **Nome**  | Introduza um nome para a ligação.  |
| **Tipo de ligação**  | Selecione **ExpressRoute**.  |
| **Autorização de redentor**  | Certifique-se de que esta caixa está selecionada.  |
| **Gateway de rede virtual** | A porta de entrada de rede virtual que criou anteriormente  |
| **Chave de autorização**  | Copie e cole a chave de autorização do separador ExpressRoute para o seu Grupo de Recursos. |
| **Circuito de pares URI**  | Copie e cole o ID ExpressRoute do separador ExpressRoute para o seu Grupo de Recursos.  |

Selecione **OK**. Isto cria a ligação entre o seu circuito ExpressRoute e a sua rede virtual.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="adicionar uma ligação" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Localize os URLs para vCenter e NSX Manager

Para iniciar sing para vVenter e NSX manager você precisará dos urls para o cliente web vCenter e o site de gerente NSX-T. Para encontrar os urls:

Navegue para a sua nuvem privada AVS, em **Manage,** select **Identity,** aqui encontrará as informações necessárias.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="localizar os urls vCenter" border="true":::

## <a name="next-steps"></a>Próximos passos

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma Rede Virtual
> * Criar um Gateway de rede virtual
> * Ligue o seu circuito ExpressRoute ao gateway
> * Localize os URLs para vCenter e NSX Manager

Continue até ao próximo tutorial para aprender a criar uma caixa de salto que é usada para se conectar ao seu ambiente para que possa gerir a sua nuvem privada localmente.

> [!div class="nextstepaction"]
> [Aceder à Cloud Privada](tutorial-access-private-cloud.md)
