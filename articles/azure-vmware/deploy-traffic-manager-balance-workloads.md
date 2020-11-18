---
title: Implementar gestor de tráfego para equilibrar cargas de trabalho da Azure VMware Solution (AVS)
description: Saiba como integrar o Traffic Manager com a Azure VMware Solution (AVS) para equilibrar as cargas de trabalho das aplicações em vários pontos finais em diferentes regiões.
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: ed74bb0dfc533abadd50af32afc06c9cb4106193
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874311"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>Implementar gestor de tráfego para equilibrar cargas de trabalho da Azure VMware Solution (AVS)

Este artigo acompanha-o através da integração do Traffic Manager com a Azure VMware Solution (AVS) para equilibrar as cargas de trabalho das aplicações em vários pontos finais. Vamos analisar um cenário em que o Traffic Manager direciona o tráfego entre três portas de aplicação que abrangem várias regiões avs: Eua Ocidental, Europa Ocidental e no local no Leste dos EUA. 

O Azure Traffic Manager é um equilibrador de carga baseado em DNS que lhe permite distribuir o tráfego da melhor forma para serviços em todas as regiões globais de Azure. Carregará o tráfego de aplicações de equilíbrio em toda a Azure executando cargas de trabalho e pontos finais públicos externos. Para mais informações sobre o Gestor de Tráfego, veja [o que é o Gestor de Tráfego?](../traffic-manager/traffic-manager-overview.md)

