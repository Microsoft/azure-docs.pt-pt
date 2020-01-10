---
title: Pontos de extremidade de segurança no serviço de provisionamento de dispositivos IoT | Microsoft Docs
description: Conceitos-como controlar o acesso ao DPS (serviço de provisionamento de dispositivos) IoT para aplicativos de back-end. Inclui informações sobre tokens de segurança.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: 2a7e0932d226b1533c039b8529c2c11de06cf525
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453813"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Controlar o acesso ao serviço de provisionamento de dispositivos no Hub IoT do Azure

Este artigo descreve as opções para proteger o serviço de provisionamento de dispositivos IoT. O serviço de provisionamento usa *permissões* para conceder acesso a cada ponto de extremidade. As permissões limitam o acesso a uma instância de serviço com base na funcionalidade.

Este artigo descreve:

* As diferentes permissões que você pode conceder a um aplicativo de back-end para acessar o serviço de provisionamento.
* O processo de autenticação e os tokens que ele usa para verificar as permissões.

### <a name="when-to-use"></a>Quando utilizar

Você deve ter as permissões apropriadas para acessar qualquer um dos pontos de extremidade do serviço de provisionamento. Por exemplo, um aplicativo de back-end deve incluir um token que contém credenciais de segurança junto com cada mensagem que ele envia para o serviço.

## <a name="access-control-and-permissions"></a>Controle de acesso e permissões

