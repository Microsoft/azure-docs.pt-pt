---
title: Inspeção de pacotes com O Observador da Rede Azure  Microsoft Docs
description: Este artigo descreve como usar o Network Watcher para realizar uma inspeção de pacotes profundos recolhida de um VM
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c937a07133dc38d2d9e1e1ef2cc324b4c8bb360e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845076"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Inspeção de pacotes com Vigilante da Rede Azure

Utilizando a funcionalidade de captura de pacotes do Network Watcher, pode iniciar e gerir sessões de captura nos seus VMs Azure a partir do portal, PowerShell, CLI e programáticaatravés do SDK e DA API REST. A captura do pacote permite-lhe abordar cenários que requerem dados de nível de pacote, fornecendo a informação num formato facilmente utilizável. Aproveitando ferramentas livremente disponíveis para inspecionar os dados, pode examinar as comunicações enviadas de e para os seus VMs e obter informações sobre o tráfego da sua rede. Alguns exemplos de utilizações de dados de captura de pacotes incluem: investigar problemas de rede ou aplicação, detetar uso indevido da rede e tentativas de intrusão, ou manter o cumprimento regulamentar. Neste artigo, mostramos como abrir um ficheiro de captura de pacotes fornecido pelo Network Watcher usando uma ferramenta popular de código aberto. Daremos também exemplos que mostrem como calcular uma latência de ligação, identificar tráfego anormal e examinar estatísticas de networking.

## <a name="before-you-begin"></a>Antes de começar

