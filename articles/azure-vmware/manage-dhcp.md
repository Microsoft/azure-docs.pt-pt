---
title: Como gerir o DHCP
description: Este artigo explica como gerir o DHCP em Azure VMware Solution (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 80791dd2041fb9d6fbc7c67f2d7d7b2d0b6c977e
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148366"
---
# <a name="how-to-manage-dhcp-in-azure-vmware-solution-avs-preview"></a>Como gerir a DHCP em Azure VMWare Solution (AVS) Preview

O NSX-T fornece a capacidade de configurar o DHCP para a sua nuvem privada. Se planeia utilizar o NSX-T para hospedar o seu servidor DHCP, consulte [o servidor Criar DHCP](#create-dhcp-server). Caso contrário, se tiver um servidor DHCP externo de 3ª parte na sua rede e pretender transmitir pedidos para esse servidor DHCP, consulte [o serviço de retransmissão Criar DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Criar servidor DHCP

Utilize os seguintes passos para configurar um servidor DHCP em NSX-T.

A partir do gestor NSX, navegue para o **separador de Networking** e selecione **DHCP** sob **gestão IP**. Selecione o botão **ADD SERVER.** Em seguida, forneça o nome do servidor e o endereço IP do servidor. Uma vez feito, **selecione Guardar**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="adicionar servidor DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Ligue o servidor DHCP ao portal Tier-1.

Selecione **Gateways Tier 1**, selecione o gateway e selecione **Editar**

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="selecione o portal para usar" border="true":::

Adicione uma sub-rede selecionando **No IP Alocação**

:::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="adicionar uma sub-rede" border="true":::

No ecrã seguinte, selecione **o Servidor Local DHCP** a partir do **dropdown tipo.** Para **o Servidor DHCP**, selecione **DHCP predefinido** e selecione **Save**.

:::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="selecionar opções para o servidor dhcp" border="true":::

Na janela **Tier -1 Gateway,** selecione **Guardar**. No ecrã seguinte verá **Alterações Guardadas,** selecione **Close Editing** para terminar.

### <a name="add-a-network-segment"></a>Adicionar um segmento de rede

Uma vez criado o seu Servidor DHCP, terá de adicionar segmentos de rede ao mesmo.

No NSX-T, selecione o **separador de Rede** e selecione **Segmentos** em **Conectividade**. Selecione **SEGMENTO DE ADICIONAR**. Nomeie o segmento e a ligação ao Gateway Tier-1. Em seguida, selecione **set Subnets** para configurar uma nova sub-rede. 

:::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="adicionar um novo segmento de rede" border="true":::

Na janela **'set Subnets',** selecione **ADD SUBNET**. Introduza o endereço IP gateway e a gama DHCP e **selecione Adicionar** e, em seguida, **APPLY**

:::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="adicionar segmento de rede" border="true":::

Quando estiver concluído, **selecione Guardar** para completar a adição de um segmento de rede.

:::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="segmentos completos" border="true":::

## <a name="create-dhcp-relay-service"></a>Criar serviço de retransmissão DHCP

Na janela NXT-T, selecione o **separador de Rede** e, em **Gestão IP,** selecione **DHCP**. Selecione **ADD SERVER**. Escolha o Relé DHCP para o **Tipo de Servidor** e introduza o nome do servidor e o endereço IP para o servidor de retransmissão. Selecione **Guardar** para guardar as alterações.

:::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="criar servidor de retransmissão dhcp" border="true":::

Selecione **Gateways Tier-1** em **conectividade**. Selecione a elipse vertical no gateway Tier-1 e escolha **Editar**.

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="editar porta de entrada de nível 1" border="true":::

Selecione **Nenhum Conjunto de Atribuição de IP** para definir a atribuição de endereço IP.

:::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="editar a atribuição de endereços ip" border="true":::

Na caixa de diálogo, para **Tipo,** selecione **o Servidor de Retransmissão DHCP**. No dropdown **do Relé DHCP,** selecione o seu servidor de retransmissão DHCP. Quando terminar, **selecione Guardar**

:::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="definir gestão de endereço ip" border="true":::

Especificar um DHCP Range IP no Segmento:

> [!NOTE]
> Esta configuração é necessária para realizar a funcionalidade de retransmissão DHCP no Segmento de Cliente DHCP. 

Em **Conectividade,** selecione **Segmentos.** Selecione as elipses verticais e **selecione Editar.** Em vez disso, se quiser adicionar um novo segmento, pode selecionar **Adicionar Segmento** para criar um novo segmento.

:::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="editar uma sub-rede de rede" border="true":::

Adicione detalhes sobre o segmento. Selecione o valor nas **sub-redes** ou **sub-redes de conjunto** para adicionar ou modificar a sub-rede.

:::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segmentos de rede" border="true":::

Selecione as elipses verticais e escolha **Editar.** Se precisar de criar uma nova sub-rede, selecione **Add Subnet** para criar um Gateway e configurar uma gama DHCP. Forneça o alcance do pool IP e **selecione Aplicar**, e, em seguida, selecione **Guardar**

:::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="editar sub-redes" border="true":::

Agora, um conjunto de servidor DHCP é atribuído ao segmento.

:::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Piscina de servidor DHCP atribuída ao segmento" border="true":::
