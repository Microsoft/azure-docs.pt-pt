---
title: Serviço de Provisionamento de Dispositivos Hub Azure IoT - Atestação de chaves simétricas
description: Este artigo fornece uma visão geral conceptual do atestado de chave simétrica utilizando o Serviço de Provisionamento de Dispositivos IoT (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 0e3d343c0a68dd527e4e8e8d23e5b3843a216a78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79271515"
---
# <a name="symmetric-key-attestation"></a>Atestado de chave simétrica

Este artigo descreve o processo de atestade de identidade ao utilizar chaves simétricas com o Serviço de Provisionamento de Dispositivos. 

O atestado de chaves simétricas é uma abordagem simples para autenticar um dispositivo com uma instância do Serviço de Provisionamento de Dispositivos. Este método de atestado representa uma experiência "Hello world" para desenvolvedores que são novos no fornecimento de dispositivos, ou não têm requisitos de segurança rigorosos. O atestado do dispositivo utilizando um [TPM](concepts-tpm-attestation.md) ou um [certificado X.509](concepts-security.md#x509-certificates) é mais seguro e deve ser utilizado para requisitos de segurança mais rigorosos.

As principais inscrições simétricas também fornecem uma ótima maneira de dispositivos legados, com funcionalidade de segurança limitada, para botas para a nuvem via Azure IoT. Para obter mais informações sobre a atestação de chaves simétricas com dispositivos legados, consulte [Como utilizar chaves simétricas com dispositivos legados](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Criação simétrica chave

Por predefinição, o Serviço de Provisionamento de Dispositivos cria novas teclas simétricas com um comprimento predefinido de 32 bytes quando novas matrículas são guardadas com a opção de **teclas de geração automática** ativada.

![Auto Generate chaves simétricas](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Também pode fornecer as suas próprias chaves simétricas para as matrículas, desativando esta opção. Ao especificar as suas próprias teclas simétricas, as suas chaves devem ter um comprimento de chave entre 16 bytes e 64 bytes. Além disso, as chaves simétricas devem ser fornecidas em formato Base64 válido.



## <a name="detailed-attestation-process"></a>Processo de atestação detalhado

O atestado da chave simétrica com o Serviço de Provisionamento de Dispositivos é realizado utilizando as mesmas [fichas](../iot-hub/iot-hub-devguide-security.md#security-token-structure) de Segurança suportadas por hubs IoT para identificar dispositivos. Estas fichas de segurança são fichas de assinatura de [acesso partilhado (SAS).](../service-bus-messaging/service-bus-sas.md) 

As fichas SAS têm uma *assinatura* hashed que é criada usando a chave simétrica. A assinatura é recriada pelo Serviço de Fornecimento de Dispositivos para verificar se um token de segurança apresentado durante o atestado é autêntico ou não.

As fichas SAS têm o seguinte formulário:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Aqui estão os componentes de cada símbolo:

| Valor | Descrição |
| --- | --- |
| {assinatura} |Uma corda de assinatura HMAC-SHA256. Para as matrículas individuais, esta assinatura é produzida utilizando a chave simétrica (primária ou secundária) para executar o hash. Para os grupos de inscrição, uma chave derivada da chave do grupo de inscrição é usada para executar o hash. O hash é realizado numa mensagem `URL-encoded-resourceURI + "\n" + expiry`do formulário: . **Importante**: A chave deve ser descodificada a partir do base64 antes de ser utilizada para realizar o cálculo HMAC-SHA256. Além disso, o resultado da assinatura deve ser codificado por URL. |
| {resourceURI} |URI do ponto final de registo que pode ser acedido com este símbolo, começando com o ID de âmbito para a instância do Serviço de Provisionamento de Dispositivos. Por exemplo, `{Scope ID}/registrations/{Registration ID}` |
| {expiração} |UTF8 cordas para o número de segundos desde a época 00:00:00 UTC em 1 de janeiro de 1970. |
| {URL-codificado-recursosURI} |Codificação de URL em maiúscula do recurso minúsculo URI |
| {policyName} |O nome da política de acesso partilhado a que se refere este símbolo. O nome de política utilizado no fornecimento com atestado de chave simétrica é **o registo**. |

Quando um dispositivo está a atestar com uma matrícula individual, o dispositivo utiliza a chave simétrica definida na entrada de matrícula individual para criar a assinatura hashed para o token SAS.

Para exemplos de código que criam uma ficha SAS, consulte [Fichas](../iot-hub/iot-hub-devguide-security.md#security-token-structure)de Segurança .

A criação de fichas de segurança para atestado de chave simétrica é apoiada pelo Azure IoT C SDK. Por exemplo, utilizando o SDK Azure IoT C para atestar com uma matrícula individual, consulte [a Provision um dispositivo simulado com teclas simétricas](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Inscrições em Grupo

As chaves simétricas para as matrículas em grupo não são utilizadas diretamente pelos dispositivos durante o fornecimento. Em vez disso, dispositivos que pertencem a uma disposição de grupo de inscrição utilizando uma chave de dispositivo derivada. 

Em primeiro lugar, é definido um ID de registo único para cada dispositivo que atesta com um grupo de inscrições. Os caracteres válidos para o ID de registo são alfanuméricos minúsculos e traços ('-'). Esta identificação de registo deve ser algo único que identifique o dispositivo. Por exemplo, um dispositivo legado pode não suportar muitas funcionalidades de segurança. O dispositivo legado só pode ter um endereço MAC ou um número de série disponível para identificar exclusivamente esse dispositivo. Nesse caso, pode ser composto um ID de registo do endereço MAC e do número de série semelhante ao seguinte:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Este exemplo exato é usado no Como fornecer dispositivos legados usando o artigo [de teclas simétricas.](how-to-legacy-device-symm-key.md)

Uma vez definido um ID de registo para o dispositivo, a chave simétrica para o grupo de inscrição é usada para calcular um hash [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo para produzir uma chave de dispositivo derivada. O hashing do ID de registo pode ser realizado com o seguinte código C# :

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

A chave do dispositivo resultante é então utilizada para gerar um token SAS para ser utilizado para a atestado. Cada dispositivo de um grupo de inscrição é obrigado a atestar usando um símbolo de segurança gerado a partir de uma chave derivada única. A chave simétrica do grupo de matrícula não pode ser utilizada diretamente para o atestado.

#### <a name="installation-of-the-derived-device-key"></a>Instalação da chave do dispositivo derivado

Idealmente, as chaves do dispositivo são derivadas e instaladas na fábrica. Este método garante que a chave do grupo nunca está incluída em qualquer software implantado no dispositivo. Quando o dispositivo é atribuído a um endereço MAC ou número de série, a chave pode ser derivada e injetada no dispositivo, no entanto o fabricante opta por armazená-lo.

Considere o diagrama seguinte que mostra uma tabela de teclas do dispositivo geradas numa fábrica, apresentando cada id de registo de dispositivo com a chave de inscrição do grupo **(K).** 

![Chaves de dispositivo atribuídas a partir de uma fábrica](./media/concepts-symmetric-key-attestation/key-diversification.png)

A identidade de cada dispositivo é representada pela identificação de registo e chave do dispositivo derivado que está instalada na fábrica. A chave do dispositivo nunca é copiada para outro local e a chave do grupo nunca é armazenada num dispositivo.

Se as chaves do dispositivo não forem instaladas na fábrica, deve ser utilizado um módulo de segurança de [hardware HSM](concepts-security.md#hardware-security-module) para armazenar de forma segura a identidade do dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma compreensão da atesta da Chave Simétrica, confira os seguintes artigos para saber mais:

* [Guia de Início Rápido: aprovisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md)
* [Conheça os conceitos de fornecimento automático](./concepts-auto-provisioning.md)
* [Começar a usar auto-provisionamento](./quick-setup-auto-provision.md) 
