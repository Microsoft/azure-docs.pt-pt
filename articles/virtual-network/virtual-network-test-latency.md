---
title: Testar a latência da rede de máquinas virtuais Azure numa rede virtual Azure | Microsoft Docs
description: Saiba como testar a latência da rede entre máquinas virtuais Azure numa rede virtual
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 7dc8aac730fdf46cab47a3297b8c001cb0b8e314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226410"
---
# <a name="test-vm-network-latency"></a>Testar a latência da rede de VMs

Para obter os resultados mais precisos, meça a latência da sua rede de máquina virtual Azure (VM) com uma ferramenta concebida para a tarefa. Ferramentas publicamente disponíveis, como o SockPerf (para Linux) e latte.exe (para Windows) podem isolar e medir a latência da rede, excluindo outros tipos de latência, como a latência da aplicação. Estas ferramentas focam-se no tipo de tráfego de rede que afeta o desempenho da aplicação (nomeadamente, o Protocolo de Controlo de Transmissão [TCP] e o tráfego do Protocolo de Datagram do Utilizador [UDP]). 

Outras ferramentas comuns de conectividade, como o Ping, podem medir a latência, mas os seus resultados podem não representar o tráfego de rede que é usado em cargas de trabalho reais. Isto porque a maioria destas ferramentas emprega o Protocolo de Mensagens de Controlo de Internet (ICMP), que pode ser tratado de forma diferente do tráfego de aplicações e cujos resultados podem não se aplicar a cargas de trabalho que usam TCP e UDP. 

Para testes precisos de latência da rede dos protocolos utilizados pela maioria das aplicações, o SockPerf (para Linux) e latte.exe (para Windows) produzem os resultados mais relevantes. Este artigo cobre ambas as ferramentas.

## <a name="overview"></a>Descrição geral

Ao utilizar dois VMs, um como remetente e outro como recetor, cria-se um canal de comunicação bidirecional. Com esta abordagem, pode enviar e receber pacotes em ambas as direções e medir o tempo de ida e volta (RTT).

Pode utilizar esta abordagem para medir a latência da rede entre dois VMs ou mesmo entre dois computadores físicos. As medições de latência podem ser úteis para os seguintes cenários:

- Estabelecer um benchmark para a latência da rede entre os VM implantados.
- Compare os efeitos das alterações na latência da rede após alterações relacionadas com:
  - Sistema operativo (SISTEMA) ou software de pilha de rede, incluindo alterações de configuração.
  - Um método de implantação de VM, como a implantação para uma zona de disponibilidade ou grupo de colocação de proximidade (PPG).
  - Propriedades VM, tais como Rede Acelerada ou alterações de tamanho.
  - Uma rede virtual, como mudanças de encaminhamento ou filtragem.

### <a name="tools-for-testing"></a>Ferramentas para testes
Para medir a latência, tem duas opções de ferramentas diferentes:

