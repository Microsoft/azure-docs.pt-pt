---
title: Suporte Azure IoT Hub TLS
description: As melhores práticas na utilização de ligações TLS seguras para dispositivos e serviços que comunicam com o IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 7ab3b48d22f116a707f68cbf6284928c7d2557e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409506"
---
# <a name="tls-support-in-iot-hub"></a>Suporte tLS no IoT Hub

O IoT Hub utiliza a Transport Layer Security (TLS) para garantir ligações a partir de dispositivos e serviços IoT. Atualmente são suportadas três versões do protocolo TLS, nomeadamente as versões 1.0, 1.1 e 1.2.

TLS 1.0 e 1.1 são considerados legados e estão previstos para depreciação. Para mais informações, consulte [Deprecating TLS 1.0 e 1.1 para IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Recomenda-se vivamente que utilize o TLS 1.2 como versão TLS preferida ao ligar-se ao IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Restringir as ligações ao TLS 1.2 no seu recurso IoT Hub

Para maior segurança, é aconselhável configurar os seus Hubs IoT para *permitir apenas* ligações ao cliente que utilizem a versão TLS 1.2 e para impor o uso de [cifras recomendadas](#recommended-ciphers).

Para o efeito, disponibilize um novo Hub IoT em `minTlsVersion` qualquer `1.2` uma das [regiões apoiadas](#supported-regions) e detetete te a propriedade na especificação de recursos ioT do seu modelo de gestor de recursos de recursos de recursos:

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

O recurso IoT Hub criado utilizando esta configuração irá recusar dispositivos e clientes de serviço que tentem ligar-se utilizando as versões TLS 1.0 e 1.1. Da mesma forma, o aperto de mão TLS será recusado se a mensagem HELLO do cliente não listar nenhuma das [cifras recomendadas](#recommended-ciphers).

> [!NOTE]
> A `minTlsVersion` propriedade é apenas de leitura e não pode ser alterada uma vez que o seu recurso IoT Hub é criado. Por isso, é essencial que teste e valide corretamente que *todos os* seus dispositivos e serviços IoT são compatíveis com TLS 1.2 e as [cifras recomendadas](#recommended-ciphers) com antecedência.

### <a name="supported-regions"></a>Regiões suportadas

Podem ser criados nos seguintes polos IoT que exijam a utilização de TLS 1.2:

* E.U.A. Leste
* E.U.A. Centro-Sul
* E.U.A.Oeste 2
* US Gov - Arizona
* US Gov - Virginia

> [!NOTE]
> Após falhas, a `minTlsVersion` propriedade do seu Hub IoT permanecerá eficaz na região geo-emparelhada após o fracasso.

### <a name="recommended-ciphers"></a>Cifras recomendadas

Os Hubs IoT que estão configurados para aceitar apenas TLS 1.2 também vão impor a utilização das seguintes cifras recomendadas:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Utilize TLS 1.2 nos seus SDKs IoT Hub

Utilize os links abaixo para configurar TLS 1.2 e permitir cifras em SDKs clientes do IoT Hub.

| Idioma | Versões suportando TLS 1.2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Etiqueta 2019-12-11 ou mais recente            | [Ligação](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Ligação](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Ligação](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Ligação](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Ligação](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Utilize TLS 1.2 na sua configuração IoT Edge

Os dispositivos IoT Edge podem ser configurados para utilizar tLS 1.2 quando comunicarem com o IoT Hub. Para o efeito, utilize a página de [documentação IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).