Este artigo passa por alguns cenários pré-configurados numa captura de pacotes que foi executado anteriormente. Estes cenários ilustram capacidades que podem ser acedidas através da revisão de uma captura de pacotes. Este cenário utiliza o [WireShark](https://www.wireshark.org/) para inspecionar a captura do pacote.

Este cenário pressupõe que já executou uma captura de pacote numa máquina virtual. Para aprender a criar uma visita de captura de [pacotes, gerencie capturas de pacotes com o portal](network-watcher-packet-capture-manage-portal.md) ou com o REST visitando API de [Managing Packet Captures com REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Cenário

Neste cenário, você:

* Reveja uma captura de pacote

## <a name="calculate-network-latency"></a>Calcular a latência da rede

Neste cenário, mostramos como ver a conversa inicial do Tempo de IDa e Volta (RTT) de um Protocolo de Controlo de Transmissão (TCP) que ocorre entre dois pontos finais.

Quando uma ligação TCP é estabelecida, os três primeiros pacotes enviados na ligação seguem um padrão vulgarmente referido como o aperto de mão de três vias. Ao examinar os dois primeiros pacotes enviados neste aperto de mão, um pedido inicial do cliente e uma resposta do servidor, podemos calcular a latência quando esta ligação foi estabelecida. Esta latência é referida como o Tempo de Ida e Volta (RTT). Para obter mais informações sobre o protocolo tCP e o aperto de mão a três, consulte o seguinte recurso. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip )

### <a name="step-1"></a>Passo 1

Lançamento Do WireShark

### <a name="step-2"></a>Passo 2

Carregue o ficheiro **.cap** a partir da sua captura do pacote. Este ficheiro pode ser encontrado na bolha que foi guardado na nossa loja local na máquina virtual, dependendo de como o configuraste.

### <a name="step-3"></a>Passo 3

Para ver o tempo inicial de ida e volta (RTT) nas conversas do TCP, estaremos apenas a olhar para os dois primeiros pacotes envolvidos no aperto de mão da TCP. Vamos usar os dois primeiros pacotes no aperto de mão a três, que são os pacotes [SYN, ACK]. São nomeados para bandeiras colocadas no cabeçalho do TCP. O último pacote no aperto de mão, o pacote [ACK], não será usado neste cenário. O pacote [SYN] é enviado pelo cliente. Uma vez recebido, o servidor envia o pacote [ACK] como um reconhecimento de receber o SYN do cliente. Aproveitando o facto de que a resposta do servidor requer muito pouca sobrecarga, calculamos o RTT subtraindo o momento em que o pacote [SYN, ACK] foi recebido pelo cliente no momento em que o pacote [SYN] foi enviado pelo cliente.

Usando o WireShark este valor é calculado para nós.

Para ver mais facilmente os dois primeiros pacotes no aperto de mão tridireccional do TCP, utilizaremos a capacidade de filtragem fornecida pela WireShark.

Para aplicar o filtro no WireShark, expanda o segmento "Protocolo de Controlo de Transmissão" de um pacote [SYN] na sua captura e examine as bandeiras colocadas no cabeçalho do TCP.

Uma vez que estamos à procura de filtrar todos os pacotes [SYN] e [SYN, ACK], sob bandeiras confirmam que a broca Syn está definida para 1, em seguida, clique à direita no bit Syn -> Apply as Filter &> Selecionado.

![figura 7][7]

### <a name="step-4"></a>Passo 4

Agora que filtraa a janela para ver apenas pacotes com o conjunto de bits [SYN], pode facilmente selecionar conversas que está interessada em ver o RTT inicial. Uma forma simples de ver o RTT em WireShark basta clicar na análise "SEQ/ACK" marcada para downdown. Em seguida, verá o RTT apresentado. Neste caso, o RTT foi de 0,0022114 segundos, ou 2.211 ms.

![figura 8][8]

## <a name="unwanted-protocols"></a>Protocolos indesejados

Pode ter muitas aplicações em execução numa máquina virtual que implementou no Azure. Muitas destas aplicações comunicam através da rede, talvez sem a sua permissão explícita. Usando a captura de pacotes para armazenar comunicação de rede, podemos investigar como a aplicação está a falar na rede e procurar quaisquer problemas.

Neste exemplo, revemos uma captura de pacotes anteriores para protocolos indesejados que podem indicar comunicação não autorizada de uma aplicação em execução na sua máquina.

### <a name="step-1"></a>Passo 1

Utilizando a mesma captura no cenário anterior clique **em estatísticas** > **Hierarquia protocolar**

![menu de hierarquia de protocolo][2]

A janela da hierarquia do protocolo aparece. Esta visão fornece uma lista de todos os protocolos que estavam em uso durante a sessão de captura e o número de pacotes transmitidos e recebidos usando os protocolos. Esta vista pode ser útil para encontrar tráfego de rede indesejado nas suas máquinas virtuais ou rede.

![hierarquia protocolo aberto][3]

Como pode ver na seguinte captura de ecrã, houve tráfego usando o protocolo BitTorrent, que é usado para a partilha de ficheiros por pares. Como administrador, não espera ver o tráfego bitTorrent nestas máquinas virtuais em particular. Agora que tem conhecimento deste tráfego, pode remover o software peer to peer que instalou nesta máquina virtual ou bloquear o tráfego usando Grupos de Segurança de Rede ou uma Firewall. Além disso, pode optar por executar capturas de pacotes numa agenda, para que possa rever regularmente o uso do protocolo nas suas máquinas virtuais. Para um exemplo sobre como automatizar tarefas de rede em azure visite [os recursos da rede Monitor com automação azure](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Encontrar destinos e portos de topo

Compreender os tipos de tráfego, os pontos finais e as portas comunicadas é importante na monitorização ou resolução de problemas de aplicações e recursos na sua rede. Utilizando um ficheiro de captura de pacotes de cima, podemos rapidamente aprender os principais destinos com que o nosso VM está a comunicar e as portas a serem utilizadas.

### <a name="step-1"></a>Passo 1

Utilizando a mesma captura no cenário anterior clique **estatísticas** > **Estatísticas IPv4** > **Destinos e Portos**

![janela de captura de pacote][4]

### <a name="step-2"></a>Passo 2

À medida que olhamos para os resultados uma linha sobressai, havia múltiplas ligações na porta 111. A porta mais utilizada foi o 3389, que é o ambiente de trabalho remoto, e os restantes são portas dinâmicas RPC.

Embora este tráfego possa não significar nada, é um porto que foi usado para muitas ligações e é desconhecido para o administrador.

![figura 5][5]

### <a name="step-3"></a>Passo 3

Agora que determinámos um porto fora do lugar, podemos filtrar a nossa captura com base no porto.

O filtro neste cenário seria:

```
tcp.port == 111
```

Introduzemos o texto do filtro de cima na caixa de texto do filtro e entramos.

![figura 6][6]

Dos resultados, podemos ver todo o tráfego vindo de uma máquina virtual local na mesma subnet. Se ainda não entendemos por que este tráfego está a ocorrer, podemos inspecionar ainda mais os pacotes para determinar por que está fazendo estas chamadas no porto 111. Com esta informação podemos tomar as medidas apropriadas.

## <a name="next-steps"></a>Passos Seguintes

Conheça as outras funcionalidades de diagnóstico do Network Watcher visitando a visão geral da monitorização da [rede Azure](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













