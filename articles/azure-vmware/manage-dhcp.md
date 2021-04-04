---
title: Gerir o DHCP para a Solução VMware Azure
description: Aprenda a criar e gerir o DHCP para a sua nuvem privada Azure VMware Solution.
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 11/09/2020
ms.openlocfilehash: bcaba4274b0e6b423e9fa490c80fc57204d4e153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97708556"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>Gerir o DHCP para a Solução VMware Azure

Aplicações e cargas de trabalho em execução em um ambiente de nuvem privada requerem serviços DHCP para atribuição de endereços IP.  Este artigo mostra-lhe como criar e gerir o DHCP na Azure VMware Solution de duas formas:

- Se estiver a utilizar o NSX-T para hospedar o seu servidor DHCP, terá de [criar um servidor DHCP](#create-a-dhcp-server) e [retransmitir para esse servidor.](#create-dhcp-relay-service) Quando criar o servidor DHCP, também adicionará um segmento de rede e especificará a gama de endereços IP DHCP.   

- Se estiver a utilizar um servidor DHCP externo de terceiros na sua rede, terá de criar um serviço de [retransmissão DHCP](#create-dhcp-relay-service). Quando criar um retransmissor para um servidor DHCP, quer utilize o NSX-T ou um terceiro para hospedar o seu servidor DHCP, terá de especificar a gama de endereços IP DOHCP.

>[!IMPORTANT]
>O DHCP não funciona para máquinas virtuais (VMs) na rede de alongamento VMware HCX L2 quando o servidor DHCP está no datacenter no local.  O NSX, por padrão, bloqueia todos os pedidos de DHCP de atravessar o trecho L2. Para obter a solução, consulte os [pedidos de Envio de DHCP para o procedimento do servidor DHCP no local.](#send-dhcp-requests-to-the-on-premises-dhcp-server)


## <a name="create-a-dhcp-server"></a>Criar um servidor DHCP

Se pretender utilizar o NSX-T para hospedar o seu servidor DHCP, criará um servidor DHCP. Em seguida, irá adicionar um segmento de rede e especificar a gama de endereços IP DHCP.

1. No NSX-T Manager, selecione **Networking**  >  **DHCP** e, em seguida, selecione **Add Server**.

1. Selecione **DHCP** para o **Tipo de Servidor,** forneça o nome do servidor e o endereço IP e, em seguida, selecione **Guardar**.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="adicionar servidor DHCP" border="true":::

1. Selecione **Gateways de nível 1,** selecione a elipse vertical no gateway Tier-1 e, em seguida, selecione **Editar**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="selecione o portal para usar" border="true":::

1. Selecione **No IP Alocação Definida** para adicionar uma sub-rede.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="adicionar uma sub-rede" border="true":::

1. Para **tipo**, selecione **DHCP Local Server**. 
   
1. Para o **servidor DHCP**, selecione **DHCP predefinido** e, em seguida, selecione **Guardar**.

1. **Selecione Guardar** novamente e, em seguida, selecione **Close Editing**.

### <a name="add-a-network-segment"></a>Adicionar um segmento de rede

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>Criar serviço de retransmissão DHCP

Se quiser utilizar um servidor DHCP externo de terceiros, terá de criar um serviço de retransmissão DHCP. Também especificará o intervalo de endereços IP DO DHCP no NSX-T Manager. 

1. No NSX-T Manager, selecione **Networking**  >  **DHCP** e, em seguida, selecione **Add Server**.

1. Selecione **o Relé DHCP** para o **tipo de servidor,** forneça o nome do servidor e o endereço IP e, em seguida, selecione **Guardar**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="criar serviço de retransmissão dhcp" border="true":::

1. Selecione **Gateways de nível 1,** selecione a elipse vertical no gateway Tier-1 e, em seguida, selecione **Editar**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="editar porta de entrada de nível 1" border="true":::

1. Selecione **Nenhum Conjunto de Atribuição de IP** para definir a atribuição de endereço IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="editar a atribuição de endereços ip" border="true":::

1. Para **tipo**, selecione **O Servidor DHCP**. 
   
1. Para o **Servidor DHCP**, selecione **o Relé DHCP** e, em seguida, selecione **Save**.

1. **Selecione Guardar** novamente e, em seguida, selecione **Close Editing**.


## <a name="specify-the-dhcp-ip-address-range"></a>Especificar a gama de endereços IP DHCP

1. No NSX-T Manager, selecione   >  **Segmentos** de Rede . 
   
1. Selecione a elipse vertical no nome do segmento e **selecione Editar**.
   
1. Selecione **Sub-redes de Conjunto para** especificar o endereço IP DHCP para a sub-rede. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segmentos de rede" border="true":::
      
1. Modifique o endereço IP do gateway, se necessário, e introduza o IP de alcance DHCP. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="editar sub-redes" border="true":::
      
1. **Selecione Aplicar** e, em seguida, **Guardar**. O segmento é atribuído a um conjunto de servidor DHCP.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Piscina de servidor DHCP atribuída ao segmento" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>Envie pedidos de DHCP para o servidor DHCP no local

Se pretender enviar pedidos dhCP dos VMs da Solução VMware Azure no segmento L2 estendido para o servidor DHCP no local, criará um perfil de segmento de segurança. 

1. Inscreva-se no seu vCenter no local, e em **Casa,** selecione **HCX**.

1. Selecione **extensão de rede** em **serviços**.

1. Selecione a extensão de rede que pretende suportar os pedidos de DHCP da Azure VMware Solution para o local. 

1. Tome nota do nome da rede de destino.  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="Screenshot de uma extensão de rede em VMware vSphere Client" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. No Gestor NSX-T da Solução VMware Azure, selecione Perfis de Segmentos **de Segmentos de Rede**  >    >  . 

1. Selecione **adicionar perfil de segmento** e, em seguida, **segmentar segurança**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="Screenshot de como adicionar um perfil de segmento em NSX-T" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. Forneça um nome e uma etiqueta e, em seguida, coloque o interruptor do **filtro bpdu** para ON e todos os alternadores DHCP para OFF.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="Screenshot mostrando o filtro bpdu ligado e o DHCP alterna" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. Remova todos os endereços MAC, se houver, na **lista de autorizações do filtro bpdu**.  Em seguida, selecione **Guardar**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="Screenshot mostrando endereços MAC na lista de autorizações do filtro bpdu":::

1. Em **Segmentos de Segmentos de**  >    >  Rede, na área de pesquisa, insira o nome de rede de definição.

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="Screenshot do campo de filtro de segmentos de > de rede":::

1. Selecione a elipse vertical no nome do segmento e **selecione Editar**.

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="Screenshot do botão de edição para o segmento" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. Altere a Segurança do **Segmento** para o perfil de segmento que criou anteriormente.

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="Screenshot do campo de Segurança do Segmento" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [manutenção do Anfitrião e gestão do ciclo de vida.](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management)