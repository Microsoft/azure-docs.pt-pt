---
title: Como usar o serviço de email SendGrid (Java) | Microsoft Docs
description: Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de código escritos em Java.
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
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 8ae948e9c79cff4cd0c896b250743fd9dc521752
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876514"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Como enviar emails usando o SendGrid do Java
Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Azure. Os exemplos são escritos em Java. Os cenários abordados incluem a **construção de email**, o **envio de email**, a adição de **anexos**, o **uso de filtros**e a atualização de **Propriedades**. Para obter mais informações sobre o SendGrid e o envio de email, consulte a seção [próximas etapas](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de email SendGrid?
O SendGrid é um [serviço de email baseado em nuvem] que fornece [entrega de email]transacional confiável, escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de uso de SendGrid incluem:

* Enviando automaticamente recibos aos clientes
* Administrando listas de distribuição para enviar aos clientes e-folhetos mensais e ofertas especiais
* Coleta de métricas em tempo real para coisas como email bloqueado e capacidade de resposta do cliente
* Gerando relatórios para ajudar a identificar tendências
* Encaminhando consultas do cliente
* Notificações por email do seu aplicativo

Para obter mais informações, <https://sendgrid.com>consulte.

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Como: Usar as bibliotecas javax. mail
Obtenha as bibliotecas javax. mail, por exemplo, <https://www.oracle.com/technetwork/java/javamail> de e importe-as para seu código. Em um alto nível, o processo para usar a biblioteca javax. mail para enviar email usando SMTP é fazer o seguinte:

1. Especifique os valores SMTP, incluindo o servidor SMTP, que para SendGrid é smtp.sendgrid.net.

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

1. Estenda a classe *javax. mail. Authenticator* e, em sua implementação do método *getPasswordAuthentication* , retorne seu nome de usuário e senha do SendGrid.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Crie uma sessão de email autenticado por meio de um objeto *javax. mail. Session* .  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Crie sua mensagem e atribua valores **para**, **de**, **assunto** e conteúdo. Isso é mostrado na [seguinte forma: Criar uma seção](#how-to-create-an-email) de email.
4. Envie a mensagem por meio de um objeto *javax. mail. Transport* . Isso é mostrado na seção [como: Enviar uma seção de email] [#how para enviar-um-email].

## <a name="how-to-create-an-email"></a>Como: Criar um email
O seguinte mostra como especificar valores para um email.

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

## <a name="how-to-send-an-email"></a>Como: Enviar um email
O seguinte mostra como enviar um email.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Como: Adicionar um anexo
O código a seguir mostra como adicionar um anexo.

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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Como: Usar filtros para habilitar rodapés, rastreamento e análise
O SendGrid fornece funcionalidade adicional de email por meio do uso de *filtros*. Essas são configurações que podem ser adicionadas a uma mensagem de email para habilitar funcionalidades específicas, como habilitar o acompanhamento de cliques, o Google Analytics, o acompanhamento de assinaturas e assim por diante. Para obter uma lista completa de filtros, consulte [configurações de filtro][Filter Settings].

* O código a seguir mostra como inserir um filtro de rodapé que resulta em texto HTML que aparece na parte inferior do email que está sendo enviado.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Outro exemplo de um filtro é o controle de clique. Digamos que seu texto de email contenha um hiperlink, como o seguinte, e você queira acompanhar a taxa de clique:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Para habilitar o acompanhamento de cliques, use o seguinte código:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Como: Atualizar propriedades de email
Algumas propriedades de email podem ser substituídas usando **definir propriedade** ou acrescentadas usando **Adicionar Propriedade**.

Por exemplo, para especificar  endereços ReplyTo, use o seguinte:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Para adicionar um destinatário de **CC** , use o seguinte:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Como: Usar serviços SendGrids adicionais
O SendGrid oferece APIs baseadas na Web que você pode usar para aproveitar a funcionalidade adicional do SendGrid do seu aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API do SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Passos seguintes
Agora que você aprendeu os conceitos básicos do serviço de email do SendGrid, siga estes links para saber mais.

* Exemplo que demonstra como usar SendGrid em uma implantação do Azure: [Como enviar emails usando o SendGrid do Java em uma implantação do Azure](store-sendgrid-java-how-to-send-email-example.md)
* SDK do Java do SendGrid:<https://sendgrid.com/docs/Code_Examples/java.html>
* Documentação da API do SendGrid:<https://sendgrid.com/docs/API_Reference/index.html>
* Oferta especial do SendGrid para clientes do Azure:<https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[serviço de email baseado em nuvem]: https://sendgrid.com/email-solutions
[entrega de email]: https://sendgrid.com/transactional-email
