---
title: Pontos finais de segurança no serviço de aprovisionamento de dispositivos de IoT | Documentos da Microsoft
description: Conceitos - como controlar o acesso ao serviço de aprovisionamento de dispositivos de IoT para aplicações de back-end. Inclui informações sobre os tokens de segurança.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: 7ff622ceac9c49eda7ba6bca1a8bb3aaabccb816
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495435"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Controlar o acesso ao serviço de aprovisionamento de dispositivos do Azure IoT Hub

Este artigo descreve as opções para proteger o seu serviço aprovisionamento de dispositivos de IoT. O serviço de aprovisionamento utiliza *permissões* para conceder acesso a cada ponto de extremidade. As permissões limitam o acesso a uma instância de serviço com base na funcionalidade.

Este artigo descreve:

* As permissões diferentes que pode conceder a uma aplicação de back-end para aceder ao seu serviço de aprovisionamento.
* O processo de autenticação e os tokens que ele usa para verificar as permissões.

### <a name="when-to-use"></a>Quando utilizar

Tem de ter permissões adequadas para aceder a qualquer um dos pontos de extremidade de serviço aprovisionamento. Por exemplo, uma aplicação de back-end tem de incluir um token que contém as credenciais de segurança, juntamente com todas as mensagens que envia para o serviço.

## <a name="access-control-and-permissions"></a>Controlo de acesso e permissões

