---
title: Suporte a TLS do Hub IoT do Azure
description: Práticas recomendadas no uso de conexões TLS seguras para dispositivos e serviços que se comunicam com o Hub IoT
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.custom: Azure IoT Hub TLS
ms.openlocfilehash: eb6b04a476ac6100962e1103af37d75b719dd546
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921251"
---
# <a name="tls-support-in-iot-hub"></a>Suporte a TLS no Hub IoT

O Hub IoT usa o protocolo TLS para proteger conexões de dispositivos e serviços de IoT. Atualmente, há suporte para três versões do protocolo TLS, principalmente as versões 1,0, 1,1 e 1,2.

O TLS 1,0 e o 1,1 são considerados herdados e são [planejados para substituição](./tls-1.2-everywhere.md). Portanto, é altamente recomendável usar o TLS 1,2 como a versão do TLS preferencial ao se conectar ao Hub IoT.


## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Restringir conexões ao TLS 1,2 em seu recurso de Hub IoT

Para maior segurança, é aconselhável configurar seus hubs IoT para permitir _somente_ conexões de cliente que usam TLS versão 1,2 e para impor o uso de [codificações recomendadas](#recommended-ciphers).

Para essa finalidade, provisione um novo hub IoT em qualquer uma das [regiões com suporte](#supported-regions) e defina a propriedade `minTlsVersion` como `1.2` na especificação de recurso do Hub IOT do modelo de Azure Resource Manager:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

O recurso do Hub IoT criado usando essa configuração recusará clientes de dispositivo e serviço que tentam se conectar usando as versões 1,0 e 1,1 do TLS. Da mesma forma, o handshake TLS será recusado se a mensagem de SAUDAção do cliente não listar nenhuma das [codificações recomendadas](#recommended-ciphers).

Observe que a propriedade `minTlsVersion` é somente leitura e não pode ser alterada depois que o recurso do Hub IoT é criado. Portanto, é essencial que você teste e valide corretamente que _todos_ os seus dispositivos e serviços de IOT sejam compatíveis com o TLS 1,2 e as [codificações recomendadas](#recommended-ciphers) com antecedência.


### <a name="supported-regions"></a>Regiões suportadas

Os hubs IoT que exigem o uso de TLS 1,2 podem ser criados nas seguintes regiões:

* Este dos E.U.A.
* E.U.A. Centro-Sul
* E.U.A. Oeste 2

> [!NOTE]
> Após os failovers, a propriedade `minTlsVersion` do Hub IoT permanecerá efetiva na região emparelhada após o failover.



### <a name="recommended-ciphers"></a>Codificações recomendadas

Os hubs IoT configurados para aceitar somente o TLS 1,2 também impedirão o uso das seguintes codificações recomendadas:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`


### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Usar o TLS 1,2 em seus SDKs do Hub IoT

Use os links abaixo para configurar o TLS 1,2 e as codificações permitidas nos SDKs do cliente do Hub IoT.

| Linguagem | Compatível com TLS 1,2 | Documentação |
|----------|-------------------|---------------|
| C        | Sim               | [Ligação](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Sim               | [Ligação](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Sim               | [Ligação](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Sim               | [Ligação](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Sim               | [Ligação](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Usar o TLS 1,2 na instalação do IoT Edge

IoT Edge dispositivos podem ser configurados para usar o TLS 1,2 ao se comunicar com o Hub IoT. Para essa finalidade, use a [página de documentação IOT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).