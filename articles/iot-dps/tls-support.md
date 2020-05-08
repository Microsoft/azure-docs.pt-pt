---
title: Suporte TLS do Serviço de Provisionamento de Dispositivos Azure IoT (DPS)
description: As melhores práticas na utilização de ligações TLS seguras para dispositivos e serviços que comunicam com o Serviço de Provisionamento de Dispositivos IoT (DPS)
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: wesmc
ms.openlocfilehash: 285832d80d37c8553ffc8e37c6f6eab5d7f6d943
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984853"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Suporte TLS no Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS)

O DPS utiliza a Segurança da Camada de Transporte (TLS) para proteger as ligações dos dispositivos IoT. Atualmente são suportadas três versões do protocolo TLS, nomeadamente as versões 1.0, 1.1 e 1.2.

TLS 1.0 e 1.1 são considerados legados e estão previstos para depreciação. Para mais informações, consulte [Deprecating TLS 1.0 e 1.1 para IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). Recomenda-se vivamente que utilize o TLS 1.2 como versão TLS preferida ao ligar-se ao DPS.

## <a name="restrict-connections-to-tls-12"></a>Restringir as ligações ao TLS 1.2

Para maior segurança, é aconselhável configurar as suas instâncias DPS para *permitir apenas* ligações ao cliente do dispositivo que utilizam a versão TLS 1.2 e para impor o uso de [cifras recomendadas](#recommended-ciphers).

Para tal, forme um novo recurso DPS em qualquer `minTlsVersion` uma `1.2` das [regiões apoiadas](#supported-regions) e detetetete a propriedade na especificação de recursos DPS do seu modelo de Gestor de Recursos Azure. O modelo de exemplo seguinte `minTlsVersion` JSON especifica a propriedade para uma nova instância de DPS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

Pode implantar o modelo com o seguinte comando Azure CLI. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Para obter mais informações sobre a criação de recursos DPS com modelos de Gestor de Recursos, consulte, [configurar dPS com um modelo](quick-setup-auto-provision-rm.md)de Gestor de Recursos Azure .

O recurso DPS criado com esta configuração irá recusar dispositivos que tentem ligar-se utilizando as versões TLS 1.0 e 1.1. Da mesma forma, o aperto de mão TLS será recusado se a mensagem HELLO do cliente do dispositivo não enumerar nenhuma das [cifras recomendadas](#recommended-ciphers).

> [!NOTE]
> A `minTlsVersion` propriedade é apenas de leitura e não pode ser alterada uma vez que o seu recurso DPS é criado. Por isso, é essencial que teste e valide corretamente que *todos os* seus dispositivos IoT são compatíveis com TLS 1.2 e as [cifras recomendadas](#recommended-ciphers) com antecedência.

## <a name="supported-regions"></a>Regiões suportadas

Podem ser criadas nas seguintes regiões as instâncias de DPS iot que exijam a utilização de TLS 1.2:

* US Gov - Arizona
* US Gov - Virginia

> [!NOTE]
> Após falhas, a `minTlsVersion` propriedade do seu DPS permanecerá eficaz na região geo-emparelhada após o fracasso.

## <a name="recommended-ciphers"></a>Cifras recomendadas

As instâncias dPS que são configuradas para aceitar apenas TLS 1.2 também vão impor a utilização das seguintes cifras recomendadas:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

## <a name="use-tls-12-in-the-iot-sdks"></a>Utilize TLS 1.2 nos SDKs IoT

Utilize os links abaixo para configurar TLS 1.2 e permitir cifras nos SDKs do cliente Azure IoT.

| Idioma | Versões suportando TLS 1.2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Etiqueta 2019-12-11 ou mais recente            | [Ligação](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Ligação](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Ligação](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Ligação](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Ligação](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-with-iot-edge"></a>Utilize TLS 1.2 com borda ioT

Os dispositivos IoT Edge podem ser configurados para utilizar TLS 1.2 quando comunicarem com o IoT Hub e o DPS. Para o efeito, utilize a página de [documentação IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).