Pode conceder [permissões](#device-provisioning-service-permissions) das seguintes formas:

* **Partilhado políticas de autorização de acesso**. Políticas de acesso partilhado podem conceder a qualquer combinação dos [permissões](#device-provisioning-service-permissions). Pode definir políticas no [portal do Azure][lnk-management-portal], ou por meio de programação, utilizando o [APIs de REST do serviço de aprovisionamento de dispositivo][lnk-resource-provider-apis]. Um serviço de aprovisionamento recém-criado tem a seguinte política predefinida:

* **provisioningserviceowner**: Política com todas as permissões.

> [!NOTE]
> Ver [permissões](#device-provisioning-service-permissions) para obter informações detalhadas.

## <a name="authentication"></a>Authentication

O Azure IoT Hub serviço aprovisionamento de dispositivos concede acesso a pontos finais verificando um token com as diretivas de acesso partilhado. Credenciais de segurança, tais como chaves simétricas, nunca são enviadas durante a transmissão.

> [!NOTE]
> O fornecedor de recursos do serviço aprovisionamento de dispositivos é protegido através da sua subscrição do Azure, assim como todos os fornecedores no [do Azure Resource Manager][lnk-azure-resource-manager].

Para obter mais informações sobre como construir e utilizar tokens de segurança, consulte a secção seguinte.

O HTTP é o único protocolo suportado e ela implementa a autenticação, incluindo um token válido na **autorização** cabeçalho do pedido.

#### <a name="example"></a>Exemplo
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> O [SDKs de serviço de aprovisionamento do Azure IoT dispositivo] [ lnk-sdks] gerar automaticamente os tokens ao ligar ao serviço.

## <a name="security-tokens"></a>Tokens de segurança

O serviço de aprovisionamento de dispositivos utiliza tokens de segurança para autenticar a serviços para evitar o envio de chaves na conexão. Além disso, os tokens de segurança são limitados em âmbito e validade de tempo. [O Azure IoT Device aprovisionamento serviço SDK] [ lnk-sdks] automaticamente gerar tokens sem a necessidade de nenhuma configuração especial. Alguns cenários requerem que gerar e utilizar tokens de segurança diretamente. Tais cenários incluem a utilização direta da superfície HTTP.

### <a name="security-token-structure"></a>Estrutura de token de segurança

Utilizar tokens de segurança para conceder acesso limitado de tempo para os serviços na funcionalidade específico do serviço de aprovisionamento de dispositivos de IoT. Para obter autorização para se ligar ao serviço de aprovisionamento, dos serviços tem de enviar os tokens de segurança assinados com um acesso partilhado ou uma chave simétrica.

Um token assinado com um acesso de concessões de chave de acesso partilhado a todas as funcionalidades associadas com as permissões de política de acesso partilhado. 

O token de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Seguem-se os valores esperados:

| Valor | Descrição |
| --- | --- |
| {signature} |Uma cadeia de caracteres de assinatura HMAC-SHA256 do formulário: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: A chave é descodificar a partir de base64 e usada como chave para realizar a computação do HMAC-SHA256.|
| {expiry} |UTF8 cadeias de caracteres para o número de segundos desde a UTC de 00:00:00 "Epoch" em 1 de Janeiro de 1970. |
| {URL-encoded-resourceURI} | Caso mais baixo-codificação do URL de URI do recurso de minúsculas. Prefixos de URI (por segmento) dos pontos de extremidade que podem ser acessados com este token, começando com o nome de anfitrião do serviço de aprovisionamento de dispositivos IoT (nenhum protocolo). Por exemplo, `mydps.azure-devices-provisioning.net`. |
| {policyName} |O nome da política de acesso partilhado ao qual se refere este token. |

**Tenha em atenção no prefixo**: O prefixo URI é calculado por segmento e não por caractere. Por exemplo `/a/b` é um prefixo para `/a/b/c` mas não para `/a/bc`.

O seguinte trecho de node. js mostra uma função chamada **generateSasToken** que computa o token de entradas `resourceUri, signingKey, policyName, expiresInMins`. As secções seguintes detalham como inicializar as diferentes entradas para os casos de utilização de token diferente.

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

Como comparação, o código de Python equivalente para gerar um token de segurança é:

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
> Uma vez que a validade de tempo do token é validada em máquinas de serviço de aprovisionamento de dispositivos do IoT, descompassos no relógio da máquina que gera o token tem de ser um mínimo.

### <a name="use-security-tokens-from-service-components"></a>Utilizar tokens de segurança de componentes do serviço

Componentes do serviço apenas podem gerar tokens de segurança através de políticas de acesso partilhado conceder as permissões adequadas, conforme explicado anteriormente.

Seguem-se as funções de serviço expostas nos pontos finais:

| Ponto Final | Funcionalidade |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Fornece operações de inscrição de dispositivos com o serviço de aprovisionamento de dispositivos. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Fornece operações para a gestão de grupos de inscrição de dispositivos. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Fornece operações para recuperação e gerenciamento o estado dos registos de dispositivo. |


Por exemplo, um serviço gerado usando uma pré-criada partilhado chamada de política de acesso **enrollmentread** criaria um token com os seguintes parâmetros:

* URI do recurso: `{mydps}.azure-devices-provisioning.net`,
* chave de assinatura: uma das chaves do `enrollmentread` política,
* nome da política: `enrollmentread`,
* qualquer time.backn de expiração

![Criar uma política de acesso partilhado para sua instância do serviço aprovisionamento de dispositivos no portal][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, o que concederia acesso para ler todos os registos de inscrição, seria:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem mais informações sobre como controlar o acesso ao seu serviço de aprovisionamento de dispositivos de IoT.

### <a name="device-provisioning-service-permissions"></a>Permissões de serviço de aprovisionamento de dispositivos

A tabela seguinte lista as permissões que pode utilizar para controlar o acesso ao seu serviço de aprovisionamento de dispositivos de IoT.

| Permissão | Notas |
| --- | --- |
| **ServiceConfig** |Concede acesso para alterar as configurações de serviço. <br/>Esta permissão é utilizada pelos serviços de cloud de back-end. |
| **EnrollmentRead** |Concede acesso de leitura às inscrições de dispositivos e grupos de inscrição. <br/>Esta permissão é utilizada pelos serviços de cloud de back-end. |
| **EnrollmentWrite** |Concede acesso de escrita para as inscrições de dispositivos e grupos de inscrição. <br/>Esta permissão é utilizada pelos serviços de cloud de back-end. |
| **RegistrationStatusRead** |Concede acesso para o estado de registo do dispositivo de leitura. <br/>Esta permissão é utilizada pelos serviços de cloud de back-end. |
| **RegistrationStatusWrite**  |Concede elimina acesso para o estado de registo do dispositivo. <br/>Esta permissão é utilizada pelos serviços de cloud de back-end. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
