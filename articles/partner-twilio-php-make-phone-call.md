---
title: Como fazer uma chamada telefónica do Twilio (PHP) | Documentos da Microsoft
description: Saiba como fazer uma chamada telefónica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos destinam-se a aplicação PHP.
documentationcenter: php
services: ''
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 03b74f5a931e1cfbf09433af76c250607b7fc80c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444451"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Como fazer uma chamada telefónica, utilizar o Twilio numa aplicação PHP no Azure
O exemplo seguinte mostra como pode utilizar o Twilio para fazer uma chamada de uma página de web PHP no Azure. O aplicativo resultante pedirá ao utilizador para os valores de chamada telefónica, conforme mostrado na captura de ecrã seguinte.

![Formulário de chamada do Azure com o Twilio e o PHP][twilio_php]

Precisará fazer o seguinte para utilizar o código neste tópico:

1. Adquirir uma conta do Twilio e autenticação de token da sua [Twilio consola][twilio_console]. Para começar a utilizar com o Twilio, avalie a preços em [ https://www.twilio.com/pricing ] [ twilio_pricing]. Pode inscrever-se para uma conta de avaliação na [ https://www.twilio.com/try-twilio ] [ try_twilio].
2. Obter o [biblioteca de Twilio para PHP](https://github.com/twilio/twilio-php) ou instalá-lo como um pacote PEAR. Para obter mais informações, consulte a [ficheiro Leia-me](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Instale o Azure SDK para PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário da web para fazer uma chamada
O seguinte código HTML mostra como criar uma página da web (**callform.html**) que obtém dados de utilizador para fazer uma chamada:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Criar o código para fazer a chamada
O código seguinte mostra como criar **makecall.php**, o que é chamado quando o usuário envia o formulário exibido pela **callform.html**. O código abaixo cria a mensagem de chamada e gera a chamada. Além disso, certifique-se de que utilizar a conta do Twilio e a autenticação de token do [Twilio consola] [ twilio_console] em vez dos valores de marcador de posição atribuídos a **$sid** e  **$token** no código abaixo.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Para além de tornar a chamada **makecall.php** apresenta alguns metadados de chamada, conforme é mostrado na imagem abaixo. Para obter mais informações sobre os metadados de chamada, consulte [ https://www.twilio.com/docs/api/rest/call#instance-properties ] [ twilio_call_properties].

![Resposta de chamada do Azure com o Twilio e PHP][twilio_php_response]

## <a name="run-the-application"></a>Executar a aplicação
A próxima etapa é [implementar a sua aplicação para aplicações Web do Azure com o Git](app-service/app-service-web-get-started-php.md) (embora não todas as informações nele são relevantes). 

## <a name="next-steps"></a>Passos Seguintes
Esse código foi fornecido para mostrar a funcionalidade básica com o Twilio no PHP no Azure. Antes de implementar para o Azure na produção, pode querer adicionar tratamento de erros mais ou outros recursos. Por exemplo:

* Em vez de usar um formulário da web, poderia usar os blobs de armazenamento do Azure ou a base de dados SQL para armazenar números de telefone e chamar o texto. Para obter informações sobre a utilização de blobs de armazenamento do Azure no PHP, consulte [utilizar o armazenamento do Azure com aplicações PHP][howto_blob_storage_php]. Para obter informações sobre como utilizar a base de dados SQL no PHP, consulte [utilizar base de dados SQL com aplicações PHP][howto_sql_azure_php].
* O **makecall.php** código utiliza o URL fornecido pelo Twilio ([https://twimlets.com/message][twimlet_message_url]) para fornecer uma resposta de linguagem de marcação do Twilio (TwiML) que informa como proceder com a Twilio a chamada. Por exemplo, o que é devolvido TwiML pode conter um `<Say>` verbo que resulta num texto a ser dito ao destinatário da chamada. Em vez de utilizar o URL fornecido pelo Twilio, poderia construir seu próprio serviço para responder à solicitação do Twilio; Para obter mais informações, consulte [como utilizar Twilio para obter capacidades de SMS no PHP e de voz][howto_twilio_voice_sms_php]. Obter mais informações sobre TwiML podem ser encontradas em [ https://www.twilio.com/docs/api/twiml ] [ twiml]e obter mais informações sobre `<Say>` e outros verbos Twilio podem ser encontrados em [ https://www.twilio.com/docs/api/twiml/say ][twilio_say].
* Leia as diretrizes de segurança do Twilio em [ https://www.twilio.com/docs/security ] [ twilio_docs_security].

Para obter informações adicionais sobre o Twilio, consulte [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="see-also"></a>Consultar Também
* [Como utilizar o Twilio para obter capacidades SMS no PHP e de voz](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: https://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: https://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
