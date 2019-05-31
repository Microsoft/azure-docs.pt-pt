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
ms.openlocfilehash: ad1a5b69e4ec7b44c0e61a5ddd2c06633464d31a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234988"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP ajuste de desempenho para as VMs do Azure

Este artigo discute técnicas comuns de ajuste de desempenho TCP/IP e algumas coisas a serem consideradas ao usá-las para máquinas virtuais em execução no Azure. Ele irá fornecer uma descrição geral básica das técnicas e explore como pode ser ajustados.

## <a name="common-tcpip-tuning-techniques"></a>Técnicas de otimização de TCP/IP comuns

### <a name="mtu-fragmentation-and-large-send-offload"></a>O MTU e descarregamento de envio grande fragmentação

#### <a name="mtu"></a>MTU

A unidade de transmissão máxima (MTU) é o maior quadro de tamanho (pacote), especificado em bytes, que podem ser enviados através de uma interface de rede. O MTU é uma definição configurável. A predefinição MTU utilizado em VMs do Azure e a configuração padrão na maioria dos dispositivos de rede globalmente, é 1.500 bytes.

#### <a name="fragmentation"></a>Fragmentação

A fragmentação ocorre quando é enviado um pacote que excede a MTU da interface de rede. A pilha de TCP/IP irá interromper o pacote em partes mais pequenas (fragmentos) que estão em conformidade com a MTU da interface. A fragmentação ocorre na camada de IP e é independente do protocolo subjacente (por exemplo, o TCP). Quando é enviado um pacote de 2.000 bytes através de uma interface de rede com uma MTU de 1.500, o pacote irá ser apresentado num pacote de 1.500 bytes e um pacote de 500 bytes.

Dispositivos de rede no caminho entre uma origem e de destino podem qualquer um dos pacotes de lista que excedem o MTU ou fragmentados o pacote em partes mais pequenas.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>O fragmento não bits num pacote IP

O bit de fragmento não (DF) é um sinalizador no cabeçalho de protocolo IP. O bit de DF indica que os dispositivos de rede no caminho entre o remetente e o receptor não devem fragmentados o pacote. Este bit poderia ser definido por muitos motivos. (Consulte a secção "Descoberta de MTU de caminho" deste artigo para obter um exemplo.) Quando um dispositivo de rede recebe um pacote com o conjunto de bits não fragmento, e esse pacote excede a interface do dispositivo MTU, o comportamento padrão é para o dispositivo remover o pacote. O dispositivo envia uma mensagem ICMP fragmentação necessária para a origem original do pacote.

#### <a name="performance-implications-of-fragmentation"></a>Implicações de desempenho de fragmentação

A fragmentação pode ter implicações de desempenho negativo. Uma das principais razões para o efeito no desempenho é o impacto da CPU/memória a fragmentação e a remontagem de pacotes. Quando um dispositivo de rede precisa fragmentados um pacote, terá de alocar recursos de CPU/memória para executar a fragmentação.

A mesma coisa acontece quando o pacote é remontado. O dispositivo de rede tem de armazenar todos os fragmentos até que eles são recebidos para que ele pode remontá-las para o pacote original. Esse processo de fragmentação e a remontagem também pode causar uma latência.

A outra implicação de desempenho negativo possível de fragmentação é que resultam em pacotes fragmentados poderá chegar fora de ordem. Quando os pacotes são recebidos fora de ordem, podem remover alguns tipos de dispositivos de rede-los. Quando isso acontece, o pacote completo tem de ser retransmitido.

Fragmentos, normalmente, são ignorados pela dispositivos de segurança, como firewalls de rede ou quando um dispositivo de rede do recebem os buffers ficam esgotados. Quando um dispositivo de rede do recebem buffers ficam esgotados, um dispositivo de rede está a tentar para a remontagem de um pacote fragmentado, mas não tem os recursos para armazenar e reassume o pacote.

A fragmentação pode ser vista como uma operação negativa, mas o suporte para a fragmentação é necessária quando está se conectando várias redes através da internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Vantagens e as conseqüências de modificar o MTU

Em termos gerais, pode criar uma rede mais eficiente, aumentando a MTU. Cada pacote que é transmitida tem informações de cabeçalho que são adicionadas ao pacote original. Quando a fragmentação cria pacotes mais, há sobrecarga cabeçalho mais e, assim que a rede menos eficiente.

