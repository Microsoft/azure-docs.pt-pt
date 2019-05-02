---
title: Inspeção de pacotes com o observador de rede do Azure | Documentos da Microsoft
description: Este artigo descreve como utilizar o observador de rede para efetuar a inspeção de pacotes aprofundada recolhida a partir de uma VM
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 7f3fc69bbfd881a26ceb25705852558b66c60153
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64716895"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Inspeção de pacotes com o observador de rede do Azure

Utilizar a funcionalidade de captura de pacotes do observador de rede, pode iniciar e gerir sessões de capturas nas suas VMs do Azure no portal, PowerShell, CLI e por meio de programação através do SDK e a REST API. Captura de pacotes permite cenários que necessitem de dados de nível de pacote, fornecendo as informações num formato pode ser utilizado imediatamente. Tirar partido das ferramentas disponíveis gratuitamente para inspecionar os dados, pode examinar as comunicações enviadas de e para as suas VMs e obtenha informações sobre o tráfego de rede. Alguns usos do exemplo de dados de captura de pacotes incluem: investigar os problemas de rede ou a aplicação, detetar tentativas de utilização indevida e intrusão de rede ou manter a conformidade regulamentar. Neste artigo, vamos mostrar como abrir um arquivo de captura de pacote fornecido pelo observador de rede usando uma ferramenta de código aberto populares. Também forneceremos exemplos que mostram como calcular uma latência de ligação, identificar o tráfego anormal e examinar as estatísticas de rede.

## <a name="before-you-begin"></a>Antes de começar

