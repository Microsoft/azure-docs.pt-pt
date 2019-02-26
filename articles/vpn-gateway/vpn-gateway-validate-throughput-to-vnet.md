---
title: Validar o débito VPN para uma rede Virtual do Microsoft Azure | Documentos da Microsoft
description: O objetivo deste documento é para o ajudar a um utilizador validar o débito de rede a partir dos seus recursos no local para uma máquina virtual do Azure.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: 819415712d8e605825957aa602fc99dcf6902d82
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821666"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Como validar o débito de VPN para uma rede virtual

Uma ligação de gateway VPN permite-lhe segura, conectividade entre a rede Virtual do Azure e no local em vários locais infraestrutura de TI.

Este artigo mostra como validar o débito de rede dos recursos no local para uma máquina virtual do Azure (VM). Ele também fornece orientações de resolução de problemas.

>[!NOTE]
>Este artigo destina-se para o ajudar a diagnosticar e corrigir problemas comuns. Se não for possível resolver o problema ao utilizar as seguintes informações [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Descrição geral

A ligação de gateway VPN envolve os seguintes componentes:

- Dispositivo VPN no local (ver uma lista dos [dispositivos VPN validados)](vpn-gateway-about-vpn-devices.md#devicetable).
- Internet pública
- Gateway VPN do Azure
- VM do Azure

O diagrama seguinte mostra a conectividade de lógica de uma rede no local a uma rede virtual do Azure através de VPN.

![Lógica conectividade de rede cliente à rede de MSFT através de VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Calcular a entrada/saída esperada máxima

1.  Determine requisitos de débito de linha de base da sua aplicação.
2.  Determine os limites de débito do gateway de VPN do Azure. Para obter ajuda, consulte a secção "SKUs de Gateway" [sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md#gwsku).
3.  Determinar os [orientações de débito de VM do Azure](../virtual-machines/virtual-machines-windows-sizes.md) para o tamanho da VM.
4.  Determine a largura de banda do fornecedor de serviços de Internet (ISP).
5.  Calcular o débito esperado - largura de banda, pelo menos, de VM, Gateway, ISP () * 0,8.

Se o seu débito calculado não cumprir os requisitos de débito de linha de base da sua aplicação, terá de aumentar a largura de banda do recurso que identificou no estrangulamento. Para redimensionar um Gateway de VPN do Azure, veja [alterar um SKU de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Para redimensionar uma máquina virtual, consulte [redimensionar uma VM](../virtual-machines/virtual-machines-windows-resize-vm.md). Se não estiver a ter largura de banda de Internet esperada, também poderá contactar o seu ISP.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Validar o débito de rede com as ferramentas de desempenho

Esta validação deve ser realizada durante o horário de pico, como saturação de débito de túnel VPN durante o teste não fornecerá resultados precisos.

A ferramenta que usamos para este teste é iPerf, que funciona no Windows e Linux e tem os modos de cliente e servidor. Está limitado a Gbps 3 para VMs Windows.

Essa ferramenta executa quaisquer operações de leitura/escrita no disco. Produz apenas tráfego TCP gerado automaticamente, desde a si. Gerou estatísticas com base na experimentação que mede a largura de banda disponível entre os nós de cliente e servidor. Ao testar entre dois nós, uma atua como o servidor e o outro como um cliente. Depois de concluído este teste, recomendamos que Inverte as funções para testar os dois carregar e transferir débito em ambos os nós.

### <a name="download-iperf"></a>Transferir iPerf
Baixe [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Para obter detalhes, consulte [iPerf documentação](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Os produtos de terceiros que este artigo aborda são fabricados por empresas que são independentes da Microsoft. A Microsoft não faz nenhuma garantia, implícita ou não, sobre o desempenho ou a confiabilidade desses produtos.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Executar iPerf (iperf3.exe)
1. Ative uma regra NSG/ACL que permite o tráfego (para o endereço IP público na VM do Azure de teste).

2. Em ambos os nós, ative uma exceção de firewall para a porta 5001.

    **Windows:** Execute o seguinte comando como administrador:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Para remover a regra durante o teste estiver concluída, execute este comando:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
     
    **Azure Linux:**  Imagens de Linux do Azure têm firewalls permissivas. Se existir uma aplicação à escuta numa porta, o tráfego é permitido através de. Imagens personalizadas que são protegidas poderão ter de portas abertas explicitamente. Firewalls de camada de SO Linux comuns incluem `iptables`, `ufw`, ou `firewalld`.

3. No nó do servidor, altere o diretório onde iperf3.exe é extraído. Em seguida, execute iPerf no modo de servidor e configurá-lo para escutar na porta 5001 como os seguintes comandos:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. No nó de cliente, altere o diretório em que a ferramenta iperf é extraída e, em seguida, execute o seguinte comando:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    O cliente está induzindo tráfego na porta 5001 para o servidor para 30 segundos. O sinalizador "-P" que indica que estamos usando 32 conexões simultâneas para o nó de servidor.

    O ecrã seguinte mostra a saída deste exemplo:

    ![Saída](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (OPCIONAL) Para preservar os resultados de testes, execute este comando:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Depois de concluir os passos anteriores, execute as mesmas etapas com as funções invertidas, para que o nó do servidor será agora o cliente e vice-versa.

## <a name="address-slow-file-copy-issues"></a>Resolver problemas de cópia de arquivo lenta
Poderá ter ficheiro lento lidar quando utilizar o Explorador do Windows ou arrastando e soltando por meio de uma sessão RDP. Esse problema é normalmente devido a um ou ambos dos seguintes fatores:

- Aplicações de cópia de ficheiros, como o Windows Explorer e o protocolo RDP, não utilize vários threads ao copiar ficheiros. Para um melhor desempenho, utilize como uma aplicação de cópia de ficheiros com múltiplos threads [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) para copiar ficheiros com 16 ou 32 threads. Para alterar o número de threads para cópia de ficheiros no Richcopy, clique em **ação** > **copiar opções** > **cópia do ficheiro**.<br><br>
![Problemas de cópia de arquivo lenta](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Velocidade de leitura/escrita do disco VM suficiente. Para obter mais informações, consulte [resolução de problemas de armazenamento do Azure](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interface com acesso externo dispositivo no local
Se o dispositivo VPN no local endereço IP de acesso à Internet está incluído nos [rede local](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) definição no Azure, poderá experienciar incapacidade para abrir a VPN, esporádica desliga ou problemas de desempenho.

## <a name="checking-latency"></a>A verificar a latência
Utilize o tracert para rastreio para o dispositivo de borda do Microsoft Azure para determinar se existem quaisquer atrasos exceder os 100 ms entre saltos.

A partir da rede no local, execute *tracert* para o VIP do Gateway do Azure ou da VM. Quando vir apenas * retornado, sabe que atingiu o limite do Azure. Quando vir os nomes DNS que incluem "MSN" devolvido, sabe que atingiu o backbone do Microsoft.<br><br>
![A verificar a latência](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações ou ajuda, veja as ligações seguintes:

- [Otimizar o débito de rede para máquinas virtuais do Azure](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