Rever primeiro os [Pré-requisitos;](#prerequisites) então vamos percorrer os procedimentos para:

> [!div class="checklist"]
> * Verifique a configuração dos seus gateways de aplicação
> * Verificar a configuração do segmento NSX-T
> * Crie o seu perfil de Gestor de Tráfego
> * Adicione pontos finais externos no seu perfil de Gestor de Tráfego

## <a name="topology"></a>Topologia

Como mostrado no seguinte número, o Azure Traffic Manager fornece um equilíbrio de carga para as aplicações ao nível do DNS entre os pontos finais regionais. Os gateways de aplicação têm membros de reserva configurados como Servidores IIS e são referenciados como pontos finais externos AVS.

A ligação sobre a rede virtual entre as duas regiões de nuvem privada AVS, Eua Ocidental e Europa Ocidental, e um servidor no local no Leste dos EUA, usa um gateway ExpressRoute.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagrama da arquitetura da integração do Gestor de Tráfego com Azure VMware Solution" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::
 
## <a name="prerequisites"></a>Pré-requisitos

- Três máquinas virtuais configuradas como Microsoft IIS Servers em diferentes regiões AVS: West US, West Europe, e nas instalações. 

- Uma porta de aplicação com pontos finais externos nos EUA Ocidentais, Europa Ocidental e nas instalações.

- Hospedeiro com conectividade de internet para verificação. 

## <a name="verify-configuration-of-your-application-gateways"></a>Verifique a configuração dos seus gateways de aplicação

[O Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) é um balanceador de carga de tráfego web de camada 7 que lhe permite gerir o tráfego para as suas aplicações web. Para mais informações sobre o Gateway de Aplicações, veja [o que é O Gateway de Aplicações Azure?](../application-gateway/overview.md) 

Neste cenário, três instâncias de gateway de aplicação são configuradas como pontos finais externos do AVS. Os gateways de aplicação têm máquinas virtuais AVS configuradas como membros do pool backend para carregar o equilíbrio dos pedidos da camada 7. (Para aprender a configurar o Gateway de aplicações com máquinas virtuais AVS como piscinas de backend, consulte [Use Azure Application Gateway para proteger as suas aplicações web na Solução VMware Azure](protect-azure-vmware-solution-with-application-gateway.md).)  

Os seguintes passos verificam a configuração correta dos seus gateways de aplicação.

1. Abra o portal Azure e selecione **gateways de aplicações** para ver uma lista dos seus atuais gateways de aplicações. 

    Para este cenário, configuramos três portais de aplicação:
    - AVS-GW-WUS
    - AVS-GW-EUS (nas instalações)
    - AVS-GW-WEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Screenshot da página de gateway de aplicação mostrando lista de gateways de aplicações configurados." lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. Selecione um dos seus gateways de aplicação previamente implantados. Abre-se uma janela mostrando várias informações no gateway de aplicação. Selecione **piscinas backend** para verificar a configuração de uma das piscinas de backend.

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Screenshot da página de gateway da aplicação mostrando detalhes do gateway de aplicações selecionado." lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. Neste caso, vemos um membro do pool de backend de máquina virtual configurado como um servidor web com um endereço IP de 172.29.1.10.
 
    :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Screenshot da página de pool backend Edit com endereço IP alvo realçado.":::

    Pode verificar da mesma forma a configuração dos outros gateways de aplicação e membros do pool backend. 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>Verificar a configuração do segmento NSX-T

Os segmentos de rede criados no NSX-T Manager são utilizados como redes para máquinas virtuais no vCenter. Para mais informações, consulte o tutorial, [Crie um segmento de rede NSX-T em Azure VMware Solution (AVS)](tutorial-nsx-t-network-segment.md).

No nosso cenário, um segmento NSX-T está configurado no ambiente AVS onde a máquina virtual membro do pool backend está anexada.

1. Selecione **Segmentos** para ver os seus segmentos configurados. Neste caso, vemos que o Contoso-segment1 está ligado ao gateway Contoso-T01, um router flexível Tier-1.

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Screenshot mostrando perfis de segmento no NSX-T Manager." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

2. Selecione **Gateways Tier-1** para ver uma lista dos seus gateways Tier-1 com o número de segmentos ligados. Selecione o segmento ligado ao Contoso-T01. Abre-se uma janela mostrando a interface lógica configurada no router Tier-01. Isto serve como porta de entrada para a máquina virtual do membro da piscina de backend ligada ao segmento.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Screenshot mostrando o endereço de gateway do segmento selecionado.":::    

3. No cliente VM vSphere, selecione a máquina virtual para ver os seus detalhes. Note que o seu endereço IP corresponde ao que vimos no passo 3 da secção anterior: 172.29.1.10.

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Screenshot mostrando detalhes VM no VSphere Client." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Selecione a máquina virtual e, em seguida, clique em **AÇÕES > Editar Definições** para verificar a ligação ao segmento NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Crie o seu perfil de Gestor de Tráfego

1. Faça login no [portal Azure](https://rc.portal.azure.com/#home). No âmbito **da Azure Services > Networking,** selecione **perfis de Gestor de Tráfego**.

2. **Selecione + Adicionar** para criar um novo perfil de Gestor de Tráfego.
 
3. Fornecer nome de perfil, método de encaminhamento (usaremos ponderado neste cenário; ver [métodos de encaminhamento de Gestor de Tráfego),](../traffic-manager/traffic-manager-routing-methods.md)subscrição e grupo de recursos, e selecione **Criar**.

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Adicione pontos finais externos no perfil de Gestor de Tráfego

1. Selecione o perfil de Gestor de Tráfego no painel de resultados de pesquisa, selecione **Pontos de Final** e, em seguida, **+ Adicionar**.

2. Introduza os detalhes necessários: Tipo, Nome, Nome de domínio totalmente qualificado (FQDN) ou IP, e Peso (neste cenário, estamos atribuindo um peso de 1 a cada ponto final). Selecione **Adicionar**. Isto cria o ponto final externo. O estado do monitor deve estar **online**. Repita os mesmos passos para criar mais dois pontos finais externos, um numa região diferente e outro no local. Uma vez criados, os três serão exibidos no perfil de Gestor de Tráfego, e o estado dos três deve estar **Online**.

3. Selecione **Descrição geral**. Copie o URL em **nome DNS**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Screenshot mostrando uma visão geral do ponto final do Traffic Manager com o nome DNS em destaque." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Cole o URL de nome DNS num browser. A imagem que se segue mostra o tráfego direcionado para a região da Europa Ocidental.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Screenshot da janela do navegador mostrando tráfego encaminhado para a Europa Ocidental."::: 

5. Atualize o seu browser. A imagem que se segue mostra o tráfego agora direcionado para outro conjunto de membros da piscina na região oeste dos EUA.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Screenshot da janela do navegador mostrando tráfego encaminhado para os EUA Ocidentais."::: 

6. Refresque o seu navegador novamente. A imagem que se segue mostra o tráfego agora direcionado para o conjunto final de membros da piscina backend no local.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Screenshot da janela do navegador mostrando tráfego encaminhado para o local.":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

- [Utilização do Gateway de Aplicações Azure na Solução VMware Azure (AVS)](protect-azure-vmware-solution-with-application-gateway.md)
- [Métodos de encaminhamento do Gestor de Tráfego](../traffic-manager/traffic-manager-routing-methods.md)
- [Combinação de serviços de equilíbrio de carga em Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Medição do desempenho do Gestor de Tráfego](../traffic-manager/traffic-manager-performance-considerations.md)