Você pode conceder [permissões](#device-provisioning-service-permissions) das seguintes maneiras:

* **Políticas de autorização de acesso compartilhado**. As políticas de acesso compartilhado podem conceder qualquer combinação de [permissões](#device-provisioning-service-permissions). Você pode definir políticas no [portal do Azure][lnk-management-portal]ou programaticamente usando as [APIs REST do serviço de provisionamento de dispositivos][lnk-resource-provider-apis]. Um serviço de provisionamento criado recentemente tem a seguinte política padrão:

* **provisioningserviceowner**: política com todas as permissões.

> [!NOTE]
> Consulte [permissões](#device-provisioning-service-permissions) para obter informações detalhadas.

## <a name="authentication"></a>Autenticação

O serviço de provisionamento de dispositivos no Hub IoT do Azure concede acesso a pontos de extremidade, verificando um token em relação às políticas de acesso compartilhado. As credenciais de segurança, como chaves simétricas, nunca são enviadas pela conexão.

> [!NOTE]
> O provedor de recursos do serviço de provisionamento de dispositivos é protegido por meio de sua assinatura do Azure, assim como todos os provedores no [Azure Resource Manager][lnk-azure-resource-manager].

Para obter mais informações sobre como construir e usar tokens de segurança, consulte a próxima seção.

O HTTP é o único protocolo com suporte e implementa a autenticação, incluindo um token válido no cabeçalho de solicitação de **autorização** .

#### <a name="example"></a>Exemplo
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Os [SDKs do serviço de provisionamento de dispositivos IOT do Azure][lnk-sdks] geram tokens automaticamente ao se conectar ao serviço.

## <a name="security-tokens"></a>Tokens de segurança

O serviço de provisionamento de dispositivos usa tokens de segurança para autenticar serviços para evitar o envio de chaves na conexão. Além disso, os tokens de segurança são limitados em tempo de validade e escopo. Os [SDKs do serviço de provisionamento de dispositivos IOT do Azure][lnk-sdks] geram tokens automaticamente sem exigir nenhuma configuração especial. Alguns cenários exigem que você gere e use tokens de segurança diretamente. Esses cenários incluem o uso direto da superfície HTTP.

### <a name="security-token-structure"></a>Estrutura do token de segurança

Você usa tokens de segurança para conceder acesso limitado a tempo para serviços a uma funcionalidade específica no serviço de provisionamento de dispositivos IoT. Para obter autorização para se conectar ao serviço de provisionamento, os serviços devem enviar tokens de segurança assinados com um acesso compartilhado ou uma chave simétrica.

Um token assinado com uma chave de acesso compartilhado concede acesso a todas as funcionalidades associadas às permissões de política de acesso compartilhado. 

O token de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Estes são os valores esperados:

| Valor | Descrição |
| --- | --- |
| {signature} |Uma cadeia de caracteres de assinatura HMAC-SHA256 no formato: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: a chave é decodificada da Base64 e usada como chave para executar a computação HMAC-SHA256.|
| expiração |Cadeias de caracteres UTF8 para o número de segundos desde a época 00:00:00 UTC em 1 de janeiro de 1970. |
| {URL-encoded-resourceURI} | Codificação de URL em minúsculas do URI de recurso em minúsculas. Prefixo do URI (por segmento) dos pontos de extremidade que podem ser acessados com esse token, começando com o nome do host do serviço de provisionamento de dispositivos IoT (sem protocolo). Por exemplo, `mydps.azure-devices-provisioning.net`. |
| {policyName} |O nome da política de acesso compartilhado à qual esse token se refere. |

**Observação no prefixo**: o prefixo URI é computado por segmento e não por caractere. Por exemplo `/a/b` é um prefixo para `/a/b/c`, mas não para `/a/bc`.

O trecho do node. js a seguir mostra uma função chamada **generateSasToken** que computa o token do `resourceUri, signingKey, policyName, expiresInMins`de entradas. As seções a seguir detalham como inicializar as diferentes entradas para os diferentes casos de uso de token.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Como uma comparação, o código Python equivalente para gerar um token de segurança é:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Como o tempo de validade do token é validado em computadores do serviço de provisionamento de dispositivos IoT, a descompasso no relógio do computador que gera o token deve ser mínima.

### <a name="use-security-tokens-from-service-components"></a>Usar tokens de segurança de componentes de serviço

Os componentes de serviço só podem gerar tokens de segurança usando políticas de acesso compartilhado, concedendo as permissões apropriadas, conforme explicado anteriormente.

Aqui estão as funções de serviço expostas nos pontos de extremidade:

| Ponto Final | Funcionalidade |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Fornece operações de registro de dispositivo com o serviço de provisionamento de dispositivos. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Fornece operações para gerenciar grupos de registro de dispositivo. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Fornece operações para recuperar e gerenciar o status dos registros do dispositivo. |


Por exemplo, um serviço gerado usando uma política de acesso compartilhado criada previamente chamada **enrollmentread** criaria um token com os seguintes parâmetros:

* URI do recurso: `{mydps}.azure-devices-provisioning.net`,
* chave de assinatura: uma das chaves da política de `enrollmentread`,
* nome da política: `enrollmentread`,
* qualquer tempo de expiração. backup

![Criar uma política de acesso compartilhado para a instância do serviço de provisionamento de dispositivos no portal][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, que concederia acesso para ler todos os registros de registro, seria:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Tópicos de referência:

Os tópicos de referência a seguir fornecem a você mais informações sobre como controlar o acesso ao serviço de provisionamento de dispositivos IoT.

### <a name="device-provisioning-service-permissions"></a>Permissões do serviço de provisionamento de dispositivos

A tabela a seguir lista as permissões que você pode usar para controlar o acesso ao serviço de provisionamento de dispositivos IoT.

| Permissão | Notas |
| --- | --- |
| **ServiceConfig** |Concede acesso para alterar as configurações de serviço. <br/>Essa permissão é usada pelos serviços de nuvem de back-end. |
| **EnrollmentRead** |Concede acesso de leitura aos registros do dispositivo e aos grupos de registro. <br/>Essa permissão é usada pelos serviços de nuvem de back-end. |
| **EnrollmentWrite** |Concede acesso de gravação aos registros do dispositivo e aos grupos de registro. <br/>Essa permissão é usada pelos serviços de nuvem de back-end. |
| **RegistrationStatusRead** |Concede acesso de leitura ao status de registro do dispositivo. <br/>Essa permissão é usada pelos serviços de nuvem de back-end. |
| **RegistrationStatusWrite**  |Concede acesso de exclusão ao status de registro do dispositivo. <br/>Essa permissão é usada pelos serviços de nuvem de back-end. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
