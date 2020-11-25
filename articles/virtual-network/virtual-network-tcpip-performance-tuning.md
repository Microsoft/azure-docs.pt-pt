---
title: Afinação do desempenho da TCP/IP para VMs Azure / Microsoft Docs
description: Aprenda várias técnicas comuns de afinação de desempenho TCP/IP e sua relação com VMs Azure.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: 67b635f09cb9407279e89b5f7b8526dab3c08946
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017615"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Afinação do desempenho TCP/IP para VMs Azure

Este artigo discute técnicas comuns de afinação de desempenho TCP/IP e algumas coisas a ter em conta quando as utiliza para máquinas virtuais que funcionam no Azure. Fornecerá uma visão geral básica das técnicas e explorará como podem ser afinadas.

## <a name="common-tcpip-tuning-techniques"></a>Técnicas comuns de afinação TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, fragmentação e grande carga de envio

#### <a name="mtu"></a>MTU

A unidade de transmissão máxima (MTU) é a maior estrutura de tamanho (pacote), especificada em bytes, que pode ser enviada através de uma interface de rede. O MTU é um cenário configurável. O MTU predefinido usado em VMs Azure, e a definição padrão na maioria dos dispositivos de rede a nível global, é de 1.500 bytes.

#### <a name="fragmentation"></a>Fragmentação

A fragmentação ocorre quando um pacote é enviado que excede o MTU de uma interface de rede. A pilha TCP/IP quebrará o pacote em pedaços menores (fragmentos) que estejam em conformidade com o MTU da interface. A fragmentação ocorre na camada de IP e é independente do protocolo subjacente (como tCP). Quando um pacote de 2.000 bytes é enviado através de uma interface de rede com um MTU de 1.500, o pacote será dividido em um pacote de 1.500 bytes e um pacote de 500 bytes.

Os dispositivos de rede no caminho entre uma fonte e um destino podem largar pacotes que excedam o MTU ou fragmentar o pacote em pedaços menores.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>A parte "Don't Fragment" num pacote IP

A bit Don't Fragment (DF) é uma bandeira no cabeçalho do protocolo IP. O bit DF indica que os dispositivos de rede no caminho entre o remetente e o recetor não devem fragmentar o pacote. Esta parte pode ser definida por muitas razões. (Consulte a secção "Path MTU Discovery" deste artigo por exemplo.) Quando um dispositivo de rede recebe um pacote com o conjunto de bits Don't Fragment, e esse pacote excede a interface do dispositivo MTU, o comportamento padrão é que o dispositivo deixe cair o pacote. O dispositivo envia uma mensagem necessária à fragmentação do ICMP para a fonte original do pacote.

#### <a name="performance-implications-of-fragmentation"></a>Implicações de desempenho da fragmentação

A fragmentação pode ter implicações negativas no desempenho. Uma das principais razões para o efeito no desempenho é o impacto CPU/memória da fragmentação e remontagem dos pacotes. Quando um dispositivo de rede precisa de fragmentar um pacote, terá de alocar recursos cpu/memória para efetuar a fragmentação.

O mesmo acontece quando o pacote é remontado. O dispositivo de rede tem de armazenar todos os fragmentos até serem recebidos para que possa montá-los no pacote original. Este processo de fragmentação e remontagem também pode causar latência.

A outra possível implicação negativa do desempenho da fragmentação é que os pacotes fragmentados podem chegar fora de ordem. Quando os pacotes são recebidos fora de ordem, alguns tipos de dispositivos de rede podem deixá-los cair. Quando isso acontece, todo o pacote tem de ser retransmitido.

Os fragmentos são normalmente largados por dispositivos de segurança como firewalls de rede ou quando os amortecedores de receção de um dispositivo de rede estão esgotados. Quando os amortecedores de receção de um dispositivo de rede estão esgotados, um dispositivo de rede está a tentar remontar um pacote fragmentado, mas não tem os recursos para armazenar e reassumar o pacote.

A fragmentação pode ser vista como uma operação negativa, mas o suporte à fragmentação é necessário quando se conectam diversas redes através da internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Benefícios e consequências da modificação do MTU

De um modo geral, pode criar uma rede mais eficiente aumentando a MTU. Todos os pacotes que são transmitidos têm informações de cabeçalho que são adicionadas ao pacote original. Quando a fragmentação cria mais pacotes, há mais cabeçalho, e isso torna a rede menos eficiente.

