---
title: Como utilizar o serviço de correio eletrónico do SendGrid (Java) | Documentos da Microsoft
description: Saiba como enviar um e-mail com o serviço de correio eletrónico do SendGrid no Azure. Exemplos de códigos escritos em Java.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 0cb75c1acb731432ed524560698e3355699b2500
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60931216"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Como enviar E-mails com o SendGrid de Java
Este guia demonstra como realizar tarefas comuns de programação com o serviço de correio eletrónico do SendGrid no Azure. Os exemplos são escritos em Java. Os cenários abrangidos incluem **construir e-mail**, **enviar mensagem de e-mail**, **adicionar anexos**, **usando filtros de**e o **a atualizar as propriedades**. Para obter mais informações sobre o SendGrid e o envio de e-mail, consulte a [próximos passos](#next-steps) secção.

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de correio eletrónico do SendGrid?
O SendGrid é um [serviço de e-mail com base na cloud] que fornece fiável [entrega de e-mail transacional], escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os cenários de utilização do SendGrid comuns incluem:

* Enviar automaticamente os recibos aos clientes
* Administrando a distribuição de lista para o envio de clientes e-Panfleto mensal e ofertas especiais
* Recolher métricas em tempo real para coisas como email bloqueada e a capacidade de resposta do cliente
* Geração de relatórios para ajudar a identificar tendências
* Consultas de cliente de reencaminhamento
* Notificações de e-mail da sua aplicação

Para obter mais informações, consulte <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Criar uma conta de SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Como: Utilizar as bibliotecas de javax.mail
Obter as bibliotecas de javax.mail, por exemplo a partir de <https://www.oracle.com/technetwork/java/javamail> e importá-los para o seu código. Num alto nível, o processo para utilizar a biblioteca de javax.mail para enviar e-mail através de SMTP é fazer o seguinte:

1. Especifique os valores de SMTP, incluindo o servidor de SMTP, que, para o SendGrid, é smtp.sendgrid.net.

```
        import java.util.Properties;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. Expandir a *javax.mail.Authenticator* classe e em sua implementação da *getPasswordAuthentication* método, retornar o nome de utilizador do SendGrid e a palavra-passe.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Criar uma sessão de e-mail autenticado por meio de um *javax.mail.Session* objeto.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Crie a sua mensagem e atribua **para**, **partir**, **assunto** e valores de conteúdo. Isso é mostrado no [como: Criar uma mensagem de E-Mail](#how-to-create-an-email) secção.
4. Enviar a mensagem por meio de um *javax.mail.Transport* objeto. Isso é mostrado no [como: Enviar um E-Mail] [# procedimentos-send-um-secção correio eletrónico].

## <a name="how-to-create-an-email"></a>Como: Criar uma mensagem de e-mail
O código a seguir mostra como especificar valores para uma mensagem de e-mail.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>Como: Enviar uma mensagem de e-mail
O código a seguir mostra como enviar uma mensagem de e-mail.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Como: Adicionar um anexo
O código seguinte mostra como adicionar um anexo.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Como: Utilizar filtros para ativar rodapés, rastreamento e análise
O SendGrid disponibiliza a funcionalidade de e-mail adicionais através da utilização de *filtros*. Estas são as definições que podem ser adicionadas a uma mensagem de e-mail para ativar a funcionalidade específica, como ativar o controlo de cliques em, do Google analytics, subscrição de controlo e assim por diante. Para obter uma lista completa de filtros de mensagens em fila, consulte [definições de filtro][Filter Settings].

* O código a seguir mostra como inserir um filtro de rodapé que resulta em texto HTML que aparece na parte inferior da mensagem de e-mail a ser enviado.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Outro exemplo de um filtro é clique controlo. Vamos supor que o texto do e-mail contém uma hiperligação, como a seguir, e pretende acompanhar a taxa de clique:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Para ativar o controlo de cliques em, utilize o seguinte código:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Como: Atualizar propriedades de e-mail
Algumas propriedades de e-mail podem ser substituídas pelos **definir a propriedade** ou acrescentados com **Adicionar propriedade**.

Por exemplo, para especificar **ReplyTo** endereços, utilize o seguinte:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Para adicionar um **Cc** destinatário, utilize o seguinte:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Como: Utilizar serviços adicionais do SendGrid
O SendGrid oferece as APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do SendGrid desde a sua aplicação do Azure. Para mais informações, consulte a [documentação da API de SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço de correio eletrónico SendGrid, siga estas ligações para saber mais.

* Exemplo que demonstra como utilizar o SendGrid numa implementação do Azure: [Como enviar e-mails com o SendGrid de Java numa implementação do Azure](store-sendgrid-java-how-to-send-email-example.md)
* SDK de Java do SendGrid: <https://sendgrid.com/docs/Code_Examples/java.html>
* Documentação da API de SendGrid: <https://sendgrid.com/docs/API_Reference/index.html>
* O SendGrid oferta especial para os clientes do Azure: <https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[serviço de e-mail com base na cloud]: https://sendgrid.com/email-solutions
[entrega de e-mail transacional]: https://sendgrid.com/transactional-email
