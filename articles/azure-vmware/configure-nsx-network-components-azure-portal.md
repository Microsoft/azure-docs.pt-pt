---
title: Configure componentes de rede NSX na Solução VMware Azure
description: Aprenda a utilizar a consola Azure VMware Solution para configurar segmentos de rede NSX-T.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: dbed29fb1063b78386f9ec1e2ee00d9c685a944e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100418152"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>Configure componentes de rede NSX na Solução VMware Azure

Uma nuvem privada Azure VMware Solution vem com NSX-T como uma rede definida por software (SDDC) por padrão. Vem pré-a provisionado com um gateway NSX-T Tier-0 no modo Ative/Ative e um gateway NSX-T Tier-1 predefinido no modo Ative/Standby.  Estes gateways permitem-lhe ligar os segmentos (interruptores lógicos) e fornecer conectividade East-West e North-South. 

Depois de implementada a nuvem privada Azure VMware Solution, pode configurar os objetos NSX-T necessários da consola Azure VMware Solution em **rede de carga** de trabalho .  A consola apresenta a visão simplificada das operações NSX-T de que um administrador VMware precisa diariamente e direcionado para utilizadores que não estão familiarizados com o NSX-T.  

Terá quatro opções para configurar componentes NSX-T na consola Azure VMware Solution:
- **Segmentos** - Criar segmentos que apresentem no NSX-T Manager e vCenter.
- **DHCP** - Crie um servidor DHCP ou um relé DHCP se pretender utilizar o DHCP.
- **Espelhamento portuário** – Crie espelhamento de porta para ajudar a resolver problemas de rede.
- **DNS** – Criar um reencaminhador DNS para enviar pedidos DNS para um servidor DNS designado para resolução.  

>[!NOTE]
>Ainda terá acesso à consola NSX-T Manager, onde poderá utilizar as definições avançadas mencionadas e outras funcionalidades NSX-T. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="Screenshot mostrando quatro opções na consola Azure VMware Solution para configurar NSX-T.":::

## <a name="prerequisites"></a>Pré-requisitos
As máquinas virtuais (VMs) criadas ou migradas para a nuvem privada Azure VMware Solution devem ser anexadas a um segmento. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Criar um segmento NSX-T no portal Azure
Pode criar e configurar um segmento NSX-T a partir da consola Azure VMware Solution no portal Azure.  Estes segmentos estão ligados ao gateway padrão Tier-1, e as cargas de trabalho nestes segmentos obtêm East-West e North-South conectividade. Assim que criar o segmento, apresenta-se no NSX-T Manager e no vCenter.

>[!NOTE]
>Se planeia utilizar o DHCP, terá de [configurar um servidor DHCP ou um relé DHCP](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) antes de poder criar e configurar um segmento NSX-T.

1. Na sua nuvem privada Azure VMware Solution, em **Rede de Carga de Trabalho,** selecione **Segmentos**  >  **Add**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="Screenshot mostrando como adicionar um novo segmento.":::

1. Forneça os detalhes para o novo segmento lógico.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/create-new-segment-details.png" alt-text="Screenshot mostrando os detalhes do novo segmento.":::
   
   - **Nome do segmento** - Nome do interruptor lógico que é visível no vCenter.
   - **Porta de sub-rede** - Endereço IP gateway para a sub-rede do interruptor lógico com uma máscara de sub-rede. Os VMs estão ligados a um interruptor lógico, e todos os VMs que se ligam a este interruptor pertencem à mesma sub-rede.  Além disso, todos os VMs ligados a este segmento lógico devem ter um endereço IP do mesmo segmento.
   - **DHCP** (opcional) - Gamas DHCP para um segmento lógico. Um [servidor DHCP ou um relé DHCP](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) devem ser configurados para consumir DHCP em segmentos.  
   - **Gateway conectado**  -  *Selecionado por predefinição e lido apenas.*  Gateway de nível 1 e tipo de informação de segmento. 
      - **T1** - Nome do portal Tier-1 no NSX-T Manager. Uma nuvem privada Azure VMware Solution vem com uma porta de entrada NSX-T Tier-0 no modo Ative/Ative e um gateway NSX-T Tier-1 predefinido no modo Ative/Standby.  Os segmentos criados através da consola Azure VMware Solution apenas ligam-se ao gateway padrão Tier-1, e as cargas de trabalho destes segmentos obtêm East-West e North-South conectividade. Só pode criar mais gateways Tier-1 através do NSX-T Manager. Os gateways de nível 1 criados a partir da consola NSX-T Manager não estão visíveis na consola Azure VMware Solution. 
      - **Tipo** - Segmento de sobreposição suportado pela Azure VMware Solution.

