---
title: store-sendgrid-java-how-to-send-email-example
description: Como enviar e-mails com o SendGrid de Java numa implementação do Azure
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: fecc3b411211ceb8aa6db5a0e0c0c6b07f19a63f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442466"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Como enviar E-mails com o SendGrid de Java numa implementação do Azure
O exemplo seguinte mostra como pode utilizar o SendGrid para enviar e-mails a partir de uma página da web alojadas no Azure. O aplicativo resultante pedirá ao utilizador para os valores de e-mail, conforme mostrado na captura de ecrã seguinte.

![Formulário de e-mail][emailform]

O e-mail resultante terá um aspeto semelhante à seguinte captura de ecrã.

![Mensagem de e-mail][emailsent]

Precisará fazer o seguinte para utilizar o código neste tópico:

1. Obter javax.mail JARs, por exemplo a partir de <https://www.oracle.com/technetwork/java/javamail/index.html>.
2. Adicione o Intune ao seu caminho de compilação de Java.
3. Se estiver a utilizar o Eclipse para criar esta aplicação de Java, pode incluir as bibliotecas do SendGrid no seu ficheiro de implementação de aplicação (WAR) usando o recurso de assemblagem de implantação do Eclipse. Se não estiver a utilizar o Eclipse para criar esta aplicação de Java, certifique-se de que as bibliotecas são incluídas na mesma função do Azure como a sua aplicação Java e adicionadas para o caminho da classe de seu aplicativo.

Também tem de ter seu próprio nome de utilizador do SendGrid e a palavra-passe, ser capaz de enviar o e-mail. Para começar a utilizar com o SendGrid, veja [como enviar e-mails com o SendGrid de Java](store-sendgrid-java-how-to-send-email.md).

Além disso, a familiaridade com as informações em [criando um aplicativo de Hello World para o Azure no Eclipse](https://msdn.microsoft.com/library/windowsazure/hh690944), ou com outras técnicas para alojar aplicações Java no Azure, se não estiver a utilizar Eclipse, é altamente recomendado.

## <a name="create-a-web-form-for-sending-email"></a>Criar um formulário da web para o envio de e-mail
O código seguinte mostra como criar um formulário da web para obter dados de utilizador para o envio de e-mail. Para efeitos deste conteúdo, o ficheiro JSP é denominado **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Criar o código para enviar a mensagem de e-mail
O código a seguir, que é chamado quando concluir o formulário no emailform.jsp, cria a mensagem de e-mail e envia-os. Para efeitos deste conteúdo, o ficheiro JSP é denominado **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%

     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");

     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;

            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {

         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";

         Properties properties;

         properties = new Properties();

         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");

         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();

         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);

         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);

         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 

         message = new MimeMessage(mailSession);

         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            

         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);

         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();

        out.println("<p>Email processing completed.</p>");

     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>

    </body>
    </html>

Para além de enviar o e-mail, emailform.jsp fornece um resultado para o usuário; um exemplo é a seguinte captura de ecrã:

![Enviar o resultado de correio][emailresult]

## <a name="next-steps"></a>Passos Seguintes
Implementar a sua aplicação no emulador de computação e dentro de um navegador executar emailform.jsp, introduza os valores no formulário, clique em **envie este e-mail**e, em seguida, ver os resultados no sendemail.jsp.

Esse código foi fornecido para lhe mostrar como utilizar o SendGrid em Java no Azure. Antes de implementar para o Azure na produção, pode querer adicionar tratamento de erros mais ou outros recursos. Por exemplo: 

* Poderia usar os blobs de armazenamento do Azure ou a base de dados SQL para armazenar endereços de e-mail e mensagens de e-mail, em vez de usar um formulário da web. Para obter informações sobre a utilização de blobs de armazenamento do Azure em Java, veja [como utilizar o serviço de armazenamento de Blobs do Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Para obter informações sobre como utilizar a base de dados SQL em Java, veja [utilizar base de dados SQL em Java](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java).
* Poderia usar `RoleEnvironment.getConfigurationSettings` para recuperar o SendGrid nome de utilizador e palavra-passe de definições de configuração da sua implementação, em vez de usar o formulário da web para obter esses valores. Para obter informações sobre o `RoleEnvironment` classe, consulte [usando a biblioteca de tempo de execução de serviço do Azure no JSP](https://msdn.microsoft.com/library/windowsazure/hh690948) e a documentação do pacote de Runtime do serviço do Azure em <http://dl.windowsazure.com/javadoc>.
* Para obter mais informações sobre como utilizar o SendGrid em Java, veja [como enviar e-mails com o SendGrid de Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
