---
title: 'Azure VPN Gateway: Configure capturas de pacotes'
description: Saiba mais sobre as funcionalidades de captura de pacotes que pode utilizar nos gateways VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75353505"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Configure capturas de pacotes para gateways VPN

A conectividade e as questões relacionadas com o desempenho são muitas vezes complexas e levam muito tempo e esforço apenas para reduzir a causa do problema. A capacidade de captura de pacotes ajuda muito a reduzir o tempo na redução do âmbito do problema para determinadas partes da rede, tais como se o problema está do lado do cliente da rede, do lado Azure da rede, ou algures no meio. Uma vez que a questão foi reduzida, é muito mais eficiente depurar e tomar medidas corretivas.

Existem algumas ferramentas comumente disponíveis para a captura de pacotes. No entanto, obter capturas de pacotes relevantes usando estas ferramentas é muitas vezes complicado especialmente quando se trabalha com cenários de tráfego de alto volume. As capacidades de filtragem fornecidas por uma captura de pacote de gateway VPN tornam-se um grande diferenciador. Pode utilizar uma captura de pacote de gateway VPN para além de ferramentas de captura de pacotes normalmente disponíveis.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Capacidades de filtragem de pacotes de gateway VPN

As capturas de pacotes de gateway VPN podem ser executadas no portal ou numa ligação específica dependendo das necessidades do cliente. Também pode executar capturas de pacotes em vários túneis ao mesmo tempo. Pode capturar tráfego único ou bidirecional, tráfego IKE e ESP, e pacotes internos juntamente com filtragem em um gateway VPN.

A utilização de 5 tuples filtro (subnet de origem, subnet de destino, porta de origem, porta de destino, protocolo) e bandeiras TCP (SYN, ACK, FIN, URG, PSH, RST) é útil quando isola questões num tráfego de grande volume.

Você pode usar apenas uma opção por propriedade enquanto executa a captura do pacote.

## <a name="setup-packet-capture-using-powershell"></a>Captura de pacote de configuração usando PowerShell

Veja os exemplos abaixo para que os comandos PowerShell iniciem e parem as capturas de pacotes. Para obter mais informações sobre as opções dos parâmetros (como como criar filtro), consulte este [documento](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)PowerShell .

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Iniciar a captura de pacotes para um gateway VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Parâmetro opcional **-FilterData** pode ser usado para aplicar filtro.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Paragem de captura de pacotes para um gateway VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Iniciar a captura de pacotes para uma ligação de gateway VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Parâmetro opcional **-FilterData** pode ser usado para aplicar filtro.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Paragem na captura de pacotes numa ligação de gateway VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Considerações principais

- A execução das capturas de pacotes pode afetar o desempenho. Lembre-se de parar a captura do pacote quando não for necessário.
- A duração mínima de captura do pacote sugerida é de 600 segundos. Ter uma duração de captura de pacote mais curta pode não fornecer dados completos devido a problemas de sincronização entre vários componentes no caminho.
- Os ficheiros de dados de captura de pacotes são gerados em formato PCAP. Utilize wireshark ou outras aplicações normalmente disponíveis para abrir ficheiros PCAP.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre vpN Gateway, consulte [Sobre VPN Gateway](vpn-gateway-about-vpngateways.md)
