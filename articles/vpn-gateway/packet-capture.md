---
title: 'Azure VPN Gateway: Capturas de pacotes de configuração'
description: Saiba mais sobre as funcionalidades de captura de pacotes que pode usar nas portas VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 6edfe0228ce4cbe21ad4ae0eb8b7316a92f1da31
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84987151"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Configure capturas de pacotes para gateways VPN

A conectividade e as questões relacionadas com o desempenho são muitas vezes complexas e demoram muito tempo e esforço apenas para reduzir a causa do problema. A capacidade de captura de pacotes ajuda muito a reduzir o tempo na redução do âmbito do problema para certas partes da rede, como se o problema está do lado do cliente da rede, do lado Azure da rede, ou algures no meio. Uma vez que a questão tenha sido restringida, é muito mais eficaz depurar e tomar medidas corretivas.

Existem algumas ferramentas geralmente disponíveis para a captura de pacotes. No entanto, obter capturas de pacotes relevantes usando estas ferramentas é muitas vezes complicado, especialmente quando se trabalha com cenários de tráfego de grande volume. As capacidades de filtragem fornecidas por uma captura de pacotes de gateway VPN tornam-se um grande diferenciador. Pode utilizar uma captura de pacotes de gateway VPN para além de ferramentas de captura de pacotes geralmente disponíveis.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Capacidades de filtragem do pacote de gateway VPN

As capturas de pacotes de gateway VPN podem ser executadas no gateway ou numa ligação específica, dependendo das necessidades do cliente. Também pode executar capturas de pacotes em vários túneis ao mesmo tempo. Você pode capturar tráfego de direção única ou bidirecional, tráfego IKE e ESP, e pacotes internos, juntamente com filtragem em um gateway VPN.

A utilização de filtros de 5 tuples (sub-rede de origem, sub-rede de destino, porta de origem, porta de destino, protocolo) e bandeiras TCP (SYN, ACK, FIN, URG, PSH, RST) é útil ao isolar problemas num tráfego de grande volume.

Você pode usar apenas uma opção por propriedade durante a captura do pacote.

## <a name="setup-packet-capture-using-powershell"></a>Captura de pacote de configuração usando PowerShell

Veja os exemplos abaixo para que os comandos PowerShell iniciem e parem as capturas de pacotes. Para obter mais informações sobre as opções de parâmetros (como criar filtro), consulte este [documento](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)PowerShell .

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Inicie a captura de pacotes para uma porta de entrada VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Parâmetro opcional **-FilterData** pode ser usado para aplicar filtro.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Pare a captura de pacotes para uma porta de entrada VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Inicie a captura de pacotes para uma ligação de gateway VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Parâmetro opcional **-FilterData** pode ser usado para aplicar filtro.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Parar a captura de pacotes numa ligação de gateway VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Considerações principais

- A execução de capturas de pacotes pode afetar o desempenho. Lembre-se de parar a captura do pacote quando não for necessário.
- A duração mínima de captura do pacote é de 600 segundos. Ter uma duração de captura de pacote mais curta pode não fornecer dados completos devido a problemas de sincronização entre vários componentes no caminho.
- Os ficheiros de dados de captura de pacotes são gerados no formato PCAP. Utilize o Wireshark ou outras aplicações comumente disponíveis para abrir ficheiros PCAP.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Gateway VPN, consulte [Sobre o Gateway VPN](vpn-gateway-about-vpngateways.md)
