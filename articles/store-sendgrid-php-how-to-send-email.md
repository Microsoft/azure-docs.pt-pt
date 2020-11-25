---
title: Como utilizar o serviço de e-mail SendGrid (PHP) Microsoft Docs
description: Saiba como enviar e-mail com o serviço de e-mail SendGrid no Azure. Amostras de código escritas em PHP.
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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023793"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Como utilizar o serviço de e-mail SendGrid da PHP

Este guia demonstra como executar tarefas de programação comuns com o serviço de e-mail SendGrid em Azure. As amostras estão escritas em PHP.
Os cenários abrangidos incluem **a construção de e-mail,** **envio de e-mail,** e **a adição de anexos.** Para mais informações sobre SendGrid e envio de e-mail, consulte a secção [Etapas Seguintes.](#next-steps)

## <a name="what-is-the-sendgrid-email-service"></a>O que é o Serviço de E-mail SendGrid?
SendGrid é um [serviço de e-mail baseado na nuvem] que fornece entrega de [e-mail transacional]fiável, escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de utilização da SendGrid incluem:

* Envio automaticamente de recibos aos clientes
* Gestão de listas de distribuição para envio de clientes e-fliers mensais e ofertas especiais
* Recolher métricas em tempo real para coisas como e-mail bloqueado e capacidade de resposta do cliente
* Gerar relatórios para ajudar a identificar tendências
* Encaminhamento de inquéritos aos clientes
* Notificações de e-mail da sua aplicação

Para obter mais informações, veja [https://sendgrid.com][https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Criar uma conta SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Utilização sendGrid da sua aplicação PHP

A utilização do SendGrid numa aplicação Azure PHP não requer nenhuma configuração ou codificação especial. Como o SendGrid é um serviço, pode ser acedido exatamente da mesma forma a partir de uma aplicação em nuvem que pode a partir de uma aplicação no local.

## <a name="how-to-send-an-email"></a>Como: Enviar um e-mail

Pode enviar e-mail usando o SMTP ou a API Web fornecida pela SendGrid.

### <a name="smtp-api"></a>SMTP API

Para enviar e-mail utilizando a API SendGrid SMTP, utilize o *Swift Mailer*, uma biblioteca baseada em componentes para o envio de e-mails de aplicações PHP. Pode baixar a [biblioteca Swift Mailer](https://swiftmailer.symfony.com/) v5.3.0 (use [o Compositor] para instalar o Swift Mailer). O envio de e-mails com a biblioteca envolve a criação de instâncias `Swift\_SmtpTransport` `Swift\_Mailer` e `Swift\_Message` aulas, definindo as propriedades apropriadas, e chamando o `Swift\_Mailer::send` método.

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
Utilize a [função de caracol][curl function] do PHP para enviar e-mails utilizando a API da Web SendGrid.

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

A API Web da SendGrid é muito semelhante a uma API REST, embora não seja verdadeiramente uma API RESTful, uma vez que, na maioria das chamadas, tanto os verbos GET como os POST podem ser usados intercambiavelmente.

## <a name="how-to-add-an-attachment"></a>Como: Adicionar um anexo

### <a name="smtp-api"></a>SMTP API

O envio de um anexo utilizando a API SMTP envolve uma linha de código adicional para o script de exemplo para o envio de um e-mail com Swift Mailer.

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

Esta linha de código chama o método de anexação no `Swift\_Message` objeto e utiliza método estático na classe para obter e `fromPath` `Swift\_Attachment` anexar um ficheiro a uma mensagem.

### <a name="web-api"></a>API Web

O envio de um anexo utilizando a API Web é muito semelhante ao envio de um e-mail utilizando a API Web. No entanto, note que no exemplo que se segue, a matriz de parâmetros deve conter este elemento:

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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Como: Utilizar filtros para ativar rodapés, rastreios e análises

A SendGrid fornece uma funcionalidade adicional de e-mail através da utilização de *filtros.* Estas são configurações que podem ser adicionadas a uma mensagem de e-mail para ativar funcionalidades específicas, tais como permitir o rastreio de cliques, análise do Google, rastreio de subscrição, e assim por diante.

Os filtros podem ser aplicados a uma mensagem utilizando a propriedade dos filtros. Cada filtro é especificado por um haxixe que contém configurações específicas do filtro. O exemplo a seguir permite o filtro de rodapé e especifica uma mensagem de texto que será anexada à parte inferior da mensagem de correio eletrónico. Para este exemplo, usaremos [a biblioteca sendgrid-php].

Use [o Compositor] para instalar a biblioteca:

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

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu o básico do serviço de email SendGrid, siga estes links para saber mais.

* Documentação SendGrid: <https://sendgrid.com/docs>
* Biblioteca SendGrid PHP: <https://github.com/sendgrid/sendgrid-php>
* Oferta especial sendGrid para clientes Azure: <https://sendgrid.com/windowsazure.html>

Para mais informações, consulte também o [Centro de Desenvolvimento php.](https://azure.microsoft.com/develop/php/)

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[serviço de e-mail baseado na nuvem]: https://sendgrid.com/email-solutions
[entrega de e-mail transacional]: https://sendgrid.com/transactional-email
[biblioteca sendgrid-php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Compositor]: https://getcomposer.org/download/
