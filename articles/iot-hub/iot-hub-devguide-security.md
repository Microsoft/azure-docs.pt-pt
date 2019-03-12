---
title: Compreender a segurança do IoT Hub do Azure | Documentos da Microsoft
description: Guia para programadores – como controlar o acesso ao IoT Hub para aplicações de dispositivos e aplicações de back-end. Inclui informações sobre os tokens de segurança e suporte para certificados X.509.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: bb402a5a059fb6f2836bddbd951220271ca77ba3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534373"
---
# <a name="control-access-to-iot-hub"></a>Controlar o acesso ao Hub IoT

Este artigo descreve as opções para proteger o seu hub IoT. O IoT Hub utiliza *permissões* para conceder acesso a cada ponto de extremidade do hub IoT. As permissões limitam o acesso a um hub IoT com base na funcionalidade.

Este artigo apresenta:

* As permissões diferentes que pode conceder a uma aplicação de back-end ou de dispositivo para aceder ao seu hub IoT.
* O processo de autenticação e os tokens que ele usa para verificar as permissões.
* Como as credenciais de âmbito para limitar o acesso a recursos específicos.
* Suporte de IoT Hub para certificados X.509.
* Mecanismos de autenticação do dispositivo personalizadas que utilizam os registos de identidade de dispositivo existentes ou esquemas de autenticação.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Tem de ter permissões adequadas para aceder a qualquer um dos pontos finais do IoT Hub. Por exemplo, um dispositivo tem de incluir um token que contém as credenciais de segurança, juntamente com todas as mensagens que envia para o IoT Hub.

## <a name="access-control-and-permissions"></a>Controlo de acesso e permissões

