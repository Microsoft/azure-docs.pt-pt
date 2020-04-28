---
title: Test Azure virtual machine network latência numa rede virtual Azure Microsoft Docs
description: Saiba testar a latência da rede entre máquinas virtuais Azure numa rede virtual
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
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74896367"
---
# <a name="test-vm-network-latency"></a>Testar a latência da rede de VMs

Para obter os resultados mais precisos, meça a latência da sua rede de rede virtual Azure (VM) com uma ferramenta que foi concebida para a tarefa. Ferramentas publicamente disponíveis, como SockPerf (para Linux) e latte.exe (para Windows) podem isolar e medir a latência da rede, excluindo outros tipos de latência, como a latência da aplicação. Estas ferramentas focam-se no tipo de tráfego de rede que afeta o desempenho da aplicação (nomeadamente, Protocolo de Controlo de Transmissão [TCP] e Tráfego do Protocolo de Datagram de Utilizador [UDP]). 

Outras ferramentas comuns de conectividade, como ping, podem medir a latência, mas os seus resultados podem não representar o tráfego de rede que é usado em cargas de trabalho reais. Isto porque a maioria destas ferramentas emprega o Protocolo de Mensagem de Controlo de Internet (ICMP), que pode ser tratado de forma diferente do tráfego de aplicações e cujos resultados podem não se aplicar a cargas de trabalho que utilizam TCP e UDP. 

Para um teste preciso de latência da rede dos protocolos utilizados pela maioria das aplicações, a SockPerf (para o Linux) e o latte.exe (para windows) produzem os resultados mais relevantes. Este artigo cobre ambas as ferramentas.

## <a name="overview"></a>Descrição geral

Ao utilizar dois VMs, um como remetente e outro como recetor, cria-se um canal de comunicações bidirecional. Com esta abordagem, pode enviar e receber pacotes em ambas as direções e medir o tempo de ida e volta (RTT).

Pode utilizar esta abordagem para medir a latência da rede entre dois VMs ou mesmo entre dois computadores físicos. As medições de latência podem ser úteis para os seguintes cenários:

- Estabelecer uma referência para a latência da rede entre os VMs implantados.
- Compare os efeitos das alterações na latência da rede após alterações relacionadas:
  - Sistema operativo (OS) ou software de pilha de rede, incluindo alterações de configuração.
  - Um método de implantação vm, como a implantação para uma zona de disponibilidade ou grupo de colocação de proximidade (PPG).
  - Propriedades VM, tais como alterações de networking acelerada ou alterações de tamanho.
  - Uma rede virtual, como o encaminhamento ou as alterações de filtragem.

### <a name="tools-for-testing"></a>Ferramentas para testes
Para medir a latência, tem duas opções diferentes de ferramenta:

* Para sistemas baseados no Windows: [latte.exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Para sistemas baseados em Linux: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Ao utilizar estas ferramentas, ajuda a garantir que apenas os prazos de entrega de carga útil TCP ou UDP são medidos e não o ICMP (Ping) ou outros tipos de pacotes que não são utilizados pelas aplicações e não afetam o seu desempenho.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Dicas para criar uma configuração VM ideal

Quando criar a sua configuração VM, tenha em mente as seguintes recomendações:
- Utilize a versão mais recente do Windows ou Do Linux.
- Ativar o Networking Acelerado para obter os melhores resultados.
- Implementar VMs com um grupo de colocação de [proximidade Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
- Os VMmaiorgeralmente têm um desempenho melhor do que os VMs menores.

### <a name="tips-for-analysis"></a>Dicas para análise

Ao analisar os resultados dos testes, lembre-se das seguintes recomendações:

- Estabeleça uma linha de base cedo, assim que a implementação, configuração e otimizações estiverem completas.
- Compare sempre novos resultados com uma linha de base ou, caso contrário, de um teste para outro com alterações controladas.
- Repita os testes sempre que as alterações forem observadas ou planeadas.


## <a name="test-vms-that-are-running-windows"></a>VMs de teste que estão executando Janelas

### <a name="get-latteexe-onto-the-vms"></a>Pegue latte.exe para os VMs

Descarregue a [versão mais recente do latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Considere colocar latte.exe em pasta separada, como *c:\tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Permitir latte.exe através do Windows Defender Firewall

No *recetor*, crie uma regra de permitir a firewall do Windows Defender para permitir a chegada do tráfego latte.exe. É mais fácil permitir que todo o programa latte.exe seja o nome, em vez de permitir a entrada de portas TCP específicas.

Permita latte.exe através do Windows Defender Firewall executando o seguinte comando:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Por exemplo, se copiasse saque.exe para a pasta *c:\tools,* este seria o comando:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Executar testes de latência

* No *recetor,* inicie latte.exe (execute-o a partir da janela CMD, não da PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Cerca de 65.000 iterações são suficientes para devolver resultados representativos.

    Qualquer número de porta disponível está bom.

    Se o VM tiver um endereço IP de 10.0.0.4, o comando seria assim:

    `latte -a 10.0.0.4:5005 -i 65100`

* No *remetente,* inicie latte.exe (execute-o a partir da janela CMD, não da PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    O comando resultante é o mesmo que no&nbsp;recetor, exceto com a adição de *-c* para indicar que este é o *cliente,* ou *remetente:*

    `latte -c -a 10.0.0.4:5005 -i 65100`

Aguarde os resultados. Dependendo da distância dos VMs, o teste pode demorar alguns minutos a terminar. Considere começar com menos iterações para testar o sucesso antes de fazer testes mais longos.

## <a name="test-vms-that-are-running-linux"></a>VMs de teste que estão executando Linux

Para testar vMs que estão a executar linux, use [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Instale SockPerf nos VMs

Nos VMs Linux, tanto *para remetente* como *recetor,* executa os seguintes comandos para preparar SockPerf nos VMs. Os comandos são fornecidos para as principais distros.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Para Red Hat Enterprise Linux (RHEL)/CentOS

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

Após a instalação SockPerf estar completa, os VMs estão prontos para executar os testes de latência. 

Primeiro, inicie sockPerf no *recetor*.

Qualquer número de porta disponível está bom. Neste exemplo, utilizamos a porta 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Agora que o servidor está a ouvir, o cliente pode começar a enviar pacotes para o servidor na porta em que está a ouvir (neste caso, 12345).

Cerca de 100 segundos é tempo suficiente para devolver resultados representativos, como mostra o seguinte exemplo:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Aguarde os resultados. Dependendo da distância dos VMs, o número de iterações variará. Para testar o sucesso antes de fazer testes mais longos, considere começar com testes mais curtos de cerca de 5 segundos.

Este exemplo sockPerf usa um tamanho de mensagem de 350 bytes, o que é típico para um pacote médio. Pode ajustar o tamanho mais alto ou inferior para obter resultados que representam com maior precisão a carga de trabalho que está a decorrer nos seus VMs.


## <a name="next-steps"></a>Passos seguintes
* Melhorar a latência com um grupo de colocação de [proximidade Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Saiba otimizar a [rede para VMs](../virtual-network/virtual-network-optimize-network-bandwidth.md) para o seu cenário.
* Leia sobre como a [largura de banda é atribuída a máquinas virtuais](../virtual-network/virtual-machine-network-throughput.md).
* Para mais informações, consulte [o Azure Virtual Network FAQ](../virtual-network/virtual-networks-faq.md).
