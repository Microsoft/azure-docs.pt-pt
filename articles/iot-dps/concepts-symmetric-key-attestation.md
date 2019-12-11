---
title: Serviço de provisionamento de dispositivos no Hub IoT do Azure-atestado de chave simétrica
description: Este artigo fornece uma visão geral conceitual do atestado de chave simétrica usando o DPS (serviço de provisionamento de dispositivos IoT).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 0e3d343c0a68dd527e4e8e8d23e5b3843a216a78
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975300"
---
# <a name="symmetric-key-attestation"></a>Atestado de chave simétrica

Este artigo descreve o processo de atestado de identidade ao usar chaves simétricas com o serviço de provisionamento de dispositivos. 

O atestado de chave simétrica é uma abordagem simples para autenticar um dispositivo com uma instância do serviço de provisionamento de dispositivos. Esse método de atestado representa uma experiência de "Olá, mundo" para desenvolvedores que são novos no provisionamento de dispositivos ou que não têm requisitos de segurança rígidos. O atestado de dispositivo usando um [TPM](concepts-tpm-attestation.md) ou um [certificado X. 509](concepts-security.md#x509-certificates) é mais seguro e deve ser usado para requisitos de segurança mais rígidos.

Os registros de chave simétrica também fornecem uma ótima maneira de dispositivos herdados, com funcionalidade de segurança limitada, para inicializar a nuvem por meio do Azure IoT. Para obter mais informações sobre o atestado de chave simétrica com dispositivos herdados, consulte [como usar chaves simétricas com dispositivos herdados](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Criação de chave simétrica

Por padrão, o serviço de provisionamento de dispositivos cria novas chaves simétricas com um comprimento padrão de 32 bytes quando novos registros são salvos com a opção **gerar chaves automaticamente** habilitada.

![Gerar automaticamente chaves simétricas](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Você também pode fornecer suas próprias chaves simétricas para registros desabilitando essa opção. Ao especificar suas próprias chaves simétricas, suas chaves devem ter um comprimento de chave entre 16 bytes e 64 bytes. Além disso, as chaves simétricas devem ser fornecidas no formato Base64 válido.



## <a name="detailed-attestation-process"></a>Processo de atestado detalhado

O atestado de chave simétrica com o serviço de provisionamento de dispositivos é executado usando os mesmos [tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-token-structure) com suporte dos hubs IOT para identificar dispositivos. Esses tokens de segurança são [tokens de assinatura de acesso compartilhado (SAS)](../service-bus-messaging/service-bus-sas.md). 

Tokens SAS têm uma *assinatura* com hash que é criada usando a chave simétrica. A assinatura é recriada pelo serviço de provisionamento de dispositivos para verificar se um token de segurança apresentado durante o atestado é autêntico ou não.

Os tokens SAS têm o seguinte formato:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Estes são os componentes de cada token:

| Valor | Descrição |
| --- | --- |
| {signature} |Uma cadeia de caracteres de assinatura HMAC-SHA256. Para registros individuais, essa assinatura é produzida usando a chave simétrica (primária ou secundária) para executar o hash. Para grupos de registro, uma chave derivada da chave do grupo de registro é usada para executar o hash. O hash é executado em uma mensagem do formato: `URL-encoded-resourceURI + "\n" + expiry`. **Importante**: a chave deve ser decodificada a partir de Base64 antes de ser usada para executar a computação HMAC-SHA256. Além disso, o resultado da assinatura deve ser codificado por URL. |
| {resourceURI} |URI do ponto de extremidade de registro que pode ser acessado com esse token, começando com a ID de escopo para a instância do serviço de provisionamento de dispositivos. Por exemplo, `{Scope ID}/registrations/{Registration ID}` |
| expiração |Cadeias de caracteres UTF8 para o número de segundos desde a época 00:00:00 UTC em 1 de janeiro de 1970. |
| {URL-encoded-resourceURI} |Codificação de URL em minúsculas do URI de recurso em letras minúsculas |
| {policyName} |O nome da política de acesso compartilhado à qual esse token se refere. O nome da política usado ao provisionar com o atestado de chave simétrica é o **registro**. |

Quando um dispositivo está atestando com um registro individual, o dispositivo usa a chave simétrica definida na entrada de registro individual para criar a assinatura com hash para o token SAS.

Para obter exemplos de código que criam um token SAS, consulte [tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

A criação de tokens de segurança para atestado de chave simétrica é suportada pelo SDK do Azure IoT C. Para obter um exemplo usando o SDK do Azure IoT C para atestar com um registro individual, consulte [provisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Registros de grupo

As chaves simétricas para registros de grupo não são usadas diretamente por dispositivos durante o provisionamento. Em vez disso, os dispositivos que pertencem a um grupo de registro provisionam usando uma chave de dispositivo derivada. 

Primeiro, uma ID de registro exclusiva é definida para cada atestado de dispositivo com um grupo de registro. Os caracteres válidos para a ID de registro são alfanuméricos minúsculos e Dash ('-'). Essa ID de registro deve ser algo exclusivo que identifica o dispositivo. Por exemplo, um dispositivo herdado pode não oferecer suporte a muitos recursos de segurança. O dispositivo herdado pode ter apenas um endereço MAC ou número de série disponível para identificar exclusivamente esse dispositivo. Nesse caso, uma ID de registro pode ser composta pelo endereço MAC e pelo número de série semelhante ao seguinte:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Esse exemplo exato é usado no artigo [como provisionar dispositivos herdados usando chaves simétricas](how-to-legacy-device-symm-key.md) .

Depois que uma ID de registro tiver sido definida para o dispositivo, a chave simétrica para o grupo de registro será usada para computar um hash [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) da ID de registro para produzir uma chave de dispositivo derivada. O hash da ID de registro pode ser executado com o seguinte C# código:

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

A chave de dispositivo resultante é usada para gerar um token SAS a ser usado para atestado. Cada dispositivo em um grupo de registro é necessário para atestar usando um token de segurança gerado por meio de uma chave derivada exclusiva. A chave simétrica do grupo de registro não pode ser usada diretamente para atestado.

#### <a name="installation-of-the-derived-device-key"></a>Instalação da chave do dispositivo derivado

O ideal é que as chaves do dispositivo sejam derivadas e instaladas na fábrica. Esse método garante que a chave do grupo nunca seja incluída em nenhum software implantado no dispositivo. Quando o dispositivo recebe um endereço MAC ou um número de série, a chave pode ser derivada e injetada no dispositivo, no entanto, o fabricante opta por armazená-lo.

Considere o diagrama a seguir que mostra uma tabela de chaves de dispositivo geradas em uma fábrica com o hash de cada ID de registro de dispositivo com a chave de registro de grupo (**K**). 

![Chaves de dispositivo atribuídas de uma fábrica](./media/concepts-symmetric-key-attestation/key-diversification.png)

A identidade de cada dispositivo é representada pela ID de registro e pela chave de dispositivo derivada instalada na fábrica. A chave do dispositivo nunca é copiada para outro local e a chave do grupo nunca é armazenada em um dispositivo.

Se as chaves de dispositivo não estiverem instaladas na fábrica, um [HSM de módulo de segurança de hardware](concepts-security.md#hardware-security-module) deverá ser usado para armazenar com segurança a identidade do dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que você tem uma compreensão do atestado de chave simétrica, confira os seguintes artigos para saber mais:

* [Início rápido: provisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md)
* [Saiba mais sobre os conceitos no provisionamento automático](./concepts-auto-provisioning.md)
* [Começar a usar o provisionamento automático](./quick-setup-auto-provision.md) 
