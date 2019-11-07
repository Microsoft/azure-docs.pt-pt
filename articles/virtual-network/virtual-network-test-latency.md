---
title: Testar a latência de rede de máquinas virtuais do Azure em uma rede virtual do Azure | Microsoft Docs
description: Saiba como testar a latência de rede entre as máquinas virtuais do Azure em uma rede virtual
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 760a181b4459db28d3a6eee5022cc0f984c4bee0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588281"
---
# <a name="testing-network-latency"></a>Testando a latência de rede

Medir a latência de rede com uma ferramenta projetada para a tarefa fornecerá os resultados mais precisos. Ferramentas disponíveis publicamente como SockPerf (para Linux) e expresso (para Windows) são exemplos de ferramentas que podem isolar e medir a latência de rede, excluindo outros tipos de latência, como a latência do aplicativo. Essas ferramentas concentram-se no tipo de tráfego de rede que afeta o desempenho do aplicativo, ou seja, TCP e UDP. Outras ferramentas de conectividade comuns, como ping, às vezes podem ser usadas para medir a latência, mas esses resultados podem não ser representativos do tráfego de rede usado em cargas de trabalho reais. Isso porque a maioria dessas ferramentas emprega o protocolo ICMP, que pode ser tratado diferentemente do tráfego do aplicativo, e os resultados podem não ser aplicáveis a cargas de trabalho que usam TCP e UDP. Para um teste preciso de latência de rede dos protocolos usados pela maioria dos aplicativos, SockPerf (para Linux) e expresso (para Windows) produzem os resultados mais relevantes. Este documento aborda essas duas ferramentas.

## <a name="overview"></a>Descrição geral

Usando duas VMs, uma como remetente e outra como receptor, um canal de comunicação bidirecional é criado para enviar e receber pacotes em ambas as direções para medir o tempo de ida e volta (RTT).

Essas etapas podem ser usadas para medir a latência de rede entre duas VMs (máquinas virtuais) ou até mesmo entre dois computadores físicos. As medidas de latência podem ser úteis para os seguintes cenários:

- Estabelecer um parâmetro de comparação para a latência de rede entre as VMs implantadas
- Compare os efeitos das alterações na latência de rede depois que as alterações relacionadas forem feitas em:
  - Software de pilha de rede ou sistema operacional, incluindo alterações de configuração
  - Método de implantação de VM, como a implantação em uma zona ou PPG
  - Propriedades da VM, como rede acelerada ou alterações de tamanho
  - Rede virtual, como roteamento ou filtragem de alterações

### <a name="tools-for-testing"></a>Ferramentas para teste
Para medir a latência, temos duas opções diferentes, uma para sistemas baseados no Windows e outra para sistemas baseados em Linux

