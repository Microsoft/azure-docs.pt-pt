---
title: Como utilizar o serviço de e-mail SendGrid (Java) | Microsoft Docs
description: Saiba como enviar e-mail com o serviço de e-mail SendGrid no Azure. Amostras de código escritas em Java.
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
ms.custom: devx-track-java
ms.openlocfilehash: 9ff006b74b6202b02a2767aee4d853b1206ce60d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015456"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Como enviar e-mail usando SendGrid de Java
Este guia demonstra como executar tarefas de programação comuns com o serviço de e-mail SendGrid em Azure. As amostras estão escritas em Java. Os cenários abrangidos incluem **a construção de e-mails,** **envio de e-mail,** **adição de anexos,** **utilização de filtros** e **atualização de propriedades.** Para obter mais informações sobre SendGrid e enviar e-mail, consulte a secção [etapas seguintes.](#next-steps)

## <a name="what-is-the-sendgrid-email-service"></a>O que é o Serviço de E-mail SendGrid?
SendGrid é um [serviço de e-mail baseado na nuvem] que fornece entrega de [e-mail transacional]fiável, escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de utilização da SendGrid incluem:

* Envio automaticamente de recibos aos clientes
* Gestão de listas de distribuição para envio de clientes e-fliers mensais e ofertas especiais
* Recolher métricas em tempo real para coisas como e-mail bloqueado e capacidade de resposta do cliente
* Gerar relatórios para ajudar a identificar tendências
* Encaminhamento de inquéritos aos clientes
* Notificações de e-mail da sua aplicação

Para obter mais informações, consulte <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Criar uma conta SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Como: Utilizar as bibliotecas javax.mail
Obtenha as bibliotecas javax.mail, por <https://www.oracle.com/technetwork/java/javamail> exemplo, e importe-as no seu código. A um nível elevado, o processo de utilização da biblioteca javax.mail para enviar e-mail usando SMTP é fazer o seguinte:

1. Especifique os valores SMTP, incluindo o servidor SMTP, que para a SendGrid é smtp.sendgrid.net.

    ```java
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

1. Prolongue a classe *javax.mail.Authenticator* e na sua implementação do método *getPasswordAuthentication,* devolva o nome de utilizador e palavra-passe sendGrid.  

    ```java
    private class SMTPAuthenticator extends javax.mail.Authenticator {
    public PasswordAuthentication getPasswordAuthentication() {
        String username = SMTP_AUTH_USER;
        String password = SMTP_AUTH_PWD;
        return new PasswordAuthentication(username, password);
    }
    ```
2. Crie uma sessão de e-mail autenticada através de um objeto *javax.mail.Session.*  

    ```java
    Authenticator auth = new SMTPAuthenticator();
    Session mailSession = Session.getDefaultInstance(properties, auth);
    ```
3. Crie a sua mensagem e atribua **a**, **A partir de,** **Valores de conteúdo** e conteúdo. Isto é mostrado na secção Como: Criar uma secção [de e-mail.](#how-to-create-an-email)
4. Envie a mensagem através de um *objeto javax.mail.Transporte.* Isto é mostrado na secção [Como: Enviar um E-mail][#how-para-enviar-um-e-mail].

## <a name="how-to-create-an-email"></a>Como: Criar um e-mail
O seguinte mostra como especificar valores para um e-mail.

```java
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
```

## <a name="how-to-send-an-email"></a>Como: Enviar um e-mail
O seguinte mostra como enviar um e-mail.

```java
Transport transport = mailSession.getTransport();
// Connect the transport object.
transport.connect();
// Send the message.
transport.sendMessage(message, message.getAllRecipients());
// Close the connection.
transport.close();
```

## <a name="how-to-add-an-attachment"></a>Como: Adicionar um anexo
O código que se segue mostra como adicionar um anexo.

```java
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
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Como: Utilizar filtros para permitir rodapés, rastreios e análises
A SendGrid fornece uma funcionalidade adicional de e-mail através da utilização de *filtros.* Estas são configurações que podem ser adicionadas a uma mensagem de e-mail para ativar funcionalidades específicas, tais como permitir o rastreio de cliques, análise do Google, rastreio de subscrição, e assim por diante. Para obter uma lista completa de filtros, consulte [as definições do filtro][Filter Settings].

* O seguinte mostra como inserir um filtro de rodapé que resulta em texto HTML que aparece na parte inferior do e-mail enviado.

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"footer\":
        {\"settings\":
        {\"enable\":1,\"text/html\":
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
    ```
* Outro exemplo de um filtro é o rastreio de cliques. Digamos que o seu texto de e-mail contém uma hiperligação, como o seguinte, e pretende rastrear a taxa de clique:

    ```java
    messagePart.setContent(
        "Hello,
        <p>This is the body of the message. Visit
        <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
        Thank you.",
        "text/html");
    ```
* Para ativar o rastreio do clique, utilize o seguinte código:

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"clicktrack\":
        {\"settings\":
        {\"enable\":1}}}}");
    ```

## <a name="how-to-update-email-properties"></a>Como: Atualizar propriedades de e-mail
Algumas propriedades de e-mail podem ser substituídas usando **a Propriedade definida** ou anexadas usando adicionar **Propriedade**.

Por exemplo, para especificar **respostas Para** endereços, utilize o seguinte:

```java
InternetAddress addresses[] =
    { new InternetAddress("john@contoso.com"),
        new InternetAddress("wendy@contoso.com") };

message.setReplyTo(addresses);
```

Para adicionar um destinatário **Cc,** utilize o seguinte:

```java
message.addRecipient(Message.RecipientType.CC, new
InternetAddress("john@contoso.com"));
```

## <a name="how-to-use-additional-sendgrid-services"></a>Como: Utilizar serviços adicionais da SendGrid
A SendGrid oferece APIs baseados na Web que pode utilizar para alavancar a funcionalidade Adicional SendGrid a partir da sua aplicação Azure. Para mais detalhes, consulte a documentação da [SendGrid API][SendGrid API documentation].

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico do serviço de email SendGrid, siga estes links para saber mais.

* Amostra que demonstra a utilização do SendGrid numa implementação do Azure: [Como enviar e-mail usando o SendGrid de Java numa implementação do Azure](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* Documentação da SendGrid API: <https://sendgrid.com/docs/API_Reference/index.html>
* Oferta especial sendGrid para clientes Azure: <https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[serviço de e-mail baseado na nuvem]: https://sendgrid.com/email-solutions
[entrega de e-mail transacional]: https://sendgrid.com/transactional-email
