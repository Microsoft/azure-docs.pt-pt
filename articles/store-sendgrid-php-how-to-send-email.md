---
title: Como usar o serviço de email SendGrid (PHP) | Microsoft Docs
description: Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de código escritos em PHP.
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: b3a9fee09d1eac6fb4d716af83c348cb2c21f7a9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67870912"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Como usar o serviço de email SendGrid do PHP

Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Azure. Os exemplos são escritos em PHP.
Os cenários abordados incluem a **construção de email**, o **envio de email**e a adição de **anexos**. Para obter mais informações sobre o SendGrid e o envio de email, consulte a seção [próximas etapas](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de email SendGrid?
O SendGrid é um [serviço de email baseado em nuvem] que fornece [entrega de email]transacional confiável, escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de uso de SendGrid incluem:

* Enviando automaticamente recibos aos clientes
* Administrando listas de distribuição para enviar aos clientes e-folhetos mensais e ofertas especiais
* Coleta de métricas em tempo real para coisas como email bloqueado e capacidade de resposta do cliente
* Gerando relatórios para ajudar a identificar tendências
* Encaminhando consultas do cliente
* Notificações por email do seu aplicativo

Para mais informações, consulte [https://sendgrid.com][https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Usando o SendGrid de seu aplicativo PHP

O uso de SendGrid em um aplicativo PHP do Azure não requer nenhuma configuração ou codificação especial. Como o SendGrid é um serviço, ele pode ser acessado exatamente da mesma forma de um aplicativo de nuvem como ele pode de um aplicativo local.

## <a name="how-to-send-an-email"></a>Como: Enviar um email

Você pode enviar email usando o SMTP ou a API Web fornecida pelo SendGrid.

### <a name="smtp-api"></a>API DE SMTP

Para enviar email usando a API SMTP do SendGrid, use o *Swift Mailer*, uma biblioteca baseada em componentes para enviar emails de aplicativos PHP. Você pode baixar a [biblioteca do Swift Mailer](https://swiftmailer.symfony.com/) v 5.3.0 (use o [Compositor] para instalar o Swift Mailer). O envio de email com a biblioteca envolve a criação `Swift\_SmtpTransport`de `Swift\_Mailer`instâncias das `Swift\_Message` classes, e, a definição das propriedades apropriadas e `Swift\_Mailer::send` a chamada do método.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
 $html = "<html>
       <head></head>
       <body>
           <p>Hi!<br>
               How are you?<br>
           </p>
       </body>
       </html>";
 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');
 // Email recipients
 $to = array(
       'john@contoso.com'=>'Destination 1 Name',
       'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
     // This will let us know how many users received this message
     echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
     echo "Something went wrong - ";
     print_r($failures);
 }
```

### <a name="web-api"></a>API Web
Use a [função de rotação][curl function] do PHP para enviar emails usando a API Web SendGrid.

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $params = array(
      'api_user' => $user,
      'api_key' => $pass,
      'to' => 'john@contoso.com',
      'subject' => 'testing from curl',
      'html' => 'testing body',
      'text' => 'testing body',
      'from' => 'anna@contoso.com',
   );

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

A API Web do SendGrid é muito semelhante a uma API REST, embora não seja realmente uma API RESTful, pois, na maioria das chamadas, os verbos GET e POST podem ser usados de maneira intercambiável.

## <a name="how-to-add-an-attachment"></a>Como: Adicionar um anexo

### <a name="smtp-api"></a>API DE SMTP

O envio de um anexo usando a API SMTP envolve uma linha de código adicional para o script de exemplo para enviar um email com o Swift Mailer.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
  $html = "<html>
      <head></head>
      <body>
         <p>Hi!<br>
            How are you?<br>
         </p>
      </body>
      </html>";

 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');

 // Email recipients
 $to = array(
      'john@contoso.com'=>'Destination 1 Name',
      'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
      // This will let us know how many users received this message
      echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
      echo "Something went wrong - ";
      print_r($failures);
 }
```

A linha de código adicional é a seguinte:

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

Essa linha de código chama o método Attach no `Swift\_Message` objeto e usa o método `fromPath` estático na `Swift\_Attachment` classe para obter e anexar um arquivo a uma mensagem.

### <a name="web-api"></a>API Web

O envio de um anexo usando a API Web é muito semelhante ao envio de um email usando a API da Web. No entanto, observe que, no exemplo a seguir, a matriz de parâmetros deve conter este elemento:

```php
    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
```

#### <a name="example"></a>Exemplo

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $fileName = 'myfile';
 $filePath = dirname(__FILE__);

 $params = array(
     'api_user' => $user,
     'api_key' => $pass,
     'to' =>'john@contoso.com',
     'subject' => 'test of file sends',
     'html' => '<p> the HTML </p>',
     'text' => 'the plain text',
     'from' => 'anna@contoso.com',
     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
 );

 print_r($params);

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Como: Usar filtros para habilitar rodapés, rastreamento e análise

O SendGrid fornece funcionalidade adicional de email por meio do uso de *filtros*. Essas são configurações que podem ser adicionadas a uma mensagem de email para habilitar funcionalidades específicas, como habilitar o acompanhamento de cliques, o Google Analytics, o acompanhamento de assinaturas e assim por diante.

Os filtros podem ser aplicados a uma mensagem usando a propriedade filtros. Cada filtro é especificado por um hash que contém configurações específicas de filtro. O exemplo a seguir habilita o filtro de rodapé e especifica uma mensagem de texto que será acrescentada à parte inferior da mensagem de email. Para este exemplo, usaremos [a biblioteca sendgrid-php].

Use o [Compositor] para instalar a biblioteca:

```bash
php composer.phar require sendgrid/sendgrid 2.1.1
```

### <a name="example"></a>Exemplo  

```php
<?php
 /*
  * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
  */
 include "vendor/autoload.php";

 $email = new SendGrid\Email();
 // The list of addresses this message will be sent to
 // [This list is used for sending multiple emails using just ONE request to SendGrid]
 $toList = array('john@contoso.com', 'anna@contoso.com');

 // Specify the names of the recipients
 $nameList = array('Name 1', 'Name 2');

 // Used as an example of variable substitution
 $timeList = array('4 PM', '5 PM');

 // Set all of the above variables
 $email->setTos($toList);
 $email->addSubstitution('-name-', $nameList);
 $email->addSubstitution('-time-', $timeList);

 // Specify that this is an initial contact message
 $email->addCategory("initial");

 // You can optionally setup individual filters here, in this example, we have
 // enabled the footer filter
 $email->addFilter('footer', 'enable', 1);
 $email->addFilter('footer', "text/plain", "Thank you for your business");
 $email->addFilter('footer', "text/html", "Thank you for your business");

 // The subject of your email
 $subject = 'Example SendGrid Email';

 // Where is this message coming from. For example, this message can be from
 // support@yourcompany.com, info@yourcompany.com
 $from = 'someone@example.com';

 // If you do not specify a sender list above, you can specify the user here. If
 // a sender list IS specified above, this email address becomes irrelevant.
 $to = 'john@contoso.com';

 # Create the body of the message (a plain-text and an HTML version).
 # text is your plain-text email
 # html is your html version of the email
 # if the receiver is able to view html emails then only the html
 # email will be displayed

 /*
  * Note the variable substitution here =)
  */
 $text = "
 Hello -name-,
 Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
 Regards,
 Fred";

 $html = "
 <html>
 <head></head>
 <body>
 <p>Hello -name-,<br>
 Thank you for your interest in our products. We have set up an appointment
 to call you at -time- EST to discuss your needs in more detail.

 Regards,

 Fred<br>
 </p>
 </body>
 </html>";

 // set subject
 $email->setSubject($subject);

 // attach the body of the email
 $email->setFrom($from);
 $email->setHtml($html);
 $email->addTo($to);
 $email->setText($text);

 // Your SendGrid account credentials
 $username = 'sendgridusername@yourdomain.com';
 $password = 'example';

 // Create SendGrid object
 $sendgrid = new SendGrid($username, $password);

 // send message
 $response = $sendgrid->send($email);

 print_r($response);
 ```

## <a name="next-steps"></a>Próximos Passos

Agora que você aprendeu os conceitos básicos do serviço de email do SendGrid, siga estes links para saber mais.

* Documentação do SendGrid:<https://sendgrid.com/docs>
* Biblioteca do SendGrid PHP:<https://github.com/sendgrid/sendgrid-php>
* Oferta especial do SendGrid para clientes do Azure:<https://sendgrid.com/windowsazure.html>

Para obter mais informações, consulte também o [centro de desenvolvedores do PHP](https://azure.microsoft.com/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[serviço de email baseado em nuvem]: https://sendgrid.com/email-solutions
[entrega de email]: https://sendgrid.com/transactional-email
[a biblioteca sendgrid-php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Compositor]: https://getcomposer.org/download/
