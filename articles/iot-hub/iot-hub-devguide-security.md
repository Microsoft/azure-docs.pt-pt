---
title: Compreender a segurança do Hub Azure IoT [ Microsoft Docs
description: Guia de desenvolvedores - como controlar o acesso ao IoT Hub para aplicações de dispositivos e aplicações de back-end. Inclui informações sobre fichas de segurança e suporte para certificados X.509.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b7f9ac7e6e7049a3b744151bc9cb05115fbac935
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729217"
---
# <a name="control-access-to-iot-hub"></a>Controlar o acesso ao Hub IoT

Este artigo descreve as opções para garantir o seu hub IoT. O IoT Hub usa *permissões* para dar acesso a cada ponto final do hub IoT. Permissões limitam o acesso a um hub IoT com base na funcionalidade.

Este artigo introduz:

* As diferentes permissões que pode conceder a um dispositivo ou app back-end para aceder ao seu hub IoT.
* O processo de autenticação e os tokens que utiliza para verificar permissões.
* Como analisar credenciais para limitar o acesso a recursos específicos.
* Suporte do IoT Hub para certificados X.509.
* Mecanismos de autenticação de dispositivos personalizados que utilizem registos de identidade do dispositivo existentes ou esquemas de autenticação.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Deve ter permissões adequadas para aceder a qualquer um dos pontos finais do IoT Hub. Por exemplo, um dispositivo deve incluir um símbolo contendo credenciais de segurança, juntamente com todas as mensagens que envia para o IoT Hub.

## <a name="access-control-and-permissions"></a>Controlo de acesso e permissões

Pode conceder [permissões](#iot-hub-permissions) das seguintes formas:

* **Políticas de acesso partilhado a nível ioT.** As políticas de acesso partilhado podem conceder qualquer combinação de [permissões](#iot-hub-permissions). Pode definir políticas no [portal Azure,](https://portal.azure.com)programáticamente utilizando as APIs DE REPOUSO DE [RECURSOS Do Hub IoT,](/rest/api/iothub/iothubresource)ou utilizando a política de [hub az iot](/cli/azure/iot/hub/policy?view=azure-cli-latest) CLI. Um centro ioT recém-criado tem as seguintes políticas padrão:
  
  | Política de Acesso Partilhado | Permissões |
  | -------------------- | ----------- |
  | iothubowner | Toda a permissão |
  | serviço | **Permissões ServiceConnect** |
  | dispositivo | **Permissões de DeviceConnect** |
  | registoLer | **RegistoDepermissões** |
  | registryReadWrite | **RegistoLeia** e **RegistryWrite** permissões |

* **Credenciais de segurança por dispositivo**. Cada IoT Hub contém um registo de [identidade](iot-hub-devguide-identity-registry.md) Para cada dispositivo neste registo de identidade, pode configurar credenciais de segurança que concedem permissões **deviceConnect** scopeed aos pontos finais do dispositivo correspondentes.

Por exemplo, numa solução típica de IoT:

* O componente de gestão do dispositivo utiliza a política *de registoReadWrite.*
* O componente do processador de eventos utiliza a política de *serviço.*
* A componente lógica do negócio do dispositivo de tempo de execução utiliza a política de *serviço.*
* Os dispositivos individuais ligam-se utilizando credenciais armazenadas no registo de identidade do centro IoT.

> [!NOTE]
> Consulte permissões para obter informações [detalhadas.](#iot-hub-permissions)

## <a name="authentication"></a>Autenticação

o Hub IoT do Azure concede acesso a pontos finais ao verificar um token nas políticas de acesso partilhado e credenciais de segurança de registo de identidades.

Credenciais de segurança, como chaves simétricas, nunca são enviadas por cima do fio.

> [!NOTE]
> O fornecedor de recursos Azure IoT Hub é garantido através da sua subscrição Azure, assim como todos os fornecedores do Gestor de [Recursos Azure.](../azure-resource-manager/management/overview.md)

Para obter mais informações sobre como construir e usar fichas de segurança, consulte fichas de [segurança do IoT Hub](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Especificidades do protocolo

Cada protocolo suportado, como MQTT, AMQP e HTTPS, transporta fichas de diferentes formas.

Ao utilizar o MQTT, o pacote CONNECT tem `{iothubhostname}/{deviceId}` o dispositivoId como O Id cliente, no campo Username e um token SAS no campo Password. `{iothubhostname}`deve ser o CName completo do hub IoT (por exemplo, contoso.azure-devices.net).

Ao utilizar [AMQP,](https://www.amqp.org/)o IoT Hub suporta [a SASL PLAIN](https://tools.ietf.org/html/rfc4616) e a [AMQP Claims-Based-Security](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Se utilizar a segurança baseada em sinistros AMQP, a norma especifica como transmitir estes tokens.

Para a SASL PLAIN, o nome de **utilizador** pode ser:

* `{policyName}@sas.root.{iothubName}`se usar fichas de nível ioT.
* `{deviceId}@sas.{iothubname}`se utilizar fichas com mira de dispositivo.

Em ambos os casos, o campo de palavra-passe contém o símbolo, conforme descrito em fichas de [segurança do IoT Hub](iot-hub-devguide-security.md#security-tokens).

HTTPS implementa autenticação incluindo um token válido no cabeçalho do pedido de **autorização.**

#### <a name="example"></a>Exemplo

Nome de utilizador (O DispositivoId é sensível a casos):`iothubname.azure-devices.net/DeviceId`

Palavra-passe (Pode gerar um token SAS com o comando de extensão CLI [az iot hub generate-sas-token,](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)ou as [Ferramentas Azure IoT para Código](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)de Estúdio Visual ):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Os [SDKs Azure IoT](iot-hub-devguide-sdks.md) geram automaticamente fichas ao ligarem-se ao serviço. Em alguns casos, os DST SDKs Azure IoT não suportam todos os protocolos ou todos os métodos de autenticação.

### <a name="special-considerations-for-sasl-plain"></a>Considerações especiais para a SASL PLAIN

Ao utilizar o SASL PLAIN com amQP, um cliente que se liga a um hub IoT pode usar um único símbolo para cada ligação TCP. Quando o símbolo expira, a ligação TCP desliga-se do serviço e aciona uma religação. Este comportamento, embora não seja problemático para uma aplicação de back-end, é prejudicial para uma aplicação de dispositivo pelas seguintes razões:

* Os gateways geralmente se conectam em nome de muitos dispositivos. Ao utilizar o SASL PLAIN, têm de criar uma ligação TCP distinta para cada dispositivo que se ligue a um hub IoT. Este cenário aumenta consideravelmente o consumo de energia e recursos de rede, aumentando a latência de cada ligação do dispositivo.

* Os dispositivos limitados aos recursos são afetados negativamente pelo aumento da utilização de recursos para reconectar-se após a expiração de cada token.

## <a name="scope-iot-hub-level-credentials"></a>Credenciais de nível de hub de âmbito IoT

Você pode consultar políticas de segurança de nível IoT criando tokens com um uri de recursos restritos. Por exemplo, o ponto final para enviar mensagens dispositivo-nuvem a partir de um dispositivo é **/dispositivos/{deviceId}/messages/events**. Também pode utilizar uma política de acesso partilhado ao nível do hub IoT com permissões **DeviceConnect** para assinar um símbolo cujo recurso URI é **/dispositivos/{deviceId}**. Esta abordagem cria um símbolo que só é utilizável para enviar mensagens em nome do **dispositivo de dispositivoId**.

Este mecanismo é semelhante à política de editores do [Event Hubs,](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)e permite-lhe implementar métodos de autenticação personalizados.

## <a name="security-tokens"></a>Fichas de segurança

O IoT Hub utiliza fichas de segurança para autenticar dispositivos e serviços para evitar o envio de chaves no fio. Além disso, as fichas de segurança são limitadas na validade e âmbito do tempo. [Os SDKs Azure IoT](iot-hub-devguide-sdks.md) geram automaticamente fichas sem necessitar de qualquer configuração especial. Alguns cenários exigem que gere e use fichas de segurança diretamente. Tais cenários incluem:

* A utilização direta das superfícies MQTT, AMQP ou HTTPS.

* A implementação do padrão de serviço simbólico, tal como explicado na autenticação do [dispositivo Personalizado.](iot-hub-devguide-security.md#custom-device-and-module-authentication)

O IoT Hub também permite que os dispositivos autentiquem com o Hub IoT utilizando [certificados X.509.](iot-hub-devguide-security.md#supported-x509-certificates)

### <a name="security-token-structure"></a>Estrutura simbólica de segurança

Utiliza fichas de segurança para conceder acesso limitado a dispositivos e serviços limitados no IoT Hub. Para obter autorização para ligar ao IoT Hub, os dispositivos e serviços devem enviar fichas de segurança assinadas com uma chave de acesso partilhado ou simétrica. Estas chaves são armazenadas com uma identidade de dispositivo no registo de identidade.

Um símbolo assinado com uma chave de acesso partilhado dá acesso a todas as funcionalidades associadas às permissões da política de acesso partilhado. Um símbolo assinado com a chave simétrica da identidade do dispositivo apenas concede a permissão **DeviceConnect** para a identidade do dispositivo associado.

O token de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Aqui estão os valores esperados:

| Valor | Descrição |
| --- | --- |
| {assinatura} |Uma cadeia de assinatura HMAC-SHA256 do formulário: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: A chave é descodificada a partir do base64 e utilizada como chave para executar o cálculo HMAC-SHA256. |
| {resourceURI} |Prefixo URI (por segmento) dos pontos finais que podem ser acedidos com este símbolo, começando com o nome anfitrião do hub IoT (sem protocolo). Por exemplo, `myHub.azure-devices.net/devices/device1` |
| {expiração} |UTF8 cordas para o número de segundos desde a época 00:00:00 UTC em 1 de janeiro de 1970. |
| {URL-codificado-recursosURI} |Codificação de URL em maiúscula do recurso minúsculo URI |
| {policyName} |O nome da política de acesso partilhado a que se refere este símbolo. Ausente se o símbolo se referir às credenciais de registo do dispositivo. |

**Nota no prefixo:** O prefixo URI é calculado por segmento e não por carácter. Por `/a/b` exemplo, é `/a/b/c` um prefixo para, mas não para `/a/bc`.

O seguinte snippet Node.js mostra uma função chamada **generateSasToken** que `resourceUri, signingKey, policyName, expiresInMins`calcula o símbolo das inputs . As secções seguintes detalham como inicializar as diferentes inputs para os diferentes casos de utilização de símbolos.

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

Em comparação, o código Python equivalente para gerar um símbolo de segurança é:

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

Seguem-se as instruções de instalação dos pré-requisitos.

[!INCLUDE [Iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]


A funcionalidade em C# para gerar um token de segurança é:

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
> Uma vez que a validade do tempo do token é validada nas máquinas IoT Hub, a deriva no relógio da máquina que gera o símbolo deve ser mínima.

### <a name="use-sas-tokens-in-a-device-app"></a>Use tokens SAS numa aplicação de dispositivo

Existem duas formas de obter permissões **DeviceConnect** com tokens de segurança: utilize uma [chave de dispositivo simétrico a partir do registo de identidade,](#use-a-symmetric-key-in-the-identity-registry)ou utilize uma chave de [acesso partilhada](#use-a-shared-access-policy).

Lembre-se que todas as funcionalidades acessíveis a partir `/devices/{deviceId}`de dispositivos são expostas por design em pontos finais com prefixo .

> [!IMPORTANT]
> A única forma de o IoT Hub autenticar um dispositivo específico é usar a chave simétrica de identidade do dispositivo. Nos casos em que uma política de acesso partilhado é utilizada para aceder à funcionalidade do dispositivo, a solução deve considerar o componente que emite o token de segurança como um subcomponente de confiança.

Os pontos finais virados para o dispositivo são (independentemente do protocolo):

| Ponto Final | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Envie mensagens de dispositivo-para-nuvem. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Receba mensagens cloud-to-device. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Use uma chave simétrica no registo de identidade

Ao utilizar a chave simétrica da identidade do dispositivo para`skn`gerar um símbolo, o elemento policyName () do símbolo é omitido.

Por exemplo, um símbolo criado para aceder a todas as funcionalidades do dispositivo deve ter os seguintes parâmetros:

* recurso URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: qualquer chave `{device id}` simétrica para a identidade,
* sem nome político,
* qualquer tempo de validade.

Um exemplo que utilize a função nó.js anterior seria:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

O resultado, que dá acesso a todas as funcionalidades do dispositivo1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> É possível gerar um token SAS com o comando de extensão CLI [az iot hub generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token), ou as [Ferramentas Azure IoT para Código](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)de Estúdio Visual .

### <a name="use-a-shared-access-policy"></a>Use uma política de acesso partilhado

Quando criar um símbolo de uma política `skn` de acesso partilhado, coloque o campo no nome da política. Esta política deve conceder a permissão **DeviceConnect.**

Os dois principais cenários para a utilização de políticas de acesso partilhado para aceder à funcionalidade do dispositivo são:

* [gateways de protocolo de nuvem,](iot-hub-devguide-endpoints.md)
* [serviços simbólicos utilizados](iot-hub-devguide-security.md#custom-device-and-module-authentication) para implementar esquemas de autenticação personalizada.

Uma vez que a política de acesso partilhado pode potencialmente conceder acesso à ligação como qualquer dispositivo, é importante utilizar o URI de recurso correto ao criar fichas de segurança. Esta definição é especialmente importante para os serviços de token, que têm de analisar o símbolo de um dispositivo específico utilizando o recurso URI. Este ponto é menos relevante para os gateways protocolares, uma vez que já estão a mediar o tráfego para todos os dispositivos.

Como exemplo, um serviço simbólico utilizando a política de acesso partilhado pré-criada chamada **dispositivo** criaria um símbolo com os seguintes parâmetros:

* recurso URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: uma `device` das chaves da apólice,
* nome da `device`política: ,
* qualquer tempo de validade.

Um exemplo que utilize a função nó.js anterior seria:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, que dá acesso a todas as funcionalidades do dispositivo1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Um gateway protocolário poderia usar o mesmo símbolo para `myhub.azure-devices.net/devices`todos os dispositivos simplesmente definindo o recurso URI para .

### <a name="use-security-tokens-from-service-components"></a>Utilize fichas de segurança dos componentes de serviço

Os componentes do serviço só podem gerar fichas de segurança utilizando políticas de acesso partilhado que concedem as permissões adequadas, conforme explicado anteriormente.

Aqui estão as funções de serviço expostas nos pontos finais:

| Ponto Final | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices` |Criar, atualizar, recuperar e eliminar identidades do dispositivo. |
| `{iot hub host name}/messages/events` |Receba mensagens de dispositivo-nuvem. |
| `{iot hub host name}/servicebound/feedback` |Receba feedback para mensagens cloud-to-device. |
| `{iot hub host name}/devicebound` |Envie mensagens cloud-to-device. |

Como exemplo, um serviço gerador utilizando a política de acesso partilhado pré-criada chamada **registryRead** criaria um símbolo com os seguintes parâmetros:

* recurso URI: `{IoT hub name}.azure-devices.net/devices`,
* chave de assinatura: uma `registryRead` das chaves da apólice,
* nome da `registryRead`política: ,
* qualquer tempo de validade.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, que daria acesso à leitura de todas as identidades do dispositivo, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Certificados X.509 suportados

Pode utilizar qualquer certificado X.509 para autenticar um dispositivo com IoT Hub, carregando uma impressão digital de certificado ou uma autoridade de certificado (CA) para o Hub Azure IoT. A autenticação utilizando impressões digitais de certificado verifica que a impressão digital apresentada corresponde à impressão digital configurada. A autenticação utilizando a autoridade do certificado valida a cadeia de certificados. De qualquer forma, o aperto de mão TLS requer que o dispositivo tenha um certificado válido e uma chave privada. Consulte a especificação TLS para obter mais detalhes, por exemplo: [RFC 5246 - A Versão 1.2](https://tools.ietf.org/html/rfc5246/)do Protocolo de Segurança da Camada de Transporte (TLS) 1.2 .

Os certificados suportados incluem:

* **Um certificado X.509 existente.** Um dispositivo pode já ter um certificado X.509 associado ao mesmo. O dispositivo pode utilizar este certificado para autenticar com o IoT Hub. Funciona com impressão digital ou autenticação CA. 

* **Certificado X.509 assinado pela CA.** Para identificar um dispositivo e autenticá-lo com o IoT Hub, pode utilizar um certificado X.509 gerado e assinado por uma Autoridade de Certificação (CA). Funciona com impressão digital ou autenticação CA.

* **Um certificado X-509 auto-gerado e auto-assinado.** Um fabricante de dispositivos ou um implantador interno pode gerar estes certificados e armazenar a chave privada correspondente (e certificado) no dispositivo. Para o efeito, pode utilizar ferramentas como [o Utilitário OpenSSL](https://www.openssl.org/) e [Windows SelfSignedCertificate.](/powershell/module/pkiclient/new-selfsignedcertificate) Só funciona com autenticação de impressão digital. 

Um dispositivo pode utilizar um certificado X.509 ou um símbolo de segurança para autenticação, mas não ambos.

Para obter mais informações sobre a autenticação utilizando a autoridade do certificado, consulte a [Autenticação do Dispositivo utilizando certificados X.509 CA](iot-hub-x509ca-overview.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registe um certificado X.509 para um dispositivo

O [Serviço Azure IoT SDK para C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (versão 1.0.8+) suporta o registo de um dispositivo que utiliza um certificado X.509 para autenticação. Outras APIs, tais como a importação/exportação de dispositivos, também suportam certificados X.509.

Também pode utilizar o comando de extensão CLI [az iot hub de identidade](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) do dispositivo para configurar certificados X.509 para dispositivos.

### <a name="c-support"></a>Apoio\# C

A classe **RegistryManager** fornece uma forma programática de registar um dispositivo. Em particular, os métodos **AddDeviceAsync** e **UpdateDeviceAsync** permitem-lhe registar e atualizar um dispositivo no registo de identidade IoT Hub. Estes dois métodos tomam uma instância **de dispositivo** como entrada. A classe **Dispositivo** inclui uma propriedade **de Autenticação** que lhe permite especificar impressões de polegares de certificado primário e secundário X.509. A impressão digital representa um hash SHA256 do certificado X.509 (armazenado com codificação binária de DER). Tem a opção de especificar uma impressão digital primária ou uma impressão de polegar secundária ou ambas. As impressões digitais primárias e secundárias são suportadas para lidar com cenários de capotamento de certificados.

Aqui está uma\# amostra c código snippet para registar um dispositivo usando uma impressão digital do certificado X.509:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Utilize um certificado X.509 durante as operações em tempo de execução

O [dispositivo Azure IoT SDK para .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (versão 1.0.11+) suporta a utilização de certificados X.509.

### <a name="c-support"></a>Apoio\# C

A classe **DeviceAuthenticationWithX509Certificate** suporta a criação de instâncias **DeviceClient** utilizando um certificado X.509. O certificado X.509 deve estar no formato PFX (também chamado PKCS #12) que inclui a chave privada.

Aqui está um corte de código de amostra:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Autenticação personalizada de dispositivo e módulo

Pode utilizar o registo de [identidade](iot-hub-devguide-identity-registry.md) IoT Hub para configurar credenciais de segurança por dispositivo/módulo e controlo de acesso utilizando [fichas](iot-hub-devguide-security.md#security-tokens). Se uma solução IoT já tiver um sistema de registo de identidade personalizado e/ou sistema de autenticação, considere criar um *serviço simbólico* para integrar esta infraestrutura com o IoT Hub. Desta forma, pode utilizar outras funcionalidades ioT na sua solução.

Um serviço simbólico é um serviço personalizado na nuvem. Utiliza uma política de *acesso partilhado* IoT Hub com permissões **DeviceConnect** ou **ModuleConnect** para criar fichas com *âmbito de dispositivo* ou de alcance *de módulos.* Estas fichas permitem que um dispositivo e um módulo se conectem ao seu hub IoT.

![Passos do padrão de serviço simbólico](./media/iot-hub-devguide-security/tokenservice.png)

Aqui estão os principais passos do padrão de serviço simbólico:

1. Crie uma política de acesso partilhado IoT Hub com permissões **DeviceConnect** ou **ModuleConnect** para o seu hub IoT. Pode criar esta política no [portal Azure](https://portal.azure.com) ou programáticamente. O serviço simbólico usa esta política para assinar os símbolos que cria.

2. Quando um dispositivo/módulo precisa de aceder ao seu hub IoT, solicita um sinal assinado do seu serviço de fichas. O dispositivo pode autenticar com o seu sistema de registo/autenticação de identidade personalizado para determinar a identidade do dispositivo/módulo que o serviço token utiliza para criar o símbolo.

3. O serviço simbólico devolve um símbolo. O símbolo é criado `/devices/{deviceId}` `/devices/{deviceId}/module/{moduleId}` utilizando `resourceURI`ou `deviceId` as, com a `moduleId` autenticação do dispositivo ou como o módulo a ser autenticado. O serviço simbólico usa a política de acesso partilhado para construir o símbolo.

4. O dispositivo/módulo utiliza o símbolo diretamente com o hub IoT.

> [!NOTE]
> Pode utilizar a classe .NET [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) ou a classe Java [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) para criar um símbolo no seu serviço simbólico.

O serviço simbólico pode definir a expiração do símbolo conforme desejado. Quando o símbolo expira, o eixo IoT corta a ligação dispositivo/módulo. Em seguida, o dispositivo/módulo deve solicitar um novo símbolo do serviço de fichas. Um curto prazo de validade aumenta a carga tanto no dispositivo/módulo como no serviço de fichas.

Para que um dispositivo/módulo se conecte ao seu hub, ainda deve adicioná-lo ao registo de identidade Do IoT Hub – mesmo que esteja a usar um símbolo e não uma chave para ligar. Por isso, pode continuar a utilizar o controlo de acesso por dispositivo/módulo, permitindo ou desativando identidades de dispositivo/módulo no [registo de identidade](iot-hub-devguide-identity-registry.md). Esta abordagem atenua os riscos de utilização de fichas com longos prazos de validade.

### <a name="comparison-with-a-custom-gateway"></a>Comparação com um gateway personalizado

O padrão de serviço simbólico é a forma recomendada de implementar um sistema de registo/autenticação de identidade personalizado com o IoT Hub. Este padrão é recomendado porque o IoT Hub continua a lidar com a maior parte do tráfego de solução. No entanto, se o regime de autenticação personalizada estiver tão interligado com o protocolo, poderá necessitar de uma porta de *entrada personalizada* para processar todo o tráfego. Um exemplo de tal cenário é a utilização de Segurança da [Camada de Transporte (TLS) e chaves pré-partilhadas (PSKs)](https://tools.ietf.org/html/rfc4279). Para mais informações, consulte o artigo gateway do [protocolo.](iot-hub-protocol-gateway.md)

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações sobre o controlo do acesso ao seu hub IoT.

## <a name="iot-hub-permissions"></a>IoT Hub permissões

A tabela seguinte lista as permissões que pode utilizar para controlar o acesso ao seu hub IoT.

| Permissão | Notas |
| --- | --- |
| **RegistoRead** |As subvenções lêem o acesso ao registo de identidade. Para mais informações, consulte [registo de identidade](iot-hub-devguide-identity-registry.md). <br/>Esta permissão é usada por serviços de nuvem de back-end. |
| **RegistryReadWrite** |As subvenções lêem e escrevem o acesso ao registo de identidade. Para mais informações, consulte [registo de identidade](iot-hub-devguide-identity-registry.md). <br/>Esta permissão é usada por serviços de nuvem de back-end. |
| **ServiceConnect** |Concede acesso à comunicação virada para o serviço na nuvem e aos pontos finais de monitorização. <br/>Concede permissão para receber mensagens dispositivo-nuvem, enviar mensagens cloud-to-device e recuperar os correspondentes reconhecimentos de entrega. <br/>Concede permissão para recuperar os reconhecimentos de entrega para uploads de ficheiros. <br/>Concede permissão para aceder a gémeos para atualizar etiquetas e propriedades desejadas, recuperar propriedades reportadas e executar consultas. <br/>Esta permissão é usada por serviços de nuvem de back-end. |
| **DispositivoConnect** |Concede acesso a pontos finais virados para dispositivos. <br/>Concede permissão para enviar mensagens de dispositivo-nuvem e receber mensagens cloud-to-device. <br/>Concede permissão para realizar o upload de ficheiros a partir de um dispositivo. <br/>Concede permissão para receber notificações de propriedade de twin desejadas do dispositivo e atualizar propriedades reportadas twin. <br/>Concede permissão para realizar uploads de ficheiros. <br/>Esta permissão é utilizada por dispositivos. |

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia de desenvolvimento do IoT Hub incluem:

* [Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais que cada hub IoT expõe para operações de execução e gestão.

* [O estrangulamento e](iot-hub-devguide-quotas-throttling.md) as quotas descrevem as quotas e comportamentos de estrangulamento que se aplicam ao serviço IoT Hub.

* O [dispositivo e o serviço Azure IoT](iot-hub-devguide-sdks.md) listam os vários SDKs linguísticos que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* A linguagem de [consulta ioT Hub](iot-hub-devguide-query-language.md) descreve a linguagem de consulta que você pode usar para obter informações do IoT Hub sobre os seus gémeos e empregos do seu dispositivo.

* [O suporte IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

* [RFC 5246 - O Protocolo de Segurança da Camada de Transporte (TLS) Versão 1.2](https://tools.ietf.org/html/rfc5246/) fornece mais informações sobre a autenticação TLS.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a controlar o acesso ioT Hub, pode estar interessado nos seguintes tópicos de guia de desenvolvimento do IoT Hub:

* [Use gémeos dispositivos para sincronizar estado e configurações](iot-hub-devguide-device-twins.md)
* [Invoque um método direto num dispositivo](iot-hub-devguide-direct-methods.md)
* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Se quiser experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais IoT Hub:

* [Começar com o Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Como enviar mensagens cloud-to-device com IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Como processar mensagens ioT Hub-to-cloud](tutorial-routing.md)
