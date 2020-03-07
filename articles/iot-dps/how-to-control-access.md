---
title: Pontos finais de segurança no Serviço de Provisionamento de Dispositivos IoT  Microsoft Docs
description: Conceitos - como controlar o acesso ao IoT Device Provisioning Service (DPS) para aplicações de backend. Inclui informações sobre fichas de segurança.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: 2a7e0932d226b1533c039b8529c2c11de06cf525
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396061"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Controlar o acesso ao Serviço de Provisionamento de Dispositivos Hub Azure IoT

Este artigo descreve as opções para garantir o seu serviço de provisionamento de dispositivos IoT. O serviço de provisionamento utiliza *permissões* para conceder acesso a cada ponto final. As permissões limitam o acesso a uma instância de serviço com base na funcionalidade.

Este artigo descreve:

* As diferentes permissões que pode conceder a uma app de backend para aceder ao seu serviço de provisionamento.
* O processo de autenticação e os tokens que utiliza para verificar permissões.

### <a name="when-to-use"></a>Quando utilizar

Deve ter permissões adequadas para aceder a qualquer um dos pontos finais do serviço de provisionamento. Por exemplo, uma aplicação de backend deve incluir um símbolo contendo credenciais de segurança, juntamente com todas as mensagens que envia para o serviço.

## <a name="access-control-and-permissions"></a>Controlo de acesso e permissões

