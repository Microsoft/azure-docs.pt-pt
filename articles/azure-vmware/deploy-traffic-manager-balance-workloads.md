---
title: Implementar Gestor de Tráfego para equilibrar cargas de trabalho da Solução VMware Azure
description: Saiba como integrar o Traffic Manager com a Azure VMware Solution para equilibrar as cargas de trabalho das aplicações em vários pontos finais em diferentes regiões.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988600"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Implementar Gestor de Tráfego para equilibrar cargas de trabalho da Solução VMware Azure

Este artigo percorre os passos de como integrar [o Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) com a Azure VMware Solution. A integração equilibra as cargas de trabalho da aplicação em vários pontos finais. Este artigo também percorre os passos de como configurar o Gestor de Tráfego para direcionar o tráfego entre três [Gateway de aplicação Azure](../application-gateway/overview.md) abrangendo várias regiões da Azure VMware Solution. 

Os gateways têm máquinas virtuais Azure VMware Solution (VMs) configuradas como membros de backend pool para carregar o equilíbrio dos pedidos da camada 7. Para mais informações, consulte [Use Azure Application Gateway para proteger as suas aplicações web na Solução VMware Azure](protect-azure-vmware-solution-with-application-gateway.md)

O diagrama mostra como o Traffic Manager fornece o equilíbrio de carga para as aplicações ao nível do DNS entre os pontos finais regionais. Os gateways têm membros de reserva configurados como Servidores IIS e referenciados como pontos finais externos Azure VMware Solution. A ligação sobre a rede virtual entre as duas regiões de nuvem privada utiliza um gateway ExpressRoute.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagrama de arquitetura da integração do Gestor de Tráfego com Azure VMware Solution" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Antes de começar, primeiro reveja os [Pré-requisitos](#prerequisites) e depois passaremos pelos procedimentos para:

> [!div class="checklist"]
> * Verifique a configuração dos seus gateways de aplicação e do segmento NSX-T
> * Crie o seu perfil de Gestor de Tráfego
> * Adicione pontos finais externos no seu perfil de Gestor de Tráfego

## <a name="prerequisites"></a>Pré-requisitos

- Três VMs configurados como Servidores Microsoft IIS em execução em diferentes regiões de Solução VMware Azure: 
   - E.U.A. Oeste
   - Europa Ocidental
   - Leste dos EUA (no local) 

- Uma porta de entrada de aplicações com pontos finais externos nas regiões Azure VMware Solution mencionadas acima.

- Hospedeiro com conectividade de internet para verificação. 

- Um [segmento de rede NSX-T criado na Solução VMware Azure](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Verifique a configuração dos seus gateways de aplicações

Os seguintes passos verificam a configuração dos seus gateways de aplicação.

1. No portal Azure, selecione **gateways de aplicação** para ver uma lista dos seus atuais gateways de aplicações:

   - AVS-GW-WUS
   - AVS-GW-EUS (no local)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Screenshot da página de gateway de aplicação mostrando lista de gateways de aplicações configurados." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Selecione um dos seus gateways de aplicação previamente implantados. 

   Abre-se uma janela mostrando várias informações no gateway de aplicação. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Screenshot da página de gateway da aplicação mostrando detalhes do gateway de aplicações selecionado." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Selecione **piscinas backend** para verificar a configuração de uma das piscinas de backend. Você vê um membro do pool de backend VM configurado como um servidor web com um endereço IP de 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Screenshot da página de pool backend Edit com endereço IP alvo realçado.":::

1. Verifique a configuração dos outros portais de aplicação e membros do pool de backend. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Verifique a configuração do segmento NSX-T

Os seguintes passos verificam a configuração do segmento NSX-T no ambiente Azure VMware Solution.

1. Selecione **Segmentos** para ver os seus segmentos configurados.  Você vê Contoso-segment1 conectado ao gateway Contoso-T01, um router flexível Tier-1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Screenshot mostrando perfis de segmento no NSX-T Manager." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Selecione **Gateways Tier-1** para ver uma lista de gateways tier-1 com o número de segmentos ligados. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Screenshot mostrando o endereço de gateway do segmento selecionado.":::    

1. Selecione o segmento ligado ao Contoso-T01. Abre-se uma janela mostrando a interface lógica configurada no router Tier-01. Serve como porta de entrada para o membro da piscina de backend VM ligado ao segmento.

1. No cliente vSphere, selecione o VM para ver os seus detalhes. 

   >[!NOTE]
   >O seu endereço IP corresponde ao membro do pool de backend VM configurado como um servidor web da secção anterior: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Screenshot mostrando detalhes VM no vSphere Client." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Selecione o VM e, em seguida, selecione **AÇÕES > Editar Definições** para verificar a ligação ao segmento NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Crie o seu perfil de Gestor de Tráfego

1. Inicie sessão no [portal do Azure](https://rc.portal.azure.com/#home). No âmbito **da Azure Services > Networking,** selecione **perfis de Gestor de Tráfego**.

2. **Selecione + Adicionar** para criar um novo perfil de Gestor de Tráfego.
 
3. Forneça as seguintes informações e, em seguida, **selecione Criar**:

   - Nome de perfil
   - Método de encaminhamento (utilização [ponderada](../traffic-manager/traffic-manager-routing-methods.md)
   - Subscrição
   - Grupo de recursos

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Adicione pontos finais externos no perfil de Gestor de Tráfego

1. Selecione o perfil de Gestor de Tráfego no painel de resultados de pesquisa, selecione **Pontos de Final** e, em seguida, **+ Adicionar**.

1. Para cada um dos pontos finais externos nas diferentes regiões, insira os detalhes necessários e, em seguida, **selecione Adicionar:** 
   - Tipo
   - Name
   - Nome de domínio totalmente qualificado (FQDN) ou IP
   - Peso (atribuir um peso de 1 a cada ponto final). 

   Uma vez criados, os três espetáculos no perfil de Gestor de Tráfego. O estado do monitor dos três deve estar **online**.

3. Selecione **Overview** e copie o URL em **nome DNS**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Screenshot mostrando uma visão geral do ponto final do Traffic Manager com o nome DNS em destaque." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Cole o URL de nome DNS num browser. A imagem mostra o tráfego direcionado para a região da Europa Ocidental.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Screenshot da janela do navegador mostrando tráfego encaminhado para a Europa Ocidental."::: 

5. Atualize o seu browser. A imagem mostra o tráfego direcionado para outro conjunto de membros da piscina na região oeste dos EUA.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Screenshot da janela do navegador mostrando tráfego encaminhado para os EUA Ocidentais."::: 

6. Refresque o seu navegador novamente. A imagem mostra o tráfego direcionado para o conjunto final de membros da piscina de backend no local.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Screenshot da janela do navegador mostrando tráfego encaminhado para o local.":::

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu a integração do Azure Traffic Manager com a Azure VMware Solution, talvez queira saber:

- [Utilização do Gateway de Aplicações Azure na Solução Azure VMware](protect-azure-vmware-solution-with-application-gateway.md).
- [Métodos de encaminhamento do Gestor de Tráfego](../traffic-manager/traffic-manager-routing-methods.md).
- [Combinando serviços de equilíbrio de carga em Azure.](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Medição do desempenho do Gestor de Tráfego.](../traffic-manager/traffic-manager-performance-considerations.md)
