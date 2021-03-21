---
title: Topologias de fluxo de chamadas nos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre as topologias de fluxo de chamadas nos Serviços de Comunicação Azure.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 526e3a1e4eeb6ef6a31a33498241d9a7443cca35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490641"
---
# <a name="call-flow-topologies"></a>Topologias de fluxo de chamadas
Este artigo descreve as topologias de fluxo de fluxo de chamadas Azure Communication Services. Este é um ótimo artigo para rever se você é um cliente empresarial que integra serviços de comunicação dentro de uma rede que você gere. Para uma introdução aos fluxos de chamada dos Serviços de Comunicação, visite a documentação conceptual dos [fluxos de chamada.](./call-flows.md)

## <a name="background"></a>Fundo

### <a name="network-concepts"></a>Conceitos de rede

Antes de rever as topologias do fluxo de chamadas, definiremos alguns termos que são referidos ao longo do documento.

Uma **rede de clientes** contém quaisquer segmentos de rede que gere. Isto pode incluir redes com fios e sem fios dentro do seu escritório ou entre escritórios, centros de dados e fornecedores de serviços de internet.

Uma rede de clientes geralmente tem vários perímetros de rede com firewalls e/ou servidores proxy que aplicam as políticas de segurança da sua organização. Recomendamos a realização de uma [avaliação abrangente da rede](/microsoftteams/3-envision-evaluate-my-environment) para garantir o melhor desempenho e qualidade da sua solução de comunicação.

A **rede de Serviços de Comunicação** é o segmento de rede que suporta os Serviços de Comunicação Azure. Esta rede é gerida pela Microsoft e é distribuída em todo o mundo com bordas próximas da maioria das redes de clientes. Esta rede é responsável pelo transporte de retransmissão, processamento de meios de comunicação para chamadas de grupo, e outros componentes que suportam comunicações de meios de comunicação em tempo real ricos.

### <a name="types-of-traffic"></a>Tipos de tráfego

Os Serviços de Comunicação **baseiam-se** principalmente em dois tipos de tráfego: meios de comunicação em tempo real e **sinalização.**

**Os meios de comunicação em tempo real** são transmitidos através do Protocolo de Transporte em Tempo Real (RTP). Este protocolo suporta transmissão de dados de áudio, vídeo e ecrã. Estes dados são sensíveis a problemas de latência da rede. Embora seja possível transmitir meios em tempo real usando TCP ou HTTP, recomendamos a utilização da UDP como protocolo de camada de transporte para suportar experiências de utilizador final de alto desempenho. As cargas de mídia transmitidas através da RTP são asseguradas através do SRTP.

Os utilizadores da sua solução de Serviços de Comunicação estarão a ligar-se aos seus serviços a partir dos seus dispositivos clientes. A comunicação entre estes dispositivos e os seus servidores é tratada com **sinalização**. Por exemplo: iniciação de chamadas e chat em tempo real são suportados por sinalização entre dispositivos e o seu serviço. A maioria dos tráfegos de sinalização usa HTTPS REST, embora em alguns cenários, o SIP possa ser usado como um protocolo de tráfego de sinalização. Embora este tipo de tráfego seja menos sensível à latência, a sinalização de baixa latência irá os utilizadores da sua solução uma experiência final agradável.

### <a name="interoperability-restrictions"></a>Restrições de interoperabilidade

Os meios de comunicação que fluem através dos Serviços de Comunicação são restringidos da seguinte forma:

**Retransmissores de terceiros.** A interoperabilidade com retransmitidas de meios de comunicação de terceiros não é apoiada. Se um dos seus pontos finais dos seus meios de comunicação for os Serviços de Comunicação, os seus meios de comunicação só podem atravessar os retransmissores de mídia nativo da Microsoft, incluindo aqueles que suportam as Equipas microsoft e o Skype para o Negócio.

Um controlador de fronteira de sessão de terceiros (SBC) na fronteira com a PSTN deve terminar o fluxo RTP/RTCP, protegido através do SRTP, e não retransmiti-lo para o próximo salto. Se retransmitir o fluxo para o próximo salto, pode não ser compreendido.

**Servidores de procuração SIP de terceiros.** Um Serviço de Comunicação que assinale o diálogo SIP com um SBC de terceiros e/ou gateway pode atravessar proxies SIP nativos da Microsoft (tal como as Equipas). A interoperabilidade com os proxies SIP de terceiros não é suportada.

**B2BUA (ou SBC) de terceiros).** Os meios de comunicação fluem de e para a PSTN é encerrado por um SBC de terceiros. A interoperabilidade com um SBC de terceiros dentro da rede de serviços de comunicação (onde um SBC de terceiros media dois pontos finais dos Serviços de Comunicação) não é suportada.

