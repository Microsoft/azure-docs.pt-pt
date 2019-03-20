---
title: Como utilizar os Hubs de notificação com Python
description: Saiba como utilizar Notification Hubs do Azure a partir de um back-end do Python.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.author: jowargo
ms.date: 01/04/2019
ms.openlocfilehash: 43a691ff9025cdb39786f965be6a2fca1b33bd3d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883581"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Como utilizar os Hubs de notificação do Python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Pode acessar todas as funcionalidades dos Hubs de notificação a partir de um back-end Java/PHP/Python/Ruby com a interface REST do Hub de notificação, conforme descrito no artigo do MSDN [APIs de REST dos Hubs de notificação](https://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Esta é uma implementação de referência de exemplo para implementar o envia de notificação no Python e não é o SDK Python do Hub de notificações suportados oficialmente. O exemplo foi criado com o Python 3.4.

Este artigo mostra-lhe como para:

- Crie um cliente REST para as funcionalidades dos Hubs de notificação no Python.
- Envie notificações através da interface de Python para as APIs de REST do Hub de notificação.
- Obter um despejo da solicitação/resposta do REST de HTTP para fins de depuração/educacionais.

Pode seguir a [tutorial de introdução](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) para a sua plataforma móvel à escolha, implementando a parte de back-end em Python.

> [!NOTE]
> O âmbito do exemplo é limitado apenas para enviar notificações e não faz qualquer gestão de registo.

## <a name="client-interface"></a>Interface do cliente

A interface principal do cliente pode fornecer os mesmos métodos que estão disponíveis no [.NET SDK dos Notification Hubs](https://msdn.microsoft.com/library/jj933431.aspx). Essa interface permite-lhe traduzir diretamente a todos os tutoriais e exemplos está disponíveis neste site e que tenham feito parte da Comunidade na internet.

Pode encontrar todo o código disponível na [exemplo de wrapper do REST do Python].

Por exemplo, para criar um cliente:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Para enviar uma notificação de alerta do Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementação

Se ainda não o fez, siga os [tutorial de introdução] a cópia de segurança para a última seção em que precisa implementar o back-end.

Todos os detalhes para implementar um invólucro REST completo podem ser encontrados no [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). Esta secção descreve a implementação de Python das principais etapas necessárias para aceder a pontos finais REST dos Hubs de notificação e enviar notificações

1. Analisar a cadeia de ligação
2. Gerar o token de autorização
3. Enviar uma notificação com a API de REST de HTTP

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação

Esta é a classe principal de implementar o cliente, cujos construtor analisa a cadeia de ligação:

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
Adicionar o seguinte métodos para o `NotificationHub` classe para criar o token com base no URI do pedido atual e as credenciais extraídas da cadeia de ligação.

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

### <a name="send-a-notification-using-http-rest-api"></a>Enviar uma notificação com a API de REST de HTTP

Utilize primeiro, vou definir uma classe que representa uma notificação.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm', 'windows', 'windowsphone', "adm", "baidu"]
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

Esta classe é um contentor para um corpo de notificação nativo ou um conjunto de propriedades de uma notificação de modelo, um conjunto de cabeçalhos, que contém o formato (plataforma nativa ou modelo) e propriedades específicas da plataforma (como propriedade de expiração da Apple e cabeçalhos WNS).

Consulte a [documentação de APIs de REST dos Hubs de notificação](https://msdn.microsoft.com/library/dn495827.aspx) e formatos das plataformas de notificação específicos para todas as opções disponíveis.

Agora com essa classe, escrever o envio métodos de notificação dentro do `NotificationHub` classe.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
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
        nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

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

Esses métodos enviam um pedido HTTP POST para o ponto de extremidade /messages do seu hub de notificação, com o corpo correto e cabeçalhos para enviar a notificação.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Usando a propriedade de depuração para ativar o registo detalhado

A permissão de depuração de propriedade ao inicializar o Notification Hub escreve as informações de Registro em log detalhado sobre o pedido HTTP e despejo de resposta, bem como a mensagem de notificação detalhada enviar o resultado.
O [TestSend de Hubs de notificação de propriedade](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) devolve informações detalhadas sobre o resultado de envio de notificação.
Para usá-lo - inicializar com o seguinte código:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

O pedido de envio do Hub de notificação HTTP URL obtém anexado com uma cadeia de caracteres de consulta de "teste", como resultado.

## <a name="complete-tutorial"></a>Concluir o tutorial

Agora pode concluir o tutorial de introdução ao enviar a notificação de um back-end do Python.

Inicializar o cliente dos Hubs de notificação (substitua o nome de hub e a cadeia de ligação com as instruções na [tutorial de introdução]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Em seguida, adicione o código de envio dependendo da sua plataforma móvel de destino. Este exemplo também adiciona métodos de nível superior para ativar as notificações de envio com base na plataforma, por exemplo, send_windows_notification para windows; send_apple_notification (para apple) etc.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8.1 (não Silverlight)

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

Execução do seu código de Python deve produzir uma notificação que aparece no seu dispositivo de destino.

## <a name="examples"></a>Exemplos

### <a name="enabling-the-debug-property"></a>Ativar o `debug` propriedade

Quando ativar o sinalizador de depuração ao inicializar o NotificationHub, verá detalhado pedido HTTP e despejo de resposta, bem como NotificationOutcome semelhante ao seguinte em que possa entender quais cabeçalhos HTTP são passados no pedido e resposta de HTTP que foi recebidos do Hub de notificação:

![][1]

Verá detalhadas por exemplo o resultado do Hub de notificação.

- Quando a mensagem é enviada com êxito para o serviço de notificação Push.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Se não existiam destinos encontrados para as notificações push, provavelmente terá para ver a seguinte saída como a resposta (o que indica que não havia nenhum registos encontrados para fornecer a notificação provavelmente porque os registros de tinham algumas sem correspondência etiquetas)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Notificação de alerta para o Windows de difusão

Tenha em atenção os cabeçalhos que obterem enviados quando estão a enviar uma notificação de alerta de difusão para cliente do Windows.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Enviar notificação de especificar uma etiqueta (ou expressão de etiqueta)

Tenha em atenção o cabeçalho de HTTP de etiquetas, o que é adicionado ao pedido de HTTP (no exemplo abaixo, a notificação é enviada apenas para registos que payload "desporto")

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Enviar notificação especificar várias etiquetas

Observe como o cabeçalho de HTTP de marcas é alterada quando são enviados a vários tags.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Notificação baseadas num modelo

Tenha em atenção que as alterações de cabeçalho de HTTP de formato e o corpo do payload é enviado como parte do corpo do pedido HTTP:

**Lado do cliente - modelo registado:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Do lado do servidor - envio da carga:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Próximos Passos

Este artigo mostrou como criar um cliente REST do Python para os Hubs de notificação. A partir daqui, pode:

- Transferir o completo [exemplo de wrapper do REST do Python], que contém todo o código neste artigo.
- Saber mais sobre o recurso de marcação dos Hubs de notificação a [tutorial de notícias de última hora]
- Saber mais sobre a funcionalidade de modelos dos Hubs de notificação no [Tutorial de notícias de localização]

<!-- URLs -->
[Exemplo de wrapper do REST do Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Tutorial de introdução]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Tutorial de notícias de última hora]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Tutorial de notícias de localização]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
