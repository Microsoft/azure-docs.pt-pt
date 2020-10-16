---
title: Como criar e gerir o DHCP
description: Este artigo explica como gerir o DHCP na Azure VMware Solution.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 2c059918f57b7f01058a031f1bf281b243855661
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332836"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Como criar e gerir o DHCP em Azure VMWare Solution

O NSX-T fornece a capacidade de configurar o DHCP para a sua nuvem privada. Se planeia utilizar o NSX-T para hospedar o seu servidor DHCP, consulte [o servidor Criar DHCP](#create-dhcp-server). Caso contrário, se tiver um servidor DHCP externo de terceiros na sua rede e pretender transmitir pedidos para esse servidor DHCP, consulte [o serviço de retransmissão DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Criar servidor DHCP

Utilize os seguintes passos para configurar um servidor DHCP em NSX-T.

A partir do gestor NSX, navegue para o **separador de Networking** e selecione **DHCP** sob **gestão IP**. Selecione o botão **ADD SERVER.** Em seguida, forneça o nome do servidor e o endereço IP do servidor. Uma vez feito, **selecione Guardar**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="adicionar servidor DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Ligue o servidor DHCP ao portal Tier-1.

1. Selecione **Gateways tier 1,** o gateway e, em seguida, selecione **Editar**

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="adicionar servidor DHCP" border="true":::

1. Adicione uma sub-rede selecionando **No IP Alocação**

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="adicionar servidor DHCP" border="true":::

1. No ecrã seguinte, selecione **o Servidor Local DHCP** a partir do **dropdown tipo.** Para **o Servidor DHCP**, selecione **DHCP predefinido** e selecione **Save**.

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="adicionar servidor DHCP" border="true":::

1. Na janela **Tier -1 Gateway,** selecione **Guardar**. No ecrã seguinte verá **Alterações Guardadas,** selecione **Close Editing** para terminar.

### <a name="add-a-network-segment"></a>Adicionar um segmento de rede

Uma vez criado o seu Servidor DHCP, terá de adicionar segmentos de rede ao mesmo.

1. No NSX-T, selecione o **separador de Rede** e selecione **Segmentos** em **Conectividade**. Selecione **SEGMENTO DE ADICIONAR**. Nomeie o segmento e a ligação ao Gateway Tier-1. Em seguida, selecione **set Subnets** para configurar uma nova sub-rede. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="adicionar servidor DHCP" border="true":::

1. Na janela **'set Subnets',** selecione **ADD SUBNET**. Introduza o endereço IP gateway e a gama DHCP e **selecione Adicionar** e, em seguida, **APPLY**

   :::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="adicionar servidor DHCP" border="true":::

1. Quando estiver concluído, **selecione Guardar** para completar a adição de um segmento de rede.

   :::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="adicionar servidor DHCP" border="true":::

## <a name="create-dhcp-relay-service"></a>Criar serviço de retransmissão DHCP

1. Na janela NXT-T, selecione o **separador de Rede** e, em **Gestão IP,** selecione **DHCP**. Selecione **ADD SERVER**. Escolha o Relé DHCP para o **Tipo de Servidor** e introduza o nome do servidor e o endereço IP para o servidor de retransmissão. Selecione **Guardar** para guardar as alterações.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="adicionar servidor DHCP" border="true":::

1. Selecione **Gateways Tier-1** em **conectividade**. Selecione a elipse vertical no gateway Tier-1 e escolha **Editar**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="adicionar servidor DHCP" border="true":::

1. Selecione **Nenhum Conjunto de Atribuição de IP** para definir a atribuição de endereço IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="adicionar servidor DHCP" border="true":::

1. Na caixa de diálogo, para **Tipo,** selecione **o Servidor de Retransmissão DHCP**. No dropdown **do Relé DHCP,** selecione o seu servidor de retransmissão DHCP. Quando terminar, **selecione Guardar**

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="adicionar servidor DHCP" border="true":::

## <a name="specify-a-dhcp-range-ip-on-segment"></a>Especificar um DHCP Range IP no Segmento

> [!NOTE]
> Esta configuração é necessária para realizar a funcionalidade de retransmissão DHCP no Segmento de Cliente DHCP. 

1. Em **Conectividade,** selecione **Segmentos.** Selecione as elipses verticais e **selecione Editar.** Em vez disso, se quiser adicionar um novo segmento, pode selecionar **Adicionar Segmento** para criar um novo segmento.

   :::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="adicionar servidor DHCP" border="true":::

1. Adicione detalhes sobre o segmento. Selecione o valor nas **sub-redes** ou **sub-redes de conjunto** para adicionar ou modificar a sub-rede.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="adicionar servidor DHCP" border="true":::

1. Selecione as elipses verticais e escolha **Editar.** Se precisar de criar uma nova sub-rede, selecione **Add Subnet** para criar um Gateway e configurar uma gama DHCP. Forneça o alcance do pool IP e **selecione Aplicar**, e, em seguida, selecione **Guardar**

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="adicionar servidor DHCP" border="true":::

1. Agora, um conjunto de servidor DHCP é atribuído ao segmento.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="adicionar servidor DHCP" border="true":::
