---
title: Compreenda a segurança do Azure IoT Hub ! Microsoft Docs
description: Guia do desenvolvedor - como controlar o acesso ao IoT Hub para aplicações de dispositivos e aplicações de back-end. Inclui informações sobre fichas de segurança e suporte para certificados X.509.
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
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
- devx-track-azurecli
ms.openlocfilehash: 8627681d843d15658882529424375486a4cdb1b9
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845174"
---
# <a name="control-access-to-iot-hub"></a>Controlar o acesso ao Hub IoT

Este artigo descreve as opções para garantir o seu hub IoT. O IoT Hub utiliza *permissões* para conceder acesso a cada ponto final do hub IoT. As permissões limitam o acesso a um hub IoT com base na funcionalidade.

Este artigo introduz:

* As diferentes permissões que pode conceder a um dispositivo ou app back-end para aceder ao seu hub IoT.
* O processo de autenticação e os tokens que utiliza para verificar permissões.
* Como estender credenciais para limitar o acesso a recursos específicos.
* Suporte IoT Hub para certificados X.509.
* Mecanismos de autenticação de dispositivos personalizados que utilizam registos de identidade de dispositivos existentes ou esquemas de autenticação.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Deve ter permissões apropriadas para aceder a qualquer um dos pontos finais do IoT Hub. Por exemplo, um dispositivo deve incluir um símbolo contendo credenciais de segurança, juntamente com todas as mensagens que envia para o IoT Hub.

## <a name="access-control-and-permissions"></a>Controlo de acesso e permissões

