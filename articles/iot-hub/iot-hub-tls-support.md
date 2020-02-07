---
title: Suporte a TLS do Hub IoT do Azure
description: Práticas recomendadas no uso de conexões TLS seguras para dispositivos e serviços que se comunicam com o Hub IoT
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 244a71d400493a2029e831b729c63bc0b0dfe559
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049654"
---
# <a name="tls-support-in-iot-hub"></a>Suporte a TLS no Hub IoT

O Hub IoT usa o protocolo TLS para proteger conexões de dispositivos e serviços de IoT. Atualmente, há suporte para três versões do protocolo TLS, principalmente as versões 1,0, 1,1 e 1,2.

TLS 1.0 e 1.1 são considerados legados e estão previstos para depreciação. Para mais informações, consulte [Deprecating TLS 1.0 e 1.1 para IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Recomenda-se vivamente que utilize o TLS 1.2 como versão TLS preferida ao ligar-se ao IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Restringir conexões ao TLS 1,2 em seu recurso de Hub IoT

Para maior segurança, é aconselhável configurar os seus Hubs IoT para *permitir apenas* ligações ao cliente que utilizem a versão TLS 1.2 e para impor o uso de [cifras recomendadas](#recommended-ciphers).

Para o efeito, disponibilize um novo Hub IoT em qualquer uma das [regiões apoiadas](#supported-regions) e coloque a propriedade `minTlsVersion` para `1.2` na especificação de recursos ioT do seu modelo de gestor de recursos de recursos de recursos:

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

O recurso do Hub IoT criado usando essa configuração recusará clientes de dispositivo e serviço que tentam se conectar usando as versões 1,0 e 1,1 do TLS. Da mesma forma, o aperto de mão TLS será recusado se a mensagem HELLO do cliente não listar nenhuma das [cifras recomendadas](#recommended-ciphers).

> [!NOTE]
> A propriedade `minTlsVersion` é apenas leitura e não pode ser alterada uma vez que o seu recurso IoT Hub é criado. Por isso, é essencial que teste e valide corretamente que *todos os* seus dispositivos e serviços IoT são compatíveis com TLS 1.2 e as [cifras recomendadas](#recommended-ciphers) com antecedência.

### <a name="supported-regions"></a>Regiões suportadas

Os hubs IoT que exigem o uso de TLS 1,2 podem ser criados nas seguintes regiões:

* E.U.A. Leste
* E.U.A. Centro-Sul
* E.U.A.Oeste 2

> [!NOTE]
> Após falhas, a propriedade `minTlsVersion` do seu Hub IoT permanecerá eficaz na região geo-emparelhada após o fracasso.

### <a name="recommended-ciphers"></a>Codificações recomendadas

Os hubs IoT configurados para aceitar somente o TLS 1,2 também impedirão o uso das seguintes codificações recomendadas:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Usar o TLS 1,2 em seus SDKs do Hub IoT

Use os links abaixo para configurar o TLS 1,2 e as codificações permitidas nos SDKs do cliente do Hub IoT.

| Idioma | Versões suportando TLS 1.2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Etiqueta 2019-12-11 ou mais recente            | [Ligação](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Ligação](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Ligação](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Ligação](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Ligação](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Usar o TLS 1,2 na instalação do IoT Edge

IoT Edge dispositivos podem ser configurados para usar o TLS 1,2 ao se comunicar com o Hub IoT. Para o efeito, utilize a página de [documentação IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).