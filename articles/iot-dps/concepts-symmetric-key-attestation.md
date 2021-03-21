---
title: Serviço de provisionamento de dispositivos Azure IoT Hub - Atestado de chave simétrica
description: Este artigo fornece uma visão geral conceptual do atestado de chave simétrica utilizando o Serviço de Provisionamento de Dispositivos IoT (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 994c2c3124d6822f047af942268ad7a401d5a976
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531564"
---
# <a name="symmetric-key-attestation"></a>Atestado de chave simétrica

Este artigo descreve o processo de atestado de identidade ao utilizar chaves simétricas com o Serviço de Provisionamento de Dispositivos. 

A tecla de teclas simétricas é uma abordagem simples para autenticar um dispositivo com uma instância de Serviço de Provisionamento de Dispositivos. Este método de atestado representa uma experiência "Hello world" para desenvolvedores que são novos no fornecimento de dispositivos, ou não têm requisitos de segurança rigorosos. O atestado do dispositivo utilizando um [certificado TPM](concepts-tpm-attestation.md) ou [X.509](concepts-x509-attestation.md) é mais seguro e deve ser utilizado para requisitos de segurança mais rigorosos.

As inscrições chave simétricas também fornecem uma ótima maneira para dispositivos antigos, com funcionalidade de segurança limitada, para a bota para a nuvem via Azure IoT. Para obter mais informações sobre a teclas simétricas com dispositivos antigos, consulte [Como utilizar chaves simétricas com dispositivos legados](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Criação de chave simétrica

Por predefinição, o Serviço de Provisionamento de Dispositivos cria novas teclas simétricas com um comprimento predefinido de 64 bytes quando as novas matrículas são guardadas com a opção **de teclas de geração automática** ativada.

![Gerar automaticamente teclas simétricas](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Também pode fornecer as suas próprias chaves simétricas para as matrículas, desativando esta opção. Ao especificar as suas próprias teclas simétricas, as suas chaves devem ter um comprimento de chave entre 16 bytes e 64 bytes. Além disso, as teclas simétricas devem ser fornecidas em formato Base64 válido.



## <a name="detailed-attestation-process"></a>Processo de atestado detalhado

A teta de chave simétrica com o Serviço de Provisionamento de Dispositivos é realizada utilizando os [mesmos tokens de segurança suportados](../iot-hub/iot-hub-devguide-security.md#security-token-structure) por hubs IoT para identificar dispositivos. Estes fichas de segurança são fichas [de assinatura de acesso partilhado (SAS).](../service-bus-messaging/service-bus-sas.md) 

Os tokens SAS têm uma *assinatura* hashed que é criada usando a chave simétrica. A assinatura é recriada pelo Serviço de Provisionamento de Dispositivos para verificar se um sinal de segurança apresentado durante o atestado é autêntico ou não.

Os tokens SAS têm a seguinte forma:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Aqui estão os componentes de cada símbolo:

| Valor | Descrição |
| --- | --- |
| {assinatura} |Uma corda de assinatura HMAC-SHA256. Para as matrículas individuais, esta assinatura é produzida utilizando a chave simétrica (primária ou secundária) para executar o haxixe. Para os grupos de inscrição, uma chave derivada da chave do grupo de inscrição é usada para executar o haxixe. O haxixe é realizado numa mensagem do formulário: `URL-encoded-resourceURI + "\n" + expiry` . **Importante**: A chave deve ser descodificada da base64 antes de ser utilizada para a realização do cálculo HMAC-SHA256. Além disso, o resultado da assinatura deve ser codificado por URL. |
| {resourceURI} |URI do ponto final de registo que pode ser acedido com este token, começando com iD de âmbito para a instância do Serviço de Provisionamento de Dispositivos. Por exemplo, `{Scope ID}/registrations/{Registration ID}` |
| {expiração} |UtF8 strings por número de segundos desde a época 00:00:00 UTC em 1 de janeiro de 1970. |
| {URL-codificado-recursosURI} |Codificação de URL minúscula do recurso uri de menor caso |
| {políticaName} |O nome da política de acesso partilhado a que se refere este símbolo. O nome da apólice utilizado no provisionamento com aestestação de chave simétrica é **o registo**. |

Quando um dispositivo está a atestar uma inscrição individual, o dispositivo utiliza a chave simétrica definida na entrada de inscrição individual para criar a assinatura hashed para o token SAS.

Para exemplos de código que criam um token SAS, consulte [Tokens de Segurança](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

A criação de fichas de segurança para a estação de chave simétrica é suportada pelo Azure IoT C SDK. Por exemplo, utilizando o Azure IoT C SDK para atestar com uma inscrição individual, consulte [Disposição de um dispositivo simulado com teclas simétricas](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Inscrições em Grupo

As chaves simétricas para as matrículas em grupo não são utilizadas diretamente pelos dispositivos no fornecimento. Em vez disso, dispositivos que pertencem a uma disposição do grupo de inscrição utilizando uma chave de dispositivo derivada. 

Em primeiro lugar, é definido um ID de registo único para cada dispositivo que ateste com um grupo de inscrição. Os caracteres válidos para o ID de registo são alfanuméricos minúsculos e traço ('-'). Este ID de registo deve ser algo único que identifique o dispositivo. Por exemplo, um dispositivo antigo pode não suportar muitas funcionalidades de segurança. O dispositivo legado só pode ter um endereço MAC ou número de série disponível para identificar exclusivamente esse dispositivo. Nesse caso, um ID de registo pode ser composto pelo endereço MAC e número de série semelhante ao seguinte:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Este exemplo exato é utilizado no [Como providenciar dispositivos antigos utilizando artigo de teclas simétricas.](how-to-legacy-device-symm-key.md)

Uma vez definido um ID de registo para o dispositivo, a chave simétrica para o grupo de inscrição é usada para calcular um haxixe [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo para produzir uma chave de dispositivo derivada. O hashing do ID de registo pode ser efetuado com o seguinte código C#:

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

A chave do dispositivo resultante é então utilizada para gerar um token SAS para ser usado para atestado. Cada dispositivo de um grupo de inscrição é necessário para atestar usando um símbolo de segurança gerado a partir de uma chave derivada única. A chave simétrica do grupo de inscrição não pode ser utilizada diretamente para a atestado.

#### <a name="installation-of-the-derived-device-key"></a>Instalação da chave do dispositivo derivado

Idealmente, as chaves do dispositivo são derivadas e instaladas na fábrica. Este método garante que a chave de grupo nunca é incluída em qualquer software implantado no dispositivo. Quando o dispositivo é atribuído a um endereço MAC ou número de série, a chave pode ser derivada e injetada no dispositivo, no entanto o fabricante opta por armazená-lo.

Considere o seguinte diagrama que mostra uma tabela de chaves do dispositivo geradas numa fábrica, hashing cada ID de registo do dispositivo com a chave de inscrição do grupo **(K**). 

![Chaves do dispositivo atribuídas a partir de uma fábrica](./media/concepts-symmetric-key-attestation/key-diversification.png)

A identidade de cada dispositivo é representada pela chave de identificação de registo e de dispositivo derivado que está instalada na fábrica. A chave do dispositivo nunca é copiada para outro local e a chave de grupo nunca é armazenada num dispositivo.

Se as teclas do dispositivo não forem instaladas na fábrica, deve ser utilizado um [módulo de segurança de hardware HSM](concepts-service.md#hardware-security-module) para armazenar a identidade do dispositivo de forma segura.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma compreensão do atestado de Chave Simétrica, confira os seguintes artigos para saber mais:

* [Guia de Início Rápido: aprovisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md)
* [Conheça os conceitos de provisionamento](about-iot-dps.md#provisioning-process)
* [Começar a usar o fornecimento automático](./quick-setup-auto-provision.md) 