Eis um exemplo. O tamanho do cabeçalho Ethernet é 14 bytes mais de uma sequência de verificação de quadro de 4 bytes para garantir a consistência do quadro. Se um pacote de 2.000 bytes é enviado, bytes 18 de sobrecarga de Ethernet é adicionado na rede. Se o pacote for fragmentado num pacote de 1.500 bytes e um pacote de 500 bytes, cada pacote terão 18 bytes do cabeçalho Ethernet, um total de 36 bytes.

Tenha em atenção que a aumentar a MTU necessariamente não irá criar uma rede mais eficiente. Se um aplicativo enviar apenas 500 bytes pacotes, a mesma sobrecarga de cabeçalho existirá se o MTU é 1.500 bytes ou de 9 000 bytes. A rede se tornará mais eficiente apenas se ele usa o maior tamanho dos de pacotes que é afetados pelo MTU.

#### <a name="azure-and-vm-mtu"></a>O Azure e a MTU da VM

A predefinição MTU para VMs do Azure é 1.500 bytes. A pilha de rede Virtual do Azure irá tentar fragmentados um pacote em bytes 1.400.

Tenha em atenção que a pilha de rede Virtual não é, inerentemente ineficiente porque ele fragmentos pacotes em bytes 1.400, apesar das VMs têm um MTU de 1.500. Uma grande percentagem dos pacotes de rede são muito menor do que 1.400 ou 1.500 bytes.

#### <a name="azure-and-fragmentation"></a>Azure e a fragmentação

Pilha de rede virtual está configurada para soltar "fragmentos fora de ordem," pacotes fragmentados ou seja, não chegam a sua ordem fragmentado original. Esses pacotes são largados principalmente por causa de uma vulnerabilidade de segurança de rede anunciada em Novembro de 2018 chamado FragmentSmack.

FragmentSmack é um defeito da forma que o kernel do Linux manipulado remontagem de fragmentados pacotes IPv4 e IPv6. Um atacante remoto pode utilizar esta falha para operações de remontagem fragmento Caro de Acionador, que poderia levar a CPU maior e uma negação de serviço no sistema de destino.

#### <a name="tune-the-mtu"></a>Otimizar o MTU

Pode configurar uma MTU de VM do Azure, como em qualquer outro sistema operacional. Mas deve considerar a fragmentação que ocorre no Azure, descrito acima, quando estiver a configurar um MTU.

Não recomendamos aos clientes aumentar MTUs de VM. Esta discussão destina-se para explicar os detalhes de como o Azure implementa o MTU e executa a fragmentação.

> [!IMPORTANT]
>Aumentando a MTU não é conhecido para melhorar o desempenho e pode ter um efeito negativo no desempenho da aplicação.
>
>

#### <a name="large-send-offload"></a>Descarregamento de envio grande