Eis um exemplo. O tamanho do cabeçalho Ethernet é de 14 bytes mais uma sequência de verificação de quadro de 4 bytes para garantir a consistência do quadro. Se um pacote de 2.000 bytes for enviado, 18 bytes de ethernet são adicionados na rede. Se o pacote for fragmentado num pacote de 1.500 bytes e num pacote de 500 bytes, cada pacote terá 18 bytes de cabeçalho Ethernet, num total de 36 bytes.

Tenha em mente que o aumento da MTU não criará necessariamente uma rede mais eficiente. Se uma aplicação enviar apenas 500 bytes, a mesma sobrecarga de cabeçada existirá se o MTU é de 1.500 bytes ou 9.000 bytes. A rede só se tornará mais eficiente se utilizar tamanhos de pacote maiores que são afetados pela MTU.

#### <a name="azure-and-vm-mtu"></a>Azure e VM MTU

O MTU predefinido para VMs Azure é de 1.500 bytes. A pilha de Rede Virtual Azure tentará fragmentar um pacote a 1.400 bytes.

Note que a pilha de Rede Virtual não é inerentemente ineficiente porque fragmenta pacotes a 1.400 bytes, mesmo que os VMs tenham um MTU de 1.500. Uma grande percentagem de pacotes de rede é muito menor do que 1.400 ou 1.500 bytes.

#### <a name="azure-and-fragmentation"></a>Azul e fragmentação

A pilha de rede virtual está configurada para deixar cair "fragmentos fora de ordem", isto é, pacotes fragmentados que não chegam na sua ordem fragmentada original. Estes pacotes são retirados principalmente devido a uma vulnerabilidade de segurança de rede anunciada em novembro de 2018 chamada FragmentSmack.

FragmentSmack é um defeito na forma como o kernel Linux manuseou a remontagem dos pacotes IPv4 e IPv6 fragmentados. Um intruso remoto poderia usar esta falha para desencadear operações de remontagem de fragmentos dispendiosos, o que poderia levar a um aumento do CPU e a uma negação de serviço no sistema alvo.

#### <a name="tune-the-mtu"></a>Sintonize o MTU

Pode configurar um Azure VM MTU, como pode em qualquer outro sistema operativo. Mas deve considerar a fragmentação que ocorre em Azure, descrito acima, quando está a configurar um MTU.

Não encorajamos os clientes a aumentar os MTUs VM. Esta discussão destina-se a explicar os detalhes de como o Azure implementa a MTU e realiza a fragmentação.

> [!IMPORTANT]
>O aumento da MTU não é conhecido por melhorar o desempenho e pode ter um efeito negativo no desempenho da aplicação.
>
>

#### <a name="large-send-offload"></a>Grande descarregamento de envio

O descarregamento de envio grande (LSO) pode melhorar o desempenho da rede descarregando a segmentação de pacotes para o adaptador Ethernet. Quando o LSO está ativado, a pilha TCP/IP cria um grande pacote TCP e envia-o para o adaptador Ethernet para segmentação antes de o encaminhar. O benefício do LSO é que pode libertar o CPU de segmentar pacotes em tamanhos que estejam em conformidade com o MTU e descarregar esse processamento para a interface Ethernet onde é realizado em hardware. Para saber mais sobre os benefícios do LSO, consulte [o carregamento de envio de grandes suportes.](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)

Quando o LSO está ativado, os clientes do Azure podem ver grandes tamanhos de moldura quando realizam capturas de pacotes. Estes grandes tamanhos de moldura podem levar alguns clientes a pensar que a fragmentação está a ocorrer ou que um grande MTU está a ser usado quando não está. Com lSO, o adaptador Ethernet pode anunciar um tamanho máximo de segmento (MSS) maior para a pilha TCP/IP para criar um pacote TCP maior. Todo este quadro não segmentado é então encaminhado para o adaptador Ethernet e seria visível numa captura de pacote realizada no VM. Mas o pacote será dividido em muitos quadros menores pelo adaptador Ethernet, de acordo com o MTU do adaptador Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Escala de janela sSC MSS e PMTUD

#### <a name="tcp-maximum-segment-size"></a>Tamanho máximo do segmento TCP

