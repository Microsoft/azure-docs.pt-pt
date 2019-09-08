---
title: Entender a segurança do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor-como controlar o acesso ao Hub IoT para aplicativos de dispositivo e aplicativos de back-end. Inclui informações sobre tokens de segurança e suporte para certificados X. 509.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: fa1aa8c560f4b9cc48c7a6a761abe4d69d5d0265
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773174"
---
# <a name="control-access-to-iot-hub"></a>Controlar o acesso ao Hub IoT

Este artigo descreve as opções para proteger o Hub IoT. O Hub IoT usa *permissões* para conceder acesso a cada ponto de extremidade do Hub IOT. As permissões limitam o acesso a um hub IoT com base na funcionalidade.

Este artigo apresenta:

* As diferentes permissões que você pode conceder a um dispositivo ou aplicativo de back-end para acessar o Hub IoT.
* O processo de autenticação e os tokens que ele usa para verificar as permissões.
* Como escopo de credenciais para limitar o acesso a recursos específicos.
* Suporte do Hub IoT para certificados X. 509.
* Mecanismos de autenticação de dispositivo personalizados que usam os registros de identidade do dispositivo ou esquemas de autenticação existentes.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Você deve ter as permissões apropriadas para acessar qualquer um dos pontos de extremidade do Hub IoT. Por exemplo, um dispositivo deve incluir um token que contém credenciais de segurança junto com cada mensagem que ele envia ao Hub IoT.

## <a name="access-control-and-permissions"></a>Controle de acesso e permissões