Pode conceder permissões das [seguintes](#iot-hub-permissions) formas:

* **Políticas de acesso partilhado ao nível do hub IoT.** As políticas de acesso partilhado podem conceder qualquer combinação de [permissões.](#iot-hub-permissions) Pode definir políticas no [portal Azure](https://portal.azure.com), programáticamente utilizando as [APIs de Recursos de IoT Hub](/rest/api/iothub/iothubresource), ou utilizando a [política az iot hub](/cli/azure/iot/hub/policy) CLI. Um hub IoT recém-criado tem as seguintes políticas padrão:
  
  | Política de Acesso Partilhado | Permissões |
  | -------------------- | ----------- |
  | iothubowner | Toda a permissão |
  | serviço | **Permissões de ServiceConnect** |
  | dispositivo | **Permissões de DeviceConnect** |
  | registryRead | **Permissões registrodidas** |
  | registryReadWrite | **RegistosRead** e **RegistoDesesesite** |

* **Credenciais de segurança por dispositivo.** Cada Hub IoT contém um [registo de identidade](iot-hub-devguide-identity-registry.md) Para cada dispositivo neste registo de identidade, pode configurar credenciais de segurança que concedem permissões **deviceConnect** miradas aos pontos finais do dispositivo correspondentes.

Por exemplo, numa solução típica de IoT:

* O componente de gestão do dispositivo utiliza a política *de registroReadWrite.*
* O componente do processador de eventos utiliza a política *de serviço.*
* O componente de lógica de negócio do dispositivo de tempo de execução utiliza a política *de serviço.*
* Os dispositivos individuais ligam-se utilizando credenciais armazenadas no registo de identidade do hub IoT.

> [!NOTE]
> Consulte as permissões para obter informações [detalhadas.](#iot-hub-permissions)

## <a name="authentication"></a>Autenticação

o Hub IoT do Azure concede acesso a pontos finais ao verificar um token nas políticas de acesso partilhado e credenciais de segurança de registo de identidades.

Credenciais de segurança, como chaves simétricas, nunca são enviadas pelo fio.

> [!NOTE]
> O fornecedor de recursos Azure IoT Hub é protegido através da sua subscrição Azure, assim como todos os fornecedores do [Azure Resource Manager](../azure-resource-manager/management/overview.md).

Para obter mais informações sobre como construir e usar fichas de segurança, consulte [fichas de segurança IoT Hub](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Especificidades do protocolo

Cada protocolo suportado, como MQTT, AMQP e HTTPS, transporta fichas de diferentes maneiras.

Ao utilizar o MQTT, o pacote CONNECT tem o dispositivoId como ClientId, `{iothubhostname}/{deviceId}` no campo username, e um token SAS no campo Password. `{iothubhostname}` deve ser o CName completo do hub IoT (por exemplo, contoso.azure-devices.net).

Ao utilizar [amQP,](https://www.amqp.org/)o IoT Hub suporta [sasl PLAIN](https://tools.ietf.org/html/rfc4616) e [AMQP Claims-Based-Security](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Se utilizar a segurança baseada em reclamações amQP, a norma especifica como transmitir estes tokens.

Para SASL PLAIN, o **nome de utilizador** pode ser:

* `{policyName}@sas.root.{iothubName}` se utilizar fichas de nível de hub IoT.
* `{deviceId}@sas.{iothubname}` se utilizar fichas com mira de dispositivos.

Em ambos os casos, o campo de palavra-passe contém o símbolo, conforme descrito em [fichas de segurança IoT Hub](iot-hub-devguide-security.md#security-tokens).

HTTPS implementa a autenticação através da inclusão de um token válido no cabeçalho do pedido **de autorização.**

#### <a name="example"></a>Exemplo

Nome de utilizador (DeviceId é sensível a casos): `iothubname.azure-devices.net/DeviceId`

Palavra-passe (Pode gerar um token SAS com o comando de extensão CLI [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token), ou as [Ferramentas IoT Azure para Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Os [Azure IoT SDKs](iot-hub-devguide-sdks.md) geram automaticamente fichas quando se ligam ao serviço. Em alguns casos, os SDKs Azure IoT não suportam todos os protocolos ou todos os métodos de autenticação.

### <a name="special-considerations-for-sasl-plain"></a>Considerações especiais para a SASL PLAIN

Ao utilizar o SASL PLAIN com AMQP, um cliente que se conecta a um hub IoT pode utilizar um único token para cada ligação TCP. Quando o token expira, a ligação TCP desliga-se do serviço e aciona uma reconexão. Este comportamento, embora não seja problemático para uma aplicação de back-end, é prejudicial para uma aplicação do dispositivo pelas seguintes razões:

* Gateways geralmente se ligam em nome de muitos dispositivos. Ao utilizar o SASL PLAIN, têm de criar uma ligação TCP distinta para cada dispositivo que se ligue a um hub IoT. Este cenário aumenta consideravelmente o consumo de energia e recursos de rede, e aumenta a latência de cada ligação do dispositivo.

* Os dispositivos com restrições de recursos são afetados negativamente pelo aumento da utilização de recursos para se reconectarem após cada validade do token.

## <a name="scope-iot-hub-level-credentials"></a>Credenciais de nível de hub IoT de âmbito

Você pode estender as políticas de segurança de nível ioT ao criar fichas com um recurso restrito URI. Por exemplo, o ponto final para enviar mensagens dispositivo-a-nuvem a partir de um dispositivo é **/dispositivo/{deviceId}/mensagens/eventos**. Também pode utilizar uma política de acesso partilhado ao nível do hub IoT com permissões **DeviceConnect** para assinar um token cujo recursoURI é **/dispositivos/{deviceId}**. Esta abordagem cria um símbolo que só é utilizável para enviar mensagens em nome do **dispositivo dispositivoId**.

Este mecanismo é semelhante à política de editores do [Event Hubs](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)e permite-lhe implementar métodos de autenticação personalizados.

## <a name="security-tokens"></a>Tokens de segurança

O IoT Hub utiliza fichas de segurança para autenticar dispositivos e serviços para evitar o envio de chaves no fio. Além disso, as fichas de segurança são limitadas na validade e âmbito de tempo. [Os SDKs Azure IoT](iot-hub-devguide-sdks.md) geram automaticamente fichas sem necessitar de qualquer configuração especial. Alguns cenários requerem que gere e use fichas de segurança diretamente. Tais cenários incluem:

* A utilização direta das superfícies MQTT, AMQP ou HTTPS.

* A implementação do padrão de serviço token, conforme explicado na [autenticação do dispositivo personalizado.](iot-hub-devguide-security.md#custom-device-and-module-authentication)

O IoT Hub também permite que os dispositivos autentem com o IoT Hub utilizando [certificados X.509](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Estrutura simbólica de segurança

Utiliza fichas de segurança para conceder acesso limitado a dispositivos e serviços a funcionalidades específicas no IoT Hub. Para obter autorização para ligar ao IoT Hub, os dispositivos e serviços devem enviar fichas de segurança assinadas com um acesso partilhado ou uma chave simétrica. Estas chaves são armazenadas com uma identidade do dispositivo no registo de identidade.

Um token assinado com uma chave de acesso partilhado dá acesso a todas as funcionalidades associadas às permissões de política de acesso partilhado. Um símbolo assinado com a chave simétrica da identidade do dispositivo apenas concede a permissão **deviceConnect** para a identidade do dispositivo associado.

O símbolo de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Aqui estão os valores esperados:

| Valor | Descrição |
| --- | --- |
| {assinatura} |Uma sequência de assinatura HMAC-SHA256 do formulário: `{URL-encoded-resourceURI} + "\n" + expiry` . **Importante**: A chave é descodificada a partir da base64 e usada como chave para executar o cálculo HMAC-SHA256. |
| {resourceURI} |Prefixo URI (por segmento) dos pontos finais que podem ser acedidos com este token, começando pelo nome de anfitrião do hub IoT (sem protocolo). Por exemplo, `myHub.azure-devices.net/devices/device1` |
| {expiração} |UtF8 strings por número de segundos desde a época 00:00:00 UTC em 1 de janeiro de 1970. |
| {URL-codificado-recursosURI} |Codificação de URL minúscula do recurso uri de menor caso |
| {políticaName} |O nome da política de acesso partilhado a que se refere este símbolo. Ausente se o símbolo se refere a credenciais de registo de dispositivos. |

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
    print(sign_key)
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

A funcionalidade em C# para gerar um símbolo de segurança é:

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

Para Java:
```java
    public static String generateSasToken(String resourceUri, String key) throws Exception {
        // Token will expire in one hour
        var expiry = Instant.now().getEpochSecond() + 3600;

        String stringToSign = URLEncoder.encode(resourceUri, StandardCharsets.UTF_8) + "\n" + expiry;
        byte[] decodedKey = Base64.getDecoder().decode(key);

        Mac sha256HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKey = new SecretKeySpec(decodedKey, "HmacSHA256");
        sha256HMAC.init(secretKey);
        Base64.Encoder encoder = Base64.getEncoder();

        String signature = new String(encoder.encode(
            sha256HMAC.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8))), StandardCharsets.UTF_8);

        String token = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, StandardCharsets.UTF_8)
                + "&sig=" + URLEncoder.encode(signature, StandardCharsets.UTF_8.name()) + "&se=" + expiry;
            
        return token;
    }
```


> [!NOTE]
> Uma vez que a validade do token é validada em máquinas IoT Hub, a deriva no relógio da máquina que gera o símbolo deve ser mínima.

### <a name="use-sas-tokens-in-a-device-app"></a>Use fichas SAS numa aplicação para dispositivos

Existem duas formas de obter permissões **deviceConnect** com ioT Hub com fichas de segurança: use uma [chave de dispositivo simétrico a partir do registo de identidade,](#use-a-symmetric-key-in-the-identity-registry)ou use uma [chave de acesso partilhada](#use-a-shared-access-policy).

Lembre-se de que todas as funcionalidades acessíveis a partir de dispositivos são expostas por design em pontos finais com prefixo `/devices/{deviceId}` .

> [!IMPORTANT]
> A única forma de o IoT Hub autenticar um dispositivo específico é utilizar a chave simétrica de identidade do dispositivo. Nos casos em que uma política de acesso partilhado é utilizada para aceder à funcionalidade do dispositivo, a solução deve considerar o componente que emite o token de segurança como um subcomponente de confiança.

Os pontos finais voltados para o dispositivo são (independentemente do protocolo):

| Ponto final | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Envie mensagens dispositivo-a-nuvem. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Receba mensagens nuvem-dispositivo. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Use uma chave simétrica no registo de identidade

Ao utilizar a chave simétrica da identidade do dispositivo para gerar um token, o elemento de políticaName `skn` () do token é omitido.

Por exemplo, um token criado para aceder a todas as funcionalidades do dispositivo deve ter os seguintes parâmetros:

* recurso URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* chave de assinatura: qualquer chave simétrica para a `{device id}` identidade,
* sem nome de política,
* qualquer tempo de validade.

Um exemplo utilizando a função Node.js anterior seria:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

O resultado, que dá acesso a todas as funcionalidades do dispositivo1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> É possível gerar um token SAS com o comando de extensão CLI [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token), ou as [Ferramentas Azure IoT para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Use uma política de acesso compartilhado

Quando criar um símbolo a partir de uma política de acesso partilhado, desacorda `skn` o campo com o nome da apólice. Esta política deve conceder a permissão **de DeviceConnect.**

Os dois principais cenários para a utilização de políticas de acesso partilhado à funcionalidade do dispositivo de acesso são:

* [gateways de protocolo de nuvem,](iot-hub-devguide-endpoints.md)
* [serviços simbólicos utilizados](iot-hub-devguide-security.md#custom-device-and-module-authentication) para implementar esquemas de autenticação personalizada.

Uma vez que a política de acesso partilhado pode potencialmente conceder acesso à ligação como qualquer dispositivo, é importante utilizar o URI de recurso correto ao criar fichas de segurança. Esta definição é especialmente importante para os serviços simbólicos, que têm de estender o token a um dispositivo específico utilizando o recurso URI. Este ponto é menos relevante para os gateways de protocolo, uma vez que já estão a mediar o tráfego para todos os dispositivos.

Como exemplo, um serviço simbólico utilizando a política de acesso partilhado pré-criada chamada **dispositivo** criaria um símbolo com os seguintes parâmetros:

* recurso URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* chave de assinatura: uma das chaves da `device` apólice,
* nome da política: `device` ,
* qualquer tempo de validade.

Um exemplo utilizando a função Node.js anterior seria:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, que dá acesso a todas as funcionalidades do dispositivo1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Um portal de protocolo poderia utilizar o mesmo símbolo para todos os dispositivos que simplesmente definem o recurso URI para `myhub.azure-devices.net/devices` .

### <a name="use-security-tokens-from-service-components"></a>Utilize fichas de segurança a partir de componentes de serviço

Os componentes de serviço só podem gerar fichas de segurança utilizando políticas de acesso partilhado que concedam as permissões apropriadas, como explicado anteriormente.

Aqui estão as funções de serviço expostas nos pontos finais:

| Ponto final | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices` |Criar, atualizar, recuperar e eliminar identidades do dispositivo. |
| `{iot hub host name}/messages/events` |Receba mensagens de dispositivo para nuvem. |
| `{iot hub host name}/servicebound/feedback` |Receba feedback para mensagens cloud-to-device. |
| `{iot hub host name}/devicebound` |Envie mensagens nuvem-a-dispositivo. |

Como exemplo, um serviço gerador de utilização da política de acesso partilhado pré-criada chamada **registryRead** criaria um símbolo com os seguintes parâmetros:

* recurso URI: `{IoT hub name}.azure-devices.net/devices` ,
* chave de assinatura: uma das chaves da `registryRead` apólice,
* nome da política: `registryRead` ,
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

Pode utilizar qualquer certificado X.509 para autenticar um dispositivo com o IoT Hub, enviando uma impressão digital de certificado ou uma autoridade de certificado (CA) para o Azure IoT Hub. A autenticação utilizando impressões digitais de certificado verifica que a impressão digital apresentada corresponde à impressão digital configurada. A autenticação através da autoridade de certificados valida a cadeia de certificados. De qualquer forma, o aperto de mão TLS requer que o dispositivo tenha um certificado válido e uma chave privada. Consulte a especificação TLS para obter mais detalhes, por exemplo: [RFC 5246 - A Versão 1.2 do Protocolo de Segurança da Camada de Transporte (TLS)](https://tools.ietf.org/html/rfc5246/)

Os certificados suportados incluem:

* **Um certificado X.509 existente.** Um dispositivo pode já ter um certificado X.509 associado ao mesmo. O dispositivo pode utilizar este certificado para autenticar com o IoT Hub. Funciona com impressão digital ou autenticação ca. 

* **Certificado X.509 assinado pela CA**. Para identificar um dispositivo e autenticá-lo com o IoT Hub, pode utilizar um certificado X.509 gerado e assinado por uma Autoridade de Certificação (CA). Funciona com impressão digital ou autenticação ca.

* **Um certificado X-509 auto-gerado e auto-assinado.** Um fabricante de dispositivos ou um implantador interno podem gerar estes certificados e armazenar a chave privada correspondente (e certificado) no dispositivo. Pode utilizar ferramentas como [o utilitário OpenSSL](https://www.openssl.org/) e [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) para este fim. Só funciona com a autenticação de impressão digital.

Um dispositivo pode utilizar um certificado X.509 ou um símbolo de segurança para autenticação, mas não ambos. Com a autenticação do certificado X.509, certifique-se de que tem uma estratégia para lidar com a capotagem do certificado quando um certificado existente expirar.

A seguinte funcionalidade não é suportada para dispositivos que utilizem a autenticação X.509 CA:

* HTTPS, MQTT sobre WebSockets e AMQP sobre os protocolos WebSockets.
* Uploads de ficheiros (todos os protocolos).

Para obter mais informações sobre a autenticação utilizando a autoridade do certificado, consulte [a Autenticação do Dispositivo utilizando certificados X.509 CA](iot-hub-x509ca-overview.md). Para obter informações sobre como carregar e verificar uma autoridade de certificados com o seu hub IoT, consulte [configurar a segurança X.509 no seu hub Azure IoT](iot-hub-security-x509-get-started.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registar um certificado X.509 para um dispositivo

O [Serviço Azure IoT SDK para C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (versão 1.0.8+) suporta o registo de um dispositivo que utiliza um certificado X.509 para autenticação. Outras APIs, tais como a importação/exportação de dispositivos, também suportam certificados X.509.

Também pode utilizar o comando de extensão CLI [az iot hub de identidade do dispositivo](/cli/azure/ext/azure-iot/iot/hub/device-identity) para configurar certificados X.509 para dispositivos.

### <a name="c-support"></a>Suporte C \#

A classe **RegistryManager** fornece uma forma programática de registar um dispositivo. Em particular, os métodos **AddDeviceAsync** e **UpdateDeviceAsync** permitem-lhe registar e atualizar um dispositivo no registo de identidade do IoT Hub. Estes dois métodos tomam uma instância **do dispositivo** como entrada. A classe **Dispositivo** inclui uma propriedade **de autenticação** que permite especificar as impressões digitais de certificado X.509 primário e secundário. A impressão digital representa um haxixe SHA256 do certificado X.509 (armazenado com codificação binária DER). Tem a opção de especificar uma impressão digital primária ou uma impressão digital secundária ou ambas. As impressões digitais primárias e secundárias são suportadas para lidar com cenários de capotamento de certificados.

Aqui está uma amostra de código C \# para registar um dispositivo usando uma impressão digital de certificado X.509:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Utilize um certificado X.509 durante as operações de tempo de execução

O [dispositivo Azure IoT SDK para .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (versão 1.0.11+) suporta a utilização de certificados X.509.

### <a name="c-support"></a>Suporte C \#

A classe **DeviceAuthenticationWithX509Certificate** suporta a criação de instâncias **deviceClient** usando um certificado X.509. O certificado X.509 deve estar no formato PFX (também chamado PKCS #12) que inclui a chave privada.

Aqui está um corte de código de amostra:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Autenticação personalizada do dispositivo e do módulo

Pode utilizar o registo [de identidade](iot-hub-devguide-identity-registry.md) IoT Hub para configurar credenciais de segurança por dispositivo/módulo e controlo de acesso utilizando [fichas](iot-hub-devguide-security.md#security-tokens). Se uma solução IoT já tiver um registo de identidade personalizado e/ou um esquema de autenticação, considere criar um *serviço simbólico* para integrar esta infraestrutura com o IoT Hub. Desta forma, pode utilizar outras funcionalidades de IoT na sua solução.

Um serviço de token é um serviço de nuvem personalizado. Utiliza uma *política de acesso compartilhado* IoT Hub com permissões **DeviceConnect** ou **ModuleConnect** para criar fichas *com âmbito de dispositivo* ou *módulos.* Estes tokens permitem que um dispositivo e módulo se conectem ao seu hub IoT.

![Passos do padrão de serviço simbólico](./media/iot-hub-devguide-security/tokenservice.png)

Aqui estão os principais passos do padrão de serviço simbólico:

1. Crie uma política de acesso compartilhado IoT Hub com permissões **DeviceConnect** ou **ModuleConnect** para o seu hub IoT. Pode criar esta política no [portal Azure](https://portal.azure.com) ou programáticamente. O serviço token usa esta política para assinar os tokens que cria.

2. Quando um dispositivo/módulo precisa de aceder ao seu hub IoT, solicita um token assinado do seu serviço de token. O dispositivo pode autenticar com o seu sistema de registo/autenticação de identidade personalizado para determinar a identidade do dispositivo/módulo que o serviço token utiliza para criar o token.

3. O serviço simbólico devolve um símbolo. O token é criado utilizando `/devices/{deviceId}` ou como , com o dispositivo a ser `/devices/{deviceId}/module/{moduleId}` `resourceURI` `deviceId` autenticado ou como o módulo a ser `moduleId` autenticado. O serviço token usa a política de acesso partilhado para construir o símbolo.

4. O dispositivo/módulo utiliza o símbolo diretamente com o hub IoT.

> [!NOTE]
> Pode utilizar a classe .NET [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) ou a classe Java [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) para criar um símbolo no seu serviço de token.

O serviço token pode definir a expiração do símbolo conforme desejado. Quando o token expira, o hub IoT corta a ligação dispositivo/módulo. Em seguida, o dispositivo/módulo deve solicitar um novo token do serviço token. Um curto prazo de validade aumenta a carga tanto no dispositivo como no módulo e no serviço token.

Para que um dispositivo/módulo se conecte ao seu hub, deve ainda adicioná-lo ao registo de identidade do IoT Hub – mesmo que esteja a utilizar um token e não uma chave para se ligar. Por isso, pode continuar a utilizar o controlo de acesso por dispositivo/módulo, ativando ou desativando identidades de dispositivo/módulo no [registo de identidade.](iot-hub-devguide-identity-registry.md) Esta abordagem atenua os riscos de utilização de fichas com longos prazos de validade.

### <a name="comparison-with-a-custom-gateway"></a>Comparação com um portal personalizado

O padrão de serviço token é a forma recomendada de implementar um sistema de registo de identidade/autenticação personalizado com o IoT Hub. Este padrão é recomendado porque o IoT Hub continua a lidar com a maior parte do tráfego de solução. No entanto, se o esquema de autenticação personalizada estiver tão interligado com o protocolo, poderá necessitar de uma *porta de entrada personalizada* para processar todo o tráfego. Um exemplo de tal cenário é a utilização de [segurança da camada de transporte (TLS) e teclas pré-partilhadas (PSKs)](https://tools.ietf.org/html/rfc4279). Para mais informações, consulte o artigo [do portal protocolar.](iot-hub-protocol-gateway.md)

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações sobre o controlo do acesso ao seu hub IoT.

## <a name="iot-hub-permissions"></a>Permissões IoT Hub

A tabela que se segue lista as permissões que pode utilizar para controlar o acesso ao seu hub IoT.

| Permissão | Notas |
| --- | --- |
| **RegistroRead** |Os subsídios lêem o acesso ao registo de identidade. Para mais informações, consulte [o registo de identidade.](iot-hub-devguide-identity-registry.md) <br/>Esta permissão é utilizada pelos serviços de nuvem de fundo. |
| **RegistroReadWrite** |Os subsídios lêem e escrevem acesso ao registo de identidade. Para mais informações, consulte [o registo de identidade.](iot-hub-devguide-identity-registry.md) <br/>Esta permissão é utilizada pelos serviços de nuvem de fundo. |
| **ServiceConnect** |Concede acesso a pontos finais de comunicação e monitorização de serviços na nuvem. <br/>Concede permissão para receber mensagens de dispositivo para nuvem, enviar mensagens nuvem-para-dispositivo e recuperar os agradecimentos de entrega correspondentes. <br/>Concede permissão para recuperar reconhecimentos de entrega para uploads de ficheiros. <br/>Concede permissão para aceder a gémeos para atualizar tags e propriedades desejadas, recuperar propriedades reportadas e executar consultas. <br/>Esta permissão é utilizada pelos serviços de nuvem de fundo. |
| **DispositivoConnect** |Concede acesso a pontos finais voltados para o dispositivo. <br/>Concede permissão para enviar mensagens de dispositivo para nuvem e receber mensagens nuvem-a-dispositivo. <br/>Concede permissão para realizar upload de ficheiros a partir de um dispositivo. <br/>Concede permissão para receber notificações de propriedade desejadas pelo dispositivo twin e atualizar propriedades reportadas pelo dispositivo twin. <br/>Concede permissão para realizar uploads de ficheiros. <br/>Esta permissão é utilizada por dispositivos. |

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia de desenvolvimento do IoT Hub incluem:

* [Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais que cada hub IoT expõe para operações de tempo de execução e gestão.

* [O estrangulamento e as quotas](iot-hub-devguide-quotas-throttling.md) descrevem as quotas e comportamentos de estrangulamento que se aplicam ao serviço IoT Hub.

* [O dispositivo e serviço Azure IoT lista](iot-hub-devguide-sdks.md) os vários SDKs de linguagem que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* [IoT Hub idioma de consulta](iot-hub-devguide-query-language.md) descreve a linguagem de consulta que você pode usar para obter informações do IoT Hub sobre os seus gémeos e empregos do seu dispositivo.

* [O suporte do IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

* [RFC 5246 - A versão 1.2 do Protocolo de Segurança da Camada de Transporte (TLS)](https://tools.ietf.org/html/rfc5246/) fornece mais informações sobre a autenticação TLS.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a controlar o acesso ao IoT Hub, poderá estar interessado nos seguintes tópicos de guia de desenvolvimento do IoT Hub:

* [Use gémeos do dispositivo para sincronizar estado e configurações](iot-hub-devguide-device-twins.md)
* [Invocar um método direto num dispositivo](iot-hub-devguide-direct-methods.md)
* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Se quiser experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais do IoT Hub:

* [Introdução ao Hub IoT do Azure](quickstart-send-telemetry-node.md)
* [Como enviar mensagens cloud-to-device com IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Como processar mensagens ioT Hub dispositivo-nuvem](tutorial-routing.md)
