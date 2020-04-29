---
title: Como usar centros de notificação com Python
description: Saiba como utilizar hubs de notificação Azure a partir de uma aplicação Python.
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
ms.openlocfilehash: 1ff8c382813654b1dee38a99bf2cc0ca67afbedd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76313832"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Como usar centros de notificação da Python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Pode aceder a todas as funcionalidades do Notification Hubs a partir de um back-end Java/PHP/Python/Ruby utilizando a interface Notification Hub REST, conforme descrito no artigo mSDN [Notification Hubs REST APIs](https://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Esta é uma implementação de referência de amostra para a implementação da notificação envia em Python e não é o Hub Python SDK oficialmente suportado. A amostra foi criada usando Python 3.4.

Este artigo mostra-lhe como:

- Construa um cliente REST para funcionalidades de Centros de Notificação em Python.
- Envie notificações utilizando a interface Python para as APIs DO Hub de Notificação.
- Obtenha uma lixeira do pedido/resposta http REST para fins de depuração/educação.

Pode seguir o [tutorial Get started](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) para a sua plataforma móvel de eleição, implementando a parte traseira em Python.

> [!NOTE]
> O âmbito da amostra limita-se apenas ao envio de notificações e não faz qualquer gestão de registo.

## <a name="client-interface"></a>Interface do cliente

A interface principal do cliente pode fornecer os mesmos métodos disponíveis no [.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx). Esta interface permite-lhe traduzir diretamente todos os tutoriais e amostras atualmente disponíveis neste site, e contribuído pela comunidade na internet.

Pode encontrar todo o código disponível na amostra de [invólucro Python REST].

Por exemplo, criar um cliente:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Para enviar uma notificação de torrada do Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementação

Se ainda não o fez, siga o tutorial get [started] até à última secção onde tem de implementar o back-end.

Todos os detalhes para implementar um invólucro REST completo podem ser encontrados na [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). Esta secção descreve a implementação python dos principais passos necessários para aceder aos pontos finais do REST dos Centros de Notificação e enviar notificações

1. Analisar a cadeia de ligação
2. Gerar o símbolo de autorização
3. Envie uma notificação usando http REST API

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação

Aqui está a classe principal implementando o cliente, cujo construtor analisa a cadeia de ligação:

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

### <a name="create-security-token"></a>Criar ficha de segurança

Os detalhes da criação de fichas de segurança estão disponíveis [aqui.](https://msdn.microsoft.com/library/dn495627.aspx)
Adicione os seguintes métodos à `NotificationHub` classe para criar o símbolo com base no URI do pedido atual e nas credenciais extraídas da cadeia de ligação.

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

### <a name="send-a-notification-using-http-rest-api"></a>Envie uma notificação usando http REST API

Em primeiro lugar, vamos definir uma classe que representa uma notificação.

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

Esta classe é um recipiente para um corpo de notificação nativo ou um conjunto de propriedades de uma notificação de modelo, um conjunto de cabeçalhos, que contém formato (plataforma ou modelo nativo) e propriedades específicas da plataforma (como propriedade de expiração da Apple e cabeçalhos WNS).

Consulte a documentação do [REST APIs](https://msdn.microsoft.com/library/dn495827.aspx) dos Centros de Notificação e os formatos das plataformas de notificação específicas para todas as opções disponíveis.

Agora com esta aula, escreva os `NotificationHub` métodos de notificação de envio dentro da classe.

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

Estes métodos enviam um pedido HTTP POST para o ponto final/mensagens do seu centro de notificação, com o corpo e cabeçalhos corretos para enviar a notificação.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Usando propriedade de depuração para permitir a exploração madeireira detalhada

Ativar a propriedade de depuração enquanto inicializa o Centro de Notificação escreve informações detalhadas sobre o depósito de pedidos e resposta http, bem como o resultado de envio de mensagem de notificação detalhada.
A [propriedade Notification Hubs TestSend](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) devolve informações detalhadas sobre o resultado do envio da notificação.
Para usá-lo - inicialize utilizando o seguinte código:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

O Hub de Notificação Enviar o pedido HTTP URL é anexado com uma corda de consulta de "teste".

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Complete o tutorial

Agora pode completar o tutorial Get Started enviando a notificação de um python back-end.

Inicialize o seu cliente De Notificação Hubs (substitua a cadeia de ligação e o nome do hub, conforme instruído no [tutorial Get started]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Em seguida, adicione o código de envio dependendo da sua plataforma móvel alvo. Esta amostra também adiciona métodos de nível mais elevado supor que permitem o envio de notificações com base na plataforma, por exemplo, send_windows_notification para janelas; send_apple_notification (para maçã) etc.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8.1 (não-Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 e 8.1 Silverlight

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

### <a name="kindle-fire"></a>Fogo de Kindle

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

A execução do seu código Python deve produzir uma notificação que apareça no seu dispositivo-alvo.

## <a name="examples"></a>Exemplos

### <a name="enabling-the-debug-property"></a>Habilitar `debug` a propriedade

Ao ativar a bandeira de depuração ao inicializar o NotificationHub, vê um pedido e um despejo de resposta detalhados em HTTP, bem como o NotificationOutcome, como o seguinte, onde pode compreender o que os cabeçalhos http são passados no pedido e qual a resposta http foi recebida do Centro de Notificação:

![][1]

Por exemplo, vê-se um resultado detalhado do Centro de Notificação.

- quando a mensagem for enviada com sucesso para o Serviço de Notificação push.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Se não houver alvos encontrados para qualquer notificação push, então é provável que veja a seguinte saída como resposta (o que indica que não foram encontrados registos para entregar a notificação provavelmente porque os registos tinham algumas etiquetas desajustadas)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Notificação de torradas transmitida sabotada para Windows

Repare nos cabeçalhos que são enviados quando está a enviar uma notificação de torrada de transmissão ao cliente do Windows.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Enviar notificação especificando uma etiqueta (ou expressão de etiqueta)

Note o cabeçalho Tags HTTP, que é adicionado ao pedido HTTP (no exemplo abaixo, a notificação é enviada apenas para inscrições com carga útil 'desportiva')

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Enviar notificação especificando várias tags

Note como o cabeçalho Tags HTTP muda quando várias tags são enviadas.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Notificação modelo

Note que o cabeçalho formato HTTP muda e o corpo de carga útil é enviado como parte do organismo de pedido http:

**Lado do cliente - modelo registado:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Lado do servidor - envio da carga útil:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Passos Seguintes

Este artigo mostrou como criar um cliente Python REST para Centros de Notificação. A partir daqui, pode:

- Descarregue a amostra completa do [invólucro Python REST,]que contém todo o código deste artigo.
- Continue a aprender sobre a funcionalidade de marcação de Centros de Notificação no [tutorial de Notícias de Última Hora]
- Continue a aprender sobre o recurso de modelos de códigos de notificação no tutorial de [notícias de localização]

<!-- URLs -->
[Amostra de invólucro Python REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Tutorial de introdução]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Tutorial de Notícias de Última Hora]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Tutorial de notícias de localização]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
