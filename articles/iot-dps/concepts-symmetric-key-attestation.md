---
title: Serviço de Azure aprovisionamento de dispositivos do IoT Hub - atestado de chave simétrica
description: Este artigo fornece uma descrição geral conceptual do atestado de chave simétrica com o serviço de aprovisionamento de dispositivos de IoT.
author: wesmc7777
ms.author: wesmc
ms.date: 08/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 2f6e1e1a27e32e567cf0eaa8ff7a99046ed81bbe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746259"
---
# <a name="symmetric-key-attestation"></a>Atestado de chave simétrica

Este artigo descreve o processo de atestado de identidade ao utilizar as chaves simétricas com o serviço de aprovisionamento de dispositivos. 

Atestado de chave simétrico é uma abordagem simples para autenticar um dispositivo com uma instância do serviço aprovisionamento de dispositivos. Este método de atestado representa uma experiência de "Hello world" para os desenvolvedores que são novos para aprovisionamento de dispositivos, ou não têm requisitos de segurança restritos. Atestado de dispositivo utilizando um [TPM](concepts-tpm-attestation.md) ou uma [certificado X.509](concepts-security.md#x509-certificates) é mais seguro e devem ser utilizados para os mais rigorosos requisitos de segurança.

Inscrições de chave simétricas também fornecem uma maneira excelente para dispositivos legados, com a funcionalidade de segurança limitada, para inicializar para a cloud através do Azure IoT. Para obter mais informações sobre o atestado de chave simétrica com dispositivos legados, veja [como utilizar as chaves simétricas com dispositivos legados](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Criação da chave simétrica

Por predefinição, o serviço aprovisionamento de dispositivos cria o novo chaves simétricas com um comprimento padrão de 32 bytes quando as novas inscrições são guardadas com o **gerar automaticamente chaves** opção ativada.

![Chaves simétricas de gerar automaticamente](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Também pode fornecer suas próprias chaves simétricas para inscrições ao desativar esta opção. Ao especificar as suas próprias chaves simétricas, as suas chaves tem de ter um comprimento de chave entre 16 bytes e de 64 bytes. Além disso, as chaves simétricas devem ser fornecidas no formato Base64 válido.



## <a name="detailed-attestation-process"></a>Processo de atestado detalhadas

Atestado de chave simétrico com o serviço de aprovisionamento de dispositivos é efetuado utilizando o mesmo [tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-token-structure) suportados pelo hubs IoT para identificar os dispositivos. Estes tokens de segurança são [tokens de assinatura de acesso partilhado (SAS)](../service-bus-messaging/service-bus-sas.md). 

Os tokens SAS têm um hash *assinatura* que é criada ao utilizar a chave simétrica. A assinatura é recriada por serviço de aprovisionamento de dispositivos para verificar se um token de segurança apresentado durante o atestado é autêntico ou não.

SAS tokens tem o seguinte formato:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Aqui estão os componentes de cada token:

| Value | Descrição |
| --- | --- |
| {signature} |Uma cadeia de caracteres de assinatura de HMAC-SHA256. Para inscrições individuais, esta assinatura é produzida utilizando a chave simétrica (primária ou secundária) para efetuar o hash. Para os grupos de inscrição, uma chave derivada da chave de grupo de inscrição é utilizada para efetuar o hash. O hash é executado numa mensagem do formulário: `URL-encoded-resourceURI + "\n" + expiry`. **Importante**: A chave tem possível descodificar a partir de base64 antes de ser usado para realizar a computação do HMAC-SHA256. Além disso, o resultado de assinatura tem de ser codificados de URL. |
| {resourceURI} |URI do ponto final do registo que pode ser acessado com este token, começando com o ID de âmbito para a instância do serviço aprovisionamento de dispositivos. Por exemplo, `{Scope ID}/registrations/{Registration ID}` |
| {expiry} |UTF8 cadeias de caracteres para o número de segundos desde a UTC de 00:00:00 "Epoch" em 1 de Janeiro de 1970. |
| {URL-encoded-resourceURI} |Caso inferiores a codificação URL do recurso minúsculas URI |
| {policyName} |O nome da política de acesso partilhado ao qual se refere este token. É o nome da política utilizado durante o aprovisionamento com o atestado de chave simétrico **registo**. |

Quando um dispositivo é atestar com uma inscrição individual, o dispositivo utiliza a chave simétrica definida na entrada de inscrição individual para criar a assinatura hash para o token SAS.

Para obter exemplos de código que criar um token SAS, consulte [Tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

A criação de tokens de segurança para o atestado de chave simétrico é suportada pelo SDK de C do IoT do Azure. Para obter um exemplo usando o SDK de C do Azure IoT para atestar com uma inscrição individual, veja [aprovisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Inscrições em grupo

As chaves simétricas para inscrições de grupo não são utilizadas diretamente pelos dispositivos durante o aprovisionamento. Em vez disso, os dispositivos que pertencem a uma inscrição de grupo aprovisionar com uma chave derivada de dispositivo. 

Em primeiro lugar, um ID de registo único é definido para cada dispositivo atestar com um grupo de inscrição. Carateres válidos para o ID de registo são minúsculas, carateres alfanuméricos e de traço ('-'). Este ID de registo deve ser algo exclusivo que identifica o dispositivo. Por exemplo, um dispositivo legado pode não suportar várias funcionalidades de segurança. O dispositivo legado só pode ter um endereço MAC ou o número de série disponível para identificar exclusivamente esse dispositivo. Nesse caso, um ID de registo pode ser composto o endereço MAC e o número de série semelhante ao seguinte:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Neste exemplo exato é utilizado na [como aprovisionar dispositivos legados com chaves simétricas](how-to-legacy-device-symm-key.md) artigo.

Assim que tiver sido definido um ID de registo para o dispositivo, a chave simétrica para o grupo de inscrição é usada para computar um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) hash do ID de registo para produzir uma chave derivada de dispositivo. O hash de ID de registo pode ser executado com o seguinte código do c#:

```C#
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

```C#
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

A chave resultante do dispositivo, em seguida, é utilizada para gerar um token SAS para ser utilizado para fins de atestado. Cada dispositivo num grupo de inscrição é necessário para atestar através de um token de segurança gerado a partir de uma chave derivada exclusiva. A chave simétrica do grupo de inscrição não pode ser utilizada diretamente para fins de atestado.

#### <a name="installation-of-the-derived-device-key"></a>Instalação da chave derivada de dispositivo

O ideal é que as chaves de dispositivo são derivadas e instaladas na fábrica de. Esse método garante que a chave de grupo nunca está incluída em qualquer software implementado no dispositivo. Quando o dispositivo é atribuído um endereço MAC ou o número de série, a chave pode ser derivada e injetada no dispositivo, no entanto o fabricante escolhe armazená-los.

Considere o seguinte diagrama mostra uma tabela de chaves de dispositivo gerados numa fábrica por hash cada ID de registo do dispositivo com a chave de inscrição de grupo (**K**). 

![Chaves de dispositivo atribuídas a partir de uma fábrica](./media/concepts-symmetric-key-attestation/key-diversification.png)

A identidade de cada dispositivo é representada pelo ID de registo e a chave de dispositivo derivada que está instalado na fábrica. Chave do dispositivo nunca é copiada para outra localização e a chave de grupo nunca é armazenada num dispositivo.

Se as chaves de dispositivo não estão instaladas na fábrica de um [módulo de hardware de segurança HSM](concepts-security.md#hardware-security-module) deve ser utilizado para armazenar em segurança a identidade de dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Agora que tenha um entendimento do atestado de chave simétrica, veja os artigos seguintes para obter mais informações:

* [Quickstart: Aprovisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md)
* [Saiba mais sobre os conceitos de aprovisionamento automático](./concepts-auto-provisioning.md)
* [Começar a utilizar o aprovisionamento automático](./quick-setup-auto-provision.md) 
