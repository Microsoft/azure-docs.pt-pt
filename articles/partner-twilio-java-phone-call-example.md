---
title: Como fazer uma ligação de Twilio (Java) [ Microsoft Docs
description: Aprenda a fazer uma chamada telefónica a partir de uma página web usando Twilio numa aplicação Java no Azure.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 168ec65cfd0ff4e87c33324daa353b554111c8aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838562"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Como fazer uma chamada telefónica usando Twilio em uma aplicação Java em Azure
O exemplo que se segue mostra como pode usar o Twilio para fazer uma chamada a partir de uma página web hospedada no Azure. A aplicação resultante irá solicitar ao utilizador valores de chamada telefónica, como mostra a seguinte imagem.

![Formulário de chamada azure usando Twilio e Java][twilio_java]

Terá de fazer o seguinte para usar o código neste tópico:

1. Adquira uma conta Twilio e um símbolo de autenticação. Para começar com Twilio, avalie os preços em [https://www.twilio.com/pricing][twilio_pricing]. Pode inscrever-se [https://www.twilio.com/try-twilio][try_twilio]em. Para obter informações sobre a API [https://www.twilio.com/api][twilio_api]fornecida por Twilio, consulte .
2. Obtenha o Twilio JAR. No [https://github.com/twilio/twilio-java][twilio_java_github], você pode baixar as fontes GitHub e criar o seu próprio JAR, ou baixar um JAR pré-construído (com ou sem dependências).
   O código neste tópico foi escrito usando o Jar TwilioJava-3.3.8 pré-construído.
3. Adicione o JAR ao seu caminho de construção Java.
4. Se estiver a utilizar o Eclipse para criar esta aplicação Java, inclua o Jar Twilio no ficheiro de implementação de aplicações (WAR) utilizando a função de montagem de implementação do Eclipse. Se não estiver a utilizar o Eclipse para criar esta aplicação Java, certifique-se de que o Twilio JAR está incluído na mesma função Azure que a sua aplicação Java, e adicionado ao percurso de classe da sua aplicação.
5. Certifique-se de que a sua loja de cacerts contém o certificado Equifax Secure Certificate Authority com impressão digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a impressão digital SHA1 é D2:32:09:D3:D3:D3:D3:D3:D3:D3:D3:D4 3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Este é o certificado da autoridade [https://api.twilio.com][twilio_api_service] do certificado (CA) para o serviço, que é chamado quando utiliza APIs Twilio. Para obter informações sobre a adição deste certificado CA à loja de cacert da sua JDK, consulte adicionar um Certificado à Loja de [Certificados Java CA][add_ca_cert].

Além disso, a familiaridade com a informação na [Creating a Hello World Application Using the Azure Toolkit for Eclipse][azure_java_eclipse_hello_world], ou com outras técnicas para hospedar aplicações Java em Azure se não estiver a usar o Eclipse, é altamente recomendado.

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário web para fazer uma chamada
O código que se segue mostra como criar um formulário web para recuperar os dados dos utilizadores para fazer uma chamada. Para efeitos deste exemplo, foi criado um novo projeto web dinâmico, chamado **TwilioCloud,** e **callform.jsp** foi adicionado como um ficheiro JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Crie o código para fazer a chamada
O seguinte código, que é chamado quando o utilizador completa o formulário apresentado por callform.jsp, cria a mensagem de chamada e gera a chamada. Para efeitos deste exemplo, o ficheiro JSP é nomeado **makecall.jsp** e foi adicionado ao projeto **TwilioCloud.** (Utilize a sua conta Twilio e o símbolo de autenticação em vez dos valores reservados atribuídos à **contaSID** e **authToken** no código abaixo.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="https://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Além de fazer a chamada, makecall.jsp exibe o ponto final twilio, a versão API e o estado de chamada. Um exemplo é a seguinte imagem:

![Resposta de chamada azure usando Twilio e Java][twilio_java_response]

## <a name="run-the-application"></a>Executar a aplicação
Seguem-se os passos de alto nível para executar a sua aplicação; detalhes para estes passos podem ser encontrados na Criação de [uma aplicação Hello World Usando o Kit de Ferramentas Azure para Eclipse][azure_java_eclipse_hello_world].

1. Exporte o seu TwilioCloud WAR para a pasta de **açaime** Azure. 
2. Modifique **o startup.cmd** para desapertar o seu TwilioCloud WAR.
3. Compile a sua aplicação para o emulador de cálculo.
4. Inicie a sua implantação no emulador de cálculo.
5. Abra um navegador `http://localhost:8080/TwilioCloud/callform.jsp`e corra.
6. Introduza valores no formulário, clique **Em Fazer esta chamada**e, em seguida, veja os resultados em makecall.jsp.

Quando estiver pronto para ser implantado no Azure, recompilar para implantação na nuvem, implementar para o Azure e executar http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp no navegador (substitua o seu valor por *your_hosted_name).*

## <a name="next-steps"></a>Passos seguintes
Este código foi fornecido para lhe mostrar a funcionalidade básica usando Twilio em Java no Azure. Antes de ser implantado para o Azure em produção, é melhor adicionar mais manipulação de erros ou outras funcionalidades. Por exemplo:

* Em vez de utilizar um formulário web, pode utilizar bolhas de armazenamento Azure ou Base de Dados SQL para armazenar números de telefone e texto de chamada. Para obter informações sobre a utilização de bolhas de armazenamento Azure em Java, consulte [Como Utilizar o Serviço de Armazenamento Blob a partir de Java][howto_blob_storage_java]. 
* Pode utilizar o **RoleEnvironment.getConfigurationSettings** para recuperar o ID da conta Twilio e o símbolo de autenticação das definições de configuração da sua implementação, em vez de codificar duramente os valores em makecall.jsp. Para obter informações sobre a aula **RoleEnvironment,** consulte Utilizar a Biblioteca de [Runtime do Serviço Azure em JSP][azure_runtime_jsp].
* O código makecall.jsp atribui um URL fornecido [https://twimlets.com/message][twimlet_message_url]por Twilio, à variável **Url.** Este URL fornece uma resposta de Twilio Markup Language (TwiML) que informa Twilio como proceder com a chamada. Por exemplo, o TwiML que ** &lt;é&gt; ** devolvido pode conter um verbo Say que resulta em texto a ser falado com o destinatário da chamada. Em vez de utilizar o URL fornecido pela Twilio, pode construir o seu próprio serviço para responder ao pedido de Twilio; para mais informações, consulte [Como Utilizar twilio para capacidades de voz e SMS em Java][howto_twilio_voice_sms_java]. Mais informações sobre o TwiML podem ser encontradas em, [https://www.twilio.com/docs/api/twiml][twiml]e mais informações sobre [https://www.twilio.com/docs/api/twiml/say][twilio_say] ** &lt;Say&gt; ** e outros verbos Twilio podem ser encontradas em .
* Leia as diretrizes de [https://www.twilio.com/docs/security][twilio_docs_security]segurança twilio em .

Para obter informações adicionais [https://www.twilio.com/docs][twilio_docs]sobre Twilio, consulte .

## <a name="see-also"></a>Veja também
* [Como usar twilio para capacidades de voz e SMS em Java][howto_twilio_voice_sms_java]
* [Adicionar um certificado à Java CA Certificate Store][add_ca_cert]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: https://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
