---
title: Como utilizar os Hubs de Notificação Azure com PHP
description: Saiba como utilizar os Hubs de Notificação Azure a partir de um back-end PHP.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: daebf7c6a5fc9056e16b77a40ee9f90db598749f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87076635"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Como utilizar os centros de notificação da PHP

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Pode aceder a todas as funcionalidades do Centro de Notificações a partir de um backend Java/PHP/Ruby utilizando a interface DeSE do Centro de Notificação de Notificações, tal como descrito no tópico MSDN [Notification Hubs REST APIs](/previous-versions/azure/reference/dn223264(v=azure.100)).

Neste tópico mostramos como:

* Construir um cliente REST para funcionalidades de Centros de Notificação em PHP;
* Siga as [notificações push enviar para aplicações iOS usando Hubs de Notificação Azure](ios-sdk-get-started.md) para a sua plataforma móvel de eleição, implementando a parte de backend em PHP.

## <a name="client-interface"></a>Interface de cliente

A interface principal do cliente pode fornecer os mesmos métodos que estão disponíveis no [.NET Notification Hubs SDK,](https://msdn.microsoft.com/library/jj933431.aspx)que lhe permite traduzir diretamente todos os tutoriais e amostras atualmente disponíveis neste site, e contribuído pela comunidade na internet.

Pode encontrar todo o código disponível na amostra de [invólucro PHP REST].

Por exemplo, para criar um cliente:

```php
$hub = new NotificationHub("connection string", "hubname");
```

Para enviar uma notificação nativa do iOS:

```php
$notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
$hub->sendNotification($notification, null);
```

## <a name="implementation"></a>Implementação

Se ainda não o fez, siga o tutorial [Começar a começar] até à última secção onde tem de implementar o backend.
Além disso, se desejar, pode utilizar o código a partir da amostra de [invólucro PHP REST] e ir diretamente para a secção Complete a secção [tutorial.](#complete-tutorial)

Todos os detalhes para implementar um invólucro REST completo podem ser encontrados na [MSDN](/previous-versions/azure/reference/dn530746(v=azure.100)). Nesta secção, descrevemos a implementação php dos principais passos necessários para aceder aos pontos finais do Centro de Notificação REST:

1. Analisar a cadeia de ligação
2. Gerar o sinal de autorização
3. Realizar a chamada HTTP

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação

Aqui está a classe principal implementando o cliente, cujo construtor que analisa a cadeia de ligação:

```php
class NotificationHub {
    const API_VERSION = "?api-version=2013-10";

    private $endpoint;
    private $hubPath;
    private $sasKeyName;
    private $sasKeyValue;

    function __construct($connectionString, $hubPath) {
        $this->hubPath = $hubPath;

        $this->parseConnectionString($connectionString);
    }

    private function parseConnectionString($connectionString) {
        $parts = explode(";", $connectionString);
        if (sizeof($parts) != 3) {
            throw new Exception("Error parsing connection string: " . $connectionString);
        }

        foreach ($parts as $part) {
            if (strpos($part, "Endpoint") === 0) {
                $this->endpoint = "https" . substr($part, 11);
            } else if (strpos($part, "SharedAccessKeyName") === 0) {
                $this->sasKeyName = substr($part, 20);
            } else if (strpos($part, "SharedAccessKey") === 0) {
                $this->sasKeyValue = substr($part, 16);
            }
        }
    }
}
```

### <a name="create-a-security-token"></a>Criar um símbolo de segurança

Consulte a documentação do Azure para obter informações sobre como [criar um token de segurança SAS](/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token).

Adicione o `generateSasToken` método à classe para criar o símbolo com base no `NotificationHub` URI do pedido atual e nas credenciais extraídas da cadeia de ligação.

```php
private function generateSasToken($uri) {
    $targetUri = strtolower(rawurlencode(strtolower($uri)));

    $expires = time();
    $expiresInMins = 60;
    $expires = $expires + $expiresInMins * 60;
    $toSign = $targetUri . "\n" . $expires;

    $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

    $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

    return $token;
}
```

### <a name="send-a-notification"></a>Enviar uma notificação

Em primeiro lugar, vamos definir uma classe que represente uma notificação.

```php
class Notification {
    public $format;
    public $payload;

    # array with keynames for headers
    # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
    # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
    public $headers;

    function __construct($format, $payload) {
        if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
            throw new Exception('Invalid format: ' . $format);
        }

        $this->format = $format;
        $this->payload = $payload;
    }
}
```

Esta classe é um recipiente para um organismo de notificação nativa, ou um conjunto de propriedades em caso de notificação de modelo, e um conjunto de cabeçalhos, que contém formato (plataforma ou modelo nativo) e propriedades específicas da plataforma (como propriedade de expiração da Apple e cabeçalhos WNS).

Consulte a documentação dos [Hubs de Notificação REST APIs](/previous-versions/azure/reference/dn495827(v=azure.100)) e os formatos específicos das plataformas de notificação para todas as opções disponíveis.

Armados com esta classe, podemos agora escrever os métodos de notificação de envio dentro da `NotificationHub` classe:

```php
public function sendNotification($notification, $tagsOrTagExpression="") {
    if (is_array($tagsOrTagExpression)) {
        $tagExpression = implode(" || ", $tagsOrTagExpression);
    } else {
        $tagExpression = $tagsOrTagExpression;
    }

    # build uri
    $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
    $ch = curl_init($uri);

    if (in_array($notification->format, ["template", "apple", "fcm"])) {
        $contentType = "application/json";
    } else {
        $contentType = "application/xml";
    }

    $token = $this->generateSasToken($uri);

    $headers = [
        'Authorization: '.$token,
        'Content-Type: '.$contentType,
        'ServiceBusNotification-Format: '.$notification->format
    ];

    if ("" !== $tagExpression) {
        $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
    }

    # add headers for other platforms
    if (is_array($notification->headers)) {
        $headers = array_merge($headers, $notification->headers);
    }

    curl_setopt_array($ch, array(
        CURLOPT_POST => TRUE,
        CURLOPT_RETURNTRANSFER => TRUE,
        CURLOPT_SSL_VERIFYPEER => FALSE,
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_POSTFIELDS => $notification->payload
    ));

    // Send the request
    $response = curl_exec($ch);

    // Check for errors
    if($response === FALSE){
        throw new Exception(curl_error($ch));
    }

    $info = curl_getinfo($ch);

    if ($info['http_code'] <> 201) {
        throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
    }
} 
```

Os métodos acima enviados enviam um pedido HTTP POST para o `/messages` ponto final do seu centro de notificação, com o corpo e cabeçalhos corretos para enviar a notificação.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Complete o tutorial

Agora pode completar o tutorial Get Started enviando a notificação a partir de um backend PHP.

Inicialize o seu cliente Desafetado hubs (substitua o colar de ligação e o nome do hub como instruído no tutorial [Começar a iniciar o tutorial]):

```php
$hub = new NotificationHub("connection string", "hubname");
```

Em seguida, adicione o código de envio dependendo da plataforma móvel alvo.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8.1 (não-Silverlight)

```php
$toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
$notification = new Notification("windows", $toast);
$notification->headers[] = 'X-WNS-Type: wns/toast';
$hub->sendNotification($notification, null);
```

### <a name="ios"></a>iOS

```php
$alert = '{"aps":{"alert":"Hello from PHP!"}}';
$notification = new Notification("apple", $alert);
$hub->sendNotification($notification, null);
```

### <a name="android"></a>Android

```php
$message = '{"data":{"msg":"Hello from PHP!"}}';
$notification = new Notification("fcm", $message);
$hub->sendNotification($notification, null);
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 e 8.1 Silverlight

```php
$toast = '<?xml version="1.0" encoding="utf-8"?>' .
            '<wp:Notification xmlns:wp="WPNotification">' .
               '<wp:Toast>' .
                    '<wp:Text1>Hello from PHP!</wp:Text1>' .
               '</wp:Toast> ' .
            '</wp:Notification>';
$notification = new Notification("windowsphone", $toast);
$notification->headers[] = 'X-WindowsPhone-Target : toast';
$notification->headers[] = 'X-NotificationClass : 2';
$hub->sendNotification($notification, null);
```

### <a name="kindle-fire"></a>Fogo de Kindle

```php
$message = '{"data":{"msg":"Hello from PHP!"}}';
$notification = new Notification("adm", $message);
$hub->sendNotification($notification, null);
```

Executar o seu código PHP deverá produzir agora uma notificação que apareça no seu dispositivo-alvo.

## <a name="next-steps"></a>Passos Seguintes

Neste tópico, mostrámos como criar um cliente Java REST simples para Centros de Notificação. A partir daqui, pode:

* Descarregue a amostra completa do [invólucro PHP REST,]que contém todo o código acima.
* Continue a aprender sobre a funcionalidade de marcação de Centros de Notificação no tutorial [Breaking News]
* Saiba mais sobre o empurrar notificações para utilizadores individuais em [Notificar tutorial dos utilizadores]

Para mais informações, consulte também o [Centro de Desenvolvimento php.](https://azure.microsoft.com/develop/php/)

[Amostra de invólucro PHP REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Enviar notificações push para aplicações iOS usando hubs de notificação Azure](ios-sdk-get-started.md))