Você pode conceder [permissões](#iot-hub-permissions) das seguintes maneiras:

* **Políticas de acesso compartilhado no nível do Hub IOT**. As políticas de acesso compartilhado podem conceder qualquer combinação de [permissões](#iot-hub-permissions). Você pode definir políticas no [portal do Azure](https://portal.azure.com), programaticamente usando as [APIs REST de recurso do Hub IOT](/rest/api/iothub/iothubresource)ou usando a CLI de [política AZ IOT Hub](/cli/azure/iot/hub/policy?view=azure-cli-latest) . Um hub IoT criado recentemente tem as seguintes políticas padrão:
  
  | Política de Acesso Partilhado | Permissões |
  | -------------------- | ----------- |
  | iothubowner | Todas as permissões |
  | serviço | Permissões do **OnConnect** |
  | dispositivo | Permissões de **DeviceConnect** |
  | registryRead | Permissões de **RegistryRead** |
  | registryReadWrite | Permissões **RegistryRead** e **RegistryWrite** |

* **Credenciais de segurança por dispositivo**. Cada Hub IoT contém um [registro de identidade](iot-hub-devguide-identity-registry.md) para cada dispositivo nesse registro de identidade, você pode configurar credenciais de segurança que concedem permissões de **DeviceConnect** com escopo para os pontos de extremidade do dispositivo correspondente.

Por exemplo, em uma solução de IoT típica:

* O componente de gerenciamento de dispositivos usa a política *registryReadWrite* .
* O componente do processador de eventos usa a política de *serviço* .
* O componente de lógica de negócios do dispositivo de tempo de execução usa a política de *serviço* .
* Os dispositivos individuais se conectam usando as credenciais armazenadas no registro de identidade do Hub IoT.

> [!NOTE]
> Consulte [permissões](#iot-hub-permissions) para obter informações detalhadas.

## <a name="authentication"></a>Authentication

O Hub IoT do Azure concede acesso aos pontos de extremidade verificando um token em relação às políticas de acesso compartilhado e às credenciais de segurança do registro de identidade.

As credenciais de segurança, como chaves simétricas, nunca são enviadas pela conexão.

> [!NOTE]
> O provedor de recursos do Hub IoT do Azure é protegido por meio de sua assinatura do Azure, assim como todos os provedores no [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

Para obter mais informações sobre como construir e usar tokens de segurança, consulte [tokens de segurança do Hub IOT](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Especificações de protocolo

Cada protocolo com suporte, como MQTT, AMQP e HTTPS, transporta tokens de maneiras diferentes.

Ao usar MQTT, o pacote Connect tem a DeviceID como ClientID, `{iothubhostname}/{deviceId}` no campo username, e um token SAS no campo password. `{iothubhostname}`deve ser o CName completo do Hub IoT (por exemplo, contoso.azure-devices.net).

Ao usar o [AMQP](https://www.amqp.org/), o Hub IOT dá suporte à [segurança baseada em declarações](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) [SASL Plain](https://tools.ietf.org/html/rfc4616) e AMQP.

Se você usar a segurança baseada em declarações do AMQP, o padrão especificará como transmitir esses tokens.

Por SASL PLAIN, o **nome de usuário** pode ser:

* `{policyName}@sas.root.{iothubName}`Se estiver usando tokens de nível de Hub IoT.
* `{deviceId}@sas.{iothubname}`Se estiver usando tokens no escopo do dispositivo.

Em ambos os casos, o campo senha contém o token, conforme descrito em [tokens de segurança do Hub IOT](iot-hub-devguide-security.md#security-tokens).

O HTTPS implementa a autenticação incluindo um token válido no cabeçalho de solicitação de **autorização** .

#### <a name="example"></a>Exemplo

Nome de usuário (DeviceID diferencia maiúsculas de minúsculas):`iothubname.azure-devices.net/DeviceId`

Senha (você pode gerar um token SAS com a ferramenta [Gerenciador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) , o comando de extensão da CLI [AZ IOT Hub Generate-SAS-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)ou as ferramentas de iot do [Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Os [SDKs do Azure IOT](iot-hub-devguide-sdks.md) geram tokens automaticamente ao se conectar ao serviço. Em alguns casos, os SDKs do IoT do Azure não dão suporte a todos os protocolos ou a todos os métodos de autenticação.

### <a name="special-considerations-for-sasl-plain"></a>Considerações especiais para SASL PLAIN

Ao usar SASL PLAIN com AMQP, um cliente que se conecta a um hub IoT pode usar um único token para cada conexão TCP. Quando o token expira, a conexão TCP é desconectada do serviço e dispara uma reconexão. Esse comportamento, embora não seja problemático para um aplicativo de back-end, é prejudicial para um aplicativo de dispositivo pelos seguintes motivos:

* Os gateways geralmente se conectam em nome de vários dispositivos. Ao usar SASL PLAIN, eles precisam criar uma conexão TCP distinta para cada dispositivo que se conecta a um hub IoT. Esse cenário aumenta consideravelmente o consumo de recursos de energia e de rede e aumenta a latência de cada conexão de dispositivo.

* Os dispositivos com restrição de recursos são afetados negativamente pelo aumento do uso dos recursos para se reconectar após cada expiração do token.

## <a name="scope-iot-hub-level-credentials"></a>Escopo de credenciais de nível de Hub IoT

Você pode delimitar as políticas de segurança no nível do Hub IoT criando tokens com um URI de recurso restrito. Por exemplo, o ponto de extremidade para enviar mensagens do dispositivo para a nuvem de um dispositivo é **/Devices/{deviceId}/messages/Events**. Você também pode usar uma política de acesso compartilhado no nível de Hub IoT com permissões **DeviceConnect** para assinar um token cujo ResourceURI seja **/Devices/{deviceId}** . Essa abordagem cria um token que só pode ser usado para enviar mensagens em nome do dispositivo **DeviceID**.

Esse mecanismo é semelhante à [política de editor de hubs de eventos](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)e permite que você implemente métodos de autenticação personalizados.

## <a name="security-tokens"></a>Tokens de segurança

O Hub IoT usa tokens de segurança para autenticar dispositivos e serviços para evitar o envio de chaves na conexão. Além disso, os tokens de segurança são limitados em tempo de validade e escopo. Os [SDKs do Azure IOT](iot-hub-devguide-sdks.md) geram automaticamente tokens sem a necessidade de qualquer configuração especial. Alguns cenários exigem que você gere e use tokens de segurança diretamente. Esses cenários incluem:

* O uso direto das superfícies MQTT, AMQP ou HTTPS.

* A implementação do padrão de serviço de token, conforme explicado em [autenticação de dispositivo personalizada](iot-hub-devguide-security.md#custom-device-and-module-authentication).

O Hub IoT também permite que os dispositivos se autentiquem no Hub IoT usando [certificados X. 509](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Estrutura do token de segurança

Você usa tokens de segurança para conceder acesso limitado a tempo a dispositivos e serviços a uma funcionalidade específica no Hub IoT. Para obter autorização para se conectar ao Hub IoT, os dispositivos e serviços devem enviar tokens de segurança assinados com um acesso compartilhado ou uma chave simétrica. Essas chaves são armazenadas com uma identidade de dispositivo no registro de identidade.

Um token assinado com uma chave de acesso compartilhado concede acesso a todas as funcionalidades associadas às permissões de política de acesso compartilhado. Um token assinado com uma chave simétrica de identidade de dispositivo concede somente a permissão **DeviceConnect** para a identidade do dispositivo associada.

O token de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Estes são os valores esperados:

| Value | Descrição |
| --- | --- |
| {signature} |Uma cadeia de caracteres de assinatura HMAC-SHA256 no `{URL-encoded-resourceURI} + "\n" + expiry`formato:. **Importante**: A chave é decodificada da Base64 e usada como chave para executar a computação HMAC-SHA256. |
| {resourceURI} |Prefixo do URI (por segmento) dos pontos de extremidade que podem ser acessados com esse token, começando com o nome do host do Hub IoT (sem protocolo). Por exemplo, `myHub.azure-devices.net/devices/device1` |
| expiração |Cadeias de caracteres UTF8 para o número de segundos desde a época 00:00:00 UTC em 1 de janeiro de 1970. |
| {URL-encoded-resourceURI} |Codificação de URL em minúsculas do URI de recurso em letras minúsculas |
| {policyName} |O nome da política de acesso compartilhado à qual esse token se refere. Ausente se o token se referir a credenciais do registro do dispositivo. |

**Observação no prefixo**: O prefixo do URI é calculado por segmento e não por caractere. Por exemplo `/a/b` , é um prefixo `/a/b/c` para, mas `/a/bc`não para.

O trecho do node. js a seguir mostra uma função chamada **generateSasToken** que computa o token das entradas `resourceUri, signingKey, policyName, expiresInMins`. As seções a seguir detalham como inicializar as diferentes entradas para os diferentes casos de uso de token.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Como uma comparação, o código Python equivalente para gerar um token de segurança é:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

Veja a seguir as instruções de instalação para os pré-requisitos.

[!INCLUDE [Iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]


A funcionalidade no C# para gerar um token de segurança é:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}

```


> [!NOTE]
> Como o tempo de validade do token é validado em computadores do Hub IoT, a descompasso no relógio do computador que gera o token deve ser mínima.

### <a name="use-sas-tokens-in-a-device-app"></a>Usar tokens SAS em um aplicativo de dispositivo

Há duas maneiras de obter permissões **DeviceConnect** com o Hub IOT com tokens de segurança: Use uma [chave de dispositivo simétrica do registro de identidade](#use-a-symmetric-key-in-the-identity-registry)ou use uma [chave de acesso compartilhado](#use-a-shared-access-policy).

Lembre-se de que todas as funcionalidades acessíveis dos dispositivos são expostas por design `/devices/{deviceId}`em pontos de extremidade com prefixo.

> [!IMPORTANT]
> A única maneira de o Hub IoT autenticar um dispositivo específico é usando a chave simétrica de identidade do dispositivo. Em casos em que uma política de acesso compartilhado é usada para acessar a funcionalidade do dispositivo, a solução deve considerar o componente que emite o token de segurança como um subcomponente confiável.

Os pontos de extremidade voltados para o dispositivo são (independentemente do protocolo):

| Ponto Final | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Enviar mensagens do dispositivo para a nuvem. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Receber mensagens da nuvem para o dispositivo. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Usar uma chave simétrica no registro de identidade

Ao usar a chave simétrica de uma identidade de dispositivo para gerar um token, o elemento`skn`PolicyName () do token é omitido.

Por exemplo, um token criado para acessar toda a funcionalidade do dispositivo deve ter os seguintes parâmetros:

* URI do recurso `{IoT hub name}.azure-devices.net/devices/{device id}`:,
* chave de assinatura: qualquer chave simétrica para `{device id}` a identidade,
* nenhum nome de política,
* qualquer hora de expiração.

Um exemplo que usa a função node. js anterior seria:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

O resultado, que concede acesso a todas as funcionalidades para Device1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> É possível gerar um token SAS com a ferramenta [Gerenciador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) , o comando de extensão CLI [AZ IOT Hub Generate-SAS-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)ou as [ferramentas de IOT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Usar uma política de acesso compartilhado

Ao criar um token de uma política de acesso compartilhado, defina o `skn` campo como o nome da política. Essa política deve conceder a permissão **DeviceConnect** .

Os dois principais cenários para usar políticas de acesso compartilhado para acessar a funcionalidade do dispositivo são:

* [gateways de protocolo de nuvem](iot-hub-devguide-endpoints.md),
* [serviços de token](iot-hub-devguide-security.md#custom-device-and-module-authentication) usados para implementar esquemas de autenticação personalizados.

Como a política de acesso compartilhado pode potencialmente conceder acesso para conectar-se como qualquer dispositivo, é importante usar o URI de recurso correto ao criar tokens de segurança. Essa configuração é especialmente importante para os serviços de token, que têm que definir o escopo do token para um dispositivo específico usando o URI do recurso. Esse ponto é menos relevante para gateways de protocolo, pois eles já estão mediando o tráfego para todos os dispositivos.

Por exemplo, um serviço de token usando a política de acesso compartilhado pré-criada chamada **Device** criaria um token com os seguintes parâmetros:

* URI do recurso `{IoT hub name}.azure-devices.net/devices/{device id}`:,
* chave de assinatura: uma das chaves da `device` política,
* nome da política `device`:,
* qualquer hora de expiração.

Um exemplo que usa a função node. js anterior seria:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, que concede acesso a todas as funcionalidades para Device1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Um gateway de protocolo poderia usar o mesmo token para todos os dispositivos, simplesmente definindo o `myhub.azure-devices.net/devices`URI de recurso como.

### <a name="use-security-tokens-from-service-components"></a>Usar tokens de segurança de componentes de serviço

Os componentes de serviço só podem gerar tokens de segurança usando políticas de acesso compartilhado, concedendo as permissões apropriadas, conforme explicado anteriormente.

Aqui estão as funções de serviço expostas nos pontos de extremidade:

| Ponto Final | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices` |Criar, atualizar, recuperar e excluir identidades de dispositivo. |
| `{iot hub host name}/messages/events` |Receber mensagens do dispositivo para a nuvem. |
| `{iot hub host name}/servicebound/feedback` |Receba comentários para mensagens da nuvem para o dispositivo. |
| `{iot hub host name}/devicebound` |Enviar mensagens da nuvem para o dispositivo. |

Por exemplo, uma geração de serviço usando a política de acesso compartilhado pré-criada chamada **registryRead** criaria um token com os seguintes parâmetros:

* URI do recurso `{IoT hub name}.azure-devices.net/devices`:,
* chave de assinatura: uma das chaves da `registryRead` política,
* nome da política `registryRead`:,
* qualquer hora de expiração.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, que concederia acesso para ler todas as identidades de dispositivo, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Certificados X. 509 com suporte

Você pode usar qualquer certificado X. 509 para autenticar um dispositivo com o Hub IoT carregando uma impressão digital do certificado ou uma autoridade de certificação (CA) para o Hub IoT do Azure. A autenticação usando impressões digitais de certificado apenas verifica se a impressão digital apresentada corresponde à impressão digital configurada. A autenticação usando a autoridade de certificação valida a cadeia de certificados. 

Os certificados com suporte incluem:

* **Um certificado X. 509 existente**. Um dispositivo pode já ter um certificado X. 509 associado a ele. O dispositivo pode usar esse certificado para autenticar com o Hub IoT. Funciona com a autenticação de impressão digital ou de CA. 

* **Certificado X. 509 assinado por AC**. Para identificar um dispositivo e autenticá-lo com o Hub IoT, você pode usar um certificado X. 509 gerado e assinado por uma autoridade de certificação (CA). Funciona com a autenticação de impressão digital ou de CA.

* **Um certificado X-509 autoemitido e autoassinado**. Um fabricante de dispositivo ou um implantador interno pode gerar esses certificados e armazenar a chave privada (e o certificado) correspondente no dispositivo. Você pode usar ferramentas como o [OpenSSL](https://www.openssl.org/) e o utilitário [SelfSignedCertificate do Windows](/powershell/module/pkiclient/new-selfsignedcertificate) para essa finalidade. Funciona apenas com autenticação de impressão digital. 

Um dispositivo pode usar um certificado X. 509 ou um token de segurança para autenticação, mas não ambos.

Para obter mais informações sobre autenticação usando a autoridade de certificação, consulte [autenticação de dispositivo usando certificados de autoridade de certificação X. 509](iot-hub-x509ca-overview.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registrar um certificado X. 509 para um dispositivo

O [SDK do serviço IOT do C# Azure para](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (versão 1.0.8 +) dá suporte ao registro de um dispositivo que usa um certificado X. 509 para autenticação. Outras APIs, como importação/exportação de dispositivos, também dão suporte a certificados X. 509.

Você também pode usar o comando de extensão da CLI [AZ IOT Hub Device-Identity](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) para configurar certificados X. 509 para dispositivos.

### <a name="c-support"></a>Suporte\# a C

A classe **registrymanager** fornece uma maneira programática de registrar um dispositivo. Em particular, os métodos **AddDeviceAsync** e **UpdateDeviceAsync** permitem que você registre e atualize um dispositivo no registro de identidade do Hub IOT. Esses dois métodos usam uma instância de **dispositivo** como entrada. A classe de **dispositivo** inclui uma propriedade de **autenticação** que permite especificar as impressões digitais do certificado X. 509 primário e secundário. A impressão digital representa um hash SHA256 do certificado X. 509 (armazenado usando a codificação Binary DER). Você tem a opção de especificar uma impressão digital primária ou uma impressão digital secundária ou ambas. Há suporte para impressões digitais primárias e secundárias para lidar com cenários de substituição de certificado.

Aqui está um exemplo de\# trecho de código C para registrar um dispositivo usando uma impressão digital do certificado X. 509:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Usar um certificado X. 509 durante operações em tempo de execução

O [SDK do dispositivo IOT do Azure para .net](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (versão 1.0.11 ou superior +) dá suporte ao uso de certificados X. 509.

### <a name="c-support"></a>Suporte\# a C

A classe **DeviceAuthenticationWithX509Certificate** dá suporte à criação de instâncias **DeviceClient** usando um certificado X. 509. O certificado X. 509 deve estar no formato PFX (também chamado de #12 PKCS) que inclui a chave privada.

Aqui está um trecho de código de exemplo:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Autenticação de módulo e dispositivo personalizado

Você pode usar o registro de [identidade](iot-hub-devguide-identity-registry.md) do Hub IOT para configurar credenciais de segurança por dispositivo/módulo e controle de acesso usando [tokens](iot-hub-devguide-security.md#security-tokens). Se uma solução de IoT já tiver um registro de identidade personalizado e/ou esquema de autenticação, considere a criação de um *serviço de token* para integrar essa infraestrutura ao Hub IOT. Dessa forma, você pode usar outros recursos de IoT em sua solução.

Um serviço de token é um serviço de nuvem personalizado. Ele usa uma política de *acesso compartilhado* do Hub IOT com permissões **DeviceConnect** ou **ModuleConnect** para criar tokens no escopo do *dispositivo* ou do *módulo* . Esses tokens permitem que um dispositivo e um módulo se conectem ao Hub IoT.

![Etapas do padrão de serviço de token](./media/iot-hub-devguide-security/tokenservice.png)

Estas são as principais etapas do padrão de serviço de token:

1. Crie uma política de acesso compartilhado do Hub IoT com as permissões **DeviceConnect** ou **ModuleConnect** para o Hub IOT. Você pode criar essa política no [portal do Azure](https://portal.azure.com) ou de forma programática. O serviço de token usa essa política para assinar os tokens que ele cria.

2. Quando um dispositivo/módulo precisa acessar o Hub IoT, ele solicita um token assinado do seu serviço de token. O dispositivo pode autenticar com seu esquema personalizado de registro/autenticação de identidade para determinar a identidade do dispositivo/módulo que o serviço de token usa para criar o token.

3. O serviço de token retorna um token. O `/devices/{deviceId}` token é criado usando o `resourceURI`ou `/devices/{deviceId}/module/{moduleId}` o, com `deviceId` o como o dispositivo que está sendo `moduleId` autenticado ou como o módulo que está sendo autenticado. O serviço de token usa a política de acesso compartilhado para construir o token.

4. O dispositivo/módulo usa o token diretamente com o Hub IoT.

> [!NOTE]
> Você pode usar a classe do .NET [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) ou a classe Java [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) para criar um token em seu serviço de token.

O serviço de token pode definir a expiração do token conforme desejado. Quando o token expira, o Hub IoT separa a conexão do dispositivo/módulo. Em seguida, o dispositivo/módulo deve solicitar um novo token do serviço de token. Um tempo de expiração curto aumenta a carga no dispositivo/módulo e no serviço de token.

Para que um dispositivo/módulo se conecte ao seu hub, você ainda deve adicioná-lo ao registro de identidade do Hub IoT — mesmo que esteja usando um token e não uma chave para se conectar. Portanto, você pode continuar a usar o controle de acesso por dispositivo/por módulo habilitando ou desabilitando as identidades do dispositivo/módulo no [registro de identidade](iot-hub-devguide-identity-registry.md). Essa abordagem reduz os riscos de usar tokens com tempos de expiração longos.

### <a name="comparison-with-a-custom-gateway"></a>Comparação com um gateway personalizado

O padrão de serviço de token é a maneira recomendada para implementar um esquema de registro/autenticação de identidade personalizado com o Hub IoT. Esse padrão é recomendado porque o Hub IoT continua a lidar com a maior parte do tráfego da solução. No entanto, se o esquema de autenticação personalizada for tão entrelaçado com o protocolo, você poderá exigir um *Gateway personalizado* para processar todo o tráfego. Um exemplo desse cenário é usar TLS ( [segurança de camada de transporte) e PSKs (chaves pré-compartilhadas)](https://tools.ietf.org/html/rfc4279). Para obter mais informações, consulte o artigo [Gateway de protocolo](iot-hub-protocol-gateway.md) .

## <a name="reference-topics"></a>Tópicos de referência:

Os tópicos de referência a seguir fornecem a você mais informações sobre como controlar o acesso ao seu hub IoT.

## <a name="iot-hub-permissions"></a>Permissões do Hub IoT

A tabela a seguir lista as permissões que você pode usar para controlar o acesso ao seu hub IoT.

| Permissão | Notas |
| --- | --- |
| **RegistryRead** |Concede acesso de leitura ao registro de identidade. Para obter mais informações, consulte [registro de identidade](iot-hub-devguide-identity-registry.md). <br/>Essa permissão é usada pelos serviços de nuvem de back-end. |
| **RegistryReadWrite** |Concede acesso de leitura e gravação ao registro de identidade. Para obter mais informações, consulte [registro de identidade](iot-hub-devguide-identity-registry.md). <br/>Essa permissão é usada pelos serviços de nuvem de back-end. |
| **ServiceConnect** |Concede acesso a pontos de extremidade de comunicação e monitoramento voltados para o serviço de nuvem. <br/>Concede permissão para receber mensagens do dispositivo para a nuvem, enviar mensagens da nuvem para o dispositivo e recuperar as confirmações de entrega correspondentes. <br/>Concede permissão para recuperar confirmações de entrega para carregamentos de arquivos. <br/>Concede permissão para acessar o gêmeos para atualizar marcas e propriedades desejadas, recuperar propriedades relatadas e executar consultas. <br/>Essa permissão é usada pelos serviços de nuvem de back-end. |
| **DeviceConnect** |Concede acesso a pontos de extremidade voltados para o dispositivo. <br/>Concede permissão para enviar mensagens do dispositivo para a nuvem e receber mensagens da nuvem para o dispositivo. <br/>Concede permissão para executar o upload de arquivo de um dispositivo. <br/>Concede permissão para receber notificações de propriedade desejada do dispositivo e atualizar Propriedades relatadas do dispositivo. <br/>Concede permissão para executar carregamentos de arquivo. <br/>Essa permissão é usada pelos dispositivos. |

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia do desenvolvedor do Hub IoT incluem:

* Os [pontos de extremidade do Hub IOT](iot-hub-devguide-endpoints.md) descrevem os vários pontos de extremidade que cada Hub IOT expõe para operações de tempo de execução e de gerenciamento.

* [Limitação e cotas](iot-hub-devguide-quotas-throttling.md) descreve as cotas e os comportamentos de limitação que se aplicam ao serviço Hub IOT.

* [SDKs de dispositivo e serviço do Azure IOT](iot-hub-devguide-sdks.md) lista os diversos SDKs de linguagem que você pode usar ao desenvolver aplicativos de dispositivo e de serviço que interagem com o Hub IOT.

* [Linguagem de consulta do Hub IOT](iot-hub-devguide-query-language.md) descreve a linguagem de consulta que você pode usar para recuperar informações do Hub IOT sobre seu dispositivo gêmeos e trabalhos.

* [Suporte a MQTT do Hub IOT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do Hub IOT para o protocolo MQTT.

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a controlar o acesso ao Hub IoT, talvez esteja interessado nos seguintes tópicos do guia do desenvolvedor do Hub IoT:

* [Usar dispositivos gêmeos para sincronizar o estado e as configurações](iot-hub-devguide-device-twins.md)
* [Invocar um método direto em um dispositivo](iot-hub-devguide-direct-methods.md)
* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Se você quiser experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais do Hub IoT:

* [Introdução ao Hub IoT do Azure](quickstart-send-telemetry-node.md)
* [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT](iot-hub-csharp-csharp-c2d.md)
* [Como processar mensagens do dispositivo para a nuvem do Hub IoT](tutorial-routing.md)