* Para sistemas baseados no Windows: [latte.exe (Windows)](https://github.com/microsoft/latte/releases/download/v0/latte.exe)
* Para sistemas baseados em Linux: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Ao utilizar estas ferramentas, ajuda a garantir que apenas os prazos de entrega de carga útil TCP ou UDP são medidos e não ICMP (Ping) ou outros tipos de pacotes que não são usados por aplicações e não afetam o seu desempenho.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Dicas para criar uma configuração VM ideal

Quando criar a sua configuração VM, tenha em mente as seguintes recomendações:
- Utilize a versão mais recente do Windows ou Linux.
- Ativar o Networking Acelerado para obter os melhores resultados.
- Implementar VMs com um [grupo de colocação de proximidade Azure](../virtual-machines/co-location.md).
- Os VM maiores geralmente funcionam melhor do que os VMs menores.

### <a name="tips-for-analysis"></a>Dicas para análise

Enquanto está a analisar os resultados dos testes, lembre-se das seguintes recomendações:

- Estabeleça uma linha de base precocemente, assim que a implementação, configuração e otimizações estiverem completas.
- Compare sempre novos resultados com uma linha de base ou, caso contrário, de um teste para outro com alterações controladas.
- Repita os testes sempre que as alterações forem observadas ou planeadas.


## <a name="test-vms-that-are-running-windows"></a>VMs de teste que estão a executar o Windows

### <a name="get-latteexe-onto-the-vms"></a>Obter latte.exe para os VMs

Descarregue a [versão mais recente do latte.exe. ](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Considere colocar latte.exe em pastas separadas, tais como *c:\tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Permitir latte.exe através do Windows Defender Firewall

No *recetor,* crie uma regra de Permitir a indicação do Windows Defender Firewall para permitir a chegada do tráfego latte.exe. É mais fácil permitir que todo o latte.exe programa pelo nome do que permitir a entrada de portas TCP específicas.

Deixe latte.exe através do Windows Defender Firewall executando o seguinte comando:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Por exemplo, se copiasse latte.exe para a pasta *c:\tools,* este seria o comando:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Executar testes de latência

* No *recetor*, inicie latte.exe (corra pela janela CMD, não pela PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Cerca de 65.000 iterações são suficientes para devolver os resultados representativos.

    Qualquer número de porta disponível está bom.

    Se o VM tiver um endereço IP de 10.0.0.4, o comando seria assim:

    `latte -a 10.0.0.4:5005 -i 65100`

* No *remetente*, comece latte.exe (corra-o a partir da janela CMD, não da PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    O comando resultante é o mesmo que no recetor, exceto com a adição de &nbsp; *-c* para indicar que este é o *cliente,* ou *remetente:*

    `latte -c -a 10.0.0.4:5005 -i 65100`

Espere pelos resultados. Dependendo da distância entre os VMs, o teste pode demorar alguns minutos a terminar. Considere começar com menos iterações para testar o sucesso antes de realizar testes mais longos.

## <a name="test-vms-that-are-running-linux"></a>VMs de teste que estão executando Linux

Para testar VMs que estão a executar Linux, utilize [o SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Instalar sockPerf nos VMs

Nos VMs Linux, tanto *remetente* como *recetor,* execute os seguintes comandos para preparar o SockPerf nos VMs. São fornecidos comandos para os maiores distros.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Para a Red Hat Enterprise Linux (RHEL)/CentOS

Execute os seguintes comandos:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Para Ubuntu

Execute os seguintes comandos:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Para todos os distros

Copiar, compilar e instalar o SockPerf de acordo com os seguintes passos:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Executar SockPerf nos VMs

Após a instalação do SockPerf estar concluída, os VMs estão prontos para executar os testes de latência. 

Primeiro, inicie o SockPerf no *recetor*.

Qualquer número de porta disponível está bom. Neste exemplo, utilizamos a porta 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Agora que o servidor está a ouvir, o cliente pode começar a enviar pacotes para o servidor na porta em que está a ouvir (neste caso, 12345).

Cerca de 100 segundos é tempo suficiente para devolver os resultados representativos, como mostra o exemplo seguinte:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Espere pelos resultados. Dependendo da distância entre os VMs, o número de iterações variará. Para testar o sucesso antes de realizar testes mais longos, considere começar com testes mais curtos de cerca de 5 segundos.

Este exemplo SockPerf usa um tamanho de mensagem de 350 byte, que é típico para um pacote médio. Pode ajustar o tamanho mais alto ou mais baixo para obter resultados que representem com maior precisão a carga de trabalho que está a funcionar nos seus VMs.


## <a name="next-steps"></a>Passos seguintes
* Melhorar a latência com um [grupo de colocação de proximidade Azure](../virtual-machines/co-location.md).
* Saiba como otimizar a [rede para VMs](../virtual-network/virtual-network-optimize-network-bandwidth.md) para o seu cenário.
* Leia sobre como a [largura de banda é atribuída a máquinas virtuais.](../virtual-network/virtual-machine-network-throughput.md)
* Para mais informações, consulte [a Azure Virtual Network FAQ](../virtual-network/virtual-networks-faq.md).