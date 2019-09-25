---
title: Como usar hubs de notificação com Python
description: Saiba como usar os hubs de notificação do Azure de um back-end do Python.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 5321802023d57bedc135e2d19b2289f1d66deb72
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213073"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Como usar hubs de notificação do Python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Você pode acessar todos os recursos de hubs de notificação de um back-end Java/PHP/Python/Ruby usando a interface REST do hub de notificação, conforme descrito no artigo do MSDN [APIs REST dos hubs de notificação](https://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Este é um exemplo de implementação de referência para implementar o envio de notificação em Python e não é o SDK do Python Hub de notificações oficialmente suportado. O exemplo foi criado usando o Python 3,4.

Este artigo mostra como:

- Crie um cliente REST para recursos de hubs de notificação no Python.
- Envie notificações usando a interface do Python para as APIs REST do hub de notificação.
- Obtenha um despejo da solicitação/resposta HTTP REST para depuração/finalidade da formação educacional.

Você pode seguir o [tutorial de introdução](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) para sua plataforma móvel de sua escolha, implementando a parte de back-end no Python.

> [!NOTE]
> O escopo do exemplo é limitado apenas para enviar notificações e não faz nenhum gerenciamento de registro.

## <a name="client-interface"></a>Interface do cliente

A interface principal do cliente pode fornecer os mesmos métodos que estão disponíveis no [SDK dos hubs de notificação do .net](https://msdn.microsoft.com/library/jj933431.aspx). Essa interface permite que você traduza diretamente todos os tutoriais e exemplos disponíveis atualmente neste site e contribuídos pela Comunidade na Internet.

Você pode encontrar todo o código disponível no [exemplo de wrapper do Python REST].

Por exemplo, para criar um cliente:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Para enviar uma notificação do sistema do Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementação

Se você ainda não fez isso, siga o [tutorial de introdução] até a última seção, em que você precisa implementar o back-end.

Todos os detalhes para implementar um wrapper REST completo podem ser encontrados no [msdn](https://msdn.microsoft.com/library/dn530746.aspx). Esta seção descreve a implementação do Python das principais etapas necessárias para acessar os pontos de extremidade REST dos hubs de notificação e enviar notificações

1. Analisar a cadeia de ligação
2. Gerar o token de autorização
3. Enviar uma notificação usando a API REST HTTP

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação

Aqui está a classe principal que implementa o cliente, cujo Construtor analisa a cadeia de conexão:

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>Criar token de segurança

Os detalhes da criação do token de segurança estão disponíveis [aqui](https://msdn.microsoft.com/library/dn495627.aspx).
Adicione os seguintes métodos à `NotificationHub` classe para criar o token com base no URI da solicitação atual e as credenciais extraídas da cadeia de conexão.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))


@staticmethod
def encode_base64(data):
    return base64.b64encode(data)


def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest


def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>Enviar uma notificação usando a API REST HTTP

Primeiro, permita que o use defina uma classe que represente uma notificação.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Essa classe é um contêiner para um corpo de notificação nativo ou um conjunto de propriedades de uma notificação de modelo, um conjunto de cabeçalhos, que contém formato (plataforma nativa ou modelo) e propriedades específicas da plataforma (como a propriedade de expiração da Apple e cabeçalhos WNS).

Consulte a [documentação de APIs REST dos hubs de notificação](https://msdn.microsoft.com/library/dn495827.aspx) e os formatos específicos das plataformas de notificação para todas as opções disponíveis.

Agora, com essa classe, escreva os métodos de notificação de envio `NotificationHub` dentro da classe.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()


def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)


def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)


def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)


def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)


def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)


def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

    self.send_notification(nh, tags)


def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)


def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

Esses métodos enviam uma solicitação HTTP POST para o ponto de extremidade/messages do hub de notificação, com o corpo e os cabeçalhos corretos para enviar a notificação.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Usando a propriedade de depuração para habilitar o log detalhado

A habilitação da propriedade de depuração durante a inicialização do hub de notificação grava as informações detalhadas de log sobre a solicitação HTTP e o despejo de resposta, bem como o resultado de envio da mensagem de notificação detalhada.
A [Propriedade TestSend dos hubs de notificação](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) retorna informações detalhadas sobre o resultado de envio da notificação.
Para usá-la-Initialize usando o código a seguir:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

A URL HTTP de solicitação de envio do hub de notificação é acrescentada com uma cadeia de caracteres de consulta de "teste" como resultado.

## <a name="complete-tutorial"></a>Concluir o tutorial

Agora você pode concluir o tutorial de introdução enviando a notificação de um back-end do Python.

Inicialize o cliente dos hubs de notificação (substitua a cadeia de conexão e o nome do Hub conforme instruído no [Tutorial de introdução]de introdução):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Em seguida, adicione o código de envio dependendo da sua plataforma móvel de destino. Este exemplo também adiciona métodos de nível superior para habilitar o envio de notificações com base na plataforma, por exemplo, send_windows_notification para Windows; send_apple_notification (para Apple) etc.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8,1 (não Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8,0 e 8,1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Executar seu código Python deve produzir uma notificação que aparece em seu dispositivo de destino.

## <a name="examples"></a>Exemplos

### <a name="enabling-the-debug-property"></a>Habilitando `debug` a propriedade

Ao habilitar o sinalizador de depuração durante a inicialização do NotificationHub, você verá um despejo de solicitação e resposta HTTP detalhado, bem como NotificationOutcome semelhante ao seguinte, em que você pode entender quais cabeçalhos HTTP são passados na solicitação e qual resposta HTTP foi recebido do hub de notificação:

![][1]

Você verá o resultado detalhado do hub de notificação, por exemplo.

- Quando a mensagem for enviada com êxito para o serviço de notificação por push.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Se não houvesse nenhum destino encontrado para nenhuma notificação por push, você provavelmente verá a saída a seguir como resposta (o que indica que nenhum registro foi encontrado para entregar a notificação provavelmente porque os registros tinham alguma incompatibilidade sinalizadores
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Transmitir notificação do sistema para o Windows

Observe os cabeçalhos que são enviados quando você está enviando uma transmissão de notificação do sistema para o cliente Windows.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Enviar notificação especificando uma marca (ou expressão de marca)

Observe o cabeçalho HTTP Tags, que é adicionado à solicitação HTTP (no exemplo a seguir, a notificação é enviada somente aos registros com carga ' esportes ')

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Enviar notificação especificando várias marcas

Observe como o cabeçalho HTTP de marcas é alterado quando várias marcas são enviadas.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Notificação de modelo

Observe que o cabeçalho HTTP do formato é alterado e o corpo da carga é enviado como parte do corpo da solicitação HTTP:

**Modelo registrado no lado do cliente:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Lado do servidor-enviando a carga:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Próximos Passos

Este artigo mostrou como criar um cliente REST do Python para os hubs de notificação. A partir daqui, você pode:

- Baixe o [exemplo de wrapper do Python REST]completo, que contém todo o código deste artigo.
- Continue aprendendo sobre o recurso de marcação de hubs de notificação no [tutorial de últimas notícias]
- Continue aprendendo sobre o recurso de modelos de hubs de notificação no [Tutorial de localização de notícias]

<!-- URLs -->
[Exemplo de wrapper do Python REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Tutorial de introdução]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Tutorial de últimas notícias]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Tutorial de localização de notícias]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