### <a name="unsupported-technologies"></a>Tecnologias não suportadas

**Rede VPN.** Os Serviços de Comunicação não suportam a transmissão de meios de comunicação sobre VPNs. Se os seus utilizadores estiverem a utilizar clientes VPN, o cliente deve dividir e encaminhar o tráfego dos meios de comunicação através de uma ligação não VPN, conforme especificado no [Enableing Lync media para contornar um túnel VPN.](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210)

*Um bilhete. Embora o título indique a Lync, é aplicável aos Serviços e Equipas de Comunicação da Azure.*

**Formadores de pacotes.** Os dispositivos de corte de pacotes, de inspeção de pacotes ou de modelação de pacotes não são suportados e podem degradar significativamente a qualidade.

### <a name="call-flow-principles"></a>Princípios de fluxo de chamadas

Existem quatro princípios gerais que sustentam os fluxos de chamadas dos Serviços de Comunicação:

* **O primeiro participante de uma chamada de grupo dos Serviços de Comunicação determina a região em que a chamada é hospedada.** Há exceções a esta regra em algumas topologias, que são descritas abaixo.
* **O ponto final dos meios de comunicação utilizado para apoiar uma chamada de Serviços de Comunicação é selecionado com base nas necessidades de processamento de meios de comunicação,** e não é afetado pelo número de participantes numa chamada. Por exemplo, uma chamada ponto a ponto pode usar um ponto final mediático na nuvem para processar os meios de comunicação para transcrição ou gravação, enquanto uma chamada com dois participantes pode não usar nenhum ponto final dos meios de comunicação. As chamadas de grupo utilizarão um ponto final mediático para fins de mistura e encaminhamento. Este ponto final é selecionado com base na região em que a chamada é hospedada. O tráfego de meios de comunicação enviado de um cliente para o ponto final dos meios de comunicação pode ser encaminhado diretamente ou pode usar um retransmissor de transporte em Azure se as restrições de firewall da rede de clientes o exigirem.
* **O tráfego mediático para chamadas par-a-par toma a rota mais direta que está disponível**, assumindo que a chamada não precisa de um ponto final mediático na nuvem. A rota preferida é diretamente para o par remoto (cliente). Se não estiver disponível uma rota direta, um ou mais retransmissores de transporte transmitirão tráfego. O tráfego de mídia não deve transversalizar servidores que agem como modeladores de pacotes, servidores VPN ou outras funções que possam atrasar o processamento e degradar a experiência do utilizador final.
* **O tráfego de sinalização vai sempre para o servidor mais próximo do utilizador.**

Para saber mais sobre os detalhes sobre o caminho mediático escolhido, consulte a documentação conceptual dos [fluxos de chamada.](./call-flows.md)


## <a name="call-flows-in-various-topologies"></a>Fluxos de chamadas em várias topologias

### <a name="communication-services-internet"></a>Serviços de Comunicação (internet)

Esta topologia é utilizada por clientes que utilizam serviços de comunicação a partir da nuvem sem qualquer implantação no local, como a INTERFACE SIP. Nesta topologia, o tráfego de e para os Serviços de Comunicação flui através da Internet.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Topologia dos Serviços de Comunicação Azure.":::

*Figura 1 - Topologia dos Serviços de Comunicação*

A direção das setas no diagrama acima reflete a direção de iniciação da comunicação que afeta a conectividade nos perímetros da empresa. No caso da UDP para os meios de comunicação, os primeiros pacotes podem fluir na direção inversa, mas estes pacotes podem ser bloqueados até que os pacotes na outra direção fluam.

Descrições do fluxo:
* Fluxo 2* – Representa um fluxo iniciado por um utilizador na rede de clientes para a Internet como parte da experiência dos Serviços de Comunicação do utilizador. Exemplos destes fluxos incluem DNS e transmissão de meios de comunicação peer-to-peer.
* Fluxo 2 – Representa um fluxo iniciado por um utilizador de Serviços de Comunicação móvel remoto, com VPN para a rede de clientes.
* Fluxo 3 – Representa um fluxo iniciado por um utilizador de Serviços de Comunicação móveis remotos para os pontos finais dos Serviços de Comunicação.
* Fluxo 4 – Representa um fluxo iniciado por um utilizador na rede de clientes para serviços de comunicação.
* Fluxo 5 – Representa um fluxo de mídia peer-to-peer entre um utilizador dos Serviços de Comunicação e outro dentro da rede de clientes.
* Fluxo 6 – Representa um fluxo de mídia peer-to-peer entre um utilizador remoto dos Serviços de Comunicação Móveis e outro utilizador remoto dos Serviços de Comunicação Móveis através da Internet.

### <a name="use-case-one-to-one"></a>Caso de utilização: um para um

