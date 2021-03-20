---
title: Validar a produção VPN para uma Rede Virtual Microsoft Azure
description: Este artigo ajuda-o a validar o rendimento da rede dos seus recursos no local para uma máquina virtual Azure.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: 2d5b51e8cfbfcb5f771e9da524231f8ddfc40a9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660938"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>How to validate VPN throughput to a virtual network (Como validar o débito da VPN para uma rede virtual)

Uma ligação de gateway VPN permite-lhe estabelecer conectividade segura e transversal entre a sua Rede Virtual dentro do Azure e a sua infraestrutura de TI no local.

Este artigo mostra como validar a produção da rede dos recursos no local para uma máquina virtual Azure (VM).

> [!NOTE]
> Este artigo destina-se a ajudar a diagnosticar e corrigir questões comuns. Se não conseguir resolver o problema utilizando as seguintes informações, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Descrição geral

A ligação de gateway VPN envolve os seguintes componentes:

* Dispositivo VPN no local (Ver uma lista de [dispositivos VPN validados](vpn-gateway-about-vpn-devices.md#devicetable).)
* Internet pública
* Gateway Azure VPN
* VM do Azure

O diagrama seguinte mostra a conectividade lógica de uma rede no local para uma rede virtual Azure através da VPN.

![Conectividade lógica da Rede de Clientes para a Rede MSFT usando VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Calcular o máximo esperado de entrada/saída

1. Determine os requisitos de produção de base da sua aplicação.
1. Determine os limites de entrada Azure VPN. Para obter ajuda, consulte a secção "Gateway SKUs" de [About VPN Gateway](vpn-gateway-about-vpngateways.md#gwsku).
1. Determine a orientação de [produção de VM Azure](../virtual-machines/sizes.md) para o seu tamanho VM.
1. Determine a largura de banda do seu Fornecedor de Serviços de Internet (ISP).
1. Calcular a sua produção esperada tomando a menor largura de banda do VM, VPN Gateway ou ISP; que é medido em Megabits-por-segundo (/) dividido por oito (8).

Se a sua produção calculada não corresponder aos requisitos de produção de base da sua aplicação, deve aumentar a largura de banda do recurso que identificou como estrangulamento. Para redimensionar um Gateway Azure VPN, consulte [alterar um gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Para redimensionar uma máquina virtual, consulte [Redimensionar um VM](../virtual-machines/windows/resize-vm.md). Se não estiver a experimentar a largura de banda da Internet esperada, também poderá contactar o seu ISP.

> [!NOTE]
> O produção do Gateway VPN é um agregado de todas as ligações Site-to-Site\VNET-to-VNET ou ponto-a-local.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Validar o rendimento da rede utilizando ferramentas de desempenho

Esta validação deve ser efetuada durante horas não de ponta, uma vez que a saturação da produção do túnel VPN durante os testes não dá resultados precisos.

A ferramenta que usamos para este teste é o iPerf, que funciona tanto no Windows como no Linux e tem modos de cliente e servidor. Limita-se a 3Gbps para VMs Windows.

Esta ferramenta não executa nenhuma operação de leitura/escrita para o disco. Produz apenas tráfego TCP autogerido de uma ponta à outra. Gera estatísticas baseadas em experimentação que mede a largura de banda disponível entre nós de cliente e servidor. Ao testar entre dois nós, um nó age como o servidor, e o outro nó age como cliente. Uma vez concluído este teste, recomendamos que inverta as funções dos nós para testar tanto o upload como o download de produção em ambos os nós.

### <a name="download-iperf"></a>Baixar iPerf

Baixar [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Para mais detalhes, consulte [a documentação do iPerf](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > Os produtos de terceiros discutidos neste artigo são fabricados por empresas independentes da Microsoft. A Microsoft não faz qualquer garantia, implícita ou não, sobre o desempenho ou fiabilidade destes produtos.

### <a name="run-iperf-iperf3exe"></a>Executar iPerf (iperf3.exe)

1. Ativar uma regra NSG/ACL que permita o tráfego (para testes de endereços IP públicos em Azure VM).

1. Em ambos os nós, permita uma exceção de firewall para a porta 5001.

   **Janelas:** Executar o seguinte comando como administrador:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Para remover a regra quando o teste estiver concluído, executar este comando:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** As imagens de Azure Linux têm firewalls permissivas. Se houver uma aplicação a ouvir num porto, o tráfego é permitido passar. Imagens personalizadas que são seguras podem necessitar de portas abertas explicitamente. As firewalls comuns da camada de OS Linux `iptables` `ufw` incluem, ou `firewalld` .

1. No nó do servidor, mude para o diretório onde iperf3.exe é extraído. Em seguida, executar o iPerf no modo servidor e defini-lo para ouvir na porta 5001 como os seguintes comandos:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > A porta 5001 é personalizável para responder a determinadas restrições de firewall no seu ambiente.

1. No nó do cliente, mude para o diretório onde a ferramenta iperf é extraída e, em seguida, executar o seguinte comando:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   O cliente está a direcionar 30 segundos de tráfego na porta 5001, para o servidor. A bandeira '-P' indica que estamos a fazer 32 ligações simultâneas ao nó do servidor.

   O seguinte ecrã mostra a saída deste exemplo:

   ![Saída](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (OPCIONAL) Para preservar os resultados dos testes, execute este comando:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Após completar os passos anteriores, execute os mesmos passos com as funções invertidas, de modo que o nó do servidor será agora o nó do cliente, e vice-versa.

> [!Note]
> A Iperf não é a única ferramenta. [O NTTTCP é uma solução alternativa para testes.](../virtual-network/virtual-network-bandwidth-testing.md)

## <a name="test-vms-running-windows"></a>VMs de teste executando janelas

### <a name="load-latteexe-onto-the-vms"></a>Carregue Latte.exe nos VMs

Descarregue a versão mais recente do [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Considere colocar Latte.exe em pasta separada, como `c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Permitir Latte.exe através da firewall do Windows

No recetor, crie uma regra de Permitir a chegada do Windows Firewall para permitir a chegada do tráfego Latte.exe. É mais fácil permitir que todo o Latte.exe programa pelo nome do que permitir a entrada de portas TCP específicas.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Permita Latte.exe através do Windows Firewall assim

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Por exemplo, se copiasse latte.exe para a pasta "c:\tools", este seria o comando

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Executar testes de latência

Iniciar latte.exe no RECETOR (executado a partir de CMD, não de PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Cerca de 65 k iterações é tempo suficiente para devolver resultados representativos.

Qualquer número de porta disponível está bom.

Se o VM tiver um endereço IP de 10.0.0.4, seria assim

`latte -c -a 10.0.0.4:5005 -i 65100`

Iniciar latte.exe no SENDER (executado a partir de CMD, não de PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

O comando resultante é o mesmo que no recetor, exceto com a adição de "-c" para indicar que este é o "cliente" ou remetente

`latte -c -a 10.0.0.4:5005 -i 65100`

Espere pelos resultados. Dependendo da distância entre os VMs, pode levar alguns minutos para ser concluído. Considere começar com menos iterações para testar o sucesso antes de realizar testes mais longos.

## <a name="test-vms-running-linux"></a>VMs de teste executando Linux

Utilize [o SockPerf](https://github.com/mellanox/sockperf) para testar VMs.

### <a name="install-sockperf-on-the-vms"></a>Instalar sockPerf nos VMs

Nos VMs Linux (tanto SENDER como RECETOR), execute estes comandos para preparar o SockPerf nos seus VMs:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - Instalar GIT e outras ferramentas úteis

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - Instalar GIT e outras ferramentas úteis

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash - todos

Da linha de comando bash (assume que git está instalado)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Fazer é mais lento, pode levar vários minutos

`make`

A instalação é rápida

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Executar SockPerf nos VMs

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Comandos de amostra após a instalação. Servidor/Recetor - assume que o IP do servidor é de 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Cliente - assume que o IP do servidor é de 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Certifique-se de que não existem lúpulos intermédios (por exemplo, aparelho virtual) durante os testes de produção entre o VM e o Gateway.
> Se houver maus resultados (em termos de produção global) provenientes dos testes iPERF/NTTTCP acima, consulte o seguinte artigo para compreender os factores-chave por detrás das possíveis causas fundamentais do problema: https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Em particular, a análise dos vestígios de captura de pacotes (Wireshark/Network Monitor) recolhidos paralelamente ao cliente e ao servidor durante esses testes ajudará nas avaliações de mau desempenho. Estes vestígios podem incluir perda de pacote, alta latência, tamanho MTU. fragmentação, Janela TCP 0, fragmentos fora da ordem, e assim por diante.

## <a name="address-slow-file-copy-issues"></a>Abordar problemas de cópia de ficheiros lentos

Mesmo que a produção global avaliada com os passos anteriores (iPERF/NTTTCP/etc.) fosse boa, pode experimentar uma resolução lenta de ficheiros quando utilizar o Windows Explorer, ou arrastar e cair através de uma sessão de PDR. Este problema deve-se normalmente a um ou ambos os seguintes fatores:

* As aplicações de cópia de ficheiros, tais como o Windows Explorer e o RDP, não utilizam vários fios ao copiar ficheiros. Para um melhor desempenho, utilize uma aplicação de cópia de ficheiros com vários fios, como [a Richcopy,](/previous-versions/technet-magazine/dd547088(v=msdn.10)) para copiar ficheiros utilizando 16 ou 32 fios. Para alterar o número de fio para cópia de ficheiro em Richcopy, clique em opções **de Cópia de Ação** Cópia de  >    >  **ficheiros**.

   ![Problemas de cópia de ficheiros lentos](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Nem todas as aplicações funcionam da mesma forma, e nem todas as aplicações/processos utilizam todos os fios. Se fizer o teste, poderá ver alguns fios vazios e não fornecerá resultados precisos de produção.
   > Para verificar o desempenho da transferência de ficheiros de aplicação, utilize vários fios aumentando o # do fio em sucessão ou diminuindo de forma a encontrar o rendimento ideal da aplicação ou transferência de ficheiros.

* Velocidade de leitura/escrita de disco VM insuficiente. Para obter mais informações, consulte [a resolução de problemas do armazenamento Azure.](/previous-versions/azure/storage/common/storage-e2e-troubleshooting)

## <a name="on-premises-device-external-facing-interface"></a>Interface externa do dispositivo no local

Mencionou as sub-redes de gamas no local que gostaria que o Azure chegasse através da VPN no Local Network Gateway. Simultaneamente, defina o espaço de endereço VNET em Azure para o dispositivo no local.

* **Route Based Gateway**: A política ou o seletor de tráfego para VPNs baseados em rotas são configurados como qualquer qualquer -para-qualquer (ou cartões selvagens).

* **Policy Based Gateway**: As VPNs baseadas em políticas encriptam e directizam pacotes através de túneis IPsec com base nas combinações de prefixos de endereços entre a sua rede no local e o VNet Azure. Normalmente, a política (ou o Seletor de Tráfego), é definido como uma lista de acesso na configuração de VPN.

* Utilização As ligações **UtilizarPolicyBasedTrafficSelector:** ("UsePolicyBasedTrafficSelectors" para $True numa ligação configurarão a porta de entrada VPN Azure para ligar à firewall VPN baseada em políticas nas instalações. Se ativar os controladores PolicyBasedTrafficS, tem de garantir que o seu dispositivo VPN tem os seletores de tráfego correspondentes definidos com todas as combinações dos prefixos da rede no local (gateway de rede local) de e para os prefixos de rede virtuais Azure, em vez de qualquer-para-qualquer.

A configuração inadequada pode levar a desconexões frequentes dentro do túnel, gotas de pacote, má produção e latência.

## <a name="check-latency"></a>Verifique a latência

Pode verificar a latência utilizando as seguintes ferramentas:

* WinMTR
* TCPTraceroute
* `ping` e `psping` (Estas ferramentas podem fornecer uma boa estimativa de RTT, mas não podem ser usadas em todos os casos.)

![Verificar Latência](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Se notar um pico elevado de latência em qualquer um dos saltos antes de entrar na espinha dorsal da MS Network, talvez queira prosseguir com novas investigações com o seu Fornecedor de Serviços de Internet.

Se um grande pico de latência incomum for notado a partir de lúpulo dentro de "msn.net", contacte o apoio dos Estados-Membros para mais investigações.

## <a name="next-steps"></a>Passos seguintes

Para mais informações ou ajuda, confira o seguinte link:

* [Suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)