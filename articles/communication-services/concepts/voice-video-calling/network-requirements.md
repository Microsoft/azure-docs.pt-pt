---
title: Prepare a rede da sua organização para serviços de comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Conheça os requisitos de rede para a chamada de voz e vídeo dos Serviços de Comunicação Azure
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 3/23/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 753bb7a88f8032d6ed9aeac1b1adf4f34d7cec43
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105051353"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Garantir meios de comunicação de alta qualidade nos Serviços de Comunicação Azure

Este documento fornece uma visão geral dos fatores e boas práticas que devem ser considerados na construção de experiências de comunicação multimédia de alta qualidade com os Serviços de Comunicação Azure.

## <a name="factors-that-affect-media-quality-and-reliability"></a>Fatores que afetam a qualidade e a fiabilidade dos meios de comunicação

Existem muitos fatores diferentes que contribuem para a qualidade dos meios de comunicação em tempo real (áudio, vídeo e partilha de aplicações). Estes incluem a qualidade da rede, largura de banda, firewall, hospedeiro e configurações do dispositivo.


### <a name="network-quality"></a>Qualidade da rede

A qualidade dos meios de comunicação em tempo real durante o PI é significativamente impactada pela qualidade da conectividade subjacente à rede, mas especialmente pela quantidade de:
* **Latência.** Este é o tempo que leva para obter um pacote IP do ponto A para o ponto B na rede. Este atraso de propagação da rede é determinado pela distância física entre os dois pontos e por qualquer sobrecarga adicional incorrido pelos dispositivos através dos quais o seu tráfego flui. A latência é medida como tempo de ida ou ida e volta (RTT).
* **Perda de pacotes**. Uma percentagem de pacotes que se perdem numa dada janela de tempo. A perda de pacotes afeta diretamente a qualidade do áudio — desde pequenos pacotes perdidos individuais que quase não têm impacto, até perdas de rebentamento consecutivas que causam um corte de áudio completo.
* **Nervosismo de chegada inter-pacote ou simplesmente nervosismo**. Esta é a variação média do atraso entre pacotes sucessivos. Os Serviços de Comunicação Azure podem adaptar-se a alguns níveis de nervosismo através de tampão. Só quando o nervosismo excede o tampão é que um participante notará os seus efeitos.

### <a name="network-bandwidth"></a>Largura de banda de rede

Certifique-se de que a sua rede está configurada para suportar a largura de banda exigida por sessões de media simultâneas dos Serviços de Comunicação Azure e outras aplicações empresariais. Testar o caminho da rede de ponta a ponta para estrangulamentos de largura de banda é fundamental para a implementação bem sucedida da sua solução de Serviços de Comunicação Multimédia.

Abaixo estão os requisitos de largura de banda para as bibliotecas de clientes JavaScript:

|Largura de banda|Cenários|
|:--|:--|
|40 kbps|Chamada de áudio peer-to-peer|
|500 kbps|Chamada de áudio peer-to-peer e partilha de ecrã|
|500 kbps|Vídeo de qualidade peer-to-peer chamando 360p a 30fps|
|1.2 Mbps|Vídeo de qualidade peer-to-peer HD com resolução de HD 720p a 30fps|
|500 kbps|Vídeo em grupo chamando 360p a 30fps|
|1.2 Mbps|Hd Group vídeo chamando com resolução de HD 720p a 30fps| 

Abaixo estão os requisitos de largura de banda para as bibliotecas de clientes Android e iOS nativos:

|Largura de banda|Cenários|
|:--|:--|
|30 kbps|Chamada de áudio peer-to-peer |
|130 kbps|Chamada de áudio peer-to-peer e partilha de ecrã|
|500 kbps|Vídeo de qualidade peer-to-peer chamando 360p a 30fps|
|1.2 Mbps|Vídeo de qualidade peer-to-peer HD com resolução de HD 720p a 30fps|
|1,5 Mbps|Vídeo de qualidade peer-to-peer HD com resolução de HD 1080p a 30fps |
|500kbps/1Mbps|Chamada de vídeo em grupo|
|1Mbps/2Mbps|Chamada de vídeo do Grupo HD (vídeos de 540p no ecrã de 1080p)|

