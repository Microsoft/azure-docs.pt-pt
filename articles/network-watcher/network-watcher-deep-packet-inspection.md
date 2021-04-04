---
title: Inspeção de pacote com Azure Network Watcher | Microsoft Docs
description: Este artigo descreve como usar o Network Watcher para realizar uma inspeção de pacotes profundos recolhida de um VM
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3bd85d6faf05fcf659e9335ee9de3d64198dfa08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011174"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Inspeção de pacotes com O Observador da Rede Azure

Utilizando a funcionalidade de captura de pacotes do Network Watcher, pode iniciar e gerir sessões de capturas nos seus VMs Azure a partir do portal, PowerShell, CLI e programáticamente através da API SDK e REST. A captura de pacotes permite-lhe abordar cenários que requerem dados de nível de pacote, fornecendo as informações num formato facilmente utilizável. Aproveitando as ferramentas disponíveis livremente para inspecionar os dados, pode examinar as comunicações enviadas de e para os seus VMs e obter informações sobre o tráfego da sua rede. Alguns exemplos de utilização de dados de captura de pacotes incluem: investigar problemas de rede ou aplicações, detetar uso indevido da rede e tentativas de intrusão, ou manter a conformidade regulamentar. Neste artigo, mostramos como abrir um ficheiro de captura de pacotes fornecido pelo Network Watcher utilizando uma popular ferramenta open source. Também forneceremos exemplos que mostram como calcular uma latência de ligação, identificar tráfego anormal e examinar estatísticas de networking.

## <a name="before-you-begin"></a>Antes de começar