O tamanho máximo do segmento TCP (MSS) é um cenário que limita o tamanho dos segmentos TCP, o que evita a fragmentação dos pacotes TCP. Os sistemas operativos normalmente utilizam esta fórmula para definir MSS:

`MSS = MTU - (IP header size + TCP header size)`

O cabeçalho IP e o cabeçalho TCP são 20 bytes cada, ou 40 bytes no total. Assim, uma interface com um MTU de 1.500 terá um MSS de 1.460. Mas o MSS é configurável.

Esta definição é acordada no aperto de mão tCP de três vias quando uma sessão de TCP é configurada entre uma fonte e um destino. Ambos os lados enviam um valor MSS, e a parte inferior dos dois é usada para a ligação TCP.

Tenha em mente que os MTUs da fonte e destino não são os únicos fatores que determinam o valor mss. Dispositivos de rede intermediários, como gateways VPN, incluindo O Gateway Azure VPN, podem ajustar o MTU independentemente da fonte e destino para garantir o melhor desempenho da rede.

#### <a name="path-mtu-discovery"></a>Descoberta do Caminho MTU

O MSS é negociado, mas pode não indicar o MSS real que pode ser usado. Isto porque outros dispositivos de rede no caminho entre a fonte e o destino podem ter um valor MTU inferior à fonte e destino. Neste caso, o dispositivo cujo MTU é menor do que o pacote deixará cair o pacote. O dispositivo enviará de volta uma mensagem de fragmentação necessária (Tipo 3, Código 4) que contém a sua MTU. Esta mensagem ICMP permite ao hospedeiro de origem reduzir adequadamente o seu Caminho MTU. O processo chama-se Path MTU Discovery (PMTUD).

O processo PMTUD é ineficiente e afeta o desempenho da rede. Quando os pacotes são enviados que excedem o MTU de um caminho de rede, os pacotes precisam de ser retransmitidos com um MSS mais baixo. Se o remetente não receber a mensagem necessária para a fragmentação do ICMP, talvez por causa de uma firewall de rede no caminho (vulgarmente designada por *buraco negro PMTUD),* o remetente não sabe que precisa de baixar o MSS e retransmitê-lo-á continuamente. É por isso que não recomendamos o aumento do Azure VM MTU.

#### <a name="vpn-and-mtu"></a>VPN e MTU

Se utilizar VMs que executam encapsulamento (como VPNs IPsec), existem algumas considerações adicionais em relação ao tamanho do pacote e MTU. As VPNs adicionam mais cabeçalhos aos pacotes, o que aumenta o tamanho do pacote e requer um MSS menor.