Este artigo aborda alguns cenários previamente configurados numa captura de pacotes que foi executada anteriormente. Estes cenários mostram capacidades que podem ser acedidas ao rever uma captura de pacotes. Este cenário utiliza [WireShark](https://www.wireshark.org/) para inspecionar a captura de pacotes.

Este cenário pressupõe que já executou uma captura de pacotes numa máquina virtual. Para saber como criar uma visita de captura de pacotes [capturas de pacotes de gerir com o portal](network-watcher-packet-capture-manage-portal.md) ou com REST, visite a página [gerir capturas de pacotes com a REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Cenário

Neste cenário,:

* Reveja uma captura de pacotes

## <a name="calculate-network-latency"></a>Calcular a latência de rede

Neste cenário, vamos mostrar como ver o tempo de ida e volta inicial (RTT) de uma conversa de protocolo de controlo de transmissão (TCP) ocorrem entre dois pontos de extremidade.

Quando uma conexão TCP é estabelecida, os primeiros três pacotes enviados na conexão de seguem um padrão de frequentemente referido como o handshake de três vias. Ao examinar os dois primeiros pacotes enviados neste handshake, uma solicitação inicial do cliente e uma resposta do servidor, podemos calcular a latência quando esta ligação foi estabelecida. Esta latência é referida como o tempo de ida e volta (RTT). Para obter mais informações sobre o protocolo TCP e o handshake de três vias, consulte o seguinte recurso. https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip

### <a name="step-1"></a>Passo 1

Inicie o WireShark

### <a name="step-2"></a>Passo 2

Carga de **. cap** ficheiro a partir do seu captura de pacotes. Este ficheiro pode ser encontrado no blob foi guardado no nosso localmente na máquina virtual, dependendo de como o configurado.

### <a name="step-3"></a>Passo 3

Para ver o tempo de ida e volta (RTT) inicial conversas de TCP, podemos irá apenas observar os dois primeiros pacotes envolvidos no handshake TCP. Usaremos os dois primeiros pacotes no handshake de três vias, que são [SYN], [SYN, ACK] pacotes. Eles são chamados para sinalizadores definidos no cabeçalho de TCP. O último pacote no handshake, o pacote [ACK], não será utilizado neste cenário. O pacote [SYN] é enviado pelo cliente. Assim que for recebido o servidor envia o pacote [ACK] como uma confirmação de receber o SYN do cliente. Aproveitando o fato de que a resposta do servidor requer muito pouca sobrecarga, Calculamos o RTT subtraindo o tempo [SYN, ACK] pacote foi recebido pelo cliente no momento [SYN] o pacote foi enviado pelo cliente.

Usando o WireShark este valor é calculado para nós.

Para ver mais facilmente os dois primeiros pacotes no handshake de três vias TCP, utilizamos a capacidade de filtragem fornecida pelo WireShark.

Para aplicar o filtro na WireShark, expanda o segmento de "Protocolo de controlo de transmissão" de um pacote de [SYN] na sua captura e examinar os sinalizadores definidos no cabeçalho de TCP.

Uma vez que estamos à procura para filtrar em todos os [SYN] e [SYN, ACK] pacotes, sob os sinalizadores de confirmar que o bit de Syn está definido como 1, em seguida, clique direito o bit de Syn -> aplicar como filtro -> selecionados.

![figura 7][7]

### <a name="step-4"></a>Passo 4

Agora que tem de filtrar a janela para ver apenas os pacotes com o conjunto de bits [SYN], pode facilmente selecionar conversas que estiver interessado em ver o RTT inicial. Uma forma simples para ver o RTT na WireShark clique simplesmente a lista pendente marcado como análise de "SEQ/ACK". Verá, em seguida, o RTT apresentado. Neste caso, o RTT foi 0.0022114 segundos ou 2.211 ms.

![figura 8][8]

## <a name="unwanted-protocols"></a>Protocolos indesejados

Pode ter muitos aplicativos em execução numa instância de máquina virtual que tiver implementado no Azure. Muitos desses aplicativos se comuniquem através da rede, talvez sem sua permissão explícita. Utilizar a captura de pacotes para armazenar a comunicação de rede, pode investigar como o aplicativo estão em comunicação na rede e verificar se há quaisquer problemas.

Neste exemplo, vamos rever anterior foi executada a captura de pacotes para protocolos indesejados que possam indicar a comunicação não-autorizada de um aplicativo em execução no seu computador.

### <a name="step-1"></a>Passo 1

Com a captura mesmo no cenário anterior, clique **estatísticas** > **hierarquia de protocolo**

![menu de hierarquia de protocolo][2]

É apresentada a janela de hierarquia de protocolo. Esta vista fornece uma lista de todos os protocolos que estavam em utilização durante a sessão de captura e o número de pacotes transmitidos e recebidos utilizando os protocolos. Esta vista pode ser útil para encontrar o tráfego de rede indesejado nas suas máquinas virtuais ou rede.

![hierarquia de protocolo aberta][3]

Como pode ver na captura de ecrã seguinte, foi tráfego utilizando o protocolo de BitTorrent, que é utilizado para a partilha de ficheiros de ponto a ponto. Como um administrador não espera ver BitTorrent tráfego neste máquinas de virtuais específico. Agora ciente de que este tráfego, pode remover o software de ponto a ponto para instalado nesta máquina virtual ou bloquear o tráfego através de grupos de segurança de rede ou uma Firewall. Além disso, pode optar por executar capturas de pacotes com base numa agenda, para poder rever a utilização do protocolo nas suas máquinas virtuais regularmente. Para obter um exemplo sobre como automatizar tarefas de rede no azure, visite [monitorizar os recursos de rede com a automatização do azure](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Localizando principais destinos e portas

Compreender os tipos de tráfego, os pontos finais e as portas comunicadas via é um importante quando a monitorização ou resolução de problemas de aplicativos e recursos na sua rede. Utilizando um ficheiro de captura de pacotes acima, pode aprender rapidamente os principais destinos que nossa VM está a comunicar com e as portas que está a ser utilizadas.

### <a name="step-1"></a>Passo 1

Com a captura mesmo no cenário anterior, clique **estatísticas** > **estatísticas de IPv4** > **destinos e portas**

![janela de captura de pacotes][4]

### <a name="step-2"></a>Passo 2

À medida que examinar os resultados de que uma linha se destaca, havia várias ligações na porta 111. A porta mais utilizada foi 3389, que é o ambiente de trabalho remoto e os restantes são portas dinâmicas de RPC.

Embora este tráfego pode significar que nada, é uma porta que foi utilizada para muitas ligações e é desconhecido para o administrador.

![Figura 5][5]

### <a name="step-3"></a>Passo 3

Agora que determinamos que vamos um fora da porta local, que podemos filtrar nosso captura baseada na porta.

O filtro neste cenário seria:

```
tcp.port == 111
```

Inserirmos o texto de filtro acima na caixa de texto de filtro e prima enter.

![Figura 6][6]

Nos resultados, podemos ver que todo o tráfego é proveniente de uma máquina virtual local na mesma sub-rede. Se ainda não sabemos por que este tráfego está a ocorrer, ainda mais, pode inspecionar os pacotes para determinar por que ele está fazendo essas chamadas na porta 111. Com essas informações podemos tirar a ação apropriada.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os recursos de diagnóstico do observador de rede, visite a página [descrição geral da monitorização de rede do Azure](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













