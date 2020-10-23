---
title: Como criar e gerir o DHCP
description: Este artigo explica como gerir o DHCP na Azure VMware Solution.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb74ed4474cc14081e59142f2f60915fccd47559
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461061"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Como criar e gerir o DHCP em Azure VMware Solution

O NSX-T fornece a capacidade de configurar o DHCP para a sua nuvem privada. Se utilizar o NSX-T para hospedar o seu servidor DHCP, consulte [o servidor Criar DHCP](#create-dhcp-server). Caso contrário, se tiver um servidor DHCP externo de terceiros na sua rede, consulte o [serviço de retransmissão Create DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Criar servidor DHCP

Utilize os seguintes passos para configurar um servidor DHCP em NSX-T.

1. No gestor NSX, navegue no **separador Networking** e selecione **DHCP**. 
1. Selecione **ADD SERVER** e, em seguida, forneça o nome do servidor e o endereço IP. 
1. Selecione **Guardar**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="adicionar servidor DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Ligue o servidor DHCP ao portal Tier-1.

1. Selecione **Gateways Tier 1**, o portal da lista e, em seguida, selecione **Editar**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="adicionar servidor DHCP" border="true":::

1. Selecione **No IP Alocação Definida** para adicionar uma sub-rede.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="adicionar servidor DHCP" border="true":::

1. Selecione **o Servidor Local DHCP** para o **tipo**. 
1. Selecione **DHCP predefinido** para o **servidor DHCP** e, em seguida, selecione **Guardar**.


1. Na janela **Tier -1 Gateway,** selecione **Guardar**. 
1. Selecione **Close Editing** para terminar.

### <a name="add-a-network-segment"></a>Adicionar um segmento de rede

Uma vez criado o seu Servidor DHCP, terá de adicionar segmentos de rede ao mesmo.

1. No NSX-T, selecione o **separador de Rede** e selecione **Segmentos** em **Conectividade**. 
1. Selecione **ADD SEGMENT** e nomeie o segmento e a ligação ao Gateway Tier-1. 
1. Selecione **set Subnets** para configurar uma nova sub-rede. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="adicionar servidor DHCP" border="true":::

1. Na janela **'set Subnets',** selecione **ADD SUBNET**. 
1. Introduza o endereço IP gateway e a gama DHCP e **selecione Adicionar** e, em seguida, **APPLY**

1. **Selecione Guardar** para adicionar o novo segmento de rede.

## <a name="create-dhcp-relay-service"></a>Criar serviço de retransmissão DHCP

1. Selecione o **separador de Rede** e, em **Gestão IP,** selecione **DHCP**. 
1. Selecione **ADD SERVER**. 
1. Selecione o Relé DHCP para o **Tipo de Servidor** e introduza o nome do servidor e o endereço IP para o servidor de retransmissão. 
1. Selecione **Guardar**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="adicionar servidor DHCP" border="true":::

1. Selecione **Gateways Tier-1** em **conectividade**. 
1. Selecione a elipse vertical no gateway Tier-1 e **selecione Editar**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="adicionar servidor DHCP" border="true":::

1. Selecione **Nenhum Conjunto de Atribuição de IP** para definir a atribuição de endereço IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="adicionar servidor DHCP" border="true":::

1. Selecione **o servidor de retransmissão DHCP** para **escrever**.
1. Selecione o seu servidor de retransmissão DHCP para **o Relé DHCP**. 
1. Selecione **Guardar**.


## <a name="specify-a-dhcp-range-ip-on-a-segment"></a>Especificar um IP de alcance DHCP num segmento

> [!NOTE]
> Esta configuração é necessária para realizar a funcionalidade de retransmissão DHCP no Segmento de Cliente DHCP. 

1. Em **Conectividade,** selecione **Segmentos.** 
1. Selecione as elipses verticais e **selecione Editar.** 

   >[!TIP]
   >Se pretender adicionar um novo segmento, selecione **Add Segment**.

1. Adicione detalhes sobre o segmento. 
1. Selecione o valor nas **sub-redes definidas** para adicionar ou modificar a sub-rede.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="adicionar servidor DHCP" border="true":::

1. Selecione as elipses verticais e escolha **Editar.** Se precisar de criar uma nova sub-rede, selecione **Add Subnet** para criar um gateway e configurar uma gama DHCP. 
1. Forneça a gama do pool IP e **selecione Aplicar**, e, em seguida, selecione **Guardar**

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="adicionar servidor DHCP" border="true":::

   Um conjunto de servidor DHCP é atribuído ao segmento.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="adicionar servidor DHCP" border="true":::
