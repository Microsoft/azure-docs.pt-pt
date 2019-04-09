---
title: Otimização de desempenho de TCP/IP para as VMs do Azure | Documentos da Microsoft
description: Aprenda várias técnicas comuns de TCP/IP desempenho Otimização e sua relação com as VMs do Azure.
services: virtual-network
documentationcenter: na
author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: 6d53b8fe46997f6b4f915bfb14a64d69b22cc5d4
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057834"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP ajuste de desempenho para as VMs do Azure

O objetivo deste artigo é discutir as técnicas comuns de ajuste de desempenho TCP/IP e suas considerações para máquinas virtuais em execução no Microsoft Azure. É importante primeiro ter uma compreensão básica dos conceitos e, em seguida, discutir como pode ser ajustados.

## <a name="common-tcpip-tuning-techniques"></a>Técnicas de otimização de TCP/IP comuns

### <a name="mtu-fragmentation-and-large-send-offload-lso"></a>O MTU, fragmentação e enviar LSO (Large Offload)

#### <a name="explanation-of-mtu"></a>Explicação de MTU

A unidade de transmissão máxima (MTU) é o maior quadro de tamanho (pacote), especificado em bytes, que podem ser enviados através de uma interface de rede. O MTU é uma definição configurável e a predefinição que MTU utilizado em VMs do Azure e a configuração padrão na maioria dos dispositivos de rede globalmente, é 1500 bytes.

#### <a name="explanation-of-fragmentation"></a>Explicação de fragmentação

A fragmentação ocorre quando é enviado um pacote que excede a MTU da interface de rede. A pilha de TCP/IP irá interromper o pacote em partes mais pequenas (fragmentos) que estão em conformidade com as interfaces de MTU. A fragmentação ocorre na camada de IP e é independente do protocolo subjacente (por exemplo, o TCP). Quando é enviado um pacote de 2000 bytes através de uma interface de rede com uma MTU de 1500, em seguida, ele será ser dividido num pacote de 1500 bytes e um pacote de 500 bytes.

Dispositivos de rede no caminho entre uma origem e de destino tem a opção para remover pacotes que excedem o MTU ou para fragmentados o pacote em partes mais pequenas.

#### <a name="the-dont-fragment-df-bit-in-an-ip-packet"></a>O bit de "não fragmento (DF)" num pacote IP

O bit de fragmento não é um sinalizador no cabeçalho de protocolo IP. Quando o bit de DF estiver definido, ele indica que os dispositivos de rede intermediário no caminho entre o remetente e o receptor não devem fragmentados o pacote. Há muitos motivos por que é possível definir esse bit (consulte a seção de descoberta de caminho abaixo para obter um exemplo). Quando um dispositivo de rede recebe um pacote com o conjunto de bits de fragmento não, esse pacote excede a MTU da interface de dispositivos, e o comportamento padrão é para o dispositivo remover o pacote e enviar um pacote de "Fragmentação de ICMP necessário" para a origem original dos pacote.

#### <a name="performance-implications-of-fragmentation"></a>Implicações de desempenho de fragmentação

A fragmentação pode ter implicações de desempenho negativo. Uma das principais razões para impacto no desempenho é o impacto de CPU/memória de fragmentação e a remontagem de pacotes. Quando um dispositivo de rede precisa fragmentados um pacote, terá de alocar recursos de CPU/memória para executar a fragmentação. O mesmo deve acontecer quando o pacote é remontado. O dispositivo de rede tem de armazenar todos os fragmentos até que eles são recebidos para que ele pode remontá-las para o pacote original. Esse processo de fragmentação/remontagem também pode causar uma latência devido ao processo de fragmentação/remontagem.

A outra implicação de desempenho negativo possível de fragmentação é que resultam em pacotes fragmentados pode chegar fora de ordem. Pacotes fora de ordem podem fazer com que determinados tipos de dispositivos de rede para ignorar os pacotes fora de ordem – o que exigirão, em seguida, o pacote completo para ser retransmitido. Cenários típicos para descartar a fragmentos incluem dispositivos de segurança, como firewalls de rede ou quando um dispositivo de rede do recebem os buffers ficam esgotados. Quando um dispositivo de rede do recebem buffers ficam esgotados, um dispositivo de rede está a tentar para a remontagem de um pacote fragmentado, mas não tem os recursos para armazenar e reassume o pacote.

A fragmentação pode ser percebida como uma operação negativa, mas o suporte para a fragmentação é necessária para ligar a diversas redes através da Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Vantagens e as conseqüências de modificar o MTU

Como uma declaração de geral, a aumentar a MTU pode criar uma rede mais eficiente. Cada pacote que é transmitida tem informações de cabeçalho adicional que são adicionadas ao pacote original. Pacote mais significa mais cabeçalho sobrecarga e a rede é menos eficiente como resultado.