Para o Azure, recomendamos que coloque o GSSS A fixação de 1.350 bytes e interface de túnel MTU para 1.400. Para obter mais informações, consulte os [dispositivos VPN e a página de parâmetros IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latência, tempo de ida e volta e escala de janelas TCP

#### <a name="latency-and-round-trip-time"></a>Tempo de latência e ida e volta

A latência da rede é regida pela velocidade da luz sobre uma rede de fibra ótica. O rendimento da rede de TCP também é efetivamente regido pelo tempo de ida e volta (RTT) entre dois dispositivos de rede.

| Rota | Distância | Tempo de ida | RTT |
| ----- | -------- | ------------ | --- |
|Nova Iorque para São Francisco|4.148 km|21 ms|42 ms|
|Nova Iorque para Londres|5.585 km|28 ms|56 ms|
|Nova Iorque para Sydney|15.993 km|80 ms|160 ms|

Esta tabela mostra a distância em linha reta entre dois locais. Nas redes, a distância é tipicamente maior do que a distância de linha reta. Aqui está uma fórmula simples para calcular o mínimo de RTT como regido pela velocidade da luz:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Pode usar 200 para a velocidade de propagação. Esta é a distância, em quilómetros, que a luz viaja em 1 milissegundo.

Vamos levar Nova Iorque a São Francisco como exemplo. A distância em linha reta é de 4.148 km. Ligando esse valor à equação, obtemos o seguinte:

`Minimum RTT = 2 * (4,148 / 200)`

A saída da equação está em milissegundos.

Se quiser obter o melhor desempenho da rede, a opção lógica é selecionar destinos com a distância mais curta entre eles. Também deve projetar a sua rede virtual para otimizar o percurso de tráfego e reduzir a latência. Para mais informações, consulte a secção "Considerações de design de rede" deste artigo.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Efeitos de tempo de latência e ida e volta na TCP

O tempo de ida e volta tem um efeito direto na produção máxima de TCP. No protocolo TCP, o tamanho da *janela* é a quantidade máxima de tráfego que pode ser enviada sobre uma ligação TCP antes que o remetente precise de receber o aviso do recetor. Se o MSS TCP estiver definido para 1.460 e o tamanho da janela TCP estiver definido para 65.535, o remetente pode enviar 45 pacotes antes de ter de receber o aviso do recetor. Se o remetente não receber reconhecimento, retransmite os dados. A fórmula é esta:

`TCP window size / TCP MSS = packets sent`

Neste exemplo, 65.535 / 1.460 é arredondado até 45.

Este estado de "espera de reconhecimento", um mecanismo para garantir a entrega fiável de dados, é o que faz com que o RTT afete o rendimento da TCP. Quanto mais tempo o remetente esperar pelo reconhecimento, mais tempo precisa de esperar para enviar mais dados.

Aqui está a fórmula para calcular a produção máxima de uma única ligação TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Esta tabela mostra o limite máximo de megabytes/por segundo de uma única ligação TCP. (Para a legibilidade, os megabytes são utilizados para a unidade de medida.)

| Tamanho da janela TCP (bytes) | Latência RTT (ms) | Megabyte máximo/segundo rendimento | Megabit máximo/segundo rendimento |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

Se os pacotes forem perdidos, a produção máxima de uma ligação TCP será reduzida enquanto o remetente retransmit os dados que já enviou.

#### <a name="tcp-window-scaling"></a>Escala de janela de TCP

O dimensionamento da janela TCP é uma técnica que aumenta dinamicamente o tamanho da janela TCP para permitir o envio de mais dados antes de ser necessário um reconhecimento. No exemplo anterior, 45 pacotes seriam enviados antes de ser necessário um reconhecimento. Se aumentar o número de pacotes que podem ser enviados antes de ser necessário um reconhecimento, está a reduzir o número de vezes que um remetente está à espera de reconhecimento, o que aumenta a produção máxima de TCP.

Esta tabela ilustra essas relações:

| Tamanho da janela TCP (bytes) | Latência RTT (ms) | Megabyte máximo/segundo rendimento | Megabit máximo/segundo rendimento |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Mas o valor do cabeçalho TCP para o tamanho da janela TCP tem apenas 2 bytes de comprimento, o que significa que o valor máximo para uma janela de receção é de 65.535. Para aumentar o tamanho máximo da janela, foi introduzido um fator de escala de janela TCP.

O fator de escala é também uma definição que pode configurar num sistema operativo. Aqui está a fórmula para calcular o tamanho da janela TCP usando fatores de escala:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Aqui está o cálculo para um fator de escala de janela de 3 e um tamanho de janela de 65.535:

`65,535 \* (2^3) = 262,140 bytes`

Um fator de escala de 14 resulta num tamanho de janela TCP de 14 (o offset máximo permitido). O tamanho da janela TCP será de 1.073.725.440 bytes (8,5 gigabits).

#### <a name="support-for-tcp-window-scaling"></a>Suporte para escala de janelas TCP

O Windows pode definir diferentes fatores de escala para diferentes tipos de ligação. (As classes de ligações incluem datacenter, internet, e assim por diante.) Utilize o `Get-NetTCPConnection` comando PowerShell para visualizar o tipo de ligação de escala da janela:

```powershell
Get-NetTCPConnection
```

Pode utilizar o `Get-NetTCPSetting` comando PowerShell para visualizar os valores de cada classe:

```powershell
Get-NetTCPSetting
```

Pode definir o tamanho inicial da janela TCP e o fator de escala TCP no Windows utilizando o `Set-NetTCPSetting` comando PowerShell. Para obter mais informações, consulte  [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Estas são as definições eficazes de TCP `AutoTuningLevel` para:

| AutoTuningLevel | Fator de escala | Multiplicador de escalas | Fórmula para<br/>calcular o tamanho máximo da janela |
| --------------- | -------------- | ------------------ | -------------------------------------------- |
|Desativado|Nenhum|Nenhum|Tamanho da janela|
|Restrito|4|2^4|Tamanho da janela * (2^4)|
|Altamente restrito|2|2^2|Tamanho da janela * (2^2)|
|Normal|8|2^8|Tamanho da janela * (2^8)|
|Experimental|14|2^14|Tamanho da janela * (2^14)|

Estas configurações são as mais prováveis de afetar o desempenho da TCP, mas tenha em mente que muitos outros fatores em toda a internet, fora do controlo do Azure, também podem afetar o desempenho do TCP.

#### <a name="increase-mtu-size"></a>Aumentar o tamanho mtu

Como um MTU maior significa um MSS maior, pode perguntar-se se o aumento do MTU pode aumentar o desempenho da TCP. Provavelmente não. Há prós e contras para o tamanho do pacote para além do tráfego TCP. Como discutido anteriormente, os fatores mais importantes que afetam o desempenho da produção TCP são o tamanho da janela TCP, a perda de pacotes e o RTT.

> [!IMPORTANT]
> Não recomendamos que os clientes Azure alterem o valor padrão da MTU em máquinas virtuais.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Rede acelerada e receber escala lateral

#### <a name="accelerated-networking"></a>Redes aceleradas

As funções de rede de máquinas virtuais têm sido historicamente intensivas no CPU tanto no VM convidado como no hipervisor/hospedeiro. Cada pacote que transite através do hospedeiro é processado em software pelo CPU anfitrião, incluindo todo o encapsulamento e decapsulação da rede virtual. Quanto mais tráfego passa pelo hospedeiro, maior é a carga do CPU. E se o CPU anfitrião estiver ocupado com outras operações, isso também afetará o rendimento da rede e a latência. O Azure aborda esta questão com uma rede acelerada.

A rede acelerada proporciona uma latência de rede ultraleve consistente através do hardware programável interno da Azure e tecnologias como a SR-IOV. A rede acelerada move grande parte da pilha de rede definida pelo software Azure para fora dos CPUs e para smartNICs baseados na FPGA. Esta alteração permite que as aplicações do utilizador final recuperem ciclos de cálculo, o que coloca menos carga no VM, diminuindo o nervosismo e a inconsistência na latência. Por outras palavras, o desempenho pode ser mais determinístico.

A rede acelerada melhora o desempenho, permitindo ao VM do hóspede contornar o hospedeiro e estabelecer um caminho de dados diretamente com o SmartNIC de um anfitrião. Aqui estão alguns benefícios da rede acelerada:

- **Menor latência / pacotes mais altos por segundo (pps)**: Remover o interruptor virtual do datapath elimina o tempo que os pacotes de tempo passam no hospedeiro para processamento de políticas e aumenta o número de pacotes que podem ser processados no VM.

- **Nervosismo reduzido**: O processamento do interruptor virtual depende da quantidade de política que precisa de ser aplicada e da carga de trabalho do CPU que está a fazer o processamento. O descarregamento da aplicação da política para o hardware remove essa variabilidade entregando pacotes diretamente ao VM, eliminando a comunicação host-to-VM e todas as interrupções de software e interruptores de contexto.

- **Utilização reduzida do CPU**: Contornar o interruptor virtual no hospedeiro leva a uma menor utilização do CPU para o tráfego da rede de processamento.

Para utilizar a rede acelerada, é necessário ativer explicitamente em cada VM aplicável. Consulte [Criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para obter instruções.

#### <a name="receive-side-scaling"></a>Receber escalagem lateral

Receber o dimensionamento lateral (RSS) é uma tecnologia de motor de rede que distribui o serviço de tráfego de rede de forma mais eficiente, distribuindo o processamento de receber em vários CPUs num sistema multiprocessador. Em termos simples, o RSS permite que um sistema processe o tráfego mais recebido porque utiliza todos os CPUs disponíveis em vez de apenas um. Para uma discussão mais técnica sobre o RSS, consulte [Introdução para receber a escala lateral.](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling)

Para obter o melhor desempenho quando a rede acelerada está ativada num VM, é necessário ativar o RSS. O RSS também pode proporcionar benefícios em VMs que não usam rede acelerada. Para uma visão geral de como determinar se o RSS está ativado e como o habilitar, consulte [a produção de rede Otimize para máquinas virtuais Azure](https://aka.ms/FastVM).

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCP TIME_WAIT e TIME_WAIT assassinato

O TIME_WAIT TCP é outro cenário comum que afeta o desempenho da rede e da aplicação. Em VMs ocupados que estão abrindo e fechando muitas tomadas, quer como clientes, quer como servidores (Fonte IP:Source Port + Destination IP:Destination Port), durante o funcionamento normal do TCP, uma tomada pode acabar num estado de TIME_WAIT por muito tempo. O estado TIME_WAIT destina-se a permitir que quaisquer dados adicionais sejam entregues numa tomada antes de o fechar. Assim, as pilhas TCP/IP geralmente impedem a reutilização de uma tomada deixando cair silenciosamente o pacote TCP SYN do cliente.

O tempo que uma tomada está em TIME_WAIT é configurável. Pode variar de 30 segundos a 240 segundos. As tomadas são um recurso finito, e o número de tomadas que podem ser utilizadas a qualquer momento é configurável. (O número de tomadas disponíveis é tipicamente de cerca de 30.000.) Se as tomadas disponíveis forem consumidas, ou se os clientes e servidores tiverem configurações TIME_WAIT desajustadas, e um VM tentar reutilizar uma tomada num estado TIME_WAIT, novas ligações falharão à medida que os pacotes TCP SYN são silenciosamente largados.

O valor para a gama de portas para tomadas de saída é geralmente configurável dentro da pilha TCP/IP de um sistema operativo. O mesmo se aplica às definições de TIME_WAIT TCP e à reutilização da tomada. A alteração destes números pode potencialmente melhorar a escalabilidade. Mas, dependendo da situação, estas alterações podem causar problemas de interoperabilidade. Deve ter cuidado se alterar estes valores.

Pode usar TIME_WAIT assassinato para resolver esta limitação de escala. TIME_WAIT assassinato permite que uma tomada seja reutilizada em determinadas situações, como quando o número de sequência no pacote IP da nova ligação excede o número de sequência do último pacote da ligação anterior. Neste caso, o sistema operativo permitirá estabelecer a nova ligação (aceitará o novo SYN/ACK) e forçará o encerramento da ligação anterior que se encontrava num estado TIME_WAIT. Esta capacidade é suportada em VMs do Windows em Azure. Para saber mais sobre o suporte em outros VMs, consulte o fornecedor de OS.

Para saber mais sobre a configuração das definições de TIME_WAIT TCP e a gama de portas de origem, consulte [Definições que podem ser modificadas para melhorar o desempenho da rede](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Fatores de rede virtuais que podem afetar o desempenho

### <a name="vm-maximum-outbound-throughput"></a>Entrada máxima de saída VM

O Azure fornece uma variedade de tamanhos e tipos VM, cada um com uma mistura diferente de capacidades de desempenho. Uma dessas capacidades é a produção de rede (ou largura de banda), que é medida em megabits por segundo (Mbps). Como as máquinas virtuais estão hospedadas em hardware partilhado, a capacidade de rede precisa de ser partilhada de forma justa entre as máquinas virtuais que usam o mesmo hardware. Máquinas virtuais maiores são atribuídas mais largura de banda do que máquinas virtuais menores.

A largura de banda de rede atribuída a cada máquina virtual é medido no tráfego de saída da máquina virtual. Todo o tráfego de rede que sai da máquina virtual é contado para o limite atribuído, independentemente do destino. Por exemplo, se uma máquina virtual tiver um limite de 1.000-Mbps, esse limite aplica-se se o tráfego de saída está destinado a outra máquina virtual na mesma rede virtual ou fora de Azure.

A entrada não é medido ou limitado diretamente. Mas há outros fatores, como CPU e limites de armazenamento, que podem afetar a capacidade de uma máquina virtual de processar dados de entrada.

A rede acelerada é projetada para melhorar o desempenho da rede, incluindo a latência, a produção e a utilização do CPU. A rede acelerada pode melhorar a produção de uma máquina virtual, mas só pode fazê-lo até à largura de banda atribuída pela máquina virtual.

As máquinas virtuais Azure têm pelo menos uma interface de rede ligada a elas. Podem ter vários. A largura de banda atribuída a uma máquina virtual é a soma de todo o tráfego de saída em todas as interfaces de rede ligadas à máquina. Por outras palavras, a largura de banda é atribuída numa base de máquina por virtual, independentemente de quantas interfaces de rede estão ligadas à máquina.

O rendimento de saída esperado e o número de interfaces de rede suportadas por cada tamanho VM são detalhados em [Tamanhos para máquinas virtuais Windows em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Para ver a produção máxima, selecione um tipo, como **propósito geral,** e, em seguida, encontre a secção sobre a série de tamanhos na página resultante (por exemplo, "Série Dv2"). Para cada série, há uma tabela que fornece especificações de networking na última coluna, que é intitulada "Max NICs / Largura de banda esperada (Mbps)".

O limite de produção aplica-se à máquina virtual. A produção não é afetada por estes fatores:

- **Número de interfaces de rede**: O limite de largura de banda aplica-se à soma de todo o tráfego de saída da máquina virtual.

- **Rede acelerada**: Embora esta funcionalidade possa ser útil para alcançar o limite publicado, não altera o limite.

- **Destino de tráfego**: Todos os destinos contam para o limite de saída.

- **Protocolo**: Todo o tráfego de saída em todos os protocolos conta para o limite.

Para obter mais informações, consulte [a largura de banda da rede de máquinas virtuais.](https://aka.ms/AzureBandwidth)

### <a name="internet-performance-considerations"></a>Considerações de desempenho na Internet

Como discutido ao longo deste artigo, os fatores na internet e fora do controlo do Azure podem afetar o desempenho da rede. Eis alguns desses fatores:

- **Latência**: O tempo de ida e volta entre dois destinos pode ser afetado por problemas nas redes intermédias, pelo tráfego que não toma o caminho de distância "mais curto" e por caminhos de observação sub-ideais.

- **Perda de pacote:** A perda de pacote pode ser causada por congestionamento de rede, problemas de trajetória física e dispositivos de rede com fraco desempenho.

- **Tamanho/Fragmentação MTU**: A fragmentação ao longo do caminho pode levar a atrasos na chegada de dados ou em pacotes que chegam fora de encomenda, o que pode afetar a entrega de pacotes.

A Traceroute é uma boa ferramenta para medir as características de desempenho da rede (como perda de pacote e latência) ao longo de cada caminho de rede entre um dispositivo de origem e um dispositivo de destino.

### <a name="network-design-considerations"></a>Considerações de design de rede

Juntamente com as considerações discutidas anteriormente neste artigo, a topologia de uma rede virtual pode afetar o desempenho da rede. Por exemplo, um design de hub-and-spoke que retrocífa o tráfego globalmente para uma rede virtual de um único hub introduzirá a latência da rede, o que afetará o desempenho global da rede.

O número de dispositivos de rede por onde passa o tráfego de rede também pode afetar a latência global. Por exemplo, num design de hub-and-spoke, se o tráfego passar através de um aparelho virtual de rede falada e de um aparelho virtual hub antes de transitar para a internet, os aparelhos virtuais da rede podem introduzir latência.

### <a name="azure-regions-virtual-networks-and-latency"></a>Regiões azure, redes virtuais e latência

As regiões azure são compostas por múltiplos centros de dados que existem dentro de uma área geográfica geral. Estes centros de dados podem não estar fisicamente ao lado um do outro. Em alguns casos, estão separados por até 10 km. A rede virtual é uma sobreposição lógica no topo da rede de datacenter físico Azure. Uma rede virtual não implica nenhuma topologia específica da rede dentro do datacenter.

Por exemplo, dois VMs que estão na mesma rede virtual e sub-rede podem estar em diferentes racks, linhas ou até mesmo centros de dados. Podem ser separados por pés de cabo de fibra ótica ou por quilómetros de cabo de fibra ótica. Esta variação poderia introduzir latência variável (alguns milissegundos diferença) entre diferentes VMs.

A colocação geográfica de VMs, e a potencial latência resultante entre dois VMs, podem ser influenciados pela configuração de conjuntos de disponibilidade e zonas de disponibilidade. Mas a distância entre centros de dados numa região é específica da região e é essencialmente influenciada pela topologia do datacenter na região.

### <a name="source-nat-port-exhaustion"></a>Exaustão da porta SOURCE NAT

Uma implantação em Azure pode comunicar com pontos finais fora de Azure na internet pública e/ou no espaço IP público. Quando uma instância inicia uma ligação de saída, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Depois de Azure criar este mapeamento, o tráfego de retorno para o fluxo originado de saída também pode chegar ao endereço IP privado de onde o fluxo originou.

Para cada ligação de saída, o Balançador de Carga Azure necessita de manter este mapeamento durante algum tempo. Com a natureza multitenant de Azure, manter este mapeamento para cada fluxo de saída para cada VM pode ser intensivo de recursos. Assim, existem limites definidos e baseados na configuração da Rede Virtual Azure. Ou, para dizer que, mais precisamente, um VM Azure só pode fazer um certo número de ligações de saída num dado momento. Quando estes limites forem atingidos, o VM não será capaz de fazer mais ligações de saída.

Mas este comportamento é configurável. Para obter mais informações sobre a exaustão do porto SNAT e SNAT, consulte [este artigo.](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)

## <a name="measure-network-performance-on-azure"></a>Medir o desempenho da rede em Azure

Alguns dos máximos de desempenho deste artigo estão relacionados com a latência /tempo de ida e volta da rede (RTT) entre dois VMs. Esta secção fornece algumas sugestões para como testar a latência/RTT e como testar o desempenho da TCP e o desempenho da rede VM. Pode sintonizar e testar os valores de TCP/IP e de rede discutidos anteriormente utilizando as técnicas descritas nesta secção. Pode ligar os valores de latência, MTU, MSS e tamanho da janela nos cálculos fornecidos anteriormente e comparar máximos teóricos com valores reais que observa durante os testes.

### <a name="measure-round-trip-time-and-packet-loss"></a>Meça o tempo de ida e volta e a perda de pacotes

O desempenho da TCP depende fortemente de RTT e perda de pacotes. O utilitário PING disponível no Windows e Linux fornece a forma mais fácil de medir a perda de RTT e pacote. A produção de PING mostrará a latência mínima/máxima/média entre uma fonte e um destino. Também mostrará perda de pacotes. Ping utiliza o protocolo ICMP por padrão. Pode utilizar o PsPing para testar o TCP RTT. Para mais informações, consulte [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Medir a produção real de uma ligação TCP

NTttcp é uma ferramenta para testar o desempenho TCP de um Linux ou Windows VM. Pode alterar várias definições de TCP e, em seguida, testar os benefícios utilizando o NTttcp. Para mais informações, consulte estes recursos:

- [Testes de largura de banda/produção (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Utilitário NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Medir a largura de banda real de uma máquina virtual

Pode testar o desempenho de diferentes tipos de VM, networking acelerado, e assim por diante, utilizando uma ferramenta chamada iPerf. O iPerf também está disponível no Linux e Windows. O iPerf pode usar TCP ou UDP para testar a produção global da rede. Os testes de produção da IPerf TCP são influenciados pelos fatores discutidos neste artigo (como a latência e o RTT). Assim, a UDP pode produzir melhores resultados se quiser testar o máximo de produção.

Para obter mais informações, veja estes artigos:

- [Resolução de problemas Desempenho da rede Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [How to validate VPN throughput to a virtual network](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet) (Como validar o débito da VPN para uma rede virtual)

### <a name="detect-inefficient-tcp-behaviors"></a>Detetar comportamentos de TCP ineficientes

Nas capturas de pacotes, os clientes da Azure podem ver pacotes de TCP com bandeiras TCP (SACK, DUP ACK, RETRANSMIT e FAST RETRANSMIT) que possam indicar problemas de desempenho da rede. Estes pacotes indicam especificamente ineficiências de rede que resultam da perda de pacotes. Mas a perda de pacotes não é necessariamente causada por problemas de desempenho do Azure. Problemas de desempenho podem ser o resultado de problemas de aplicação, problemas do sistema operativo ou outros problemas que podem não estar diretamente relacionados com a plataforma Azure.

Além disso, tenha em mente que alguma retransmissão e ACKs duplicados são normais numa rede. Os protocolos da TCP foram construídos para serem fiáveis. As provas destes pacotes de TCP numa captura de pacotes não indicam necessariamente um problema de rede sistémica, a não ser que sejam excessivos.

Ainda assim, estes tipos de pacotes são indicações de que a produção da TCP não está a atingir o seu desempenho máximo, por razões discutidas noutras secções deste artigo.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre a afinação de desempenho da TCP/IP para VMs Azure, talvez queira ler sobre outras considerações para [planear redes virtuais](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) ou aprender mais sobre a [ligação e configuração de redes virtuais.](https://docs.microsoft.com/azure/virtual-network/)