Para Windows: expresso. exe (Windows) [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Para Linux: SockPerf (Linux) [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

O uso dessas ferramentas garante que apenas os tempos de entrega de carga TCP ou UDP sejam medidos e não ICMP (ping) ou outros tipos de pacotes que não são usados por aplicativos e não afetam seu desempenho.

### <a name="tips-for-an-optimal-vm-configuration"></a>Dicas para uma configuração de VM ideal:

- Usar a versão mais recente do Windows ou Linux
- Habilitar a rede acelerada para obter melhores resultados
- Implantar VMs com o [grupo de posicionamento de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
- VMs maiores geralmente têm um desempenho melhor do que as VMs menores

### <a name="tips-for-analysis"></a>Dicas para análise

- Estabeleça uma linha de base antecipadamente, assim que a implantação, a configuração e as otimizações forem concluídas
- Sempre Compare novos resultados a uma linha de base ou de outra forma de um teste para outro com alterações controladas
- Repetir testes sempre que as alterações forem observadas ou planejadas


## <a name="testing-vms-running-windows"></a>Testando VMs executando o Windows

## <a name="get-latteexe-onto-the-vms"></a>Obter o expresso. exe nas VMs

Baixe a versão mais recente: [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Considere colocar expresso. exe em uma pasta separada, como c:\Tools

## <a name="allow-latteexe-through-the-windows-firewall"></a>Permitir expresso. exe por meio do firewall do Windows

No receptor, crie uma regra de permissão no firewall do Windows para permitir que o tráfego expresso. exe chegue. É mais fácil permitir todo o programa expresso. exe por nome em vez de permitir portas TCP específicas de entrada.

Permita o expresso. exe por meio do firewall do Windows da seguinte maneira:

netsh advfirewall firewall Adicionar regra programa =\<caminho\>\\expresso. exe nome =&quot;expresso&quot; protocolo = qualquer dir = in ação = permitir habilitar = Sim perfil = qualquer


Por exemplo, se você copiou expresso. exe para a pasta &quot;c:\\Tools&quot;, esse seria o comando:

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>Executando testes de latência

Inicie o expresso. exe no receptor (execute do CMD, não do PowerShell):

expresso-um&gt;de endereço IP do destinatário &lt;:&lt;&gt; de portas-i &lt;iterações&gt;

Ao contrário das iterações do 65 mil é longa o suficiente para retornar resultados representativos.

Qualquer número de porta disponível está correto.

Se a VM tiver um endereço IP de 10.0.0.4, ela ficaria assim:

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

Inicie o expresso. exe no remetente (execute do CMD, não do PowerShell):

expresso-c-a \<\>de endereço IP do destinatário:\<\> de portas-i \<iterações\>


O comando resultante é o mesmo que no receptor, exceto com a adição de &quot;-c&quot; para indicar que este é o &quot;cliente&quot; ou o remetente:

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

Aguarde os resultados. Dependendo da distância entre as VMs, pode levar alguns minutos para ser concluída. Considere iniciar com menos iterações para testar o sucesso antes de executar testes mais longos.



## <a name="testing-vms-running-linux"></a>Testando VMs executando Linux

Use SockPerf. Ele está disponível em [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

### <a name="install-sockperf-on-the-vms"></a>Instalar o SockPerf nas VMs

Nas VMs do Linux (remetente e destinatário), execute estes comandos para preparar o SockPerf em suas VMs. São fornecidos comandos para o distribuições principal.

#### <a name="for-rhel--centos-follow-these-steps"></a>Para RHEL/CentOS, siga estas etapas:
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>Para o Ubuntu, siga estas etapas:
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>Para todos os distribuições, copie, compile e instale o SockPerf de acordo com as seguintes etapas:
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Executar SockPerf nas VMs

Com a instalação do SockPerf concluída, as VMs estão prontas para executar os testes de latência. 

Primeiro, inicie o SockPerf no receptor.

Qualquer número de porta disponível está correto. Neste exemplo, usamos a porta 12345:
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
Agora que o servidor está escutando, o cliente pode começar a enviar pacotes para o servidor na porta em que está escutando, 12345 nesse caso.

Cerca de 100 segundos é longo o suficiente para retornar resultados representativos, conforme mostrado no exemplo a seguir:
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Aguarde os resultados. Dependendo da distância entre as VMs, o número de iterações varia. Considere a possibilidade de começar com testes mais curtos de cerca de 5 segundos para testar o sucesso antes de executar testes mais longos.

Este exemplo de SockPerf usa um tamanho de mensagem de 350 bytes porque é um pacote de tamanho médio típico. O tamanho da mensagem pode ser ajustado para maior ou menor para obter resultados que representem com mais precisão a carga de trabalho que será executada nas VMs.


## <a name="next-steps"></a>Passos seguintes
* Melhorar a latência com o [grupo de posicionamento de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Saiba mais sobre como [otimizar a rede para VMs](../virtual-network/virtual-network-optimize-network-bandwidth.md) para seu cenário.
* Leia sobre como [a largura de banda é alocada para máquinas virtuais](../virtual-network/virtual-machine-network-throughput.md)
* Saiba mais com as perguntas frequentes sobre a [rede virtual do Azure](../virtual-network/virtual-networks-faq.md)