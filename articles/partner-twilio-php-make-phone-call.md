---
title: Como fazer uma ligação de Twilio (PHP) | Microsoft Docs
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço API Twilio em Azure. As amostras são para aplicação PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: ef0642b26898249d78f5103e8a8f80198887bed9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95542529"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Como fazer uma chamada telefónica usando twilio em uma aplicação PHP em Azure
O exemplo que se segue mostra como pode usar o Twilio para fazer uma chamada a partir de uma página web php hospedada em Azure. A aplicação resultante irá solicitar ao utilizador valores de chamada telefónica, como mostrado na imagem seguinte.

![Formulário de chamada Azure usando Twilio e PHP][twilio_php]

Terá de fazer o seguinte para utilizar o código neste tópico:

1. Adquira uma conta Twilio e ficha de autenticação da sua [Consola Twilio.][twilio_console] Para começar com twilio, avalie os preços em [https://www.twilio.com/pricing][twilio_pricing] . Pode inscrever-se numa conta experimental em [https://www.twilio.com/try-twilio][try_twilio] .
2. Obtenha a [biblioteca Twilio para PHP](https://github.com/twilio/twilio-php) ou instale-a como um pacote PEAR. Para mais informações, consulte o [ficheiro readme](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Instale o Azure SDK para PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](./app-service/quickstart-php.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário web para fazer uma chamada
O seguinte código HTML mostra como construir uma página web **(callform.html**) que recupera dados do utilizador para fazer uma chamada:

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
O código que se segue mostra como construir **a makecall.php**, que é chamada quando o utilizador submete o formulário apresentado por **callform.html .** O código apresentado abaixo cria a mensagem de chamada e gera a chamada. Além disso, certifique-se de utilizar a sua conta Twilio e ficha de autenticação da [Consola Twilio][twilio_console] em vez dos valores reservados atribuídos a **$sid** e **$token** no código abaixo.

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

Além de fazer a chamada, **a chamada.php** exibe alguns metadados de chamada, como é mostrado na imagem abaixo. Para obter mais informações sobre metadados de chamada, consulte [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties] .

![Resposta de chamada Azure Usando Twilio e PHP][twilio_php_response]

## <a name="run-the-application"></a>Executar a aplicação
O próximo passo é implementar a [sua aplicação para Azure Web Apps com Git](app-service/quickstart-php.md) (embora nem todas as informações aí são relevantes). 

## <a name="next-steps"></a>Passos seguintes
Este código foi fornecido para mostrar-lhe a funcionalidade básica usando Twilio em PHP em Azure. Antes de ser implantado no Azure em produção, é melhor adicionar mais manipulação de erros ou outras funcionalidades. Por exemplo:

* Em vez de utilizar um formulário web, pode utilizar bolhas de armazenamento Azure ou Base de Dados SQL para armazenar números de telefone e texto de chamada. Para obter informações sobre a utilização de bolhas de armazenamento Azure em PHP, consulte [utilizar o armazenamento Azure com aplicações PHP][howto_blob_storage_php]. Para obter informações sobre a utilização da Base de Dados SQL em PHP, consulte [utilizar a Base de Dados SQL com aplicações PHP][howto_sql_azure_php].
* O código **de .php** utiliza URL fornecido por Twilio para fornecer uma [https://twimlets.com/message][twimlet_message_url] resposta Twilio Markup Language (TwiML) que informa twilio como proceder com a chamada. Por exemplo, o TwiML que é devolvido pode conter um `<Say>` verbo que resulta em texto a ser falado com o destinatário da chamada. Em vez de utilizar o URL fornecido por Twilio, poderá construir o seu próprio serviço para responder ao pedido de Twilio; para obter mais informações, consulte [Como utilizar o Twilio para capacidades de voz e SMS em PHP][howto_twilio_voice_sms_php]. Mais informações sobre o TwiML podem ser encontradas em [https://www.twilio.com/docs/api/twiml][twiml] , e mais informações sobre e `<Say>` outros verbos Twilio podem ser encontrados em [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Leia as diretrizes de segurança do Twilio em [https://www.twilio.com/docs/security][twilio_docs_security] .

Para obter informações adicionais sobre Twilio, [https://www.twilio.com/docs][twilio_docs] consulte.

## <a name="see-also"></a>Consulte também
* [Como usar twilio para capacidades de voz e SMS em PHP](partner-twilio-php-how-to-use-voice-sms.md)

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
[howto_blob_storage_php]: ./storage/blobs/storage-quickstart-blobs-php.md
[howto_sql_azure_php]: ./azure-sql/database/connect-query-content-reference-guide.md
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php