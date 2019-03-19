---
title: Como utilizar os Hubs de notificação com PHP
description: Saiba como utilizar Notification Hubs do Azure a partir de um back-end do PHP.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 054edaf321d90015840fd84e1697fca742fd7e1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838600"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Como utilizar os Hubs de notificação de PHP

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Pode acessar todas as funcionalidades dos Hubs de notificação de um back-end de Java/PHP/Ruby usando a interface REST do Hub de notificação, conforme descrito no tópico do MSDN [APIs de REST dos Hubs de notificação](https://msdn.microsoft.com/library/dn223264.aspx).

Este tópico vamos mostrar como:

* Criar um cliente REST para as funcionalidades dos Hubs de notificação no PHP;
* Siga os [tutorial de introdução](notification-hubs-ios-apple-push-notification-apns-get-started.md) para a sua plataforma móvel à escolha, implementando a parte de back-end em PHP.

## <a name="client-interface"></a>Interface do cliente

A interface principal do cliente pode fornecer os mesmos métodos que estão disponíveis no [.NET SDK dos Notification Hubs](https://msdn.microsoft.com/library/jj933431.aspx), que permite-lhe traduzir diretamente a todos os tutoriais e exemplos está disponíveis neste site e contribuiu o Comunidade na internet.

Pode encontrar todo o código disponível na [exemplo de wrapper do REST do PHP].

Por exemplo, para criar um cliente:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Para enviar um iOS nativa notificação:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Implementação

Se ainda não o fez, siga os [tutorial de introdução] a cópia de segurança para a última seção em que precisa implementar o back-end.
Além disso, se pretender que pode utilizar o código a partir do [exemplo de wrapper do REST do PHP] e aceda diretamente à [concluir o tutorial](#complete-tutorial) secção.

Todos os detalhes para implementar um invólucro REST completo podem ser encontrados no [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). Nesta seção, descrevemos a implementação de PHP das principais etapas necessárias para aceder a pontos finais REST dos Hubs de notificação:

1. Analisar a cadeia de ligação
2. Gerar o token de autorização
3. Efetuar a chamada HTTP

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação

Esta é a classe principal de implementar o cliente, cujos construtor que analisa a cadeia de ligação:

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

### <a name="create-a-security-token"></a>Criar um token de segurança

Consulte a documentação do Azure para obter informações sobre como [criar um Token de segurança do SAS](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token).

Adicionar a `generateSasToken` método para o `NotificationHub` classe para criar o token com base no URI do pedido atual e as credenciais extraídas da cadeia de ligação.

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

Em primeiro lugar, vamos defina uma classe que representa uma notificação.

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

Essa classe é um contentor para um corpo de notificação nativo, ou um conjunto de propriedades no caso de uma notificação de modelo e um conjunto de cabeçalhos, que contém o formato (plataforma nativa ou modelo) e propriedades específicas da plataforma (como propriedade de expiração da Apple e o WNS cabeçalhos).

Consulte a [documentação de APIs de REST dos Hubs de notificação](https://msdn.microsoft.com/library/dn495827.aspx) e formatos das plataformas de notificação específicos para todas as opções disponíveis.

Munido dessa classe, agora podemos escrever o envio métodos de notificação dentro do `NotificationHub` classe:

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

Os métodos acima enviar um pedido de HTTP POST para o `/messages` ponto final do seu hub de notificação, com o corpo correto e cabeçalhos para enviar a notificação.

## <a name="complete-tutorial"></a>Concluir o tutorial

Agora pode concluir o tutorial de introdução ao enviar a notificação de um back-end do PHP.

Inicializar o cliente dos Hubs de notificação (substitua o nome de hub e a cadeia de ligação com as instruções na [tutorial de introdução]):

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Em seguida, adicione o código de envio dependendo da sua plataforma móvel de destino.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8.1 (não Silverlight)

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

### <a name="kindle-fire"></a>Kindle Fire

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

Execução do seu código PHP deve produzir agora uma notificação que aparece no seu dispositivo de destino.

## <a name="next-steps"></a>Próximos Passos

Neste tópico, mostramos como criar um cliente de Java REST simples para os Hubs de notificação. A partir daqui, pode:

* Transferir o completo [exemplo de wrapper do REST do PHP], que contém todo o código acima.
* Saber mais sobre os Hubs de notificação funcionalidade de marcação [tutorial de notícias de última hora]
* Saiba mais sobre como enviar notificações push para utilizadores individuais [tutorial notificar utilizadores]

Para obter mais informações, consulte também os [Centro de programadores PHP](https://azure.microsoft.com/develop/php/).

[Exemplo de wrapper do REST do PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Tutorial de introdução]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
