---
title: Suporte Azure IoT Hub TLS
description: Melhores práticas na utilização de ligações TLS seguras para dispositivos e serviços que comunicam com o IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jlian
ms.openlocfilehash: 08ecb766a1a9bd7ff75bf97647be811577212eb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90006045"
---
# <a name="tls-support-in-iot-hub"></a>Suporte TLS no IoT Hub

O IoT Hub utiliza a Transport Layer Security (TLS) para garantir ligações a partir de dispositivos e serviços IoT. Atualmente são suportadas três versões do protocolo TLS, nomeadamente as versões 1.0, 1.1 e 1.2.

Os TLS 1.0 e 1.1 são considerados legados e estão previstos para a depreciação. Para obter mais informações, consulte [deprecisando TLS 1.0 e 1.1 para IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Recomenda-se vivamente que utilize o TLS 1.2 como a versão TLS preferida ao ligar-se ao IoT Hub.

## <a name="tls-12-enforcement-available-in-select-regions"></a>Aplicação TLS 1.2 disponível em regiões selecionadas

Para maior segurança, configuure os seus Hubs IoT *apenas* para permitir ligações ao cliente que utilizem a versão 1.2 da TLS e para impor a utilização de suites de [cifra](#cipher-suites). Esta característica só é suportada nestas regiões:

* E.U.A. Leste
* E.U.A. Centro-Sul
* E.U.A. Oeste 2
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

O recurso IoT Hub criado utilizando esta configuração recusará clientes de dispositivos e serviços que tentem ligar usando as versões TLS 1.0 e 1.1. Da mesma forma, o aperto de mão TLS será recusado se a `ClientHello` mensagem não enumerar nenhuma das [cifras recomendadas](#cipher-suites).

> [!NOTE]
> A `minTlsVersion` propriedade é apenas de leitura e não pode ser alterada uma vez que o seu recurso IoT Hub é criado. Por isso, é essencial que teste e valide adequadamente que *todos os* seus dispositivos e serviços IoT sejam compatíveis com o TLS 1.2 e as [cifras recomendadas](#cipher-suites) com antecedência.
> 
> Após os failovers, a `minTlsVersion` propriedade do seu IoT Hub permanecerá eficaz na região geo-emparelhada após o failover.

## <a name="cipher-suites"></a>Suítes cifra

Os hubs IoT configurados para aceitar apenas TLS 1.2 também aplicam a utilização das seguintes suítes de cifra recomendadas:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

Para os hubs IoT não configurados para a execução TLS 1.2, o TLS 1.2 ainda funciona com as seguintes suítes cifras:

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

Um cliente pode sugerir uma lista de suítes de cifra mais altas para usar durante `ClientHello` . No entanto, alguns deles podem não ser apoiados pelo IoT Hub (por exemplo, `ECDHE-ECDSA-AES256-GCM-SHA384` ). Neste caso, o IoT Hub tentará seguir a preferência do cliente, mas eventualmente negociará a suite de cifra com `ServerHello` .

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>Utilize TLS 1.2 nos seus SDKs IoT Hub

Utilize os links abaixo para configurar o TLS 1.2 e permitiu cifras em SDKs cliente IoT Hub.

| Idioma | Versões que suportam TLS 1.2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 ou mais recente            | [Ligação](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Ligação](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Ligação](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Ligação](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Ligação](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>Utilize o TLS 1.2 na sua configuração IoT Edge

Os dispositivos IoT Edge podem ser configurados para utilizar o TLS 1.2 quando comunicam com o IoT Hub. Para o efeito, utilize a página de [documentação IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).

## <a name="device-authentication"></a>Autenticação do dispositivo

Após um aperto de mão TLS bem sucedido, o IoT Hub pode autenticar um dispositivo utilizando uma chave simétrica ou um certificado X.509. Para a autenticação baseada em certificados, este pode ser qualquer certificado X.509, incluindo ECC. O IoT Hub valida o certificado com a impressão digital ou a autoridade de certificados (CA) que fornece. O IoT Hub ainda não suporta a autenticação mútua baseada em X.509 (mTLS). Para saber mais, consulte [os certificados X.509 suportados.](iot-hub-devguide-security.md#supported-x509-certificates)