Por exemplo, o tamanho do cabeçalho Ethernet é 14 bytes além de uma ferramenta 4 bytes quadro verificar sequência (FCS) para garantir a consistência do quadro. Se um pacote de 2000 bytes é enviado, bytes 18 de sobrecarga de Ethernet é adicionado na rede. Se o pacote for fragmentado num pacote de 1500 bytes e um pacote de 500 bytes, em seguida, cada pacote terá 18 bytes do cabeçalho Ethernet - ou 36 bytes. Ao passo que um único pacote de bytes de 2000 teria apenas um cabeçalho de Ethernet de 18 bytes.

É importante observar que aumentar a MTU por si só não necessariamente criará uma rede mais eficiente. Se um aplicativo enviar apenas 500 bytes pacotes, em seguida, a mesma sobrecarga de cabeçalho existirá se o MTU é 1500 bytes ou 9000 bytes. Para que a rede ser mais eficiente, em seguida, ele também tem de utilizar tamanhos maiores de pacotes que são relativas a MTU.

#### <a name="azure-and-vm-mtu"></a>O Azure e a MTU da VM

O MTU para VMs do Azure a predefinição é 1500 bytes. A pilha de rede Virtual do Azure irá tentar fragmentados um pacote em 1400 bytes. No entanto, a pilha de rede Virtual do Azure irá permitir pacotes até 2006 bytes quando o bit de "Fragmento não" é definido no cabeçalho IP.

É importante observar que essa fragmentação não implica que a pilha de rede Virtual do Azure é inerentemente ineficiente porque ele fragmentos pacotes em 1400 bytes enquanto as VMs têm um MTU de 1500. A realidade é que uma grande percentagem dos pacotes de rede é muito menor do que os bytes 1400 ou 1500 bytes.

#### <a name="azure-and-fragmentation"></a>Azure e a fragmentação

Pilha de rede Virtual do Azure hoje mesmo está configurada para soltar "Fora de ordem fragmentos" – que significa que em pacotes fragmentados que não chegam, por ordem fragmentado original. Esses pacotes são largados principalmente devido a uma vulnerabilidade de segurança de rede anunciada em Novembro de 2018 chamado FragmentStack.

FragmentSmack é um defeito da forma que o kernel do Linux manipulado remontagem de fragmentados pacotes IPv4 e IPv6. Um atacante remoto pode utilizar esta falha para operações de remontagem fragmento Caro de Acionador, que levam a CPU maior e uma negação de serviço no sistema de destino.

#### <a name="tune-the-mtu"></a>Otimizar o MTU

VMs do Azure suportam um MTU configurável, assim como qualquer outro sistema operacional. No entanto, a fragmentação que ocorre dentro do Azure e é detalhada acima, deve ser considerada quando configurar o MTU.

Azure não encoraje os clientes a aumentar seus MTU de VM. Esta discussão destina-se para explicar em detalhes como o Azure implementa o MTU e executa a fragmentação hoje mesmo.

> [!IMPORTANT]
>Aumentando a MTU não tenha mostrado para melhorar o desempenho e pode ter um efeito negativo no desempenho da aplicação.
>
>

#### <a name="large-send-offload-lso"></a>Descarregamento de envio grande (LSO)

