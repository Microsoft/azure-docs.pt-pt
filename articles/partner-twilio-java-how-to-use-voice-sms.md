---
title: Como utilizar o Twilio para voz e SMS (Java) | Documentos da Microsoft
description: Saiba como fazer uma chamada telefónica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de códigos escritos em Java.
services: ''
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 386b4b8440c74f6599e7147996b5843ea0f67e68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623957"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Como utilizar o Twilio para voz e SMS capacidades em Java
Este guia demonstra como realizar tarefas comuns de programação com o serviço de Twilio API no Azure. Os cenários abrangidos incluem uma ligação telefônica e enviar uma mensagem de serviço de mensagens curtas (SMS). Para obter mais informações sobre o Twilio e voz e SMS a utilizar nas suas aplicações, consulte a [passos seguintes](#NextSteps) secção.

## <a id="WhatIs"></a>O que é o Twilio?
Twilio é uma API de serviço web de telefonia que permite que use suas habilidades e linguagens da web existentes para criar aplicativos de SMS e de voz. Twilio é um serviço de terceiros (não uma funcionalidade do Azure e não um produto da Microsoft).

**Serviços de voz Twilio** permite que seus aplicativos fazer e receber chamadas telefónicas. **Twilio SMS** permite que seus aplicativos fazer e receber mensagens SMS. **Cliente do Twilio** permite que seus aplicativos permitir a comunicação de voz através de ligações de Internet existentes, incluindo ligações móveis.

## <a id="Pricing"></a>Preços do Twilio e ofertas especiais
Estão disponíveis em informações sobre os preços do Twilio [preços de Twilio][twilio_pricing]. Os clientes do Azure recebem uma [oferta especial][special_offer]: um crédito gratuito de textos de 1000 ou 1000 em minutos de entrada. Inscrever-se para a oferta ou obter mais informações, visite [ https://ahoy.twilio.com/azure ] [ special_offer].

## <a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful, que fornece funcionalidade SMS e de voz para aplicações. As bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [bibliotecas de APIS do Twilio][twilio_libraries].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação do Twilio (TwiML).

### <a id="Verbs"></a>Twilio Verbs
A API usa Twilio verbos; Por exemplo, o **&lt;Digamos&gt;** verbo instrui o Twilio para poder ouvi-lo entregar uma mensagem numa chamada.

Segue-se uma lista de verbos Twilio.

* **&lt;Dial&gt;**: Liga-se o autor da chamada para outro telefone.
* **&lt;Gather&gt;**: Recolhe os dígitos numéricos introduzidos no teclado do telefone.
* **&lt;Hangup&gt;**: Termina uma chamada.
* **&lt;Reproduzir&gt;**: Reproduz um arquivo de áudio.
* **&lt;Fila&gt;**: Adicionar à uma fila de chamadores.
* **&lt;Pause&gt;**: Aguarda silenciosamente um número de segundos especificado.
* **&lt;Registo&gt;**: Regista a voz do chamador e retorna uma URL de um ficheiro que contém a gravação.
* **&lt;Redirect&gt;**: Transfere o controle de uma chamada ou SMS para o TwiML numa URL diferente.
* **&lt;Reject&gt;**: Rejeita uma chamada de entrada para o seu número do Twilio sem de faturação.
* **&lt;Say&gt;**: Converte texto em voz, que é efetuado numa chamada.
* **&lt;Sms&gt;**: Envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções com base em XML, com base nos verbos de Twilio informam Twilio de como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML seria converter o texto **Olá, mundo!** a conversão de voz.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Quando seu aplicativo chama a API Twilio, um dos parâmetros de API é o URL que devolve a resposta de TwiML. Para fins de desenvolvimento, pode utilizar URLs fornecidos pelo Twilio para fornecer as respostas de TwiML utilizadas pelas suas aplicações. Também pudesse hospedar suas próprias URLs para produzir as respostas de TwiML e outra opção consiste em utilizar o **TwiMLResponse** objeto.

Para obter mais informações sobre o Twilio verbos, os atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do Twilio
Quando estiver pronto para obter uma conta do Twilio, inscreva-se em [tente Twilio][try_twilio]. Pode começar com uma conta gratuita e atualize a sua conta mais tarde.

Quando se inscreve para uma conta do Twilio, receberá um ID de conta e um token de autenticação. Ambos serão necessários para fazer chamadas à API do Twilio. Para impedir acesso não autorizado à sua conta, manter o token de autenticação segura. Sua ID de conta e a autenticação de token são visíveis no [Twilio consola][twilio_console], nos campos nome **SID da conta** e **TOKEN de autenticação de**, respectivamente.

## <a id="create_app"></a>Criar uma aplicação Java
1. Obtenha o JAR do Twilio e adicioná-lo ao seu caminho de compilação de Java e o assembly de implementação de guerra. Em [ https://github.com/twilio/twilio-java ] [ twilio_java], pode transferir as origens do GitHub e crie seu próprio JAR ou transferir um JAR previamente criado (com ou sem dependências).
2. Certifique-se o JDK **cacerts** keystore contém o certificado de autoridade de certificação segura Equifax com impressão digital de MD5 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e o SHA1 impressão digital é D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Este é o certificado de autoridade de certificado para o [ https://api.twilio.com ] [ twilio_api_service] serviço, que é chamado quando usar APIs do Twilio. Para obter informações sobre a garantia de sua JDK **cacerts** keystore contém o certificado de AC correto, consulte [adicionando um certificado para o Store de certificado de AC de Java][add_ca_cert].

Estão disponíveis em instruções detalhadas para com a biblioteca de cliente do Twilio para Java [como tornar uma Twilio através de chamada telefónica numa aplicação de Java no Azure][howto_phonecall_java].

## <a id="configure_app"></a>Configurar a sua aplicação utilizar bibliotecas do Twilio
No seu código, pode adicionar **importar** declarações na parte superior dos seus ficheiros de origem para o Twilio pacotes ou classes que pretende utilizar na sua aplicação.

Para ficheiros de origem Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Para ficheiros de origem da página de servidor de Java (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Dependendo de qual Twilio pacotes ou classes que pretende utilizar, sua **importar** instruções podem ser diferentes.

## <a id="howto_make_call"></a>Como: Efetuar uma chamada de saída
O seguinte mostra como tornar uma saída chamar utilizando o **chamar** classe. Esse código também utiliza um site fornecida pelo Twilio para retornar a resposta de linguagem de marcação do Twilio (TwiML). Substitua os valores para o **partir** e **para** números de telefone e certifique-se de que verifique o **de** número de telefone para sua conta do Twilio antes de executar o código.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Para obter mais informações sobre os parâmetros transmitidos para o **Call.creator** método, consulte [ https://www.twilio.com/docs/api/rest/making-calls ] [ twilio_rest_making_calls].

Conforme mencionado, esse código usa um site fornecida pelo Twilio para retornar a resposta de TwiML. Em vez disso, poderia usar seu próprio site para fornecer a resposta de TwiML; Para obter mais informações, consulte [como fornecer TwiML as respostas numa aplicação de Java no Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como: Envie uma mensagem SMS
O seguinte mostra como enviar uma mensagem SMS através do **mensagem** classe. O **partir** número, **4155992671**, é fornecido pela Twilio para contas de avaliação para enviar mensagens SMS. O **para** número deve ser verificado para a sua conta do Twilio antes de executar o código.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Para obter mais informações sobre os parâmetros transmitidos para o **Message.creator** método, consulte [ https://www.twilio.com/docs/api/rest/sending-sms ] [ twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas TwiML do seu próprio Web site
Quando a aplicação inicia uma chamada à API do Twilio, por exemplo através da **CallCreator.create** método, Twilio irá enviar o pedido para um URL que deverá devolver uma resposta de TwiML. O exemplo acima utiliza o URL fornecido pelo Twilio [ https://twimlets.com/message ] [ twimlet_message_url]. (Apesar de TwiML foi concebido para utilização pelos serviços Web, pode ver o TwiML no seu browser. Por exemplo, clique em [ https://twimlets.com/message ] [ twimlet_message_url] para ver vazio **&lt;resposta&gt;** elemento; como outro exemplo, clique em [ https://twimlets.com/message?Message%5B0%5D=Hello%20World%21 ] [ twimlet_message_url_hello_world] para ver uma **&lt;resposta&gt;** elemento que contém um **&lt;Digamos&gt;** elemento.)

Em vez de contar com o URL fornecido pelo Twilio, pode criar seu próprio site URL que devolve as respostas HTTP. Pode criar o site em qualquer linguagem que retorna as respostas HTTP; Este tópico parte do princípio de que irá alojar o URL numa página JSP.

A seguinte página JSP resulta numa resposta de TwiML diz **Olá, mundo!** na chamada.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

A seguinte página JSP resulta numa resposta de TwiML que diz algum texto, tem várias interrupções e diz informações sobre a versão de API do Twilio e o nome da função do Azure.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

O **ApiVersion** parâmetro está disponível nos pedidos de voz Twilio (não pedidos SMS). Para ver os parâmetros de solicitação de disponibilidade para serviços de voz Twilio e pedidos SMS, consulte <https://www.twilio.com/docs/api/twiml/twilio_request> e <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, respectivamente. O **RoleName** variável de ambiente está disponível como parte da implementação do Azure. (Se quiser adicionar variáveis de ambiente personalizadas, de modo que poderiam ser captados **System.getenv**, consulte a secção de variáveis de ambiente no [diversas definições de configuração de função] [ misc_role_config_settings].)

Assim que tiver a sua página JSP configurada para fornecer respostas TwiML, utilize o URL da página JSP, como o URL transmitido para o **Call.creator** método. Por exemplo, se tiver um aplicativo Web chamado MyTwiML implementado a Azure alojado o serviço e o nome da página JSP é mytwiml.jsp, o URL pode ser passado para **Call.creator** conforme mostrado a seguir:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Outra opção para responder com TwiML é através do **VoiceResponse** classe, que está disponível na **com.twilio.twiml** pacote.

Para obter mais informações sobre como utilizar o Twilio no Azure com Java, veja [como tornar uma Twilio através de chamada telefónica numa aplicação de Java no Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Como: Utilizar os serviços adicionais do Twilio
Para além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do Twilio através da aplicação do Azure. Para mais informações, consulte a [documentação da API do Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço Twilio, siga estas ligações para saber mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [Twilio tutoriais sobre como e o código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do Twilio][twilio_quickstarts]
* [Twilio no GitHub][twilio_on_github]
* [Comunicar com o suporte do Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
