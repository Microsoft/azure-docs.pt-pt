---
title: Como fazer uma ligação de Twilio (Java) | Microsoft Docs
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
ms.custom: devx-track-java
ms.openlocfilehash: fdaa2eeef58599cbff8dbf7daa34f9a30f31b9cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548765"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Como fazer uma chamada telefónica usando twilio em uma aplicação java em Azure
O exemplo que se segue mostra como pode usar o Twilio para fazer uma chamada a partir de uma página web hospedada em Azure. A aplicação resultante irá solicitar ao utilizador valores de chamada telefónica, como mostrado na imagem seguinte.

![Formulário de chamada Azure usando Twilio e Java][twilio_java]

Terá de fazer o seguinte para utilizar o código neste tópico:

1. Adquira uma conta Twilio e um token de autenticação. Para começar com twilio, avalie os preços em [https://www.twilio.com/pricing][twilio_pricing] . Pode inscrever-se na [https://www.twilio.com/try-twilio][try_twilio] . Para obter informações sobre a API fornecidas pela Twilio, consulte [https://www.twilio.com/api][twilio_api] .
2. Obtenha o Jar Twilio. Em [https://github.com/twilio/twilio-java][twilio_java_github] , você pode baixar as fontes GitHub e criar o seu próprio JAR, ou baixar um JAR pré-construído (com ou sem dependências).
   O código deste tópico foi escrito usando o JAR TwilioJava-3.3.8-com-dependências pré-construído.
3. Adicione o JAR ao seu caminho de construção java.
4. Se estiver a utilizar o Eclipse para criar esta aplicação Java, inclua o Jar Twilio no seu ficheiro de implementação de aplicações (WAR) utilizando a função de montagem de implementação do Eclipse. Se não estiver a utilizar o Eclipse para criar esta aplicação Java, certifique-se de que o Twilio JAR está incluído no mesmo papel Azure que a sua aplicação Java, e adicionado ao caminho de classe da sua aplicação.
5. Certifique-se de que o seu teclas de cacerts contém o certificado Equifax Secure Certificate Authority com impressão digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:DE F4:CF e a impressão digital SHA1 é D2:32:09:AD:23:D3:14:23:21:74:4:0D:7F:9D:62:13:97:86:63:3A). Este é o certificado da Autoridade de Certificados (CA) para o [https://api.twilio.com][twilio_api_service] serviço, que é chamado quando utiliza Twilio APIs.

Além disso, a familiaridade com as informações na [Criação de uma Aplicação Hello World Utilizando o Kit de Ferramentas Azure para Eclipse][azure_java_eclipse_hello_world], ou com outras técnicas para hospedar aplicações Java em Azure se não estiver a utilizar o Eclipse, é altamente recomendada.

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário web para fazer uma chamada
O código que se segue mostra como criar um formulário web para recuperar dados do utilizador para fazer uma chamada. Para efeitos deste exemplo, foi criado um novo projeto web dinâmico, chamado **TwilioCloud,** e **callform.jsp** foi adicionado como um ficheiro JSP.

```jsp
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
```

## <a name="create-the-code-to-make-the-call"></a>Criar o código para fazer a chamada
O seguinte código, que é chamado quando o utilizador preenche o formulário apresentado por callform.jsp, cria a mensagem de chamada e gera a chamada. Para efeitos deste exemplo, o ficheiro JSP é nomeado **makecall.jsp** e foi adicionado ao projeto **TwilioCloud.** (Utilize a sua conta Twilio e ficha de autenticação em vez dos valores de espaço reservado atribuídos às **contasSID** e **authToken** no código abaixo.)

```jsp
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
```

Além de fazer a chamada, makecall.jsp exibe o ponto final Twilio, versão API e o estado da chamada. Um exemplo é a seguinte imagem:

![Resposta de chamada Azure Usando Twilio e Java][twilio_java_response]

## <a name="run-the-application"></a>Executar a aplicação
Seguem-se os passos de alto nível para executar a sua aplicação; detalhes para estes passos podem ser encontrados na [Criação de uma aplicação Hello World Usando o Kit de Ferramentas Azure para Eclipse][azure_java_eclipse_hello_world].

1. Exporte o seu TwilioCloud  WAR para a pasta Azure. 
2. Modifique **o startup.cmd** para desapertar o seu TwilioCloud WAR.
3. Compile a sua aplicação para o emulador computacional.
4. Inicie a sua implantação no emulador computacional.
5. Abra um navegador e `http://localhost:8080/TwilioCloud/callform.jsp` corra.
6. Introduza valores no formulário, clique em **Fazer esta chamada** e, em seguida, ver os resultados em makecall.jsp.

Quando estiver pronto para partir para a Azure, recompile para a implementação da nuvem, desloque-se para Azure e corra http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp no navegador (substitua o seu valor por *your_hosted_name).*

## <a name="next-steps"></a>Passos seguintes
Este código foi fornecido para mostrar-lhe a funcionalidade básica usando Twilio em Java em Azure. Antes de ser implantado no Azure em produção, é melhor adicionar mais manipulação de erros ou outras funcionalidades. Por exemplo:

* Em vez de utilizar um formulário web, pode utilizar bolhas de armazenamento Azure ou Base de Dados SQL para armazenar números de telefone e texto de chamada. Para obter informações sobre a utilização de bolhas de armazenamento Azure em Java, consulte [Como Utilizar o Serviço de Armazenamento blob de Java][howto_blob_storage_java]. 
* Pode utilizar **RoleEnvironment.getConfigurationSettings** para recuperar o ID da conta Twilio e o token de autenticação das definições de configuração da sua implementação, em vez de codificar duramente os valores em makecall.jsp. Para obter informações sobre a classe **RoleEnvironment,** consulte [utilizar a Biblioteca de Tempo de Execução do Serviço Azure em JSP][azure_runtime_jsp].
* O código p makecall.jsatribui um URL fornecido por Twilio, [https://twimlets.com/message][twimlet_message_url] à variável **Url.** Este URL fornece uma resposta Twilio Markup Language (TwiML) que informa twilio como proceder com a chamada. Por exemplo, o TwiML que é devolvido pode conter um verbo **&lt; Say &gt;** que resulta em texto a ser falado com o destinatário da chamada. Em vez de utilizar o URL fornecido por Twilio, poderá construir o seu próprio serviço para responder ao pedido de Twilio; para obter mais informações, consulte [Como utilizar twilio para capacidades de voz e SMS em Java.][howto_twilio_voice_sms_java] Mais informações sobre o TwiML podem ser encontradas em [https://www.twilio.com/docs/api/twiml][twiml] , e mais informações sobre **&lt; &gt; Say** e outros verbos Twilio podem ser encontrados em [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Leia as diretrizes de segurança do Twilio em [https://www.twilio.com/docs/security][twilio_docs_security] .

Para obter informações adicionais sobre Twilio, [https://www.twilio.com/docs][twilio_docs] consulte.

## <a name="see-also"></a>Consulte também
* [Como usar twilio para capacidades de voz e SMS em Java][howto_twilio_voice_sms_java]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[azure_java_eclipse_hello_world]: /java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: /previous-versions/azure/hh690948(v=azure.100)
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
