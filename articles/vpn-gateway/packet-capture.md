---
title: 'Azure VPN Gateway: Captura de pacotes de configure'
description: Saiba mais sobre a funcionalidade de captura de pacotes que pode usar nas portas VPN para ajudar a reduzir a causa de um problema.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: radwiv
ms.openlocfilehash: 486ac23f26a7eee6b31322de79bfb68076a598ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441600"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>Captura de pacotes de configuração para gateways VPN

A conectividade e os problemas relacionados com o desempenho são muitas vezes complexos. Pode levar tempo e esforço significativos apenas para reduzir a causa do problema. A captura de pacotes pode ajudá-lo a reduzir o âmbito de um problema a certas partes da rede. Pode ajudá-lo a determinar se o problema está do lado do cliente da rede, do lado Azure da rede, ou algures no meio. Depois de reduzir o problema, é mais eficiente depurar e tomar medidas corretivas.

Existem algumas ferramentas de captura de pacotes geralmente disponíveis. Obter capturas de pacotes relevantes com estas ferramentas pode ser complicado, especialmente em cenários de tráfego de grande volume. As capacidades de filtragem fornecidas pela captura de pacotes Azure VPN Gateway são um diferenciador importante. Você pode usar a captura de pacotes VPN Gateway juntamente com ferramentas de captura de pacotes geralmente disponíveis.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Capacidades de filtragem de pacotes VPN Gateway

Pode executar a captura de pacotes VPN Gateway no gateway ou numa ligação específica, dependendo das suas necessidades. Também pode executar a captura de pacotes em vários túneis ao mesmo tempo. Você pode capturar tráfego unidirecional ou bidirecional, tráfego IKE e ESP, e pacotes internos, juntamente com filtragem em um gateway VPN.

É útil usar um filtro de cinco tuples (sub-rede de origem, sub-rede de destino, porta de origem, porta de destino, protocolo) e bandeiras TCP (SYN, ACK, FIN, URG, PSH, RST) quando você está isolando problemas em tráfego de grande volume.

Os seguintes exemplos de JSON e um esquema JSON fornecem explicações de cada propriedade. Aqui estão algumas limitações a ter em mente quando executar capturas de pacotes:
- No esquema mostrado aqui, o filtro é uma matriz, mas atualmente apenas um filtro pode ser usado de cada vez.
- Não pode executar várias capturas de pacotes em toda a porta ao mesmo tempo.
- Não é possível executar várias capturas de pacotes numa única ligação ao mesmo tempo. Pode executar várias capturas de pacotes em diferentes ligações ao mesmo tempo.
- Um máximo de cinco capturas de pacotes pode ser executado em paralelo por gateway. Estas capturas de pacotes podem ser uma combinação de capturas de pacotes em larga porta e capturas de pacotes por ligação.

### <a name="example-json"></a>Exemplo JSON
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>Esquema JSON
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="set-up-packet-capture-by-using-powershell"></a>Configurar a captura de pacotes utilizando o PowerShell

Os exemplos a seguir mostram comandos PowerShell que iniciam e param as capturas de pacotes. Para obter mais informações sobre as opções de parâmetros, consulte [este documento PowerShell](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Inicie a captura de pacotes para uma porta de entrada VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Pode utilizar o parâmetro opcional `-FilterData` para aplicar um filtro.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Pare a captura de pacotes para uma porta de entrada VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Inicie a captura de pacotes para uma ligação de gateway VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Pode utilizar o parâmetro opcional `-FilterData` para aplicar um filtro.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Parar a captura de pacotes numa ligação de gateway VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Considerações principais

- A captura de pacotes de execução pode afetar o desempenho. Lembre-se de parar a captura do pacote quando não precisar.
- A duração mínima de captura do pacote é de 600 segundos. Devido a problemas de sincronização entre vários componentes no caminho, capturas de pacotes mais curtos podem não fornecer dados completos.
- Os ficheiros de dados de captura de pacotes são gerados no formato PCAP. Utilize o Wireshark ou outras aplicações comumente disponíveis para abrir ficheiros PCAP.
- As capturas de pacotes não são suportadas em gateways baseados em políticas.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o VPN Gateway, veja [o que é VPN Gateway?](vpn-gateway-about-vpngateways.md)