Descarregamento de envio grande (LSO) pode melhorar o desempenho de rede ao descarregar a segmentação de pacotes para o adaptador de Ethernet. Quando LSO está ativada, a pilha de TCP/IP cria um grande pacote TCP e envia-os para o adaptador de Ethernet para segmentação antes do reencaminhamento-lo. O benefício de LSO é que pode gratuitamente a CPU de segmentar pacotes em tamanhos de que está em conformidade com o MTU e descarregar esse processamento para a interface de Ethernet, onde é executado no hardware. Para saber mais sobre os benefícios de LSO, veja [descarga de envio grande de suporte](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Quando LSO está ativada, os clientes do Azure poderão ver tamanhos grandes de quadro, ao executar capturas de pacotes. Estes tamanhos de quadro grande podem levar alguns clientes pensar a fragmentação está a ocorrer ou que está a ser utilizado um MTU grandes, quando não estiver. Com o LSO, o adaptador Ethernet pode anunciar um tamanho maior de máximo de segmento (MSS) para a pilha de TCP/IP para criar um pacote maior de TCP. Este quadro não-segmentado inteiro, em seguida, é reencaminhado para o adaptador de Ethernet e ficavam visível numa captura de pacotes executada na VM. Mas o pacote será ser dividido em vários quadros menores pelo adaptador Ethernet, de acordo com MTU o adaptador de Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Dimensionamento de janela TCP MSS e PMTUD

#### <a name="tcp-maximum-segment-size"></a>Tamanho máximo de segmento TCP

Tamanho máximo de segmento TCP (MSS) é uma definição que limita o tamanho de segmentos TCP, o que evita a fragmentação de pacotes TCP. Sistemas operativos, normalmente, irá utilizar esta fórmula para definir MSS:

`MSS = MTU - (IP header size + TCP header size)`

O cabeçalho IP e o cabeçalho TCP são 20 bytes ou total de bytes de 40. Portanto, uma interface com uma MTU de 1.500 terão um MSS de 1,460. Mas o MSS é configurável.

Esta definição é acordada para o handshake de três vias TCP quando uma sessão TCP é configurada entre uma origem e um destino. Ambos os lados enviar um valor de MSS e, o menor dos dois é utilizado para a ligação de TCP.

Tenha em atenção que o MTUs de origem e de destino não são os fatores únicos que determinam o valor de MSS. Dispositivos de rede intermediário, como gateways de VPN, incluindo o Gateway de VPN do Azure, podem ajustar o MTU independentemente de origem e de destino para assegurar o desempenho de rede não ideais.

#### <a name="path-mtu-discovery"></a>Deteção MTU do caminho

MSS é negociada, mas não pode indicar o MSS real que pode ser utilizado. Isso ocorre porque os outros dispositivos de rede no caminho entre a origem e o destino podem ter um valor MTU inferior de origem e de destino. Neste caso, o dispositivo cujo MTU é menor do que o pacote irá remover o pacote. O dispositivo enviará de volta uma mensagem ICMP fragmentação necessária (tipo 3, 4 de código) que contém o MTU. Esta mensagem ICMP permite que o anfitrião de origem reduzir o seu caminho de MTU adequadamente. O processo é chamado de deteção de MTU de caminho (PMTUD).

O processo PMTUD é ineficiente e afeta o desempenho de rede. Quando os pacotes são enviados que excedem o MTU de um caminho de rede, os pacotes tem de ser retransmitido com um MSS inferior. Se o remetente não recebe a mensagem ICMP fragmentação necessária, talvez devido a uma firewall de rede no caminho (normalmente conhecido como um *PMTUD blackhole*), o remetente não souber o que precisa reduzir o MSS e será continuamente retransmitir o pacote. É por isso não é recomendada a aumentar a MTU de VM do Azure.

#### <a name="vpn-and-mtu"></a>VPN e MTU

Se utilizar VMs que executam o encapsulamento (como IPsec VPNs), existem algumas considerações adicionais sobre o tamanho do pacote e de MTU. VPNs adicionar mais cabeçalhos a pacotes, que aumenta o tamanho do pacote e requer um MSS menores.

Para o Azure, recomendamos que defina a afixação de MSS de TCP em 1,350 bytes e túnel interface MTU para 1.400. Para obter mais informações, consulte a [VPN dispositivos e a página de parâmetros de IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latência, o tempo de ida e volta e o dimensionamento de janela TCP

#### <a name="latency-and-round-trip-time"></a>Tempo de latência e de ida e volta

Latência de rede é regida pela velocidade da luz através de uma rede óptica de fibra. Débito de rede do TCP também efetivamente é regido pelo tempo de ida e volta (RTT) entre dois dispositivos de rede.

| | | | |
|-|-|-|-|
|**rota**|**distância**|**Tempo unidirecional**|**RTT**|
|Nova Iorque a San Francisco|4,148 km de distância|21 ms|42 ms|
|Nova York para Londres|5,585 km de distância|28 ms|56 ms|
|Nova Iorque, para que Sydney|15,993 km de distância|80 ms|160 ms|

Esta tabela mostra a distância em linha reta entre duas localizações. Em redes, a distância é normalmente mais tempo do que a distância em linha reta. Aqui está uma fórmula simples para calcular o mínimo RTT como regida pela velocidade da luz:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Pode usar 200 de velocidade de propagação. Esta é a distância, em metros, que a luz viaja em 1 milissegundo.

Vamos dar Nova Iorque a San Francisco como exemplo. A distância em linha reta é 4,148 km de distância. Esse valor se conectando a equação, obtemos o seguinte:

`Minimum RTT = 2 * (4,148 / 200)`

A saída da equação é em milissegundos.

Se quiser obter o melhor desempenho de rede, é a opção de lógica selecionar destinos com a menor distância entre eles. Também deve conceber a sua rede virtual para otimizar o caminho do tráfego e reduzir a latência. Para obter mais informações, consulte a secção "Considerações de design de rede" deste artigo.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Efeitos de tempo de latência e de ida e volta em TCP

Tempo de ida e volta tem um efeito direto na débito máximo do TCP. No protocolo TCP, *tamanho da janela* é a quantidade máxima de tráfego que pode ser enviado através de uma ligação de TCP, antes do remetente tem de receber a confirmação do destinatário. Se o MSS TCP está definido como 1,460 e o tamanho da janela TCP está definido como 65.535, o remetente pode enviar 45 pacotes antes que ele tenha que receber a confirmação do destinatário. Se o remetente não receber a confirmação, ele será retransmitir os dados. Eis a fórmula:

`TCP window size / TCP MSS = packets sent`

Neste exemplo, 65.535 / 1,460 é arredondado até 45.

Neste estado "a aguardar confirmação", um mecanismo para garantir uma entrega fiável de dados, é o que faz com que RTT afetar a taxa de transferência. O remetente aguarda, mais tempo, confirmação, mais tempo ele precisa aguardar antes de a enviar mais dados.

Eis a fórmula para calcular o débito máximo de uma única ligação de TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Esta tabela mostra o máximo de megabytes / por segundo de débito de uma única ligação de TCP. (Para facilitar a leitura, é utilizada em megabytes para a unidade de medida.)

| | | | |
|-|-|-|-|
|**Tamanho de janela TCP (bytes)**|**Latência RTT (ms)**|**Máximo megabyte/segundo de débito**|**Máximo megabit/segundo de débito**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

Se perdem-se os pacotes, o débito máximo de uma conexão TCP ser reduzido enquanto o remetente retransmite dados já enviou.

#### <a name="tcp-window-scaling"></a>Dimensionamento de janela TCP

Dimensionamento de janela TCP é uma técnica que dinamicamente aumenta o tamanho da janela TCP para permitir que mais dados a serem enviados antes de uma confirmação é necessária. No exemplo anterior, 45 pacotes seriam enviados antes de uma confirmação foi necessária. Se aumentar o número de pacotes que podem ser enviados antes de uma confirmação é necessária, está reduzindo o número de vezes que um remetente está a aguardar confirmação, o que aumenta o débito máximo do TCP.

Esta tabela ilustra essas relações:

| | | | |
|-|-|-|-|
|**Tamanho de janela TCP (bytes)**|**Latência RTT (ms)**|**Máximo megabyte/segundo de débito**|**Máximo megabit/segundo de débito**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Mas o valor de cabeçalho TCP para o tamanho de janela TCP é apenas de 2 bytes de comprimento, que significa que o valor máximo para uma janela de recepção é de 65535. Para aumentar o tamanho máximo da janela, foi introduzido um fator de dimensionamento de janela TCP.

O fator de dimensionamento também é uma definição que pode configurar num sistema operativo. Eis a fórmula para calcular o tamanho da janela TCP através de fatores de dimensionamento:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Eis o cálculo para um fator de dimensionamento de janela de 3 e um tamanho de janela de 65.535:

`65,535 \* (2^3) = 262,140 bytes`

Um fator de dimensionamento de 14 resulta num tamanho de janela TCP de 14 (o desvio máximo permitido). O tamanho da janela TCP será 1,073,725,440 bytes (gigabits 8,5).

#### <a name="support-for-tcp-window-scaling"></a>Suporte para o dimensionamento de janela TCP

Windows podem definir os fatores de dimensionamento diferentes para diferentes tipos de ligação. (As classes de ligações incluem datacenter, internet e assim por diante). Utilizar o `Get-NetTCPConnection` comando do PowerShell para ver a tipo de ligação de dimensionamento da janela:

```powershell
Get-NetTCPConnection
```

Pode utilizar o `Get-NetTCPSetting` comando do PowerShell para ver os valores de cada classe:

```powershell
Get-NetTCPSetting
```

Pode definir o tamanho de janela TCP inicial e o fator de dimensionamento de TCP no Windows utilizando o `Set-NetTCPSetting` comando do PowerShell. Para obter mais informações, consulte [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Estas são as definições de TCP em vigor para `AutoTuningLevel`:

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**Fator de dimensionamento**|**Multiplicador de dimensionamento**|**Fórmula para<br/>calcular o tamanho máximo da janela**|
|Desativado|Nenhuma|Nenhuma|Tamanho da janela|
|Restringido|4|2^4|Tamanho da janela * (2 ^ 4)|
|Altamente restritos|2|2^2|Tamanho da janela * (2 ^ 2)|
|Normal|8|2^8|Tamanho da janela * (2 ^ 8)|
|Experimental|14|2^14|Tamanho da janela * (2 ^ 14)|

Estas definições estão a maior probabilidade de afetar o desempenho do TCP, mas lembre-se de que muitos outros fatores na internet, fora do controlo do Azure, também podem afetar o desempenho de TCP.

#### <a name="increase-mtu-size"></a>Aumentar o tamanho MTU

Uma vez que uma MTU maior significa um MSS maiores, deve estar imaginando se aumentar o MTU pode aumentar o desempenho de TCP. Provavelmente não. Há prós e contras ao tamanho do pacote para além de apenas tráfego TCP. Como discutido anteriormente, os fatores mais importantes que afetam o desempenho de taxa de transferência TCP são tamanho de janela TCP, perda de pacotes e RTT.

> [!IMPORTANT]
> Não recomendamos que os clientes do Azure alterar o valor MTU predefinido em máquinas virtuais.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Accelerated networking e dimensionamento do lado da receção

#### <a name="accelerated-networking"></a>Redes aceleradas

Funções de rede de máquina virtual tem sido historicamente CPU intensiva em VM do convidado e anfitrião de hipervisor /. Cada pacote que transits através do anfitrião do é processado no software através do anfitrião da CPU, incluindo todos os encapsulamento da rede virtual e descapsulação. Para que mais o tráfego que atravessa o anfitrião, maior será a CPU de carga. E se a CPU do anfitrião está ocupada com outras operações, que também irá afetar o débito de rede e a latência. Azure resolve esse problema com o funcionamento em rede acelerado.

Funcionamento em rede acelerado fornece a latência de rede ultralow consistente através do hardware programável interna do Azure e tecnologias como SR-IOV. Funcionamento em rede acelerado passa grande parte do Azure definida pelo software pilha de rede desativado as CPUs e em baseada em FPGA SmartNICs. Esta alteração permite que as aplicações de utilizador final recuperar os ciclos de computação, que coloca uma carga menor na VM, diminuindo a interferência e inconsistência de latência. Em outras palavras, o desempenho pode ser mais determinístico.

Funcionamento em rede acelerado melhora o desempenho ao permitir que a VM para ignorar o host e estabelecer um datapath diretamente com SmartNIC um anfitrião do convidado. Aqui estão alguns dos benefícios de funcionamento em rede acelerado:

- **Menor latência / superior pacotes por segundo (pps)** : Remover o comutador virtual do datapath elimina o tempo gasto de pacotes no anfitrião para processamento de diretiva e aumenta o número de pacotes que podem ser processadas na VM.

- **Reduzido distorção**: Processamento de comutador virtual depende da quantidade de política que tem de ser aplicadas e a carga de trabalho da CPU que está a fazer o processamento. Descarregar a imposição de política para o hardware remove essa variabilidade fornecendo pacotes diretamente à VM, eliminando a comunicação de anfitrião de VM e todas as interrupções de software e Alternâncias de contexto.

- **Diminui a utilização da CPU**: Ignorar o comutador virtual no anfitrião leva para menos de utilização da CPU para processar o tráfego de rede.

Para utilizar as redes aceleradas, terá de ativá-lo explicitamente em cada VM aplicável. Ver [criar uma máquina virtual Linux com redes aceleradas](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para obter instruções.

#### <a name="receive-side-scaling"></a>Dimensionamento do lado da receção

Receba lado que dimensionamento (RSS) é uma tecnologia de controlador de rede que distribui a receção de tráfego de rede com mais eficiência ao distribuir o processamento de receber entre várias CPUs num sistema com vários processadores. Em termos simples, o RSS permite que um sistema processar mais tráfego de recebimento, porque utiliza a todas as CPUs disponíveis em vez de apenas um. Para ver um debate mais técnico de RSS, consulte [introdução ao dimensionamento do lado da receção](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Para obter o melhor desempenho quando o funcionamento em rede acelerado é habilitado numa VM, terá de ativar o RSS. RSS também pode fornecer benefícios em VMs que não usam accelerated networking. Para uma descrição geral de como determinar se o RSS está ativado e como ativá-lo, consulte [otimizar o débito de rede para máquinas virtuais do Azure](https://aka.ms/FastVM).

### <a name="tcp-timewait-and-timewait-assassination"></a>TCP TIME_WAIT e TIME_WAIT assassination

TCP TIME_WAIT é outra configuração comum que afeta o desempenho de rede e a aplicação. Em VMs ocupadas abrir e fechar sockets muitos, como os clientes ou como servidores (origem IP:Source porta + porta IP:Destination de destino), durante o funcionamento normal do TCP, um soquete determinado pode acabar num estado TIME_WAIT durante muito tempo. O estado TIME_WAIT destina-se para permitir que quaisquer dados adicionais ser entregue num soquete antes de fechá-lo. Então, pilhas de TCP/IP em geral, impedem a reutilização de um socket soltando silenciosamente os pacotes de TCP SYN do cliente.

A quantidade de tempo que é de um socket em TIME_WAIT é configurável. Pode ir de 30 segundos a 240 segundos. Sockets são um recurso finito e o número de sockets que podem ser utilizadas num determinado momento é configurável. (O número de sockets disponíveis é, normalmente, cerca de 30 000.) Se o sockets disponíveis são consumidos ou, se os clientes e servidores têm definições de TIME_WAIT sem correspondência, e uma VM tenta reutilizar um socket num estado TIME_WAIT, novas ligações não como SYN TCP silenciosamente os pacotes são largados.

O valor de intervalo de portas para sockets de saída é normalmente configurável dentro da pilha de TCP/IP de um sistema operacional. O mesmo é verdadeiro para definições de TCP TIME_WAIT e reutilização de socket. Esses números a alteração pode potencialmente melhorar a escalabilidade. No entanto, dependendo da situação, estas alterações podem causar problemas de interoperabilidade. Deve ter cuidado se alterar estes valores.

Pode usar TIME_WAIT assassination para resolver essa limitação de dimensionamento. TIME_WAIT assassination permite que um soquete ser reutilizado em determinadas situações, como quando o número de sequência em que o pacote IP da nova ligação excede o número de sequência do último pacote a partir da ligação anterior. Neste caso, o sistema operativo permitirá que a nova ligação seja estabelecida (ele vai aceitar o ACK/SYN novo) e força fechar a ligação anterior que estava num estado TIME_WAIT. Esta capacidade é suportada em VMs do Windows no Azure. Para saber mais sobre o suporte em outras VMs, contacte o fornecedor do sistema operacional.

Para saber mais sobre como configurar definições de TCP TIME_WAIT e o intervalo de portas de origem, veja [as definições que podem ser modificadas para melhorar o desempenho de rede](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Fatores de rede virtual que podem afetar o desempenho

### <a name="vm-maximum-outbound-throughput"></a>Débito de saída máximo de VM

O Azure fornece uma variedade de tamanhos de VM e tipos, cada um com uma mistura diferentes capacidades de desempenho. Um desses recursos é a rede débito (ou de largura de banda), que é medido em megabits por segundo (Mbps). Uma vez que as máquinas virtuais estão alojadas no hardware partilhado, a capacidade de rede tem de ser divididos igualitariamente entre máquinas virtuais que utilizem o mesmo hardware. Máquinas de virtuais maiores são alocadas mais largura de banda que mais pequeno de máquinas virtuais.

A largura de banda atribuída a cada máquina virtual tem tráfego limitada sobre o tráfego de saída (saída) da máquina virtual. Todo o tráfego de rede deixar a máquina virtual é contado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tem um limite de 1000 Mbps, esse limite aplica-se quer o tráfego de saída é destinado a outra máquina virtual na mesma rede virtual ou um fora do Azure.

Entrada não é limitada ou limitada diretamente. Mas há outros fatores, como limites de CPU e armazenamento, que podem afetar a capacidade de uma máquina virtual para processar dados de entrada.

Funcionamento em rede acelerado foi concebido para melhorar o desempenho de rede, incluindo latência, débito e de utilização da CPU. Funcionamento em rede acelerado pode melhorar o débito de uma máquina virtual, mas ele pode fazer isso apenas até a largura de banda alocada da máquina virtual.

Máquinas virtuais do Azure têm, pelo menos, uma interface de rede ligada aos mesmos. Pode ter vários. A largura de banda atribuída a uma máquina virtual é a soma de todo o tráfego de saída em todas as interfaces de rede ligadas à máquina. Em outras palavras, a largura de banda atribuída numa base por-virtual machine, independentemente de quantas interfaces de rede estão ligados à máquina.

Débito de saída esperado e o número de interfaces de rede suportadas por cada tamanho de VM são detalhados em [máquinas de virtuais de tamanhos para Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Para ver o débito máximo, selecione um tipo, como **fins gerais**e, em seguida, localize a secção sobre a série de tamanho da página resultante (por exemplo, "série Dv2"). Para cada série, existe uma tabela que fornece especificações de rede na última coluna, que é intitulada "NICs. Máx. / esperado de largura de banda de rede (Mbps)."

O limite de débito aplicam-se à máquina virtual. Débito não é afetado por esses fatores:

- **Número de interfaces de rede**: O limite de largura de banda aplica-se para a soma de todo o tráfego de saída a partir da máquina virtual.

- **Funcionamento de rede acelerado**: Embora esse recurso pode ser útil para atingir o limite de publicado, ele não altera o limite.

- **Destino do tráfego**: Todos os destinos contam para o limite de saída.

- **Protocolo**: Todo o tráfego de saída ao longo de todos os protocolos de conta para o limite.

Para obter mais informações, consulte [largura de banda de rede de Máquina Virtual](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Considerações de desempenho de Internet

Como discutido ao longo deste artigo, fatores na internet e fora do controlo do Azure podem afetar o desempenho de rede. Aqui estão alguns desses fatores:

- **Latência**: O tempo de ida e volta entre duas destinos pode ser afetado por problemas em redes intermediários, o tráfego que não usa o "menor" caminho de distância e caminhos do peering inferior ao ideal.

- **Perda de pacotes**: Perda de pacotes pode ser causada por congestionamento de rede, problemas de caminho físico e com baixo desempenho dispositivos de rede.

- **Tamanho MTU/fragmentação**: Fragmentação ao longo do caminho pode levar a atrasos na chegada de dados ou em pacotes que chegam fora de ordem, que podem afetar a entrega de pacotes.

O traceroute é uma boa ferramenta para medir as características de desempenho de rede (como perda de pacotes e latência) ao longo de cada caminho de rede entre um dispositivo de origem e de um dispositivo de destino.

### <a name="network-design-considerations"></a>Considerações de design de rede

Juntamente com as considerações abordadas anteriormente neste artigo, a topologia de uma rede virtual pode afetar o desempenho da rede. Por exemplo, um design de hub-and-spoke que backhauls tráfego globalmente a uma rede virtual único hub apresentará a latência de rede, que irá afetar o desempenho geral da rede.

O número de dispositivos de rede que o tráfego de rede passa pelo também pode afetar a latência geral. Por exemplo, num design de hub-and-spoke, se o tráfego passa por meio de uma aplicação virtual de rede do spoke e uma aplicação virtual de hub, antes de trânsito para a internet, dispositivos de rede virtual podem introduzir a latência.

### <a name="azure-regions-virtual-networks-and-latency"></a>Regiões do Azure, redes virtuais e de latência

Regiões do Azure são compostas de vários datacenters que existem dentro de uma área geográfica geral. Esses data Centers podem não estar fisicamente próximos uns dos outros. Em alguns casos estão separados por até 10 quilômetros. A rede virtual é uma lógica sobreposição por cima da rede física do datacenter do Azure. Uma rede virtual não implica qualquer topologia de rede específico no Centro de dados.

Por exemplo, duas VMs que estão na mesma rede virtual e sub-rede podem estar em diferentes, linhas ou até mesmo centros de dados. Poderia ser separados por pés de cabo de fibra óptica ou por quilômetros de cabo de fibra óptica. Essa variação pode introduzir latência variável (diferença de alguns milissegundos) entre VMs diferentes.

A colocação geográfica das VMs e a potencial latência resultante entre duas VMs, podem ser influenciados pela configuração de conjuntos de disponibilidade e zonas de disponibilidade. Mas a distância entre datacenters numa região é específico da região e principalmente influenciado por topologia de datacenter na região.

### <a name="source-nat-port-exhaustion"></a>Exaustão de porta NAT de origem

Uma implementação no Azure pode comunicar com pontos finais de fora do Azure na internet pública e/ou no espaço de IP público. Quando uma instância inicia uma conexão de saída, do Azure maps dinamicamente o endereço IP privado para um endereço IP público. Depois do Azure cria esse mapeamento, tráfego de retorno para o fluxo originado pela saída também pode acessar o endereço IP privado em que o fluxo teve origem.

Para cada ligação de saída, o Balanceador de carga do Azure tem de manter esse mapeamento para um determinado período de tempo. Com a natureza multi-inquilino do Azure, a manter esse mapeamento para cada fluxo de saída para cada VM pode ser utilizar muitos recursos. Portanto, existem limites que são definidas e com base na configuração da rede Virtual do Azure. Em alternativa, para dizer que mais precisamente, uma VM do Azure só pode fazer um determinado número de ligações de saída num determinado momento. Quando estes limites são atingidos, a VM não será capaz de ligações de saída mais.

Mas esse comportamento é configurável. Para obter mais informações sobre o SNAT e SNAT de porta esgotamento, consulte [este artigo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Medir o desempenho de rede no Azure

Um número dos valores máximos de desempenho neste artigo está relacionadas com a latência de rede / IDA e volta (RTT) de tempo entre duas VMs. Esta seção fornece algumas sugestões sobre como testar a latência/RTT e como testar o desempenho do TCP e o desempenho da rede VM. Pode otimizar e os valores de TCP/IP e de rede discutidos anteriormente, utilizando as técnicas descritas nesta secção do teste de desempenho. Pode conectar os valores de tamanho de latência, MTU, MSS e janela os cálculos fornecidos anteriormente e comparar valores máximos teóricos aos valores reais que observar durante os testes.

### <a name="measure-round-trip-time-and-packet-loss"></a>Tempo de ida e volta de medida e perda de pacotes

Desempenho do TCP depende muito de RTT e a perda de pacotes. O utilitário de PING disponível no Windows e Linux fornece a forma mais fácil de medir o RTT e perda de pacotes. A saída do PING mostrará a latência de máximo/mínimo/médio entre uma origem e de destino. Também mostrará a perda de pacotes. O PING utiliza o protocolo ICMP por predefinição. Pode utilizar o PsPing para testar o RTT de TCP. Para obter mais informações, consulte [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Débito real de medidas de uma conexão TCP

NTttcp é uma ferramenta para testar o desempenho de TCP de uma VM do Windows ou do Linux. Pode alterar várias definições de TCP e, em seguida, testar os benefícios com NTttcp. Para obter mais informações, consulte estes recursos:

- [Largura de banda/débito testes (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Utilitário de NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Largura de banda real do medidas de uma máquina virtual

Pode testar o desempenho de diferentes tipos de VM, accelerated networking e assim por diante, ao utilizar uma ferramenta chamada iPerf. iPerf também está disponível no Linux e Windows. iPerf pode utilizar o TCP ou UDP para testar o débito de rede global. testes de taxa de transferência TCP iPerf são influenciados por fatores discutidos neste artigo (como latência e RTT). Então, UDP poderá produzir resultados melhores, se pretender testar o débito máximo.

Para obter mais informações, veja estes artigos:

- [Resolução de problemas de desempenho de rede do Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [How to validate VPN throughput to a virtual network](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet) (Como validar o débito da VPN para uma rede virtual)

### <a name="detect-inefficient-tcp-behaviors"></a>Detetar comportamentos TCP ineficientes

Capturas de pacotes, os clientes do Azure podem ver os pacotes TCP com sinalizadores TCP (SACK, ACK duplicado, retransmissão e rápido RETRANSMITIR) que possam indicar problemas de desempenho de rede. Estes pacotes indicam especificamente ineficiências de rede que resultam de perda de pacotes. Mas a perda de pacotes não é necessariamente causada por problemas de desempenho do Azure. Problemas de desempenho podem ser o resultado de problemas de aplicativos, problemas de sistema operativo ou outros problemas que podem não estar diretamente relacionadas com a plataforma do Azure.

Além disso, tenha em atenção que alguns retransmissão e ACKs duplicados são normais numa rede. Protocolos TCP foram criados para ser confiável. Evidências destes pacotes TCP numa captura de pacotes de não indicam necessariamente um problema de rede sistêmicos, a menos que eles são excessivos.

Ainda assim, esses tipos de pacotes estão indicações de que a taxa de transferência não estiver a alcançar o desempenho máximo, por motivos discutidos em outras seções deste artigo.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu sobre a otimização de desempenho de TCP/IP para as VMs do Azure, pode querer ler sobre outras considerações sobre [Planear redes virtuais](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) ou [Saiba mais sobre como ligar e configurar redes virtuais ](https://docs.microsoft.com/azure/virtual-network/).