Enviar LSO (Large Offload) pode melhorar o desempenho de rede ao descarregar a segmentação de pacotes para o adaptador de Ethernet. Com o LSO ativado, a pilha de TCP/IP irá criar um grande pacote TCP e, em seguida, enviar para o adaptador Ethernet para segmentação antes do reencaminhamento-lo. O benefício de LSO é que pode gratuitamente a CPU de segmentar pacotes em tamanhos de pacotes que estão em conformidade com o MTU e descarregar esse processamento para a interface de Ethernet, onde é executado no hardware. Podem encontrar mais informações sobre os benefícios de LSO na [desempenho na documentação do adaptador de rede Microsoft](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Quando LSO está ativada, os clientes do Azure poderão ver tamanhos grandes de quadro quando capturas de pacotes de desempenho. Estes tamanhos de quadro grande podem levar alguns clientes a acreditar que a fragmentação ou um frames que MTU está a ser utilizado quando não estiver. Com o LSO, o adaptador ethernet pode anunciar uma MSS maior para a pilha de TCP/IP para criar um pacote maior de TCP. Este quadro não-segmentado inteiro, em seguida, é reencaminhado para o adaptador de Ethernet e ficavam visível numa captura de pacotes executada na VM. No entanto, o pacote será ser dividido em vários quadros menores pelo adaptador Ethernet, de acordo com MTU o adaptador de Ethernet.

### <a name="tcpmss-window-scaling-and-pmtud"></a>Dimensionamento de janela TCP/MSS e PMTUD

#### <a name="explanation-of-tcp-mss"></a>Explicação de MSS de TCP

TCP tamanho de segmento máximo (MSS) é uma configuração que se destina a definir o tamanho máximo de segmento TCP como para evitar a fragmentação de pacotes TCP. Sistemas operativos, normalmente, irá definir MSS como MSS = MTU - tamanho do cabeçalho de TCP & IP (20 bytes ou total de bytes 40). Portanto, uma interface com uma MTU de 1500 terão um MSS de 1460. No entanto, o MSS, é configurável.

Esta definição é acordada o handshake de três vias TCP quando uma sessão TCP é configurada entre uma origem e de destino. Ambos os lados enviar um valor de MSS e o menor dos dois é utilizado para a ligação de TCP.

Dispositivos de rede intermediário, como Gateways de VPN, incluindo o Gateway de VPN do Azure, tem a possibilidade de ajustar o MTU independente de origem e de destino para assegurar o desempenho de rede não ideais. Por isso, é importante observar que o MTU de origem e de destino autónomo não é os únicos fatores o valor de MSS real.

#### <a name="explanation-of-path-mtu-discovery-pmtud"></a>Explicação da deteção MTU do caminho (PMTUD)

Enquanto é negociada MSS, pode não indicar o MSS real que podem ser utilizadas como outros dispositivos de rede no caminho entre a origem e de destino pode ter um valor MTU inferior de origem e de destino. Neste caso, o dispositivo cujo MTU é menor do que o pacote irá remover o pacote e enviar uma mensagem de fragmentação controle mensagem ICMP (Internet Protocol) necessário (tipo 3, 4 de código) que contêm o MTU de volta. Esta mensagem ICMP permite que o anfitrião de origem reduzir o seu caminho de MTU adequadamente. O processo é chamado de deteção de MTU do caminho.

O processo de PMTUD é inerentemente ineficaz e tem implicações no desempenho da rede. Quando os pacotes são enviados que exceder uma MTU de caminhos de rede, esses pacotes tem retransmitidos com um MSS inferior. Se o remetente não receber o pacote necessário de fragmentação de ICMP, talvez devido a uma firewall de rede no caminho (geralmente conhecida como PMTUD blackhole), em seguida, o remetente não sabe que precisa reduzir o MSS e será continuamente retransmitir o pacote. Por esse motivo, recomendamos que não aumente a MTU de VM do Azure.

#### <a name="vpn-considerations-with-mtu"></a>Considerações de VPN com o MTU

Os clientes que utilizam VMs que executam o encapsulamento (por exemplo, IPSec VPNs) podem ter implicações adicionais para o tamanho do pacote e de MTU. VPNs adicionar cabeçalhos adicionais serão adicionados ao pacote original, assim, aumentar o tamanho do pacote e requer um MSS menores.

A recomendação atual para o Azure é definir a afixação de MSS de TCP para 1350 bytes e a interface de túnel MTU para 1400. Obter mais informações podem ser encontradas no [VPN dispositivos e a página de parâmetros de IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latência, o tempo de ida e volta e o dimensionamento de janela TCP

#### <a name="latency-and-round-trip-time"></a>Tempo de latência e de ida e volta

Latência de rede é regida pela velocidade da luz através de uma rede óptica de fibra. A realidade é que débito de rede do TCP é também efetivamente governados (práticos máximos) devido a tempo de ida e volta (RTT) entre dois dispositivos de rede.

| | | | |
|-|-|-|-|
|Encaminhar|Distância|Tempo unidirecional|Tempo de ida e volta (RTT)|
|Nova Iorque a San Francisco|4,148 km de distância|21 ms|42 ms|
|Nova York para Londres|5,585 km de distância|28 ms|56 ms|
|Nova Iorque, para que Sydney|15,993 km de distância|80 ms|160 ms|

Esta tabela mostra a distância em linha reta entre duas localizações, no entanto, nas redes, a distância é normalmente mais tempo do que a distância em linha reta. É uma fórmula simples para calcular o mínimo de RTT como regida pela velocidade da luz: mínimo RTT = 2 * (quilômetros de distância / velocidade de propagação).

Um valor padrão de 200 pode ser utilizado para a velocidade de propagação - valor é que a distância, em metros light viaja em 1 milissegundo.

No exemplo Nova Iorque a San Francisco, é 4,148 km distância em linha reta. RTT mínimo = 2 * (4,148 / 200). A saída da equação estará em milissegundos.

Conforme a distância física entre duas localizações é uma realidade fixa, se for necessário o desempenho de rede máxima, em seguida, a opção mais lógica é selecionar destinos com a menor distância entre eles. Principal, as decisões de design dentro da rede virtual podem ser feitas para otimizar o caminho do tráfego e reduzir a latência. Estas considerações de rede virtual são descritas na secção considerações de Design de rede abaixo.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Efeitos de tempo de latência e de ida e volta em TCP

Ida e volta (RTT tempo) tem um efeito direto na débito máximo do TCP. O protocolo TCP tem um conceito de tamanho da janela. O tamanho da janela é a quantidade máxima de tráfego que pode ser enviado através de uma ligação de TCP, antes do remetente deve receber a confirmação do destinatário. Se o MSS TCP está definido como 1460 e o tamanho da janela TCP está definido como 65535, em seguida, o remetente pode enviar 45 pacotes antes de ele deve receber a confirmação do destinatário. Se não for recebida a confirmação, em seguida, irá retransmitir o remetente. Neste exemplo, o tamanho de janela TCP / TCP MSS = pacotes enviados. Ou 65535 / 1460 é arredondado até 45.

Neste estado "a aguardar confirmação", como um mecanismo para criar uma entrega fiável de dados, é o que RTT afetar a taxa de transferência faz com que efetivamente. O remetente aguarda, mais tempo, confirmação, mais tempo também deve aguardar antes de a enviar mais dados.

A fórmula para calcular o débito máximo de uma única ligação de TCP é o seguinte: Tamanho da janela / (latência RTT em milissegundos / 1000) = máximo de bytes por segundo. A tabela a seguir é formatada em Megabytes, para facilitar a leitura e mostra o máximo megabyte / por segundo de débito de uma única ligação de TCP.

| | | | |
|-|-|-|-|
|Tamanho de janela TCP em Bytes|Latência RTT<br/>em milissegundos|Máximo<br/>Megabytes por segundo de débito|Máximo<br/> Megabit por segundo de débito|
|65535|1|65.54|524.29|
|65535|30|2.18|17.48|
|65535|60|1.09|8.74|
|65535|90|.73|5.83|
|65535|120|.55|4.37|

Se houver qualquer perda de pacotes, em seguida, reduzir o débito máximo de uma conexão TCP enquanto o remetente retransmite dados já enviou.

#### <a name="explanation-of-tcp-window-scaling"></a>Explicação de dimensionamento de janela TCP

Dimensionamento de janela TCP é um conceito que dinamicamente aumenta o tamanho de janela TCP permitindo que mais dados a serem enviados antes de uma confirmação é necessária. No nosso exemplo anterior, 45 pacotes seriam enviados antes de uma confirmação foi necessária. Se o número de pacotes que são enviados antes de uma confirmação for aumentada, em seguida, o débito máximo do TCP também aumenta, reduzindo o número de vezes que um remetente está a aguardar confirmação.

A taxa de transferência é demonstrada numa tabela simples abaixo:

| | | | |
|-|-|-|-|
|Tamanho de janela TCP<br/>in Bytes|Latência RTT em milissegundos|Máximo<br/>Megabytes por segundo de débito|Máximo<br/> Megabit por segundo de débito|
|65535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

No entanto, o valor de cabeçalho TCP para o tamanho de janela TCP é apenas de 2 bytes de comprimento, que significa que o valor máximo para uma janela de recepção é a 65535. Para aumentar o tamanho máximo da janela, foi introduzido um fator de dimensionamento de janela TCP.

O fator de dimensionamento também é uma configuração que pode ser configurada num sistema operativo. A fórmula para calcular o tamanho da janela TCP através de fatores de expansão é o seguinte: Tamanho de janela TCP = tamanho de janela TCP em Bytes \* (2 ^ fator de dimensionamento). Se o fator de dimensionamento de janela for 3 e o tamanho da janela de 65535, o cálculo é o seguinte: 65535 \* (2 ^ 3) = 262,140 bytes. Um fator de dimensionamento de 14 resulta num tamanho de janela TCP de 14 (o desvio máximo permitido), em seguida, o tamanho da janela TCP será 1,073,725,440 bytes (gigabits 8,5).

#### <a name="support-for-tcp-window-scaling"></a>Suporte para o dimensionamento de janela TCP

Windows tem a capacidade de definir diferente fatores de dimensionamento num por base do tipo de ligação – existem várias classes de ligações (Centro de dados, internet e assim por diante). Pode ver a classificação de ligação dimensionamento da janela com o comando do powershell Get-NetTCPConnection.

```powershell
Get-NetTCPConnection
```

Pode ver os valores de cada classe com o comando do powershell Get-NetTCPSetting.

```powershell
Get-NetTCPSetting
```

O tamanho de janela TCP inicial e o fator de dimensionamento de TCP podem ser definido no Windows por meio do comando do powershell Set-NetTCPSetting. Obter mais informações podem ser encontradas no [NetTCPSetting do conjunto de página](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)

```powershell
Set-NetTCPSetting
```

Seguem-se as definições de TCP em vigor para AutoTuningLevel.

| | | | |
|-|-|-|-|
|AutoTuningLevel|Fator de dimensionamento|Multiplicador de dimensionamento|Fórmula para<br/>calcular o tamanho máximo da janela|
|Desativado|Nenhuma|Nenhuma|Tamanho da janela|
|Restringido|4|2^4|Tamanho da janela * (2 ^ 4)|
|Altamente restritos|2|2^2|Tamanho da janela * (2 ^ 2)|
|Normal|8|2^8|Tamanho da janela * (2 ^ 8)|
|Experimental|14|2^14|Tamanho da janela * (2 ^ 14)|

Estas definições são o maior probabilidade de afetar o desempenho do TCP, é importante observar que muitos outros fatores na Internet, fora do controlo do Azure, também podem afetar o desempenho de TCP.

#### <a name="increase-mtu-size"></a>Aumentar o tamanho MTU

Uma pergunta lógica a fazer é "pode aumentar o MTU aumentar o desempenho de TCP como um MTU maior significa um maior MSS"? A resposta simples é, provavelmente não. Como discutido, há prós e contras ao tamanho do pacote que são aplicáveis para além de apenas tráfego TCP. Conforme mencionado acima, os fatores mais importantes que afetam o desempenho de taxa de transferência TCP é o tamanho de janela TCP, perda de pacotes e RTT.

> [!IMPORTANT]
> Azure não recomenda que os clientes do Azure, modifique o valor MTU predefinido em máquinas virtuais.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Accelerated Networking e dimensionamento do lado da receção

#### <a name="accelerated-networking"></a>Redes Aceleradas

Funções de rede de máquina virtual tem sido historicamente CPU intensivas em termos no convidado da VM e o anfitrião de hipervisor /. Cada pacote que transits através do anfitrião é processado no software pelo host CPU – incluindo todos os a rede Virtual encapsulamento/Alemanha-capsulation. Portanto, mais tráfego que passa pelo anfitrião, em seguida, maior será a carga de CPU. E, se a CPU do anfitrião está ocupada a realizar outras operações, em seguida, que também irá afetar o débito de rede e a latência. Este problema estar resolvido por meio de redes aceleradas.

Funcionamento em rede acelerado fornece a latência de rede de ultra baixas consistentes através de hardware programável interna e tecnologias, como o SR-IOV do Azure. Ao mover muito da pilha de rede definida pelo software do Azure as CPUs de desconto em baseada em FPGA SmartNICs, computação e ciclos são recuperados por aplicações de utilizador final, colocando uma carga menor na VM, diminuindo a interferência e inconsistência na latência. Em outras palavras, o desempenho pode ser mais determinístico.

Funcionamento em rede acelerado alcança melhoramentos de desempenho, permitindo que a VM de convidado para ignorar o host e estabelecer um datapath diretamente com SmartNIC um anfitrião. Benefícios de redes aceleradas são:

- **Menor latência / superior pacotes por segundo (pps)**: Remover o comutador virtual do datapath remove o tempo gasto de pacotes no anfitrião para processamento de diretiva e aumenta o número de pacotes que podem ser processadas dentro da VM.

- **Reduzido distorção**: Processamento de comutador virtual depende da quantidade de política que tem de ser aplicadas e a carga de trabalho da CPU que está a fazer o processamento. Descarregar a imposição de política para o hardware remove essa variabilidade fornecendo pacotes diretamente à VM, remover o anfitrião a comunicação de VM e todas as interrupções de software e Alternâncias de contexto.

- **Diminui a utilização da CPU**: Ignorar o comutador virtual no anfitrião leva para menos de utilização da CPU para processar o tráfego de rede.

Redes aceleradas tem de ser explicitamente ativada numa base por VM. Estão disponíveis instruções para ativar o Accelerated Networking numa VM no [criar uma máquina virtual Linux com redes aceleradas página](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

#### <a name="receive-side-scaling-rss"></a>Do lado da receção (RSS)

Dimensionamento do lado da receção é uma tecnologia de controlador de rede que distribui a receção de tráfego de rede com mais eficiência ao distribuir o processamento de receber entre várias CPUs num sistema com vários processadores. Em termos simples, o RSS permite que um sistema processar uma maior quantidade de tráfego de recebimento, porque utiliza a todas as CPUs disponíveis em vez de apenas um. Uma discussão mais técnica de RSS que pode ser encontrada no [introdução à página do lado da receção](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

RSS é necessário para obter o máximo desempenho quando o Accelerated Networking está ativado numa VM. Também pode haver vantagens em utilizar o RSS no VMs que não têm a aceleração de rede ativada. Visão geral sobre como determinar se o RSS está ativado e configuração para ativá-la pode ser encontrada no [otimizar o débito de rede para a página de máquinas virtuais do Azure](http://aka.ms/FastVM).

### <a name="tcp-time-wait-and-time-wait-assassination"></a>Espera de tempo TCP e o tempo aguardar Assassination

Outro problema comum que afeta o desempenho de rede e a aplicação é a definição de espera de tempo de TCP. Em ocupado VMs que estão a abrir e fechar sockets muitos, como um cliente ou servidor (origem IP:Source porta + porta IP:Destination de destino), durante o funcionamento normal do TCP, um soquete determinado pode acabar em estado de espera de tempo para uma quantidade substancial de tempo. Este estado de "tempo de espera" destina-se para permitir que quaisquer dados adicionais ser entregue num soquete antes de fechá-lo. Por conseguinte, pilhas de TCP/IP em geral, impedem a reutilização de um socket soltando silenciosamente o pacote TCP SYN de clientes.

Este período de tempo é de um socket em estado de espera é configurável, mas pode ir de 30 segundos a 240 segundos de tempo. Sockets são um recurso finito e o número de sockets que podem ser utilizadas num determinado momento é configurável (o número está geralmente cerca de 30 000 sockets potenciais). Se este número se esgota, ou clientes e servidores têm definições de espera de tempo sem correspondência, e uma VM tenta reutilizar um soquete no estado de espera de tempo, em seguida, novas ligações não como silenciosamente os pacotes de TCP SYN são largados.

Normalmente, o valor de intervalo de portas para os sockets de saída, bem como as definições de TCP tempo de espera e reutilização de soquete são configuráveis dentro da pilha de TCP/IP de um sistema operacional. Esses números a alteração pode potencialmente melhorar a escalabilidade, mas dependendo do cenário, pode introduzir problemas de interoperabilidade e deve ser alterada com cuidado.

Um recurso chamado Assassination de espera de tempo foi introduzido para lidar com isso, limitação de dimensionamento. Assassination de espera de tempo permite que um soquete ser reutilizado em determinados cenários, como quando o número de sequência no pacote IP da nova ligação excede o número de sequência do último pacote a partir da ligação anterior. Neste caso, o sistema operativo permitirá que a nova ligação seja estabelecida (aceitar o ACK de SYN novo) e força fechar a ligação anterior que foi no tempo de espera estado. Esta capacidade é suportada em VMs do Windows no Azure hoje mesmo e suporte dentro de outras VMs deve ser investigado por clientes do Azure com os respetivos fornecedores de SO.

Documentação sobre como configurar as definições de TCP tempo de espera e o intervalo de portas de origem está disponível na [as definições que podem ser modificadas para a página de melhorar o desempenho de rede](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Fatores de rede virtual que podem afetar o desempenho

### <a name="vm-maximum-outbound-throughput"></a>Débito de saída máximo de VM

O Azure oferece uma variedade de tamanhos de VM e tipos, cada um com uma mistura diferentes capacidades de desempenho. Um recurso de desempenho desse tipo é a rede débito (ou largura de banda), medida em megabits por segundo (Mbps). Uma vez que as máquinas virtuais estão alojadas no hardware partilhado, a capacidade de rede tem de ser partilhada bastante entre as máquinas de virtuais partilhar o mesmo hardware. Máquinas de virtuais maiores são alocadas relativamente mais largura de banda que mais pequeno de máquinas virtuais.

A largura de banda atribuída a cada máquina virtual tem tráfego limitada sobre o tráfego de saída (saída) da máquina virtual. Todo o tráfego de rede deixar a máquina virtual é contado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tem um limite de 1000 Mbps, esse limite aplica-se quer o tráfego de saída é destinado a outra máquina virtual na mesma rede virtual ou fora do Azure.
Entrada não é limitada ou limitada diretamente. No entanto, há outros fatores, como limites de CPU e armazenamento, que podem afetar a capacidade de uma máquina virtual para processar dados de entrada.

Funcionamento em rede acelerado é um recurso criado para melhorar o desempenho da rede, incluindo latência, débito e de utilização da CPU. Embora o Accelerated Networking pode melhorar o débito de uma máquina virtual, pode fazer isso apenas até a máquina virtual está alocada da largura de banda.

Máquinas virtuais do Azure tem de ter um, mas Maio possui vários, anexados de interfaces de rede. Largura de banda atribuída a uma máquina virtual é a soma de todo o tráfego de saída em todas as interfaces de rede ligadas a uma máquina virtual. Em outras palavras, a largura de banda alocada é por máquina virtual, independentemente de quantas interfaces de rede estão ligados à máquina virtual.
 
Débito de saída esperado e o número de interfaces de rede suportadas por cada tamanho de VM está detalhada aqui. Para ver o débito máximo, selecione um tipo, como para fins gerais, em seguida, selecione uma série de tamanho sobre a página resultante, como a série Dv2. Cada série tem uma tabela com as especificações na última coluna com o título, NICs. Máx. de rede / desempenho de rede (Mbps) esperado.

O limite de débito aplicam-se à máquina virtual. Débito não é afetado pelos seguintes fatores:

- **Número de interfaces de rede**: O limite de largura de banda é cumulativo de todo o tráfego de saída da máquina virtual.

- **Funcionamento de rede acelerado**: Embora a funcionalidade pode ser útil atingir o limite de publicados, não altera o limite.

- **Destino do tráfego**: Todos os destinos contam para o limite de saída.

- **Protocolo**: Todo o tráfego de saída ao longo de todos os protocolos de conta para o limite.

R [tabela de largura de banda máxima por tipo de VM pode ser encontrada ao visitar essa página](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) e clicar no respetivo tipo VM. Em cada página de tipo, uma tabela mostrará os NICs máximas e largura de banda de rede esperado máxima.

Obter mais informações sobre a largura de banda de rede de VM podem ser encontradas em [largura de banda de rede de Máquina Virtual](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Considerações de desempenho de Internet

Como discutido ao longo deste artigo, fatores na Internet e fora do controlo do Azure podem afetar o desempenho de rede. Esses fatores são:

- **Latência**: O tempo de ida e volta entre duas destinos pode ser afetado por problemas em redes intermediários, deixando a distância "mais curta" caminhos do peering possíveis e inferior ao ideal – caminho de tráfego

- **Perda de pacotes**: Perda de pacotes pode ser causada por congestionamento de rede, problemas de caminho físico e dispositivos de rede em execução

- **Tamanho MTU/fragmentação**: Fragmentação ao longo do caminho pode levar a atrasos na chegada de dados ou os pacotes que chegam fora de ordem, que podem afetar a entrega de pacotes

O traceroute é uma boa ferramenta para medir as características de desempenho de rede (por exemplo, perda de pacotes e latência) ao longo de cada caminho de rede entre um dispositivo de origem e de destino.

### <a name="network-design-considerations"></a>Considerações de design de rede

Juntamente com as considerações acima, a topologia de uma rede Virtual pode afetar o desempenho de rede Virtual. Por exemplo, um hub- and -spoke design que o tráfego de backhauls globalmente para uma rede virtual único hub irá introduzir a latência de rede e, portanto, afetar o desempenho geral da rede. Da mesma forma, o número de dispositivos de rede que o tráfego de rede passa pelo também pode afetar a latência geral. Por exemplo, num design de hub- and -spoke, se o tráfego passa por meio de um spoke aplicação Virtual de rede e uma aplicação Virtual de Hub antes de trânsito para a Internet, em seguida, latência pode ser introduzida por aplicações virtuais de rede.

### <a name="azure-regions-virtual-networks-and-latency"></a>Regiões do Azure, redes virtuais e de latência

Regiões do Azure são compostas de vários centros de dados que existem dentro de uma área geográfica geral. Esses data Centers podem não estar fisicamente próximos uns dos outros e em alguns casos podem ser separados por até 10 quilômetros. A rede Virtual é uma lógica sobreposição por cima da rede do Centro de dados físico do Azure e uma rede Virtual não implica qualquer topologia de rede específico no Centro de dados. Por exemplo, A VM e B de VM estão na mesma rede Virtual e sub-rede, mas podem estar em diferentes racks, linhas ou até mesmo centros de dados. Eles podem ser separados por pés de cabo de fibra óptica ou quilômetros de cabo de fibra óptica. Essa realidade pode introduzir latência variável (diferença de alguns milissegundos) entre VMs diferentes.

Esta colocação geográfica e, portanto, a latência entre duas VMs, podem ser influenciados por meio da configuração de conjuntos de disponibilidade e zonas de disponibilidade, no entanto, a distância entre datacenters numa região é a região específicos e predominantemente influenciado por topologia de Datacenter na região.

### <a name="source-nat-port-exhaustion"></a>Exaustão de porta NAT de origem

Uma implementação no Azure pode comunicar com pontos finais de fora do Azure na Internet pública e/ou do espaço de IP público. Quando uma instância inicia a ligação de saída, do Azure maps dinamicamente o endereço IP privado para um endereço IP público. Depois de criar esse mapeamento, tráfego de retorno para este fluxo originado pela saída também pode acessar o endereço IP privado em que o fluxo teve origem.

Para cada ligação de saída, o Balanceador de carga do Azure tem de manter esse mapeamento para um determinado período de tempo. Com a natureza de multi-inquilino do Azure, a manter esse mapeamento para cada fluxo de saída para cada VM pode ser com muitos recursos. Portanto, existem limites que são definidas e com base na configuração da rede Virtual do Azure. Ou, mais precisamente - indicado uma VM do Azure pode fazer apenas um determinado número de ligações de saída num determinado momento. Quando estes limites são esgotados, em seguida, a VM do Azure irá ser impedida de fazer quaisquer ligações de saída ainda mais.

Este comportamento é, no entanto, configurável. Para obter mais informações sobre o [SNAT e SNAT porta esgotamento], consulte [este artigo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Medir o desempenho de rede no Azure

Um número dos valores máximos de desempenho neste artigo está relacionadas com a latência de rede / IDA e volta (RTT) de tempo entre duas VMs. Esta seção fornece algumas sugestões sobre como testar a latência/RTT, como o desempenho de TCP e o desempenho da rede VM. Os valores de TCP/IP & rede descritos acima podem ser ajustados e desempenho testadas usando as técnicas descritas abaixo. Os valores de latência, MTU, MSS e tamanho da janela podem ser usados nos cálculos de listados acima e valores máximos teóricos possam ser comparados aos valores reais foi observadas durante o teste.

### <a name="measure-round-trip-time-and-packet-loss"></a>Tempo de ida e volta de medida e perda de pacotes

Desempenho do TCP depende muito de RTT e a perda de pacotes. A forma mais simples para medir o RTT e a perda de pacotes está a utilizar o utilitário de ping disponível no Windows e Linux. A saída do ping irá mostrar Mín/Máx. / Méd latência entre uma origem e destino, bem como perda de pacotes. O ping utiliza o protocolo ICMP por predefinição. Para testar o RTT de TCP, em seguida, PsPing pode ser utilizado. Estão disponíveis em mais informações sobre PsPing [esta ligação](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Débito real de medidas de uma conexão TCP

NTttcp é uma ferramenta que é utilizada para testar o desempenho de TCP de uma VM do Windows ou do Linux. Várias definições de TCP podem ser refinadas e os benefícios testadas usando NTttcp. Podem encontrar mais informações sobre NTttcp nas ligações abaixo.

- [Largura de banda/débito testes (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Utilitário de NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Largura de banda real do medidas de uma máquina virtual

Testes de desempenho de diferentes tipos VM, redes aceleradas e assim por diante, podem ser testado usando uma ferramenta chamada Iperf, também está disponível no Linux e Windows. Iperf pode utilizar o TCP ou UDP para testar o débito de rede global. Testes de taxa de transferência TCP usando Iperf são influenciados por fatores discutidos neste artigo (latência, RTT e assim por diante). Então, UDP poderão resultar em melhores resultados de teste simplesmente o débito máximo.

Pode encontrar informações adicionais abaixo:

- [Resolução de problemas de desempenho de rede do Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Como validar o débito de VPN para uma rede virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Detetar comportamentos TCP ineficientes

Os clientes do Azure poderão ver os pacotes TCP com sinalizadores de TCP (SACK, ACK duplicado, retransmissão e RETRANSMITIR rápida) no capturas de pacotes que possam indicar problemas de desempenho de rede. Estes pacotes indicam especificamente ineficiências de rede como resultado de perda de pacotes. No entanto, perda de pacotes não é necessariamente devido a problemas de desempenho do Azure. Problemas de desempenho podem ser o resultado do aplicativo, sistema operacional ou outros problemas que podem não estar diretamente relacionadas com a plataforma do Azure. Também é importante observar que algumas retransmissão ou ACKs duplicados numa rede é normal – protocolos TCP foram criados para ser confiável. Além disso, evidências destes pacotes TCP numa captura de pacotes não indicam necessariamente um problema de rede sistêmicos a menos que estejam excessivas.

No entanto, ele deve ser declarado claramente que esses tipos de pacotes são indicações de que a taxa de transferência não estiver a obter o máximo desempenho – pelos motivos relacionados discutidos em outras seções.

## <a name="next-steps"></a>Próximos Passos

Agora que discutimos a otimização de desempenho de TCP/IP para as VMs do Azure, as próximas etapas incluem [planear a outra rede Virtual](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-vnet-plan-design-arm) considerações ou [Saiba mais sobre como ligar e configurar redes virtuais](https://docs.microsoft.com/en-us/azure/virtual-network/).