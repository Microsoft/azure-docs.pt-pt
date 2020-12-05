---
title: Suporte Azure IoT Hub TLS
description: Saiba mais sobre a utilização de ligações TLS seguras para dispositivos e serviços que comunicam com o IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jlian
ms.openlocfilehash: f4438aebcb81d665a19a595ac7ade4fea27fc43f
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621013"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>Suporte de segurança da camada de transporte (TLS) no IoT Hub

O IoT Hub utiliza a Transport Layer Security (TLS) para garantir ligações a partir de dispositivos e serviços IoT. Atualmente são suportadas três versões do protocolo TLS, nomeadamente as versões 1.0, 1.1 e 1.2.

Os TLS 1.0 e 1.1 são considerados legados e estão previstos para a depreciação. Para obter mais informações, consulte [deprecisando TLS 1.0 e 1.1 para IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Para evitar problemas futuros, utilize o TLS 1.2 como a única versão TLS ao ligar-se ao IoT Hub.

## <a name="iot-hubs-server-tls-certificate"></a>Certificado TLS do servidor do IoT Hub

Durante um aperto de mão TLS, o IoT Hub apresenta certificados de servidor com chave RSA para clientes de ligação. A sua raiz é a Baltimore Cybertrust Root CA. Recentemente, lançámos uma alteração no nosso certificado de servidor TLS para que seja agora emitido pelas novas autoridades de certificados intermédios (ICA). Para mais informações, consulte a [atualização do certificado IoT Hub TLS](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/).

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>Certificado de Criptografia de Curva Elíptica (ECC) (pré-visualização)

O certificado TLS do servidor ECC do IoT Hub está disponível para pré-visualização pública. Ao oferecer uma segurança semelhante aos certificados RSA, a validação do certificado ECC (com suites de cifra apenas ECC) utiliza até 40% menos computação, memória e largura de banda. Estas poupanças são importantes para dispositivos IoT devido aos seus perfis e memória mais pequenos, e para suportar casos de utilização em ambientes limitados de largura de banda de rede. 

Para pré-visualizar o certificado de servidor ECC do IoT Hub:

1. [Crie um novo hub IoT com modo de pré-visualização ligado](iot-hub-preview-mode.md).
1. [Configure o seu cliente](#tls-configuration-for-sdk-and-iot-edge) para incluir *apenas* suítes de cifra ECDSA e *excluir* quaisquer unidades RSA. Estas são as suítes de cifra suportadas para a pré-visualização pública do certificado ECC:
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. Ligue o seu cliente ao centro IoT de pré-visualização.

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

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>Configuração TLS para SDK e IoT Edge

Utilize os links abaixo para configurar o TLS 1.2 e permitiu cifras em SDKs cliente IoT Hub.

| Linguagem | Versões que suportam TLS 1.2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 ou mais recente            | [Ligação](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Ligação](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Ligação](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Ligação](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Ligação](https://aka.ms/Tls_Node_SDK_IoT) |

Os dispositivos IoT Edge podem ser configurados para utilizar o TLS 1.2 quando comunicam com o IoT Hub. Para o efeito, utilize a página de [documentação IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).

## <a name="device-authentication"></a>Autenticação do dispositivo

Após um aperto de mão TLS bem sucedido, o IoT Hub pode autenticar um dispositivo utilizando uma chave simétrica ou um certificado X.509. Para a autenticação baseada em certificados, este pode ser qualquer certificado X.509, incluindo ECC. O IoT Hub valida o certificado com a impressão digital ou a autoridade de certificados (CA) que fornece. Para saber mais, consulte [os certificados X.509 suportados.](iot-hub-devguide-security.md#supported-x509-certificates)

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>Negociação do comprimento máximo do fragmento TLS (pré-visualização)

O IoT Hub também apoia a negociação do comprimento máximo do fragmento TLS, que por vezes é conhecida como negociação do tamanho do quadro TLS. Esta funcionalidade está em pré-visualização pública. 

Utilize esta função para especificar o comprimento máximo do fragmento de texto simples para um valor inferior ao padrão de 2^14 bytes. Uma vez negociado, o IoT Hub e o cliente começam a fragmentar mensagens para garantir que todos os fragmentos são menores do que o comprimento negociado. Este comportamento é útil para calcular ou remexame em dispositivos constrangidos. Para saber mais, consulte a especificação oficial de [extensão TLS](https://tools.ietf.org/html/rfc6066#section-4).

O suporte oficial da SDK para esta funcionalidade de pré-visualização pública ainda não está disponível. Para começar

1. [Crie um novo hub IoT com modo de pré-visualização ligado](iot-hub-preview-mode.md).
1. Ao utilizar o OpenSSL, ligue [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) para especificar o tamanho do fragmento.
1. Ligue o seu cliente ao IoT Hub de pré-visualização.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a segurança do IoT Hub e o controlo de acessos, consulte [o Control access to IoT Hub](iot-hub-devguide-security.md).
- Para saber mais sobre a utilização do certificado X509 para autenticação do dispositivo, consulte [a autenticação do dispositivo utilizando certificados X.509 CA](iot-hub-x509ca-overview.md)