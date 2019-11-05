---
title: Capturas de pacotes do gateway de VPN do Azure | Microsoft Docs
description: Saiba mais sobre as funcionalidades de captura de pacote que você pode usar em gateways de VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 0957fabf58a68efe9e215b390d28dbf160574963
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517439"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Configurar capturas de pacote para gateways de VPN

Os problemas relacionados à conectividade e ao desempenho geralmente são complexos e levam muito tempo e esforço para restringir a causa do problema. A capacidade de capturar pacotes ajuda muito a reduzir o tempo delimitando o escopo do problema para determinadas partes da rede, como se o problema está no lado do cliente da rede, no lado do Azure da rede ou em algum lugar entre eles. Depois que o problema foi restringido, é muito mais eficiente depurar e tomar medidas corretivas.

Há algumas ferramentas disponíveis para a captura de pacotes. No entanto, obter capturas de pacotes relevantes usando essas ferramentas costuma ser um trabalho complicado especialmente ao trabalhar com cenários de tráfego de alto volume. Os recursos de filtragem fornecidos por uma captura de pacotes de gateway de VPN se tornam um importante diferencial. Você pode usar uma captura de pacotes de gateway de VPN além das ferramentas de captura de pacote normalmente disponíveis.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Recursos de filtragem de captura de pacotes do gateway de VPN

As capturas de pacotes do gateway de VPN podem ser executadas no gateway ou em uma conexão específica, dependendo das necessidades do cliente. Você também pode executar capturas de pacote em vários túneis ao mesmo tempo. Você pode capturar o tráfego único ou de bi-direction, tráfego IKE e ESP e pacotes internos, juntamente com a filtragem em um gateway de VPN.

Usar filtros de 5 tuplas (sub-rede de origem, sub-rede de destino, porta de origem, porta de destino, protocolo) e sinalizadores TCP (SYN, ACK, FIN, URG, PSH, RST) é útil ao isolar problemas em um tráfego de alto volume.

## <a name="setup-packet-capture-using-powershell"></a>Configurar a captura de pacotes usando o PowerShell

Consulte os exemplos abaixo para obter os comandos do PowerShell para iniciar e parar as capturas de pacotes. Para obter mais informações sobre as opções de parâmetro (como criar filtros), consulte este [documento](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)do PowerShell.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Iniciar captura de pacote para um gateway de VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

O parâmetro opcional **-FilterData** pode ser usado para aplicar filtros.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Parar captura de pacote para um gateway de VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Iniciar captura de pacote para uma conexão de gateway de VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

O parâmetro opcional **-FilterData** pode ser usado para aplicar filtros.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Parar captura de pacote em uma conexão de gateway de VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Considerações principais

- A execução de capturas de pacote pode afetar o desempenho. Lembre-se de interromper a captura de pacotes quando ela não for necessária.
- A duração mínima da captura de pacotes sugerida é de 600 segundos. Ter uma duração de captura de pacotes mais curta pode não fornecer dados completos devido à sincronização de problemas entre vários componentes no caminho.
- Os arquivos de dados de captura de pacote são gerados nos formatos PCAP ou ETL. Talvez você precise do analisador de Netmon para entender os dados.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o gateway de VPN, consulte [sobre o gateway de VPN](vpn-gateway-about-vpngateways.md)