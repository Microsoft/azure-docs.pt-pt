---
title: Como fazer uma chamada telefónica do Twilio (Java) | Documentos da Microsoft
description: Saiba como fazer uma chamada telefónica de uma página da web usando o Twilio num aplicativo de Java no Azure.
services: ''
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 71ffcc420372215a393f560d01d6c08dee03dfbd
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433676"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Como fazer uma chamada telefónica, utilizar o Twilio numa aplicação de Java no Azure
O exemplo seguinte mostra como pode utilizar o Twilio para fazer uma chamada de uma página da web alojadas no Azure. O aplicativo resultante pedirá ao utilizador para os valores de chamada telefónica, conforme mostrado na captura de ecrã seguinte.

![Formulário de chamada do Azure com o Twilio e Java][twilio_java]

Precisará fazer o seguinte para utilizar o código neste tópico:

1. Adquirir uma conta do Twilio e autenticação de token. Para começar a utilizar com o Twilio, avalie a preços em [ https://www.twilio.com/pricing ] [ twilio_pricing]. Pode inscrever-se no [ https://www.twilio.com/try-twilio ] [ try_twilio]. Para obter informações sobre a API fornecida pelo Twilio, consulte [ https://www.twilio.com/api ] [ twilio_api].
2. Obter o JAR do Twilio. Em [ https://github.com/twilio/twilio-java ] [ twilio_java_github], pode transferir as origens do GitHub e crie seu próprio JAR ou transferir um JAR previamente criado (com ou sem dependências).
   O código deste tópico foi escrito usando o JAR TwilioJava 3.3.8 com dependências criado previamente.
3. Adicione JAR ao seu caminho de compilação de Java.
4. Se estiver a utilizar o Eclipse para criar esta aplicação de Java, incluem o JAR do Twilio, no seu ficheiro de implementação de aplicação (WAR) usando o recurso de assemblagem de implantação do Eclipse. Se não estiver a utilizar o Eclipse para criar esta aplicação de Java, certifique-se de que o Twilio JAR é incluído na mesma função do Azure como a sua aplicação Java e adicionado para o caminho da classe de seu aplicativo.
5. Certifique-se de que sua keystore cacerts contém o certificado de autoridade de certificação segura Equifax com impressão digital de MD5 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a impressão digital SHA1 é D2:32:09:AD:23:D 3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Este é o certificado de autoridade de certificado para o [ https://api.twilio.com ] [ twilio_api_service] serviço, que é chamado quando usar APIs do Twilio. Para obter informações sobre como adicionar este certificado da AC para arquivo de cacert o JDK, consulte [adicionando um certificado para o Store de certificado de AC do Java][add_ca_cert].

Além disso, a familiaridade com as informações em [criando um Hello World aplicação a utilizar o Azure Toolkit para Eclipse][azure_java_eclipse_hello_world], ou com outras técnicas para se estiver a alojar aplicações Java no Azure não utilizar o Eclipse, é altamente recomendado.

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário da web para fazer uma chamada
O código seguinte mostra como criar um formulário da web para obter dados de utilizador para fazer uma chamada. Para efeitos deste exemplo, um novo projeto web dinâmico, com o nome **TwilioCloud**, foi criado, e **callform.jsp** foi adicionado como um ficheiro JSP.

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

## <a name="create-the-code-to-make-the-call"></a>Criar o código para fazer a chamada
O código a seguir, que é chamado quando o utilizador concluir o formulário exibido pela callform.jsp, cria a mensagem de chamada e gera a chamada. Para efeitos deste exemplo, o ficheiro JSP é denominado **makecall.jsp** e foi adicionado para o **TwilioCloud** projeto. (Utilize a sua conta do Twilio e autenticação de token em vez dos valores de marcador de posição atribuídos a **accountSID** e **authToken** no código abaixo.)

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

Além de tornar a chamada, makecall.jsp apresenta o ponto de extremidade do Twilio, a versão de API e o estado da chamada. Um exemplo é a seguinte captura de ecrã:

![Resposta de chamada do Azure com o Twilio e Java][twilio_java_response]

## <a name="run-the-application"></a>Executar a aplicação
Seguem-se os passos de alto nível para executar seu aplicativo; os detalhes para estes passos podem ser encontrados em [criando um Hello World aplicação a utilizar o Azure Toolkit para Eclipse][azure_java_eclipse_hello_world].

1. Exportar WAR sua TwilioCloud para o Azure **approot** pasta. 
2. Modificar **startup.cmd** para descomprimir o WAR TwilioCloud.
3. Compile seu aplicativo para o emulador de computação.
4. Inicie a implementação no emulador de computação.
5. Abra um browser e execute **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Introduza os valores no formulário, clique em **fazer esta chamada**e, em seguida, ver os resultados no makecall.jsp.

Quando estiver pronto para implementar no Azure, recompilação para a implementação para a cloud, implementar no Azure e execute http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp no browser (substitua o valor para  *your_hosted_name*).

## <a name="next-steps"></a>Passos Seguintes
Esse código foi fornecido para mostrar a funcionalidade básica com o Twilio em Java no Azure. Antes de implementar para o Azure na produção, pode querer adicionar tratamento de erros mais ou outros recursos. Por exemplo:

* Em vez de usar um formulário da web, poderia usar os blobs de armazenamento do Azure ou a base de dados SQL para armazenar números de telefone e chamar o texto. Para obter informações sobre a utilização de blobs de armazenamento do Azure em Java, veja [como utilizar o serviço de armazenamento de Blobs do Java][howto_blob_storage_java]. 
* Poderia usar **RoleEnvironment.getConfigurationSettings** para recuperar o Twilio ID da conta e a autenticação de token de definições de configuração da sua implementação, em vez de embutir no código os valores na makecall.jsp. Para obter informações sobre o **RoleEnvironment** de classe, consulte [usando a biblioteca de tempo de execução de serviço do Azure no JSP] [ azure_runtime_jsp] e a documentação do pacote de Runtime do serviço do Azure em [ http://dl.windowsazure.com/javadoc][azure_javadoc].
* O código de makecall.jsp atribui um URL fornecido pelo Twilio [ https://twimlets.com/message ] [ twimlet_message_url], para o **Url** variável. Este URL fornece uma resposta de linguagem de marcação do Twilio (TwiML) que informa Twilio como proceder com a chamada. Por exemplo, o que é devolvido TwiML pode conter uma **&lt;Digamos&gt;** verbo que resulta num texto a ser dito ao destinatário da chamada. Em vez de utilizar o URL fornecido pelo Twilio, poderia construir seu próprio serviço para responder à solicitação do Twilio; Para obter mais informações, consulte [como Twilio do uso de voz e SMS capacidades em Java][howto_twilio_voice_sms_java]. Obter mais informações sobre TwiML podem ser encontradas em [ https://www.twilio.com/docs/api/twiml ] [ twiml]e obter mais informações sobre **&lt;Digamos&gt;** e outros verbos Twilio podem disponível em [ https://www.twilio.com/docs/api/twiml/say ] [ twilio_say].
* Leia as diretrizes de segurança do Twilio em [ https://www.twilio.com/docs/security ] [ twilio_docs_security].

Para obter informações adicionais sobre o Twilio, consulte [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="see-also"></a>Consultar Também
* [Como utilizar o Twilio para voz e SMS capacidades em Java][howto_twilio_voice_sms_java]
* [Adicionar um certificado para o Store de certificado de AC de Java][add_ca_cert]

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
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