Pode conceder [permissões](#iot-hub-permissions) das seguintes formas:

* **Políticas de acesso de partilhado ao nível do IoT hub**. Políticas de acesso partilhado podem conceder a qualquer combinação dos [permissões](#iot-hub-permissions). Pode definir políticas no [portal do Azure](https://portal.azure.com), por meio de programação, utilizando o [APIs de REST de recursos do IoT Hub](/rest/api/iothub/iothubresource), ou utilizando o [política do az iot hub](/cli/azure/iot/hub/policy?view=azure-cli-latest) CLI. Um IoT hub recentemente criado tem as seguintes políticas padrão:
  
  | Política de Acesso Partilhado | Permissões |
  | -------------------- | ----------- |
  | iothubowner | Todas as permissões |
  | serviço | **ServiceConnect** permissões |
  | dispositivo | **DeviceConnect** permissões |
  | registryRead | **RegistryRead** permissões |
  | registryReadWrite | **RegistryRead** e **RegistryWrite** permissões |

* **Credenciais de segurança por dispositivo**. Cada IoT Hub contém um [registo de identidade](iot-hub-devguide-identity-registry.md) para cada dispositivo este registo de identidade, pode configurar as credenciais de segurança que concedem **DeviceConnect** permissões no âmbito para o dispositivo correspondente pontos de extremidade.

Por exemplo, numa solução de IoT típica:

* O componente de gestão de dispositivos utiliza a *registryReadWrite* política.
* O componente de processador de eventos utiliza a *serviço* política.
* O componente de lógica de negócio de dispositivos de tempo de execução utiliza a *serviço* política.
* Dispositivos individuais ligarem com as credenciais armazenadas no registo de identidade do hub IoT.

> [!NOTE]
> Ver [permissões](#iot-hub-permissions) para obter informações detalhadas.

## <a name="authentication"></a>Authentication

O IoT Hub do Azure concede acesso a pontos finais verificando um token com a políticas de acesso partilhado e as credenciais de segurança do registo de identidade.

Credenciais de segurança, tais como chaves simétricas, nunca são enviadas durante a transmissão.

> [!NOTE]
> O fornecedor de recursos do IoT Hub do Azure é protegido através da sua subscrição do Azure, assim como todos os fornecedores no [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

Para obter mais informações sobre como construir e utilizar tokens de segurança, consulte [tokens de segurança do IoT Hub](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Especificações de protocolo

Cada protocolo suportado, como MQTT, AMQP e HTTPS, transporta tokens de formas diferentes.

Quando utiliza MQTT, o pacote do CONNECT tem o ID do dispositivo como ClientId, `{iothubhostname}/{deviceId}` no campo de nome de utilizador e um token SAS no campo de palavra-passe. `{iothubhostname}` deve ser o CName completo do hub IoT (por exemplo, contoso. Azure devices.net).

Ao usar [AMQP](https://www.amqp.org/), IoT Hub suporta [SASL simples](https://tools.ietf.org/html/rfc4616) e [AMQP afirmações com base-segurança](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Se utilizar o AMQP afirmações-com base-security, o padrão Especifica como transmitir esses tokens.

Para SASL simples, o **nome de utilizador** pode ser:

* `{policyName}@sas.root.{iothubName}` Se utilizar tokens de ao nível do hub IoT.
* `{deviceId}@sas.{iothubname}` Se utilizar tokens no âmbito do dispositivo.

Em ambos os casos, o campo de palavra-passe contém o token, conforme descrito em [tokens de segurança do IoT Hub](iot-hub-devguide-security.md#security-tokens).

HTTPS implementa a autenticação, incluindo um token válido na **autorização** cabeçalho do pedido.

#### <a name="example"></a>Exemplo

Nome de utilizador (DeviceId é sensível a maiúsculas e minúsculas): `iothubname.azure-devices.net/DeviceId`

Palavra-passe (pode gerar um token SAS com o [Explorador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ferramenta, o comando de extensão da CLI [az iot hub gerar--token sas](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token), ou o [ferramentas de IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> O [do Azure IoT SDKs](iot-hub-devguide-sdks.md) gerar automaticamente os tokens ao ligar ao serviço. Em alguns casos, os SDKs IoT do Azure não suportam todos os protocolos ou todos os métodos de autenticação.

### <a name="special-considerations-for-sasl-plain"></a>Considerações especiais sobre SASL simples

Ao utilizar o SASL simples com AMQP, um cliente estabelecer ligação a um hub IoT pode utilizar um único token para cada ligação de TCP. Quando o token expira, a conexão TCP se desliga do serviço e aciona uma nova ligação. Este comportamento, embora não problemático para uma aplicação de back-end, é perigoso para uma aplicação de dispositivo pelos seguintes motivos:

* Gateways se liga normalmente em nome de vários dispositivos. Ao utilizar o SASL simples, eles têm que criar uma ligação de TCP distinta para cada dispositivo a ligar a um hub IoT. Este cenário consideravelmente aumenta o consumo de energia e recursos de rede e aumenta a latência de cada ligação de dispositivo.

* Dispositivos de restrição de recursos são afetados negativamente pelo aumento da utilização de recursos para a nova ligação após cada expiração do token.

## <a name="scope-iot-hub-level-credentials"></a>Credenciais de ao nível do hub de IoT de âmbito

Pode definir o âmbito políticas de segurança ao nível do hub IoT através da criação de tokens com um URI do recurso restrito. Por exemplo, o ponto final para enviar mensagens de dispositivo para a cloud a partir de um dispositivo é **/devices/ {deviceId} / mensagens/eventos**. Também pode utilizar uma política de acesso partilhado de ao nível do hub IoT com **DeviceConnect** permissões para assinar um token é cujo resourceURI **/devices/ {deviceId}**. Essa abordagem cria um token que só pode ser utilizado para enviar mensagens em nome do dispositivo **deviceId**.

Esse mecanismo é semelhante para o [política do publicador de Hubs de eventos](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)e permite-lhe implementar métodos de autenticação personalizados.

## <a name="security-tokens"></a>Tokens de segurança

IoT Hub utiliza tokens de segurança para autenticar dispositivos e serviços para evitar o envio de chaves na conexão. Além disso, os tokens de segurança são limitados em âmbito e validade de tempo. [Os SDKs IoT do Azure](iot-hub-devguide-sdks.md) automaticamente gerar tokens sem a necessidade de nenhuma configuração especial. Alguns cenários requerem que gerar e utilizar tokens de segurança diretamente. Tais cenários incluem:

* O uso direto das superfícies MQTT, AMQP ou HTTPS.

* A implementação do padrão do serviço de token, conforme explicado [autenticação de dispositivo personalizada](iot-hub-devguide-security.md#custom-device-and-module-authentication).

IoT Hub também permite que os dispositivos autenticar com o IoT Hub usando [certificados X.509](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Estrutura de token de segurança

Utilizar tokens de segurança para conceder acesso de tempo limitado aos dispositivos e serviços para funcionalidades específicas do IoT Hub. Para obter autorização para ligar ao IoT Hub, dispositivos e serviços tem de enviar os tokens de segurança assinados com um acesso partilhado ou uma chave simétrica. Estas chaves são armazenadas com uma identidade de dispositivo no registo de identidade.

Um token assinado com um acesso de concessões de chave de acesso partilhado a todas as funcionalidades associadas com as permissões de política de acesso partilhado. Um token assinado com concessões de apenas chave simétrica uma identidade de dispositivo a **DeviceConnect** permissão para a identidade de dispositivo associados.

O token de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Seguem-se os valores esperados:

| Value | Descrição |
| --- | --- |
| {signature} |Uma cadeia de caracteres de assinatura HMAC-SHA256 do formulário: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: A chave é descodificar a partir de base64 e usada como chave para realizar a computação do HMAC-SHA256. |
| {resourceURI} |Prefixos de URI (por segmento) dos pontos de extremidade que podem ser acessados com este token, começando com o nome de anfitrião do hub IoT (nenhum protocolo). Por exemplo, `myHub.azure-devices.net/devices/device1` |
| {expiry} |UTF8 cadeias de caracteres para o número de segundos desde a UTC de 00:00:00 "Epoch" em 1 de Janeiro de 1970. |
| {URL-encoded-resourceURI} |Caso inferiores a codificação URL do recurso minúsculas URI |
| {policyName} |O nome da política de acesso partilhado ao qual se refere este token. Ausente se o token refere-se às credenciais de registo do dispositivo. |

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
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
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
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

A funcionalidade em c# para gerar um token de segurança é:

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
> Uma vez que a validade de tempo do token é validada em máquinas do IoT Hub, descompassos no relógio da máquina que gera o token tem de ser um mínimo.

### <a name="use-sas-tokens-in-a-device-app"></a>Utilizar SAS tokens num aplicativo de dispositivo

Existem duas formas de obter **DeviceConnect** permissões com o IoT Hub com tokens de segurança: utilizar um [chave simétrica de dispositivo do registo de identidades](#use-a-symmetric-key-in-the-identity-registry), ou utilize um [chave de acesso partilhado](#use-a-shared-access-policy).

Lembre-se de que toda a funcionalidade acessível a partir de dispositivos é exposta por design em pontos de extremidade com prefixo `/devices/{deviceId}`.

> [!IMPORTANT]
> A única forma que o IoT Hub autentica um dispositivo específico está a utilizar a chave simétrica de identidade do dispositivo. Em casos quando uma política de acesso partilhado é usada para acessar a funcionalidade do dispositivo, a solução tem de considerar o componente de emitir o token de segurança como um subcomponente fidedigno.

Os pontos de extremidade do dispositivo com acesso à são (independentemente do protocolo):

| Ponto Final | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Envie mensagens dispositivo-para-cloud. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Receba mensagens da cloud para o dispositivo. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Utilizar uma chave simétrica no registo de identidade

Ao utilizar a chave simétrica de uma identidade de dispositivo para gerar um token, o policyName (`skn`) elemento do token for omitido.

Por exemplo, um token que criou para aceder a todas as funcionalidades do dispositivo deve ter os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: qualquer chave simétrica para a `{device id}` identity,
* nenhum nome de política
* hora de expiração.

Um exemplo de uso a função de node. js anterior seria:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

O resultado, que concede acesso a todas as funcionalidades para device1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> É possível gerar um token SAS com o [Explorador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ferramenta, o comando de extensão da CLI [az iot hub gerar--token sas](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token), ou o [ferramentas de IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Utilizar uma política de acesso partilhado

Quando cria um token a partir de uma política de acesso partilhado, defina o `skn` campo para o nome da política. Esta política tem de conceder a **DeviceConnect** permissão.

Os dois cenários principais para utilizar políticas de acesso partilhado para acessar a funcionalidade do dispositivo são:

* [gateways de protocolos de nuvem](iot-hub-devguide-endpoints.md),
* [Serviços de token](iot-hub-devguide-security.md#custom-device-and-module-authentication) usadas para implementar os esquemas de autenticação personalizado.

Uma vez que a política de acesso partilhado potencialmente pode conceder acesso a ligar-se como qualquer dispositivo, é importante usar o URI do recurso correto durante a criação de tokens de segurança. Esta definição é especialmente importante para os serviços de token, que tem de definir o âmbito do token para um dispositivo específico com o URI do recurso. Neste ponto é menos relevante para gateways de protocolos como eles já estão mediating tráfego para todos os dispositivos.

Por exemplo, um serviço de token usando previamente criada partilhado chamada de política de acesso **dispositivo** criaria um token com os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: uma das chaves do `device` política,
* nome da política: `device`,
* hora de expiração.

Um exemplo de uso a função de node. js anterior seria:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, que concede acesso a todas as funcionalidades para device1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Um gateway de protocolo poderia usar o mesmo token para todos os dispositivos que definir simplesmente o URI do recurso para `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Utilizar tokens de segurança de componentes do serviço

Componentes do serviço apenas podem gerar tokens de segurança através de políticas de acesso partilhado conceder as permissões adequadas, conforme explicado anteriormente.

Seguem-se as funções de serviço expostas nos pontos finais:

| Ponto Final | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices` |Criar, atualizar, obter e eliminar as identidades do dispositivo. |
| `{iot hub host name}/messages/events` |Receba mensagens dispositivo-para-cloud. |
| `{iot hub host name}/servicebound/feedback` |Receba comentários para mensagens na cloud para o dispositivo. |
| `{iot hub host name}/devicebound` |Envie mensagens da cloud para o dispositivo. |

Por exemplo, um serviço de gerar usando previamente criada partilhado chamada de política de acesso **registryRead** criaria um token com os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices`,
* chave de assinatura: uma das chaves do `registryRead` política,
* nome da política: `registryRead`,
* hora de expiração.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, o que concederia acesso para ler todas as identidades de dispositivos, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Certificados X.509 suportados

Pode utilizar qualquer certificado X.509 para autenticar um dispositivo com o IoT Hub através do carregamento de um thumbprint do certificado ou uma autoridade de certificação (AC) para o IoT Hub do Azure. Autenticação com thumbprints de certificado só verifica se o thumbprint apresentado corresponde ao o thumbprint configurado. Autenticação com a autoridade de certificação valida a cadeia de certificados. 

Certificados suportados incluem:

* **Um certificado X.509 existente**. Um dispositivo pode já ter um certificado X.509 associado a ele. O dispositivo pode utilizar este certificado para autenticar com o IoT Hub. Funciona com o thumbprint ou autenticação de AC. 

* **Certificado X.509 assinado pela AC**. Para identificar um dispositivo e autenticá-lo com o IoT Hub, pode utilizar um certificado X.509 gerado e assinado por uma autoridade de certificação (AC). Funciona com o thumbprint ou autenticação de AC.

* **A Self-gerado e auto-assinado certificado 509**. Um fabricante do dispositivo ou implementador interna, pode gerar estes certificados e armazenar a chave privada correspondente (e o certificado) no dispositivo. Pode usar ferramentas como [OpenSSL](https://www.openssl.org/) e [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) utilitário para esta finalidade. Só funciona com a autenticação de thumbprint. 

Um dispositivo pode utilizar um certificado X.509 ou um token de segurança para autenticação, mas não ambos.

Para obter mais informações sobre a autenticação com a autoridade de certificação, consulte [autenticação de dispositivo usando certificados de AC X.509](iot-hub-x509ca-overview.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registe-se de um certificado X.509 para um dispositivo

O [SDK do serviço do Azure IoT para C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service) (versão 1.0.8+) ofereça suporte ao registro de um dispositivo que utiliza um certificado X.509 para autenticação. Outras APIs como importar e exportar de dispositivos também suportam certificados X.509.

Também pode utilizar o comando de extensão da CLI [az iot hub-identidade de dispositivo](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) configurar certificados X.509 para dispositivos.

### <a name="c-support"></a>C\# suporte

O **RegistryManager** classe fornece uma forma programática para registar um dispositivo. Em particular, o **AddDeviceAsync** e **UpdateDeviceAsync** métodos permitem-lhe registar e atualizar um dispositivo no registo de identidade do IoT Hub. Esses dois métodos adotam uma **dispositivo** instância como entrada. O **dispositivo** classe inclui uma **autenticação** propriedade permite-lhe especificar os thumbprints de certificado X.509 primários e secundários. O thumbprint representa um hash de SHA256 do certificado X.509 (armazenado com a codificação binária de DER). Tem a opção de especificar um thumbprint primário ou um thumbprint secundário ou ambos. Thumbprints primárias e secundárias são suportadas para manipular os cenários de rollover do certificado.

Eis um exemplo de C\# trecho de código para registar um dispositivo com um thumbprint do certificado X.509:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Utilizar um certificado X.509 durante as operações de tempo de execução

O [Azure IoT device SDK para .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device) (versão 1.0.11+) suporta a utilização de certificados X.509.

### <a name="c-support"></a>C\# suporte

A classe **DeviceAuthenticationWithX509Certificate** suporta a criação de **DeviceClient** instâncias usando um certificado X.509. O certificado X.509 tem de estar no formato PFX (também chamado de PKCS #12) que inclui a chave privada.

Este é um trecho de código de exemplo:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Autenticação de dispositivo e o módulo personalizado

Pode utilizar o IoT Hub [registo de identidade](iot-hub-devguide-identity-registry.md) para configurar as credenciais de segurança por-/ módulo dispositivo e utilizar o controlo de acesso [tokens](iot-hub-devguide-security.md#security-tokens). Se uma solução de IoT já tem um esquema de registo e/ou a autenticação de identidade personalizada, considere criar um *serviço de token* para integrar esta infraestrutura com o IoT Hub. Dessa forma, pode utilizar outras funcionalidades de IoT na sua solução.

Um serviço de token é um serviço cloud personalizado. Ele usa um IoT Hub *partilhado a política de acesso* com **DeviceConnect** ou **ModuleConnect** permissões para criar *no âmbito do dispositivo* ou *no âmbito do módulo* tokens. Estes tokens permitem que um dispositivo e o módulo para ligar ao seu hub IoT.

![Passos para o padrão de serviço de token](./media/iot-hub-devguide-security/tokenservice.png)

Aqui estão as etapas principais do padrão de serviço de token:

1. Criar uma política de acesso partilhado do Hub IoT com **DeviceConnect** ou **ModuleConnect** permissões para o seu hub IoT. Pode criar esta política no [portal do Azure](https://portal.azure.com) ou programaticamente. O serviço de token utiliza esta política para assinar os tokens que cria.

2. Quando um módulo/dispositivo precisa de aceder ao seu hub IoT, ele solicita um token assinado do seu serviço de token. O dispositivo pode autenticar com o esquema de autenticação do registo de identidade personalizada para determinar a identidade de dispositivo/módulo que o serviço de token utiliza para criar o token.

3. O serviço de token devolve um token. O token é criado utilizando `/devices/{deviceId}` ou `/devices/{deviceId}/module/{moduleId}` como `resourceURI`, com `deviceId` como o dispositivo a ser autenticado ou `moduleId` como o módulo a ser autenticado. O serviço de token utiliza a política de acesso partilhado para construir o token.

4. O dispositivo/módulo utiliza o token diretamente com o hub IoT.

> [!NOTE]
> Pode usar a classe do .NET [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) ou a classe Java [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) para criar um token no seu serviço de token.

O serviço de tokens pode definir a expiração do token conforme pretendido. Quando o token expira, o hub IoT servidores a ligação de módulo/dispositivo. Em seguida, o dispositivo/módulo tem de pedir um novo token o serviço de tokens de. Uma hora de expiração curto aumenta a carga sobre o dispositivo/módulo e o serviço de token.

Para um dispositivo/módulo ligar ao seu hub, deve ainda adicioná-lo para o registo de identidade do IoT Hub — mesmo que está a utilizar um token e não uma chave para se ligar. Portanto, pode continuar a utilizar o controlo de acesso por-dispositivo/por-module ativando ou desativando as identidades de dispositivo/módulo no [registo de identidade](iot-hub-devguide-identity-registry.md). Essa abordagem reduz os riscos de utilização de tokens com os períodos de tempo de expiração.

### <a name="comparison-with-a-custom-gateway"></a>Comparação com um gateway personalizado

O padrão de serviço de token é a forma recomendada para implementar um esquema de autenticação do registo de identidade personalizada com o IoT Hub. Este padrão é recomendado porque o IoT Hub continua a lidar com a maior parte do tráfego de solução. No entanto, se o esquema de autenticação personalizado é então entrelaçado com o protocolo, podem exigir um *gateway personalizado* para processar todo o tráfego. Um exemplo de cenário está a utilizar [Transport Layer Security (TLS) e as chaves pré-partilhadas (PSKs)](https://tools.ietf.org/html/rfc4279). Para obter mais informações, consulte a [gateway de protocolo](iot-hub-protocol-gateway.md) artigo.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem mais informações sobre como controlar o acesso ao seu hub IoT.

## <a name="iot-hub-permissions"></a>Permissões do IoT Hub

A tabela seguinte lista as permissões que pode utilizar para controlar o acesso ao seu hub IoT.

| Permissão | Notas |
| --- | --- |
| **RegistryRead** |Concede acesso de leitura ao registo de identidade. Para obter mais informações, consulte [registo de identidade](iot-hub-devguide-identity-registry.md). <br/>Esta permissão é utilizada pelos serviços cloud de back-end. |
| **RegistryReadWrite** |As concessões de leitura e escrita ao registo de identidade. Para obter mais informações, consulte [registo de identidade](iot-hub-devguide-identity-registry.md). <br/>Esta permissão é utilizada pelos serviços cloud de back-end. |
| **ServiceConnect** |Concede acesso para a cloud voltado para o serviço de comunicação e monitorização de pontos finais. <br/>Concede permissão para receber mensagens dispositivo-para-cloud, enviar mensagens da cloud para dispositivo e obter as confirmações de entrega correspondente. <br/>Concede permissão para recuperar as confirmações de entrega para o ficheiro carrega. <br/>Concede permissão ao duplos de acesso para atualizar as etiquetas e propriedades pretendidas, obter propriedades comunicadas e executar consultas. <br/>Esta permissão é utilizada pelos serviços cloud de back-end. |
| **DeviceConnect** |Concede acesso a pontos finais de voltado para o dispositivo. <br/>Concede permissão para enviar mensagens dispositivo-para-cloud e receber mensagens da cloud para o dispositivo. <br/>Concede permissão para efetuar o carregamento de ficheiros a partir de um dispositivo. <br/>Concede permissão para receber notificações de propriedade do dispositivo duplo pretendido e atualizar o dispositivo duplo propriedades comunicadas. <br/>Concede permissão para executar o ficheiro carrega. <br/>Esta permissão é utilizado pelos dispositivos. |

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador do IoT Hub incluem:

* [Pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descreve vários pontos de extremidade que cada hub IoT expõe para operações de tempo de execução e gestão.

* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md) descreve as quotas e limitação comportamentos que se aplicam ao serviço IoT Hub.

* [Azure SDKs de dispositivo e de serviços de IoT](iot-hub-devguide-sdks.md) indica o idioma de vários SDKs, pode utilizar ao desenvolver aplicações de dispositivos e de serviços que interagem com o IoT Hub.

* [Linguagem de consulta do IoT Hub](iot-hub-devguide-query-language.md) descreve a linguagem de consulta, pode usar para recuperar informações a partir do IoT Hub sobre os seus dispositivos duplos e trabalhos.

* [Suporte para MQTT do IoT Hub](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como controlar o acesso ao IoT Hub, poderá estar interessado nos seguintes tópicos de guia de programadores de IoT Hub:

* [Utilizar dispositivos duplos para sincronizar o estado e configurações](iot-hub-devguide-device-twins.md)
* [Invocar um método direto num dispositivo](iot-hub-devguide-direct-methods.md)
* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Se quiser experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais do IoT Hub:

* [Introdução ao Hub IoT do Azure](quickstart-send-telemetry-node.md)
* [Como enviar mensagens da cloud para o dispositivo com o IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Como processar mensagens de dispositivo-para-cloud do IoT Hub](tutorial-routing.md)