Este artigo passa por alguns cenários pré-configurados numa captura de pacotes que foi executado anteriormente. Estes cenários ilustram as capacidades que podem ser acedidas através da revisão de uma captura de pacotes. Este cenário utiliza [o WireShark](https://www.wireshark.org/) para inspecionar a captura do pacote.

Este cenário pressupõe que já executou uma captura de pacotes numa máquina virtual. Para aprender a criar uma visita de captura de [pacotes, gerencie os pacotes com o portal](network-watcher-packet-capture-manage-portal.md) ou com o REST visitando [Managing Packet Captures com REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scenario

Neste cenário, você:

* Reveja uma captura de pacotes

## <a name="calculate-network-latency"></a>Calcular a latência da rede

Neste cenário, mostramos como ver a conversa inicial do Tempo de Viagem Redonda (RTT) de uma conversa do Protocolo de Controlo de Transmissão (TCP) que ocorre entre dois pontos finais.

Quando uma ligação TCP é estabelecida, os três primeiros pacotes enviados na ligação seguem um padrão vulgarmente referido como o aperto de mão de três vias. Examinando os dois primeiros pacotes enviados neste aperto de mão, um pedido inicial do cliente e uma resposta do servidor, podemos calcular a latência quando esta ligação foi estabelecida. Esta latência é referida como o Tempo de Viagem Redonda (RTT). Para obter mais informações sobre o protocolo TCP e o aperto de mão a três, consulte o seguinte recurso. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>Passo 1

Lançamento do WireShark

### <a name="step-2"></a>Passo 2

Carregue o ficheiro **.cap** da captura do seu pacote. Este ficheiro pode ser encontrado na bolha que foi guardada na nossa máquina virtual, dependendo da configuração do ficheiro.

### <a name="step-3"></a>Passo 3

Para ver o tempo de ida e volta inicial (RTT) nas conversas da TCP, vamos apenas olhar para os dois primeiros pacotes envolvidos no aperto de mão TCP. Vamos usar os dois primeiros pacotes no aperto de mão a três, que são os pacotes [SYN], [SYN, ACK]. São nomeados para bandeiras colocadas no cabeçalho da TCP. O último pacote no aperto de mão, o pacote [ACK], não será usado neste cenário. O pacote [SYN] é enviado pelo cliente. Uma vez recebido, o servidor envia o pacote [ACK] como reconhecimento de receber o SYN do cliente. Aproveitando o facto de que a resposta do servidor requer muito pouca sobrecarga, calculamos o RTT subtraindo o tempo que o pacote [SYN, ACK] foi recebido pelo cliente no momento em que o pacote [SYN] foi enviado pelo cliente.

Utilizando o WireShark este valor é calculado para nós.

Para ver mais facilmente os dois primeiros pacotes no aperto de mão tCP de três vias, utilizaremos a capacidade de filtragem fornecida pela WireShark.

Para aplicar o filtro no WireShark, expanda o segmento "Protocolo de Controlo de Transmissão" de um pacote [SYN] na sua captura e examine as bandeiras definidas no cabeçalho TCP.

Uma vez que estamos à procura de filtrar todos os pacotes [SYN] e [SYN, ACK], sob bandeiras confirmam que a bit Syn está definida para 1, em seguida, clique à direita no bit Syn -> Apply as Filter -> Selected.

![figura 7][7]

### <a name="step-4"></a>Passo 4

Agora que filtra a janela para ver apenas pacotes com o conjunto de bits [SYN], pode facilmente selecionar conversas que lhe interessam para ver o RTT inicial. Uma forma simples de ver o RTT no WireShark basta clicar na análise "SEQ/ACK" marcada para o dropdown. Em seguida, verá o RTT exibido. Neste caso, o RTT foi de 0,0022114 segundos, ou 2.211 ms.

![figura 8][8]

## <a name="unwanted-protocols"></a>Protocolos indesejados

Pode ter muitas aplicações em execução numa caixa de máquina virtual que implementou em Azure. Muitas destas aplicações comunicam através da rede, talvez sem a sua permissão explícita. Usando a captura de pacotes para armazenar comunicação de rede, podemos investigar como a aplicação está falando na rede e procurar quaisquer problemas.

Neste exemplo, revemos uma captura de pacotes anteriores para protocolos indesejados que podem indicar comunicação não autorizada a partir de uma aplicação em execução na sua máquina.

### <a name="step-1"></a>Passo 1

Usando a mesma captura no cenário anterior clique na  >  **Hierarquia do Protocolo** de Estatísticas

![menu de hierarquia de protocolo][2]

A janela da hierarquia do protocolo aparece. Esta visão fornece uma lista de todos os protocolos que foram utilizados durante a sessão de captura e o número de pacotes transmitidos e recebidos usando os protocolos. Esta vista pode ser útil para encontrar tráfego de rede indesejado nas suas máquinas virtuais ou rede.

![hierarquia protocolo aberto][3]

Como pode ver na seguinte captura de ecrã, houve tráfego usando o protocolo BitTorrent, que é usado para par para par para par de ficheiros. Como administrador, não espera ver o tráfego BitTorrent nestas máquinas virtuais em particular. Agora que tem conhecimento deste tráfego, pode remover o software peer to peer que instalou nesta máquina virtual, ou bloquear o tráfego usando Grupos de Segurança de Rede ou uma Firewall. Além disso, pode optar por executar capturas de pacotes num horário, para que possa rever regularmente o uso do protocolo nas suas máquinas virtuais. Para um exemplo sobre como automatizar tarefas de rede em azul visite recursos de [rede monitor com automação azul](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Encontrar destinos e portos de topo

Compreender os tipos de tráfego, os pontos finais e as portas comunicadas é importante para monitorizar ou resolver problemas de aplicações e recursos na sua rede. Utilizando um ficheiro de captura de pacotes de cima, podemos rapidamente aprender os principais destinos com os quais o nosso VM está a comunicar e as portas a serem utilizadas.

### <a name="step-1"></a>Passo 1

Utilizando a mesma captura no cenário anterior clique em **Estatísticas**  >  **IPv4 Estatísticas**  >  **Destinos e Portos**

![janela de captura de pacotes][4]

### <a name="step-2"></a>Passo 2

Ao olharmos para os resultados, uma linha destaca-se, houve várias ligações na porta 111. A porta mais utilizada foi o 3389, que é um ambiente de trabalho remoto, e os restantes são portas dinâmicas RPC.

Embora este tráfego possa não significar nada, é um porto que foi usado para muitas ligações e é desconhecido para o administrador.

![figura 5][5]

### <a name="step-3"></a>Passo 3

Agora que determinámos uma porta fora do lugar, podemos filtrar a nossa captura com base no porto.

O filtro neste cenário seria:

```
tcp.port == 111
```

Introduzimos o texto do filtro de cima na caixa de texto do filtro e entramos.

![figura 6][6]

Pelos resultados, podemos ver que todo o tráfego vem de uma máquina virtual local na mesma sub-rede. Se ainda não entendermos porque é que este tráfego está a ocorrer, podemos inspecionar os pacotes para determinar porque está a fazer estas chamadas no porto 111. Com esta informação podemos tomar as medidas apropriadas.

## <a name="next-steps"></a>Passos seguintes

Conheça as outras funcionalidades de diagnóstico do Network Watcher visitando a [visão geral de monitorização da rede Azure](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