Pode conceder [permissões](#device-provisioning-service-permissions) das seguintes formas:

* Políticas de **autorização de acesso partilhado.** As políticas de acesso partilhado podem conceder qualquer combinação de [permissões](#device-provisioning-service-permissions). Pode definir políticas no [portal Azure,][lnk-management-portal]ou programáticamente utilizando o Serviço de Fornecimento de [Dispositivos REST APIs][lnk-resource-provider-apis]. Um serviço de provisionamento recém-criado tem a seguinte política de incumprimento:

* **prestadorde serviços**: Política com todas as permissões.

> [!NOTE]
> Consulte permissões para obter informações [detalhadas.](#device-provisioning-service-permissions)

## <a name="authentication"></a>Autenticação

O Serviço de Provisionamento de Dispositivos Azure IoT Hub dá acesso a pontos finais, verificando um símbolo contra as políticas de acesso partilhado. Credenciais de segurança, como chaves simétricas, nunca são enviadas por cima do fio.

> [!NOTE]
> O fornecedor de recursos do Serviço de Provisionamento de Dispositivos é assegurado através da sua subscrição Azure, assim como todos os fornecedores do Gestor de [Recursos Azure.][lnk-azure-resource-manager]

Para obter mais informações sobre como construir e usar fichas de segurança, consulte a secção seguinte.

HTTP é o único protocolo suportado, e implementa a autenticação, incluindo um símbolo válido no cabeçalho do pedido de **autorização.**

#### <a name="example"></a>Exemplo
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Os SDKs do Serviço de Provisionamento de [Dispositivos Azure IoT][lnk-sdks] geram automaticamente fichas ao ligarem-se ao serviço.

## <a name="security-tokens"></a>Fichas de segurança

O Serviço de Provisionamento de Dispositivos utiliza fichas de segurança para autenticar os serviços para evitar o envio de chaves no fio. Além disso, as fichas de segurança são limitadas na validade e âmbito do tempo. [Os SDKs do Serviço de Provisionamento de Dispositivos Azure IoT][lnk-sdks] geram automaticamente fichas sem necessitar de qualquer configuração especial. Alguns cenários exigem que gere e use fichas de segurança diretamente. Tais cenários incluem a utilização direta da superfície HTTP.

### <a name="security-token-structure"></a>Estrutura simbólica de segurança

Utiliza fichas de segurança para conceder acesso limitado a tempo para serviços a funcionalidades específicas no Serviço de Provisionamento de Dispositivos IoT. Para obter autorização para ligar ao serviço de provisionamento, os serviços devem enviar fichas de segurança assinadas com uma chave de acesso partilhado ou simétrica.

Um símbolo assinado com uma chave de acesso partilhado dá acesso a todas as funcionalidades associadas às permissões da política de acesso partilhado. 

O token de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Aqui estão os valores esperados:

| Valor | Descrição |
| --- | --- |
| {signature} |Uma cadeia de assinatura HMAC-SHA256 do formulário: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: A chave é descodificada a partir do base64 e utilizada como chave para executar o cálculo HMAC-SHA256.|
| {expiração} |UTF8 cordas para o número de segundos desde a época 00:00:00 UTC em 1 de janeiro de 1970. |
| {URL-encoded-resourceURI} | Codificação de URL minúscula do recurso minúsculo URI. Prefixo URI (por segmento) dos pontos finais que podem ser acedidos com este símbolo, começando com o nome anfitrião do Serviço de Provisionamento de Dispositivos IoT (sem protocolo). Por exemplo, `mydps.azure-devices-provisioning.net`. |
| {policyName} |O nome da política de acesso partilhado a que se refere este símbolo. |

**Nota no prefixo:** O prefixo URI é calculado por segmento e não por carácter. Por exemplo, `/a/b` é um prefixo para `/a/b/c`, mas não para `/a/bc`.

O seguinte snippet Node.js mostra uma função chamada **generateSasToken** que calcula o símbolo das inputs `resourceUri, signingKey, policyName, expiresInMins`. As secções seguintes detalham como inicializar as diferentes inputs para os diferentes casos de utilização de símbolos.

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
> Uma vez que a validade do tempo do token é validada nas máquinas do Serviço de Provisionamento de Dispositivos IoT, a deriva no relógio da máquina que gera o símbolo deve ser mínima.

### <a name="use-security-tokens-from-service-components"></a>Utilize fichas de segurança dos componentes de serviço

Os componentes do serviço só podem gerar fichas de segurança utilizando políticas de acesso partilhado que concedem as permissões adequadas, conforme explicado anteriormente.

Aqui estão as funções de serviço expostas nos pontos finais:

| Ponto Final | Funcionalidade |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Fornece operações de inscrição de dispositivos com o Serviço de Provisionamento de Dispositivos. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Fornece operações para gerir grupos de inscrição de dispositivos. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Fornece operações de recuperação e gestão do estado dos registos do dispositivo. |


Como exemplo, um serviço gerado usando uma política de acesso partilhado pré-criada chamada **inscrição** criaria um símbolo com os seguintes parâmetros:

* recurso URI: `{mydps}.azure-devices-provisioning.net`,
* chave de assinatura: uma das chaves da política `enrollmentread`,
* nome da política: `enrollmentread`,
* qualquer tempo de expiração.backn

![Crie uma política de acesso partilhado para a sua instância de serviço de fornecimento de dispositivos no portal][img-add-shared-access-policy]

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

### <a name="device-provisioning-service-permissions"></a>Permissões do Serviço de Provisionamento de Dispositivos

A tabela que se segue lista as permissões que pode utilizar para controlar o acesso ao seu Serviço de Provisionamento de Dispositivos IoT.

| Permissão | Notas |
| --- | --- |
| **ServiçoConfig** |Concede acesso para alterar as configurações do serviço. <br/>Esta permissão é usada pelos serviços de nuvem de backend. |
| **Leitura de Inscrições** |As subvenções lêem o acesso às matrículas e grupos de matrículas do dispositivo. <br/>Esta permissão é usada pelos serviços de nuvem de backend. |
| **InscriçãoEscrita** |As subvenções escrevem o acesso às matrículas e grupos de matrículas do dispositivo. <br/>Esta permissão é usada pelos serviços de nuvem de backend. |
| **Leitura do Estado das Inscrições** |As subvenções lêem o acesso ao estado de registo do dispositivo. <br/>Esta permissão é usada pelos serviços de nuvem de backend. |
| **Inscrição StatusWrite**  |As subvenções eliminam o acesso ao estado de registo do dispositivo. <br/>Esta permissão é usada pelos serviços de nuvem de backend. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
