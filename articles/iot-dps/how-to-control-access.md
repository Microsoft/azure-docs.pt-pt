---
title: Pontos finais de segurança no Serviço de Provisionamento de Dispositivos IoT | Microsoft Docs
description: Conceitos - como controlar o acesso ao Serviço de Provisionamento de Dispositivos IoT (DPS) para aplicações de backend. Inclui informações sobre fichas de segurança.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 024dbf6518748a4048873de4eb54a53f9d9a6362
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954329"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Controlar o acesso ao Serviço de Aprovisionamento de Dispositivos no Hub IoT do Azure

Este artigo descreve as opções para garantir o seu serviço de provisionamento de dispositivos IoT. O serviço de fornecimento utiliza *permissões* para conceder acesso a cada ponto final. As permissões limitam o acesso a uma instância de serviço com base na funcionalidade.

Este artigo descreve:

* As diferentes permissões que pode conceder a uma aplicação de backend para aceder ao seu serviço de fornecimento.
* O processo de autenticação e os tokens que utiliza para verificar permissões.

### <a name="when-to-use"></a>Quando utilizar

Deve ter permissões adequadas para aceder a qualquer um dos pontos finais do serviço de fornecimento. Por exemplo, uma aplicação de backend deve incluir um símbolo contendo credenciais de segurança, juntamente com todas as mensagens que envia para o serviço.

## <a name="access-control-and-permissions"></a>Controlo de acesso e permissões