As chamadas um-para-um utilizam um modelo comum no qual o chamador obterá um conjunto de candidatos compostos por endereços/portas IP, incluindo locais, retransmissores e reflexivos (endereço IP público do cliente, como visto pelos candidatos do relé). O chamador envia estes candidatos para o chamado partido; o chamado partido também obtém um conjunto semelhante de candidatos e envia-os para o chamador. As mensagens de verificação de conectividade STUN são usadas para descobrir quais os caminhos de chamada/chamados meios de comunicação partidários funcionam, e o melhor caminho de trabalho é selecionado. Os meios de comunicação (isto é, os pacotes RTP/RTCP protegidos através do SRTP) são então enviados utilizando o par de candidatos selecionados. O relé de transporte é implantado como parte dos Serviços de Comunicação Azure.

Se o endereço IP local/candidatos portuários ou os candidatos reflexivos tiverem conectividade, então o caminho direto entre os clientes (ou usando um NAT) será selecionado para meios de comunicação. Se os clientes estiverem ambos na rede de clientes, então o caminho direto deve ser selecionado. Isto requer conectividade udp direta dentro da rede de clientes. Se os clientes forem ambos utilizadores de nuvem nómada, então, dependendo do NAT/firewall, os meios de comunicação podem utilizar conectividade direta.

Se um cliente é interno na rede de clientes e um cliente é externo (por exemplo, um utilizador de nuvem móvel), então é improvável que a conectividade direta entre os candidatos locais ou reflexivos esteja funcionando. Neste caso, uma opção é utilizar um dos candidatos de retransmissão de transporte de qualquer cliente (por exemplo, o cliente interno obteve um retransmissor do retransmissor de transporte em Azure; o cliente externo precisa de ser capaz de enviar pacotes STUN/RTP/RTCP para o relé de transporte). Outra opção é o cliente interno que envia para o candidato de retransmissão obtido pelo cliente de nuvem móvel. Embora a conectividade UDP para os meios de comunicação seja altamente recomendada, o TCP é suportado.

**Passos de alto nível:**
1.  Serviços de Comunicação O utilizador A resolve o nome de domínio URL (DNS) utilizando o Fluxo 2.
2.  O Utilizador A atribui uma porta de retransmissão de mídia no relé de transporte de Equipas utilizando o Flow 4.
3.  O Utilizador de Serviços de Comunicação A envia um "convite" com candidatos do ICE utilizando o Fluxo 4 para serviços de comunicação.
4.  Os serviços de comunicação notificam o Utilizador B utilizando o Flow 4.
5.  O Utilizador B atribui uma porta de retransmissão de meios de comunicação em relé de transporte de Equipas utilizando o Flow 4.
6.  O Utilizador B envia "resposta" com os candidatos do ICE utilizando o Flow 4, que é reencaminhado para o Utilizador A utilizando o Flow 4.
7.  O Utilizador A e o Utilizador B invocam testes de conectividade ICE e o melhor caminho de mídia disponível é selecionado (ver diagramas abaixo para vários casos de utilização).
8.  Ambos os utilizadores enviam telemetria aos Serviços de Comunicação utilizando o Flow 4.

### <a name="customer-network-intranet"></a>Rede de clientes (intranet)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="Fluxo de tráfego dentro da Rede de Clientes.":::

*Figura 2 - Dentro da rede de clientes*

No passo 7, é selecionado o fluxo 5 dos meios de comunicação peer-to-peer.

Esta transmissão mediática é bidirecional. A direção do Fluxo 5 indica que um lado inicia a comunicação a partir de uma perspetiva de conectividade. Neste caso, não importa qual a direção utilizada porque ambos os pontos finais estão dentro da rede de clientes.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>Rede de clientes para utilizador externo (suporte retransmitido por Teams Transport Relay)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="Um a um fluxo de chamada através de um relé.":::

*Figura 3 - Rede de clientes para utilizador externo (suporte retransmitido pela Azure Transport Relay)*

No passo 7, são selecionados o Fluxo 4 (da rede de clientes para serviços de comunicação) e o Flow 3 (de um utilizador de Serviços de Comunicação móvel remoto para serviços de comunicação Azure).

Estes fluxos são retransmitidos por Teams Transport Relay dentro de Azure.

Esta transmissão mediática é bidirecional. A direção indica que lado inicia a comunicação a partir de uma perspetiva de conectividade. Neste caso, estes fluxos são utilizados para sinalização e meios de comunicação, utilizando diferentes protocolos de transporte e endereços.

### <a name="customer-network-to-external-user-direct-media"></a>Rede de clientes para utilizador externo (meios diretos)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="Fluxo de chamada de um para um com um utilizador externo.":::

