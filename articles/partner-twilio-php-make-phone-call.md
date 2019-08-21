---
title: Como fazer uma chamada telefônica do twilio (PHP) | Microsoft Docs
description: Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do twilio no Azure. Os exemplos são para o aplicativo PHP.
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
ms.openlocfilehash: fb1623c4a409f1c6cba94bad56d773e166d2b182
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637312"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Como fazer uma chamada telefônica usando o twilio em um aplicativo PHP no Azure
O exemplo a seguir mostra como você pode usar twilio para fazer uma chamada de uma página da Web do PHP hospedada no Azure. O aplicativo resultante solicitará valores de chamada telefônica ao usuário, conforme mostrado na captura de tela a seguir.

![Formulário de chamada do Azure usando twilio e PHP][twilio_php]

Você precisará fazer o seguinte para usar o código neste tópico:

1. Adquira uma conta do twilio e um token de autenticação do seu [console do twilio][twilio_console]. Para começar a usar o twilio, avalie os [https://www.twilio.com/pricing][twilio_pricing]preços em. Você pode se inscrever para uma conta de [https://www.twilio.com/try-twilio][try_twilio]avaliação em.
2. Obtenha a [biblioteca twilio para php](https://github.com/twilio/twilio-php) ou instale-a como um pacote do pêra. Para obter mais informações, consulte o [arquivo Leiame](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Instale o SDK do Azure para PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário da Web para fazer uma chamada
O código HTML a seguir mostra como criar uma página da Web (**callform. html**) que recupera dados do usuário para fazer uma chamada:

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
O código a seguir mostra como criar **MakeCall. php**, que é chamado quando o usuário envia o formulário exibido por **callform. html**. O código mostrado abaixo cria a mensagem de chamada e gera a chamada. Além disso, certifique-se de usar sua conta do twilio e o token de autenticação do [console do twilio][twilio_console] em vez dos valores de espaço reservado atribuídos a **$Sid** e **$token** no código a seguir.

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

Além de fazer a chamada, **MakeCall. php** exibe alguns metadados de chamada, como é mostrado na imagem abaixo. Para obter mais informações sobre metadados de chamada [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties], consulte.

![Resposta de chamada do Azure usando twilio e PHP][twilio_php_response]

## <a name="run-the-application"></a>Executar a aplicação
A próxima etapa é [implantar seu aplicativo em aplicativos Web do Azure com o Git](app-service/app-service-web-get-started-php.md) (embora nem todas as informações sejam relevantes). 

## <a name="next-steps"></a>Passos Seguintes
Esse código foi fornecido para mostrar a funcionalidade básica usando o twilio no PHP no Azure. Antes de implantar no Azure em produção, convém adicionar mais tratamento de erro ou outros recursos. Por exemplo:

* Em vez de usar um formulário da Web, você pode usar os blobs de armazenamento do Azure ou o banco de dados SQL para armazenar números de telefone e texto de chamada. Para obter informações sobre como usar os blobs de armazenamento do Azure no PHP, consulte [usando o armazenamento do Azure com aplicativos PHP][howto_blob_storage_php]. Para obter informações sobre como usar o banco de dados SQL no PHP, consulte [usando o banco de dados SQL com aplicativos PHP][howto_sql_azure_php].
* O código **MakeCall. php** usa a URL fornecida pelo twilio[https://twimlets.com/message][twimlet_message_url]() para fornecer uma resposta TwiML (twilio Markup Language) que informa ao twilio como proceder com a chamada. Por exemplo, o TwiML retornado pode conter um `<Say>` verbo que resulta na fala do texto para o destinatário da chamada. Em vez de usar a URL fornecida pelo twilio, você pode criar seu próprio serviço para responder à solicitação do twilio; para obter mais informações, consulte [como usar o twilio para recursos de voz e SMS no php][howto_twilio_voice_sms_php]. Mais informações sobre TwiML podem ser encontradas em [https://www.twilio.com/docs/api/twiml][twiml], e mais informações sobre `<Say>` e outros verbos twilio podem ser encontradas em [https://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leia as diretrizes de segurança do [https://www.twilio.com/docs/security][twilio_docs_security]twilio em.

Para obter informações adicionais sobre twilio, [https://www.twilio.com/docs][twilio_docs]consulte.

## <a name="see-also"></a>Consultar Também
* [Como usar o twilio para recursos de voz e SMS no PHP](partner-twilio-php-how-to-use-voice-sms.md)

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