Pode conceder permissões das [seguintes](#device-provisioning-service-permissions) formas:

* **Políticas de autorização de acesso partilhado.** As políticas de acesso partilhado podem conceder qualquer combinação de [permissões.](#device-provisioning-service-permissions) Pode definir políticas no [portal Azure,][lnk-management-portal]ou programáticamente utilizando as APIs do [Serviço de Provisionamento de Dispositivos][lnk-resource-provider-apis]. Um serviço de fornecimento recém-criado tem a seguinte política de incumprimento:

* **provisioningserviceowner**: Política com todas as permissões.

> [!NOTE]
> Consulte as permissões para obter informações [detalhadas.](#device-provisioning-service-permissions)

## <a name="authentication"></a>Autenticação

O Serviço de Provisionamento de Dispositivos Azure IoT Hub concede acesso aos pontos finais, verificando um símbolo contra as políticas de acesso partilhado. Credenciais de segurança, como chaves simétricas, nunca são enviadas pelo fio.

> [!NOTE]
> O fornecedor de recursos do Serviço de Provisionamento de Dispositivos é protegido através da sua subscrição Azure, assim como todos os fornecedores do [Azure Resource Manager][lnk-azure-resource-manager].

Para obter mais informações sobre como construir e usar fichas de segurança, consulte a secção seguinte.

HTTP é o único protocolo suportado, e implementa a autenticação através da inclusão de um token válido no cabeçalho do pedido **de Autorização.**

#### <a name="example"></a>Exemplo
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Os [SDKs de serviço de fornecimento de dispositivos Azure IoT][lnk-sdks] geram automaticamente fichas quando se ligam ao serviço.

## <a name="security-tokens"></a>Tokens de segurança

O Serviço de Provisionamento de Dispositivos utiliza fichas de segurança para autenticar serviços para evitar o envio de chaves no fio. Além disso, as fichas de segurança são limitadas na validade e âmbito de tempo. [Os SDKs de serviço de provisão de dispositivos Azure IoT][lnk-sdks] geram automaticamente fichas sem necessitar de qualquer configuração especial. Alguns cenários requerem que gere e use fichas de segurança diretamente. Tais cenários incluem a utilização direta da superfície HTTP.

### <a name="security-token-structure"></a>Estrutura simbólica de segurança

Utiliza fichas de segurança para conceder acesso limitado a tempo para serviços a funcionalidades específicas no Serviço de Provisionamento de Dispositivos IoT. Para obter autorização para se ligar ao serviço de prestação de serviços, os serviços devem enviar fichas de segurança assinadas com uma chave de acesso partilhado ou simétrica.

Um token assinado com uma chave de acesso partilhado dá acesso a todas as funcionalidades associadas às permissões de política de acesso partilhado. 

O símbolo de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Aqui estão os valores esperados:

| Valor | Descrição |
| --- | --- |
| {assinatura} |Uma sequência de assinatura HMAC-SHA256 do formulário: `{URL-encoded-resourceURI} + "\n" + expiry` . **Importante**: A chave é descodificada a partir da base64 e usada como chave para executar o cálculo HMAC-SHA256.|
| {expiração} |UtF8 strings por número de segundos desde a época 00:00:00 UTC em 1 de janeiro de 1970. |
| {URL-codificado-recursosURI} | Codificação de URL minúscula do recurso uri de recursos de caixa inferior. Prefixo URI (por segmento) dos pontos finais que podem ser acedidos com este token, começando pelo nome de anfitrião do Serviço de Provisionamento de Dispositivos IoT (sem protocolo). Por exemplo, `mydps.azure-devices-provisioning.net`. |
| {políticaName} |O nome da política de acesso partilhado a que se refere este símbolo. |

**Nota no prefixo**: O prefixo URI é calculado por segmento e não pelo carácter. Por `/a/b` exemplo, é um prefixo `/a/b/c` para, mas não para `/a/bc` .

O seguinte Node.js snippet mostra uma função chamada **GenerateSasToken** que calcula o token a partir das entradas `resourceUri, signingKey, policyName, expiresInMins` . As secções seguintes detalham como inicializar as diferentes entradas para os diferentes casos de utilização de token.

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

Em comparação, o código Python equivalente para gerar um símbolo de segurança é:

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
> Uma vez que a validade do token é validada nas máquinas de serviço de fornecimento de dispositivos IoT, a deriva no relógio da máquina que gera o símbolo deve ser mínima.

### <a name="use-security-tokens-from-service-components"></a>Utilize fichas de segurança a partir de componentes de serviço

Os componentes de serviço só podem gerar fichas de segurança utilizando políticas de acesso partilhado que concedam as permissões apropriadas, como explicado anteriormente.

Aqui estão as funções de serviço expostas nos pontos finais:

| Ponto final | Funcionalidade |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Fornece operações de inscrição de dispositivos com o Serviço de Provisionamento de Dispositivos. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Fornece operações para gerir grupos de inscrição de dispositivos. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Fornece operações de recuperação e gestão do estado dos registos do dispositivo. |


Como exemplo, um serviço gerado usando uma política de acesso partilhado pré-criada chamada **enrollmentread** criaria um símbolo com os seguintes parâmetros:

* recurso URI: `{mydps}.azure-devices-provisioning.net` ,
* chave de assinatura: uma das chaves da `enrollmentread` apólice,
* nome da política: `enrollmentread` ,
* qualquer tempo de expiração.backn

![Crie uma política de acesso partilhado para a sua instância de serviço de Provisionamento de Dispositivos no portal][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, que daria acesso à leitura de todos os registos de matrículas, seria:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações sobre o controlo do acesso ao seu Serviço de Provisionamento de Dispositivos IoT.

### <a name="device-provisioning-service-permissions"></a>Permissões de serviço de fornecimento de dispositivos

A tabela que se segue lista as permissões que pode utilizar para controlar o acesso ao seu Serviço de Provisionamento de Dispositivos IoT.

| Permissão | Notas |
| --- | --- |
| **ServiceConfig** |Concede acesso para alterar as configurações do serviço. <br/>Esta permissão é utilizada pelos serviços de backend cloud. |
| **InscriçãoLe** |As bolsas lêem o acesso às matrículas do dispositivo e aos grupos de inscrição. <br/>Esta permissão é utilizada pelos serviços de backend cloud. |
| **InscriçãoDessar** |As subvenções escrevem acesso às matrículas do dispositivo e aos grupos de inscrição. <br/>Esta permissão é utilizada pelos serviços de backend cloud. |
| **RegistroSesread** |As bolsas lêem o acesso ao estado de registo do dispositivo. <br/>Esta permissão é utilizada pelos serviços de backend cloud. |
| **RegistroSosdite**  |As subvenções apagam o acesso ao estado de registo do dispositivo. <br/>Esta permissão é utilizada pelos serviços de backend cloud. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/