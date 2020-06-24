---
title: Suporte Azure IoT Hub TLS
description: Melhores práticas na utilização de ligações TLS seguras para dispositivos e serviços que comunicam com o IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: jlian
ms.openlocfilehash: 8c52037684215d1672ed813389d0bbace9a03e42
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080612"
---
# <a name="tls-support-in-iot-hub"></a>Suporte TLS no IoT Hub

O IoT Hub utiliza a Transport Layer Security (TLS) para garantir ligações a partir de dispositivos e serviços IoT. Atualmente são suportadas três versões do protocolo TLS, nomeadamente as versões 1.0, 1.1 e 1.2.

Os TLS 1.0 e 1.1 são considerados legados e estão previstos para a depreciação. Para obter mais informações, consulte [deprecisando TLS 1.0 e 1.1 para IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Recomenda-se vivamente que utilize o TLS 1.2 como a versão TLS preferida ao ligar-se ao IoT Hub.

## <a name="tls-12-enforcement-available-in-select-regions"></a>Aplicação TLS 1.2 disponível em regiões selecionadas

Para uma maior segurança, configuure os seus Hubs IoT *apenas* para permitir ligações ao cliente que utilizem a versão 1.2 do TLS e para impor a utilização de [cifras recomendadas](#recommended-ciphers). Esta característica só é suportada nestas regiões:

* E.U.A. Leste
* E.U.A. Centro-Sul
* E.U.A.Oeste 2
* US Gov - Arizona
* US Gov - Virginia

Para o efeito, provisão de um novo Hub IoT em qualquer uma das regiões apoiadas e definir a `minTlsVersion` propriedade `1.2` na especificação de recursos ioT do seu modelo de IoT do seu modelo de ioT do seu modelo de ioT:

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

O recurso IoT Hub criado utilizando esta configuração recusará clientes de dispositivos e serviços que tentem ligar usando as versões TLS 1.0 e 1.1. Da mesma forma, o aperto de mão TLS será recusado se a mensagem HELLO do cliente não enumerar nenhuma das [cifras recomendadas](#recommended-ciphers).

> [!NOTE]
> A `minTlsVersion` propriedade é apenas de leitura e não pode ser alterada uma vez que o seu recurso IoT Hub é criado. Por isso, é essencial que teste e valide adequadamente que *todos os* seus dispositivos e serviços IoT sejam compatíveis com o TLS 1.2 e as [cifras recomendadas](#recommended-ciphers) com antecedência.
> 
> Após os failovers, a `minTlsVersion` propriedade do seu IoT Hub permanecerá eficaz na região geo-emparelhada após o failover.

## <a name="recommended-ciphers"></a>Cifras recomendadas

Os hubs IoT configurados para aceitar apenas TLS 1.2 também aplicam a utilização das seguintes cifras recomendadas:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

Para os hubs IoT não configurados para a execução TLS 1.2, o TLS 1.2 ainda funciona com as seguintes cifras:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>Utilize TLS 1.2 nos seus SDKs IoT Hub

Utilize os links abaixo para configurar o TLS 1.2 e permitiu cifras em SDKs cliente IoT Hub.

| Linguagem | Versões que suportam TLS 1.2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 ou mais recente            | [Ligação](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Ligação](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Ligação](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Ligação](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Ligação](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>Utilize o TLS 1.2 na sua configuração IoT Edge

Os dispositivos IoT Edge podem ser configurados para utilizar o TLS 1.2 quando comunicam com o IoT Hub. Para o efeito, utilize a página de [documentação IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).