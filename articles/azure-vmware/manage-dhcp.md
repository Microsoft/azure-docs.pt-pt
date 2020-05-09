---
title: Como gerir o DHCP
description: Este artigo explica como gerir o DHCP na Azure VMWare Solution (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ccf28c94e1991681c238f51847fe228313abe29e
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740450"
---
# <a name="how-to-manage-dhcp-in-azure-vmware-solution-avs-preview"></a>Como gerir o DHCP na Pré-visualização da Solução Azure VMWare (AVS)

O NSX-T fornece a capacidade de configurar o DHCP para a sua nuvem privada. Se planeia utilizar o NSX-T para hospedar o seu servidor DHCP, consulte [o servidor Create DHCP](#create-dhcp-server). Caso contrário, se tiver um servidor DHCP externo de 3ª parte na sua rede e pretender retransmitir pedidos para esse servidor DHCP, consulte o serviço de [retransmissão Create DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Criar servidor DHCP

Utilize os seguintes passos para configurar um servidor DHCP no NSX-T.

Do gestor NSX, navegue para o separador **Networking** e selecione **DHCP** sob **gestão IP**. Selecione o botão **ADD SERVER.** Em seguida, forneça o nome do servidor e o endereço IP do servidor. Uma vez feito, selecione **Guardar**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="adicionar servidor DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Ligue o servidor DHCP ao portal Tier-1.

Selecione **Gateways de Nível 1,** selecione o gateway e selecione **Editar**

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="selecionar o portal para usar" border="true":::

Adicione uma sub-rede selecionando nenhum conjunto de **atribuição ip**

:::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="adicionar uma sub-rede" border="true":::

No ecrã seguinte, selecione **DHCP Local Server** a partir do **dropdown type.** Para **o servidor DHCP,** selecione **'DHCP' predefinido** e selecione **Save**.

:::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="selecionar opções para o servidor dhcp" border="true":::

Na janela **Gateway Tier -1,** selecione **Guardar**. No ecrã seguinte verá **alterações guardadas**, selecione **Edição De Perto** para terminar.

### <a name="add-a-network-segment"></a>Adicione um segmento de rede

Assim que tiver criado o seu Servidor DHCP, terá de adicionar segmentos de rede ao mesmo.

No NSX-T, selecione o separador **de rede** e selecione **Segmentos** sob **conectividade**. Selecione **SEGMENTO ADD**. Nomeie o segmento e a ligação ao Gateway Tier-1. Em seguida, selecione **Subnets de conjunto** para configurar uma nova sub-rede. 

:::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="adicionar um novo segmento de rede" border="true":::

Na janela **set Subnets,** selecione **ADD SUBNET**. Introduza o endereço IP gateway e a gama DHCP e selecione **Adicionar** e, em seguida, **APLICAR**

:::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="adicionar segmento de rede" border="true":::

Quando estiver completo, selecione **Guardar** para completar a adição de um segmento de rede.

:::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="segmentos completos" border="true":::

## <a name="create-dhcp-relay-service"></a>Criar serviço de retransmissão DHCP

Na janela NXT-T, selecione o separador **De Rede** e, em **gestão IP,** selecione **DHCP**. Selecione **ADD SERVER**. Escolha o Relé DHCP para o Tipo de **Servidor** e introduza o nome do servidor e o endereço IP para o servidor de retransmissão. Selecione **Guardar** para guardar as alterações.

:::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="criar servidor de retransmissão dhcp" border="true":::

Selecione **Gateways de Nível 1** sob **conectividade**. Selecione a elipse vertical no gateway Tier-1 e escolha **Editar**.

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="editar porta de entrada nível 1" border="true":::

Selecione **nenhum conjunto de atribuição ip** para definir a atribuição de endereços IP.

:::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="editar atribuição de endereçoip" border="true":::

Na caixa de diálogo, para **Tipo,** selecione **DHCP Relay Server**. No **dHCP Relay** dropdown, selecione o seu servidor de retransmissão DHCP. Quando terminar, selecione **Guardar**

:::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="definir a gestão de endereçoip" border="true":::

Especifique um IP de intervalo DHCP no segmento:

> [!NOTE]
> Esta configuração é necessária para perceber a funcionalidade de retransmissão DHCP no Segmento de Cliente DHCP. 

Em **Conectividade, selecione** **Segmentos**. Selecione as elipses verticais e selecione **Editar**. Em vez disso, se quiser adicionar um novo segmento, pode selecionar o **Segmento Adicionar** para criar um novo segmento.

:::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="editar uma subnet de rede" border="true":::

Adicione detalhes sobre o segmento. Selecione o valor em **Subredes** ou **Subnets para** adicionar ou modificar a sub-rede.

:::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segmentos de rede" border="true":::

Selecione as elipses verticais e escolha **Editar**. Se precisar de criar uma nova sub-rede, **selecione Adicionar Subnet** para criar um Gateway e configurar uma gama DHCP. Forneça a gama da piscina IP e selecione **Aplicar,** e depois selecione **Save**

:::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="editar subredes" border="true":::

Agora, um conjunto de servidores DHCP é atribuído ao segmento.

:::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Piscina de servidor DHCP atribuída ao segmento" border="true":::
