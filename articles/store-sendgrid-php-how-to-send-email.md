---
title: Como utilizar o serviço de correio eletrónico do SendGrid (PHP) | Documentos da Microsoft
description: Saiba como enviar um e-mail com o serviço de correio eletrónico do SendGrid no Azure. Exemplos de código escritos em PHP.
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
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: db3333aa52782ceb949ef3f46a903b618f6e3f2f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60931233"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Como utilizar o serviço de correio eletrónico SendGrid do PHP

Este guia demonstra como realizar tarefas comuns de programação com o serviço de correio eletrónico do SendGrid no Azure. Os exemplos são escritos em PHP.
Os cenários abrangidos incluem **construir e-mail**, **enviar mensagem de e-mail**, e **adicionar anexos**. Para obter mais informações sobre o SendGrid e o envio de e-mail, consulte a [passos seguintes](#next-steps) secção.

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de correio eletrónico do SendGrid?
O SendGrid é um [serviço de e-mail com base na cloud] que fornece fiável [entrega de e-mail transacional], escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os cenários de utilização do SendGrid comuns incluem:

* Enviar automaticamente os recibos aos clientes
* Administrando a distribuição de lista para o envio de clientes e-Panfleto mensal e ofertas especiais
* Recolher métricas em tempo real para coisas como email bloqueada e a capacidade de resposta do cliente
* Geração de relatórios para ajudar a identificar tendências
* Consultas de cliente de reencaminhamento
* Notificações de e-mail da sua aplicação

Para obter mais informações, consulte [ https://sendgrid.com ] [ https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Criar uma conta de SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Com o SendGrid da sua aplicação PHP

Com o SendGrid num aplicativo PHP do Azure não requer nenhum especial de codificação ou configuração. Como o SendGrid é um serviço, pode ser acedido exatamente da mesma forma de uma aplicação na cloud possível de uma aplicação no local.

## <a name="how-to-send-an-email"></a>Como: Enviar um E-Mail

Pode enviar um e-mail com a API Web fornecidos pela SendGrid ou SMTP.

### <a name="smtp-api"></a>API DE SMTP

Para enviar correio eletrónico com a API de SMTP do SendGrid, utilize *Swift Mailer*, uma biblioteca baseada em componentes para enviar e-mails de aplicações PHP. Pode baixar o [biblioteca Swift Mailer](https://swiftmailer.symfony.com/) v5.3.0 (utilizar [compositor] para instalar o Swift Mailer). Enviar mensagem de e-mail com a biblioteca envolve a criação de instâncias do `Swift\_SmtpTransport`, `Swift\_Mailer`, e `Swift\_Message` classes, definir as propriedades adequadas e, ao chamar o `Swift\_Mailer::send` método.

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
Utilizar o do PHP [curl função] [ curl function] enviar e-mails com a API Web do SendGrid.

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

API de Web do SendGrid é muito semelhante a uma API REST, que não esteja verdadeiramente uma API RESTful, uma vez que, na maioria das chamadas, ambos OBTERÃO e verbos POST podem ser utilizados alternadamente.

## <a name="how-to-add-an-attachment"></a>Como: Adicionar um anexo

### <a name="smtp-api"></a>API DE SMTP

Enviar um anexo com a API de SMTP envolve uma linha adicional de código para o script de exemplo para enviar um e-mail com Mailer do Swift.

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

A linha de código adicional é o seguinte:

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

Esta linha de código chama o método attach a `Swift\_Message` de objeto e utiliza o método estático `fromPath` no `Swift\_Attachment` classe a obter e anexar um ficheiro a uma mensagem.

### <a name="web-api"></a>API Web

Enviar um anexo com a API Web é muito semelhante a enviar um e-mail com a API Web. No entanto, tenha em atenção que no exemplo que se segue, a matriz de parâmetro tem de conter este elemento:

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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Como: Utilizar filtros para ativar rodapés, rastreamento e análise

O SendGrid disponibiliza a funcionalidade de e-mail adicionais através da utilização de *filtros*. Estas são as definições que podem ser adicionadas a uma mensagem de e-mail para ativar a funcionalidade específica, como ativar o controlo de cliques em, do Google analytics, subscrição de controlo e assim por diante.

Filtros podem ser aplicados a uma mensagem utilizando a propriedade de filtros. Cada filtro é especificado por um valor de hash que contém definições específicas do filtro. O exemplo seguinte ativa o filtro de rodapé e especifica uma mensagem de texto que será anexada à parte inferior da mensagem de e-mail. Neste exemplo utilizamos [sendgrid-php biblioteca].

Uso [compositor] para instalar a biblioteca:

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

Agora que aprendeu as noções básicas do serviço de correio eletrónico SendGrid, siga estas ligações para saber mais.

* Documentação do SendGrid: <https://sendgrid.com/docs>
* Biblioteca de SendGrid PHP: <https://github.com/sendgrid/sendgrid-php>
* O SendGrid oferta especial para os clientes do Azure: <https://sendgrid.com/windowsazure.html>

Para obter mais informações, consulte também os [Centro de programadores PHP](https://azure.microsoft.com/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[serviço de e-mail com base na cloud]: https://sendgrid.com/email-solutions
[entrega de e-mail transacional]: https://sendgrid.com/transactional-email
[sendgrid-php biblioteca]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Compositor]: https://getcomposer.org/download/