*Figura 4 - Rede de clientes para utilizador externo (meios diretos)*

No passo 7, o Flow 2 (da rede de clientes ao par do cliente através da Internet) é selecionado.

Os meios de comunicação diretos com um utilizador móvel remoto (não retransmitido através do Azure) são opcionais. Por outras palavras, pode bloquear este caminho para impor um caminho mediático através de um relé de transporte em Azure.

Esta transmissão mediática é bidirecional. A direção do Flow 2 para o utilizador móvel remoto indica que um lado inicia a comunicação a partir de uma perspetiva de conectividade.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>Utilizador VPN para utilizador interno (meios de comunicação retransmitidos por Teams Transport Relay)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="Um a um fluxo de chamada com um utilizador VPN via Relay.":::

*Figura 5 - Utilizador VPN para utilizador interno (meios retransmitidos pela Azure Relay*

A sinalização entre a VPN para a rede de clientes utiliza o Flow 2*. A sinalização entre a rede de clientes e a Azure utiliza o Flow 4. No entanto, os meios de comunicação contornam a VPN e são encaminhados usando os Fluxos 3 e 4 através do Azure Media Relay.

### <a name="vpn-user-to-internal-user-direct-media"></a>Utilizador VPN para utilizador interno (meios diretos)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="Um a um fluxo de chamada (utilizador interno) com uma VPN com Meios Diretos":::

*Figura 6 - Utilizador VPN ao utilizador interno (meios diretos)*

A sinalização entre a VPN para a rede de clientes utiliza o Flow 2'. A sinalização entre a rede de clientes e a Azure está a utilizar o fluxo 4. No entanto, os meios de comunicação contornam a VPN e são encaminhados utilizando o fluxo 2 da rede de clientes para a Internet.

Esta transmissão mediática é bidirecional. A direção do Flow 2 para o utilizador móvel remoto indica que um lado inicia a comunicação a partir de uma perspetiva de conectividade.

### <a name="vpn-user-to-external-user-direct-media"></a>Utilizador VPN para utilizador externo (suporte direto)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="Um a um fluxo de chamada (utilizador externo) com uma VPN com Meios Diretos":::

*Figura 7 - Utilizador VPN para utilizador externo (meios diretos)*

A sinalização entre o utilizador VPN para a rede de clientes utiliza o Fluxo 2* e o Fluxo 4 para Azure. No entanto, os meios de comunicação contornam a VPN e são encaminhados usando o Flow 6.

Esta transmissão mediática é bidirecional. A direção do Flow 6 para o utilizador móvel remoto indica que um lado inicia a comunicação a partir de uma perspetiva de conectividade.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>Caso de Utilização: Cliente de serviços de comunicação à PSTN através do tronco de serviços de comunicação

Os Serviços de Comunicação permitem a colocação e receção de chamadas da Rede Telefónica Pública Comutado (PSTN). Se o porta-malas PSTN estiver ligado utilizando números de telefone fornecidos pelos Serviços de Comunicação, não existem requisitos especiais de conectividade para este caso de utilização. Se quiser ligar o seu próprio porta-malas PSTN aos Serviços de Comunicação Azure, pode utilizar a Interface SIP (disponível em CY2021).

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="Uma a uma chamada com um participante pstn":::

*Figura 8 - Utilizador de Serviços de Comunicação para PSTN através do Tronco Azure*

Neste caso, a sinalização e os suportes de comunicação da rede de clientes para o Azure utilizam o Flow 4.

### <a name="use-case-communication-services-group-calls"></a>Caso de utilização: Chamadas de grupo de serviços de comunicação

O serviço de partilha áudio/vídeo/ecrã (VBSS) faz parte dos Serviços de Comunicação Azure. Tem um endereço IP público que deve ser acessível a partir da rede de clientes e deve ser acessível a partir de um cliente Nomadic Cloud. Cada cliente/ponto final tem de ser capaz de se conectar ao serviço.

Os clientes internos obterão candidatos locais, reflexivos e retransmitimentos da mesma forma que descrito para chamadas um-para-um. Os clientes enviarão estes candidatos ao serviço num convite. O serviço não utiliza um retransmissor uma vez que tem um endereço IP acessível publicamente, pelo que responde com o seu candidato a endereço IP local. O cliente e o serviço verificarão a conectividade da mesma forma descrita para chamadas um-para-um.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="Chamada do grupo OACS":::

*Figura 9 - Chamadas do Grupo de Serviços de Comunicação*

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começa com a chamada](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Os seguintes documentos podem ser interessantes para si:

- Saiba mais sobre [tipos de chamadas](../concepts/voice-video-calling/about-call-types.md)
- Conheça a [arquitetura do servidor de clientes](./client-and-server-architecture.md)