### <a name="firewalls-configuration"></a>Configuração de firewall

As ligações Azure Communication Services requerem conectividade da Internet a portas específicas e endereços IP para proporcionar experiências multimédia de alta qualidade. Sem acesso a estas portas e endereços IP, os Serviços de Comunicação Azure ainda podem funcionar. No entanto, a experiência ideal é fornecida quando as portas recomendadas e as gamas IP estão abertas.

| Categoria | Gamas IP ou FQDN | Portas | 
| :-- | :-- | :-- |
| Tráfego mediático | [Gama de endereços IP em nuvem pública de Azure](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 a 3481, portas TCP 443 |
| Sinalização, telemetria, registo| *.skype.com, *.microsoft.com, *.azure.net, *.azureedge.net, *.office.com, *.trouter.io | TCP 443, 80 |

### <a name="network-optimization"></a>Otimização de rede

As seguintes tarefas são opcionais e não são necessárias para a execução dos Serviços de Comunicação Azure. Utilize esta orientação para otimizar o desempenho da sua rede e dos Serviços de Comunicação Azure ou se souber que tem algumas limitações de rede.
Talvez queira otimizar ainda mais se:
* Os Serviços de Comunicação Azure funciona lentamente (talvez tenha largura de banda insuficiente)
* As chamadas continuam a cair (pode ser devido a bloqueadores de firewall ou proxy)
* As chamadas têm estática e cortadas, ou vozes soam como robôs (pode ser nervosismo ou perda de pacotes)

| Tarefa de otimização de rede | Detalhes |
| :-- | :-- |
| Planeie a sua rede | Nesta documentação pode encontrar requisitos mínimos para a sua rede para chamadas. Consulte o [exemplo de Equipas para planear a sua rede](https://docs.microsoft.com/microsoftteams/tutorial-network-planner-example) |
| Resolução de nomes externos | Certifique-se de que todos os computadores que executam as bibliotecas de clientes do Azure Communications Services podem resolver consultas externas de DNS para descobrir os serviços prestados pelos Serviços de Comunicação Azure e que as suas firewalls não estão a impedir o acesso. Certifique-se de que as bibliotecas do cliente podem resolver endereços *.skype.com, *.microsoft.com, *.azure.net, *.azureedge.net, *.office.com, *.trouter.io  |
| Manter a persistência da sessão | Certifique-se de que a sua firewall não altera os endereços ou portas de tradução de endereços de rede mapeados (NAT) para UDP
Validar o tamanho da piscina NAT | Validar o tamanho da piscina de tradução de endereço de rede (NAT) necessário para a conectividade do utilizador. Quando vários utilizadores e dispositivos acederem a Serviços de Comunicação Azure utilizando [a Tradução de Endereços de Rede (NAT) ou tradução de endereços portuários (PAT)](https://docs.microsoft.com/office365/enterprise/nat-support-with-office-365), certifique-se de que os dispositivos escondidos atrás de cada endereço IP publicamente roteado não excedem o número suportado. Certifique-se de que os endereços IP públicos adequados são atribuídos às piscinas NAT para evitar a exaustão do porto. A exaustão portuária contribuirá para que os utilizadores internos e dispositivos não sejam capazes de se ligarem aos Serviços de Comunicação do Azure |
| Orientação de Deteção e Prevenção de Intrusões | Se o seu ambiente tiver um Sistema [de Deteção](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools) ou Prevenção de Intrusões (IDS/IPS) implantado para uma camada extra de segurança para ligações de saída, permita que todos os URLs de Serviços de Comunicação Azure |
| VPN de túnel dividido configure | Recomendamos que forneça um caminho alternativo para o tráfego das equipas que contorna a rede privada virtual (VPN), vulgarmente conhecida como [VPN de túnel dividido.](https://docs.microsoft.com/windows/security/identity-protection/vpn/vpn-routing) O túnel dividido significa que o tráfego para os Serviços de Comunicações Azure não passa pela VPN, mas vai diretamente para Azure. Contornar a sua VPN terá um impacto positivo na qualidade dos meios de comunicação, e reduz a carga dos dispositivos VPN e da rede da organização. Para implementar uma VPN de túnel dividido, trabalhe com o seu fornecedor VPN. Outras razões pelas quais recomendamos contornar a VPN: <ul><li> As VPNs não são normalmente concebidas ou configuradas para suportar meios de comunicação em tempo real.</li><li> As VPNs também podem não apoiar a UDP (que é necessária para os Serviços de Comunicação Azure)</li><li>As VPNs também introduzem uma camada extra de encriptação em cima do tráfego de mídia que já está encriptado.</li><li>A conectividade com os Serviços de Comunicação Azure pode não ser eficiente devido ao tráfego de fixação de cabelo através de um dispositivo VPN.</li></ul>|
| Implementar QoS | [Utilizar a Qualidade de Serviço (QoS)](https://docs.microsoft.com/microsoftteams/qos-in-teams) para configurar a priorização do pacote. Isto melhorará a qualidade da chamada e ajudá-lo-á a monitorizar e a resolver a qualidade da chamada. O QoS deve ser implementado em todos os segmentos de uma rede gerida. Mesmo quando uma rede foi adequadamente aprovisionada para a largura de banda, a QoS proporciona uma mitigação de riscos em caso de eventos de rede inesperados. Com o QoS, o tráfego de voz é priorizado para que estes eventos inesperados não afetem negativamente a qualidade. | 
| Otimizar Wi-Fi | Semelhantes à VPN, as redes Wi-Fi não são necessariamente concebidas ou configuradas para suportar meios de comunicação em tempo real. Planear ou otimizar uma rede Wi-Fi para apoiar os Serviços de Comunicação Azure é uma consideração importante para uma implementação de alta qualidade. Considere estes fatores: <ul><li>Implemente qoS ou WiFi Multimedia (WMM) para garantir que o tráfego de mídia está a ser priorizado adequadamente sobre as suas redes Wi-Fi.</li><li>Planeie e otimize as bandas Wi-Fi e a colocação do ponto de acesso. A gama de 2,4 GHz pode proporcionar uma experiência adequada dependendo da colocação do ponto de acesso, mas os pontos de acesso são frequentemente afetados por outros dispositivos de consumo que operam nessa gama. A gama de 5 GHz é mais adequada para os meios de comunicação em tempo real devido à sua densa gama, mas requer mais pontos de acesso para obter cobertura suficiente. Os pontos finais também precisam de suportar esse alcance e ser configurados para alavancar essas bandas em conformidade.</li><li>Se estiver a utilizar redes Wi-Fi de banda dupla, considere implementar a direção da banda. A direção da banda é uma técnica implementada por fornecedores Wi-Fi para influenciar clientes de dupla banda a utilizar a gama de 5 GHz.</li><li>Quando os pontos de acesso do mesmo canal estão demasiado próximos, podem causar sobreposição de sinais e competir involuntariamente, resultando numa experiência de utilizador degradada. Certifique-se de que os pontos de acesso que estão ao lado uns dos outros estão em canais que não se sobrepõem.</li></ul> Cada fornecedor sem fios tem as suas próprias recomendações para implementar a sua solução sem fios. Consulte o seu fornecedor Wi-Fi para obter orientações específicas.|



### <a name="operating-system-and-browsers-for-javascript-client-libraries"></a>Sistema operativo e Browsers (para bibliotecas de clientes JavaScript)

As bibliotecas de clientes de voz/vídeo do Azure Communication Services suportam certos sistemas operativos e navegadores.
Saiba mais sobre os sistemas operativos e navegadores que as bibliotecas do cliente de chamada suportam na [documentação conceptual da chamada.](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)

## <a name="next-steps"></a>Passos seguintes

Os seguintes documentos podem ser interessantes para si:

- Saiba mais sobre [chamar bibliotecas](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)
- Conheça a [arquitetura do servidor de clientes](https://docs.microsoft.com/azure/communication-services/concepts/client-and-server-architecture)
- Saiba mais sobre [as topologias do fluxo de chamadas](https://docs.microsoft.com/azure/communication-services/concepts/call-flows)