1. Selecione **OK** para criar o segmento e prenda-o ao gateway Tier-1. 

   O segmento está agora visível na consola Azure VMware Solution, NSX-T Manger e vCenter.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Crie um servidor DHCP ou relé DHCP no portal Azure
Pode criar um servidor DHCP ou retransmitir diretamente a partir da consola Azure VMware Solution no portal Azure. O servidor ou retransmissor DHCP conecta-se ao gateway Tier-1, que é criado quando implementa a Solução VMware Azure. Todos os segmentos onde deu gamas DHCP farão parte deste DHCP.  Depois de ter criado um servidor DHCP ou um relé DHCP, tem de definir uma sub-rede ou alcance a nível de segmento para o consumir.

1. Na sua nuvem privada Azure VMware Solution, em **Rede de Carga de Trabalho,** selecione **DHCP**  >  **Add**.

2. Selecione o **SERVIDOR DHCP** ou **o RElé DHCP** e, em seguida, forneça um nome para o servidor ou retransmissão e três endereços IP. 

   >[!NOTE]
   >Para o retransmissor DHCP, apenas é necessário um endereço IP para uma configuração bem sucedida.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Screenshot mostrando como adicionar um servidor DHCP ou relé DHCP em Azure VMware Solutions.":::

4. Complete a configuração DHCP [fornecendo gamas DHCP nos segmentos lógicos](#create-an-nsx-t-segment-in-the-azure-portal) e, em seguida, selecione **OK**.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Configure o espelho da porta no portal Azure
Pode configurar o espelhamento da porta para monitorizar o tráfego de rede que envolve o encaminhamento de uma cópia de cada pacote de uma porta de comutador de rede para outra. Esta opção coloca um analisador de protocolo na porta que recebe os dados espelhados. Analisa o tráfego de uma fonte, um VM ou um grupo de VMs, e depois enviado para um destino definido. 

Para configurar o espelhamento de porta na consola Azure VMware Solution, você vai:

* [Passo 1. Criar VMs de origem e destino ou grupos VM](#step-1-create-source-and-destination-vms-or-vm-groups) – O grupo de origem tem um único VM ou vários VMs onde o tráfego está espelhado.

* [Passo 2. Criar um perfil de espelhamento de porta](#step-2-create-a-port-mirroring-profile) – Definirá a direção de tráfego para os grupos VM de origem e destino.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>Passo 1. Criar VMs de origem e destino ou grupos VM

Neste passo, você vai criar um grupo VM de origem e um grupo VM destino.

1. Na sua nuvem privada Azure VMware Solution, em **Rede de Carga de Trabalho,** selecione grupos VM **espelhantes de porta**  >    >  **Add**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="Screenshot mostrando como criar um grupo VM para espelhamento de porta.":::

1. Forneça um nome para o novo grupo VM, selecione os VMs desejados da lista e, em seguida, **Ok**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="Screenshot mostrando a lista de VMs para adicionar ao grupo VM.":::

1. Repita estes passos para criar o grupo VM de destino.

### <a name="step-2-create-a-port-mirroring-profile"></a>Passo 2. Crie um perfil de espelhamento de porta

Neste passo, definirá um perfil para a direção de tráfego dos grupos VM de origem e destino.

>[!NOTE]
>Certifique-se de que tem os grupos VM de origem e destino criados.

1. Selecione **o espelho de porta**  >  **Adicionar** e, em seguida, fornecer:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="Screenshot mostrando as informações necessárias para o perfil de espelhamento da porta.":::

   - **Nome espelhante da** porta - Nome descritivo para o perfil.
   - **Direção** - Selecione de Ingress, Egress ou Bi-direcional.
   - **Fonte** - Selecione o grupo VM de origem.
   - **Destino** - Selecione o grupo VM de destino.
   - **Descrição** - Introduza uma descrição para o espelhamento da porta.

1. Selecione **OK** para completar o perfil. 

   O perfil e os grupos VM são visíveis na consola Azure VMware Solution.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Configurar um remetente DNS no portal Azure
Irá configurar um reencaminhador DNS onde os pedidos específicos de DNS são reencaminhados para um servidor DNS designado para resolução.  Um reencaminhador DENS está associado a uma **zona DE DNS predefinido** e até três **zonas FQDN**.

>[!TIP]
>Também pode utilizar a [consola NSX-T Manager para configurar um reencaminhador DNS](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html).

Para configurar um reencaminhador DNS na consola Azure VMware Solution, irá:

* [Passo 1. Configurar uma zona DE DNS predefinida e zona FQDN](#step-1-configure-a-default-dns-zone-and-fqdn-zone) – Quando uma consulta DE DNS é recebida, um reencaminhador DENS compara o nome de domínio com os nomes de domínio na zona de DNS FQDN. 

* [Passo 2. Configure o serviço DNS](#step-2-configure-dns-service) - Configurará o serviço de reencaminhamento DNS.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>Passo 1. Configurar uma zona DE DNS predefinida e zona FQDN
Configurará uma zona DE DNS predefinida e zona FQDN para enviar consultas de DNS para o servidor a montante.  Quando uma consulta DNS é recebida, o reencaminhador DNS compara o nome de domínio na consulta com os nomes de domínio das zonas de DNS da FQDN. Se for encontrada uma correspondência, a consulta é reencaminhada para os servidores DNS especificados na zona de DNS FQDN. Se não for encontrada qualquer correspondência, a consulta é reencaminhada para os servidores DNS especificados na zona DE DNS predefinida.

>[!NOTE]
>Uma zona DE DNS predefinida deve ser definida antes de configurar uma zona FQDN. 

1. Na sua nuvem privada Azure VMware Solution, em **Rede de Carga de Trabalho**, selecione **zonas**  >  **DNS DNS**  >  **Add**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="Screenshot mostrando como adicionar zonas DNS e um serviço DNS.":::

1. Selecione **a zona DE DNS predefinido** e forneça:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="Screenshot mostrando como adicionar uma zona DE DNS padrão.":::

   1. Um nome para a zona do DNS.

   1. Até três endereços IP do servidor DNS no formato de **8.8.8.8**.

1. Selecione **a zona FQDN** e forneça:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Screenshot mostrando como adicionar uma zona FQDN. ":::

   1. Um nome para a zona do DNS.

   1. O domínio FQDN.

   1. Até três endereços IP do servidor DNS no formato de **8.8.8.8**.

1. Selecione **OK** para terminar a adição da zona DE DNS predefinido e o serviço DNS.

### <a name="step-2-configure-dns-service"></a>Passo 2. Configure o serviço DNS

1. Selecione o separador **de serviço DNS,** selecione **Add** e, em seguida, forneça:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="Screenshot mostrando as informações necessárias para o serviço DNS.":::

   1. Um nome para o serviço DNS.

   1. Insira o endereço IP para o serviço DNS.

   1. Selecione a zona DNS predefinida que criou no separador zonas DNS.

   1. Selecione as zonas FQDN que adicionou no separador zonas DNS.

   1. Selecione o **nível de Registo**.

   >[!TIP]
   >**O Gateway Tier-1** é selecionado por predefinição e reflete o gateway criado ao implementar a Solução VMware Azure.

1. Selecione **OK**. 

   O serviço DNS foi adicionado com sucesso.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="Screenshot mostrando o serviço DNS adicionado com sucesso.":::

