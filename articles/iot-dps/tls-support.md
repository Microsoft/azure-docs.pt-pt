---
title: Suporte de serviço de fornecimento de dispositivos Azure IoT (DPS)
description: Melhores práticas na utilização de ligações TLS seguras para dispositivos e serviços que comunicam com o Serviço de Provisionamento de Dispositivos IoT (DPS)
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: wesmc
ms.openlocfilehash: 3a8910cf0e81bd041d74ef95f45220f1c1e0b34c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91761299"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Suporte TLS no Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS)

O DPS utiliza [a Segurança da Camada de Transporte (TLS)](http://wikipedia.org/wiki/Transport_Layer_Security) para proteger as ligações a partir de dispositivos IoT. 

As versões atuais do protocolo TLS suportadas por DPS são: 
* TLS 1.2

Os TLS 1.0 e 1.1 são considerados legados e estão previstos para a depreciação. Para obter mais informações, consulte [deprecisando TLS 1.0 e 1.1 para IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). 

## <a name="restrict-connections-to-tls-12"></a>Restringir as ligações ao TLS 1.2

Para uma maior segurança, é aconselhável configurar as suas instâncias DPS *apenas* para permitir ligações do cliente do dispositivo que utilizem a versão 1.2 do TLS e para impor a utilização de [cifras recomendadas](#recommended-ciphers).

Para isso, forja um novo recurso DPS que define a `minTlsVersion` propriedade `1.2` para a especificação de recursos DPS do seu modelo de DPS do seu modelo de recurso Azure. O modelo de exemplo a seguir JSON especifica a `minTlsVersion` propriedade para uma nova instância DPS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-region>",
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

Para obter mais informações sobre a criação de recursos DPS com modelos de Gestor de Recursos, consulte, [confiem DPS com um modelo de Gestor de Recursos Azure](quick-setup-auto-provision-rm.md).

O recurso DPS criado com esta configuração recusará dispositivos que tentem ligar-se através das versões TLS 1.0 e 1.1. Da mesma forma, o aperto de mão TLS será recusado se a mensagem HELLO do cliente do dispositivo não enumerar nenhuma das [cifras recomendadas](#recommended-ciphers).

> [!NOTE]
> A `minTlsVersion` propriedade é apenas de leitura e não pode ser alterada uma vez que o seu recurso DPS é criado. Por isso, é essencial que teste e valide corretamente que *todos os* seus dispositivos IoT sejam compatíveis com tls 1.2 e as [cifras recomendadas](#recommended-ciphers) com antecedência.


> [!NOTE]
> Após os failovers, a `minTlsVersion` propriedade do seu DPS permanecerá eficaz na região geo-emparelhada após o failover.

## <a name="recommended-ciphers"></a>Cifras recomendadas

As instâncias DPS configuradas para aceitar apenas TLS 1.2 também aplicam a utilização das seguintes suítes cifra:


| Suítes de cifra recomendadas TLS 1.2 |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |


### <a name="legacy-cipher-suites"></a>Suítes de cifra legacy 

Estas suítes de cifra ainda são suportadas por DPS, mas serão amortizadas. Utilize as suítes de cifra recomendadas acima, se possível.

| Opção #1 (melhor segurança) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| Opção #2 (melhor desempenho) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>Utilizar TLS 1.2 nos SDKs IoT

Utilize os links abaixo para configurar tls 1.2 e tenha sido permitida a cifras nos SDKs clienteS Azure IoT.

| Linguagem | Versões que suportam TLS 1.2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 ou mais recente            | [Ligação](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Ligação](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Ligação](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Ligação](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Ligação](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>Use TLS 1.2 com IoT Hub

O IoT Hub pode ser configurado para utilizar o TLS 1.2 quando se comunica com dispositivos. Para obter mais informações, consulte [deprecisando TLS 1.0 e 1.1 para IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md).

## <a name="use-tls-12-with-iot-edge"></a>Use TLS 1.2 com IoT Edge

Os dispositivos IoT Edge podem ser configurados para utilizar o TLS 1.2 quando comunicam com o IoT Hub e o DPS. Para mais informações, consulte a página de [documentação IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).
