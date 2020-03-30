---
title: Valide a entrada de VPN numa rede virtual do Microsoft Azure
description: O objetivo deste documento é ajudar um utilizador a validar a entrada da rede dos seus recursos no local para uma máquina virtual Azure.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: a88e339e82484c2ec1cd2276f6218fa718b990f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860491"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>How to validate VPN throughput to a virtual network (Como validar o débito da VPN para uma rede virtual)

Uma ligação de gateway VPN permite-lhe estabelecer uma conectividade segura e transversal entre a sua Rede Virtual dentro do Azure e a sua infraestrutura de TI no local.

Este artigo mostra como validar a entrada da rede dos recursos no local para uma máquina virtual Azure (VM).

> [!NOTE]
> Este artigo destina-se a ajudar a diagnosticar e corrigir questões comuns. Se não conseguir resolver o problema utilizando as seguintes informações, suporte de [contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Descrição geral

A ligação de gateway VPN envolve os seguintes componentes:

* Dispositivo VPN no local (Ver uma lista de [dispositivos VPN validados](vpn-gateway-about-vpn-devices.md#devicetable).)
* Internet pública
* Gateway Azure VPN
* VM do Azure

O diagrama seguinte mostra a conectividade lógica de uma rede no local para uma rede virtual Azure através de VPN.

![Conectividade Lógica da Rede de Clientes à Rede MSFT utilizando VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Calcular a entrada/saída máxima esperada

1. Determine os requisitos de entrada de base da sua aplicação.
1. Determine os limites de entrada de gateway Azure VPN. Para obter ajuda, consulte a secção "Gateway SKUs" de [About VPN Gateway](vpn-gateway-about-vpngateways.md#gwsku).
1. Determine a orientação de entrada de [VM Azure](../virtual-machines/virtual-machines-windows-sizes.md) para o seu tamanho VM.
1. Determine a largura de banda do fornecedor de serviços de Internet (ISP).
1. Calcular a sua entrada esperada tomando a largura de banda menos larga do VM, VPN Gateway ou ISP; que é medido em Megabits-por-segundo (/) dividido por oito (8).

Se a sua entrada calculada não cumprir os requisitos de entrada de base da sua aplicação, deve aumentar a largura de banda do recurso que identificou como o estrangulamento. Para redimensionar um Gateway VpN Azure, consulte [Mudar um portal SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Para redimensionar uma máquina virtual, consulte [Resize a VM](../virtual-machines/virtual-machines-windows-resize-vm.md). Se não estiver a experimentar a largura de banda esperada da Internet, também poderá contactar o seu ISP.

> [!NOTE]
> A entrada vpn Gateway é um agregado de todas as ligações Site-to-Site\VNET-to-VNET ou Ponto-a-Local.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Validar a entrada da rede utilizando ferramentas de desempenho

Esta validação deve ser efetuada durante horas não-pico, uma vez que a saturação de entrada de túnel VPN durante os testes não dá resultados precisos.

A ferramenta que utilizamos para este teste é o iPerf, que funciona tanto no Windows como no Linux e tem os modos de cliente e servidor. Está limitado a 3Gbps para VMs windows.

Esta ferramenta não realiza quaisquer operações de leitura/escrita para o disco. Produz apenas tráfego de TCP autogerado de uma ponta à outra. Gera estatísticas baseadas em experimentação que mede a largura de banda disponível entre os nós do cliente e do servidor. Ao testar entre dois nós, um nó atua como servidor, e o outro nó age como um cliente. Uma vez concluído este teste, recomendamos que inverta as funções dos nós para testar tanto o upload como o download de entrada em ambos os nós.

### <a name="download-iperf"></a>Baixar iPerf

Baixar [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Para mais detalhes, consulte [a documentação iPerf](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > Os produtos de terceiros discutidos neste artigo são fabricados por empresas independentes da Microsoft. A Microsoft não garante, implícita ou não, o desempenho ou a fiabilidade destes produtos.

### <a name="run-iperf-iperf3exe"></a>Executar iPerf (iperf3.exe)

1. Ativar uma regra NSG/ACL que permita o tráfego (para testes de endereçoIP públicos em VM Azure).

1. Em ambos os nós, permita uma exceção de firewall para a porta 5001.

   **Janelas:** Executar o seguinte comando como administrador:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Para remover a regra quando os testes estiverem completos, execute este comando:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** As imagens do Azure Linux têm firewalls permissivas. Se houver uma aplicação a ouvir num porto, o tráfego é permitido. Imagens personalizadas que estejam seguras podem necessitar de portas abertas explicitamente. As firewalls comuns da `iptables` `ufw`camada `firewalld`de Sistema Linux incluem, ou .

1. No nó do servidor, mude para o diretório onde iperf3.exe é extraído. Em seguida, executar iPerf no modo servidor, e defini-lo para ouvir na porta 5001 como os seguintes comandos:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > O Porta 5001 é personalizável para responder a restrições específicas de firewall no seu ambiente.

1. No nó do cliente, mude para o diretório onde a ferramenta iperf é extraída e, em seguida, executar o seguinte comando:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   O cliente está a direcionar 30 segundos de tráfego na porta 5001, para o servidor. A bandeira '-P' indica que estamos a fazer 32 ligações simultâneas ao nó do servidor.

   O ecrã seguinte mostra a saída deste exemplo:

   ![Saída](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (OPCIONAL) Para preservar os resultados dos testes, execute este comando:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Depois de completar os passos anteriores, execute os mesmos passos com as funções invertidas, de modo que o nó do servidor será agora o nó do cliente, e vice-versa.

> [!Note]
> Iperf não é a única ferramenta. [NtTTCP é uma solução alternativa para o teste.](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing)

## <a name="test-vms-running-windows"></a>VMs de teste executando janelas

### <a name="load-latteexe-onto-the-vms"></a>Carregue latte.exe sobre os VMs

Descarregue a versão mais recente de [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Considere colocar Latte.exe em pasta separada, como`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Permitir latte.exe através da firewall windows

No recetor, crie uma regra de permitir a entrada do Windows para permitir a chegada do tráfego Latte.exe. É mais fácil permitir que todo o programa Latte.exe seja o nome, em vez de permitir a entrada de portas TCP específicas.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Permita latte.exe através do Firewall windows assim

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Por exemplo, se copiasse saquear latte.exe para a pasta "c:\tools", este seria o comando

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Executar testes de latência

Inicie latte.exe no RECETOR (executado a partir de CMD, não de PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Cerca de 65 k iterações é tempo suficiente para devolver resultados representativos.

Qualquer número de porta disponível está bom.

Se o VM tem um endereço IP de 10.0.0.4, seria assim

`latte -c -a 10.0.0.4:5005 -i 65100`

Inicie latte.exe no SENDER (executado a partir de CMD, não da PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

O comando resultante é o mesmo que no recetor, exceto com a adição de "-c" para indicar que este é o "cliente" ou remetente

`latte -c -a 10.0.0.4:5005 -i 65100`

Aguarde os resultados. Dependendo da distância dos VMs, pode levar alguns minutos para ser concluído. Considere começar com menos iterações para testar o sucesso antes de fazer testes mais longos.

## <a name="test-vms-running-linux"></a>VMs de teste executando Linux

Use [sockPerf](https://github.com/mellanox/sockperf) para testar VMs.

### <a name="install-sockperf-on-the-vms"></a>Instale SockPerf nos VMs

Nos VMs Linux (SENDER e RECETOR), execute estes comandos para preparar sockPerf nos seus VMs:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - Instale gIT e outras ferramentas úteis

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - Instale gIT e outras ferramentas úteis

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash - todos

Da linha de comando de batida (assume que git está instalado)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Fazer é mais lento, pode levar vários minutos

`make`

Fazer a instalação é rápido

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Executar SockPerf nos VMs

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Comandos de amostra após a instalação. Servidor/Recetor - assume que o IP do servidor é 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Cliente - assume que o IP do servidor é 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Certifique-se de que não existem lúpulo intermédio (por exemplo, Aparelho Virtual) durante os testes de entrada entre o VM e o Gateway.
> Se houver maus resultados (em termos de entrada global) provenientes dos testes iPERF/NTTTCP acima, consulte o seguinte artigo para compreender os factores-chave por detrás das possíveis causas fundamentais do problema:https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Em particular, a análise dos vestígios de captura de pacotes (Wireshark/Network Monitor) recolhidos em paralelo do cliente e do servidor durante esses testes ajudará nas avaliações do mau desempenho. Estes vestígios podem incluir perda de pacote, alta latência, tamanho MTU. fragmentação, Janela TCP 0, fragmentos fora de ordem, e assim por diante.

## <a name="address-slow-file-copy-issues"></a>Endereço problemas de cópia lenta de ficheiro

Mesmo que a entrada global avaliada com os passos anteriores (iPERF/NTTTCP/etc.) fosse boa, poderá experimentar uma lenta resolução de ficheiros quando utilizar o Windows Explorer, ou arrastar e cair através de uma sessão de RDP. Este problema deve-se normalmente a um ou ambos os seguintes fatores:

* As aplicações de cópia de ficheiros, tais como o Windows Explorer e o RDP, não utilizam vários fios ao copiar ficheiros. Para um melhor desempenho, utilize uma aplicação de cópia de ficheiros com fios múltiplos, como a [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) para copiar ficheiros utilizando 16 ou 32 fios. Para alterar o número de linha para cópia de ficheiros em Richcopy, clique na cópia do**Ficheiro**de**opções** >  **de Cópia de Ação** > .

   ![Problemas de cópia de ficheiros lentos](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Nem todas as aplicações funcionam da mesma forma, e nem todas as aplicações/processos utilizam todos os fios. Se fizer o teste, poderá ver alguns fios vazios e não fornecerá resultados precisos de entrada.
   > Para verificar o desempenho da transferência de ficheiros de aplicação, utilize várias linhas aumentando o # de fio em sucessão ou diminua para encontrar a entrada ideal da aplicação ou transferência de ficheiros.

* Velocidade insuficiente de leitura/escrita de disco VM. Para mais informações, consulte [Azure Storage Troubleshooting](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interface externa do dispositivo no local

Mencionei as subredes das gamas no local que gostaria que o Azure chegasse via VPN no Gateway da Rede Local. Simultaneamente, defina o espaço de endereço VNET em Azure para o dispositivo no local.

* **Gateway baseado em rota**: A política ou seletor de tráfego para VPNs baseados em rotas estão configuradas como qualquer um para qualquer (ou wild cards).

* **Gateway baseado em políticas**: VPNs baseados em políticas encriptam e pacotes diretos através de túneis IPsec com base nas combinações de prefixos de endereço entre a sua rede no local e o Azure VNet. Normalmente, a política (ou o Seletor de Tráfego), é definido como uma lista de acesso na configuração de VPN.

* **UtilizaçãoAsLigações DoSelector** de Tráfego Baseados de Utilização: ("UsePolicyBasedTrafficSelectors" para $True numa ligação configurará o gateway VPN Azure para ligar à firewall VPN baseada em políticas nas instalações. Se ativar os selecionadores PolicyBasedTrafficS, tem de garantir que o seu dispositivo VPN tem os seletores de tráfego correspondentes definidos com todas as combinações da sua rede no local (gateway de rede local) prefixos de e para os prefixos da rede virtual Azure, em vez de qualquer a qualquer.

Uma configuração inadequada pode levar a desconexões frequentes dentro do túnel, gotas de pacote, má entrada e latência.

## <a name="check-latency"></a>Verifique a latência

Pode verificar a latência utilizando as seguintes ferramentas:

* WinMTR
* TCPTraceroute
* `ping`e `psping` (Estas ferramentas podem fornecer uma boa estimativa de RTT, mas não podem ser usadas em todos os casos.)

![Verificar Latência](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Se notar um pico de latência em qualquer um dos lúpulos antes de entrar na espinha dorsal da Rede MS, talvez queira prosseguir com mais investigações com o seu Fornecedor de Serviços de Internet.

Se um grande e invulgar pico de latência for notado a partir de lúpulo dentro de "msn.net", contacte o apoio dos EmS para mais investigações.

## <a name="next-steps"></a>Passos seguintes

Para mais informações ou ajuda, consulte o seguinte link:

* [Suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
