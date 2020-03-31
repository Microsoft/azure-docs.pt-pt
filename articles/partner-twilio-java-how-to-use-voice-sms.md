---
title: Como usar o Twilio para voz e SMS (Java) [ Microsoft Docs
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço Twilio API no Azure. Amostras de código escritas em Java.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 18e93ce18ed746612996399dc1aeb258abd26165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637216"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Como usar twilio para capacidades de voz e SMS em Java
Este guia demonstra como executar tarefas comuns de programação com o serviço Twilio API no Azure. Os cenários abordados incluem fazer uma chamada telefónica e enviar uma mensagem de Serviço de Mensagens Curtas (SMS). Para obter mais informações sobre twilio e usando voz e SMS nas suas aplicações, consulte a secção [Next Steps.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>O que é Twilio?
Twilio é um API de serviço web de telefonia que permite usar as suas línguas e habilidades web existentes para construir aplicações de voz e SMS. Twilio é um serviço de terceiros (não uma funcionalidade Azure e não um produto Microsoft).

**A Twilio Voice** permite que as suas aplicações façam e recebam chamadas telefónicas. **O SMS Twilio** permite que as suas aplicações eçam e recebam mensagens SMS. **O Twilio Client** permite que as suas aplicações permitam a comunicação por voz utilizando as ligações de Internet existentes, incluindo ligações móveis.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Preços twilio e ofertas especiais
Informações sobre preços Twilio estão disponíveis na [Twilio Pricing][twilio_pricing]. Os clientes azure recebem uma [oferta especial:][special_offer]um crédito gratuito de 1000 textos ou 1000 minutos de entrada. Para se inscrever para esta oferta ou [https://ahoy.twilio.com/azure][special_offer]obter mais informações, visite .

## <a name="concepts"></a><a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece funcionalidade de voz e SMS para aplicações. As bibliotecas de clientes estão disponíveis em vários idiomas; para uma lista, consulte [bibliotecas Twilio API][twilio_libraries].

Os aspectos-chave da API Twilio são os verbos Twilio e a Linguagem Twilio Markup (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Verbos
A API utiliza os verbos Twilio; por exemplo, o ** &lt;verbo Say&gt; ** instrui Twilio a entregar uma mensagem audível numa chamada.

Segue-se uma lista de verbos Twilio.

* **Marcação&gt;: Liga o chamador a outro &lt;** telefone.
* Recolha : Recolhe os dígitos numéricos introduzidos no teclado do telefone. ** &lt;&gt;**
* **Hangup&gt;: Termina uma &lt;** chamada.
* **Reprodução&gt;: Reproduz um ficheiro &lt;** áudio.
* Fila : Adicione o a uma fila de chamadas. ** &lt;&gt;**
* Pausa : Aguarda silenciosamente um número determinado de segundos. ** &lt;&gt;**
* **Gravar:&gt;Grava a voz do chamador e devolve um URL de um ficheiro que contém a &lt;** gravação.
* **Redirecionamento&gt;: Transfere o controlo de uma chamada ou SMS para o TwiML num URL diferente. &lt;**
* Rejeitar : Rejeita uma chamada para o seu número Twilio sem cobrar. ** &lt;&gt;**
* Diga : Converte texto em discurso que é feito numa chamada. ** &lt;&gt;**
* **SMS&gt;: Envia uma mensagem SMS. &lt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseadas em XML baseadas nos verbos Twilio que informam Twilio de como processar uma chamada ou SMS.

Como exemplo, o seguinte TwiML converteria o texto **Hello World!** para falar.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Quando a sua aplicação chama a API Twilio, um dos parâmetros DaPI é o URL que devolve a resposta TwiML. Para fins de desenvolvimento, pode utilizar URLs fornecidos pela Twilio para fornecer as respostas TwiML utilizadas pelas suas aplicações. Também pode alojar os seus próprios URLs para produzir as respostas TwiML, e outra opção é utilizar o objeto **TwiMLResponse.**

Para mais informações sobre os verbos Twilio, os seus atributos, e o TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [a Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Criar uma conta Twilio
Quando estiver pronto para obter uma conta Twilio, inscreva-se no [Try Twilio.][try_twilio] Pode começar com uma conta gratuita e fazer upgrade da sua conta mais tarde.

Quando se inscrever numa conta Twilio, receberá uma identificação da conta e um símbolo de autenticação. Ambos serão necessários para fazer chamadas da API twilio. Para evitar o acesso não autorizado à sua conta, mantenha o seu símbolo de autenticação seguro. O id da sua conta e o token de autenticação são visualizados na [Consola Twilio,][twilio_console]nos campos rotulados **DE IS e** **ToKEN AUTH,** respectivamente.

## <a name="create-a-java-application"></a><a id="create_app"></a>Criar uma Aplicação Java
1. Obtenha o Twilio JAR e adicione-o ao seu caminho de construção Java e ao seu conjunto de implantação WAR. No [https://github.com/twilio/twilio-java][twilio_java], você pode baixar as fontes GitHub e criar o seu próprio JAR, ou baixar um JAR pré-construído (com ou sem dependências).
2. Certifique-se de que o seu keystore de **cacerts** da JDK contém o certificado Equifax Secure Certificate Authority com impressão digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a impressão digital SHA1 é D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Este é o certificado da autoridade [https://api.twilio.com][twilio_api_service] do certificado (CA) para o serviço, que é chamado quando utiliza APIs Twilio. Para obter informações sobre como garantir que a loja de **cateerts** da JDK contém o certificado CA correto, consulte adicionar um Certificado à Loja de [Certificados Java CA][add_ca_cert].

Instruções detalhadas para a utilização da biblioteca de clientes Twilio para Java estão disponíveis em [Como Fazer uma Chamada Telefónica Usando Twilio numa Aplicação Java no Azure][howto_phonecall_java].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Configure a sua aplicação para usar bibliotecas Twilio
Dentro do seu código, pode adicionar declarações de **importação** no topo dos seus ficheiros de origem para os pacotes ou classes Twilio que pretende utilizar na sua aplicação.

Para ficheiros de origem Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Para ficheiros de origem da página do servidor de Java (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Dependendo dos pacotes ou classes Twilio que pretende utilizar, as suas declarações de **importação** podem ser diferentes.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída usando a classe **Call.** Este código também utiliza um site fornecido por Twilio para devolver a resposta da Linguagem De Marcação Twilio (TwiML). Substitua os seus valores pelos números de telefone **e** **de telefone** e certifique-se de que verifica o número de telefone **da** sua conta Twilio antes de executar o código.

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

Para obter mais informações sobre os parâmetros transmitidos ao método **Call.creator,** consulte [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Como mencionado, este código utiliza um site fornecido por Twilio para devolver a resposta TwiML. Em vez disso, poderia utilizar o seu próprio site para fornecer a resposta TwiML; para mais informações, consulte [Como Fornecer Respostas TwiML numa Aplicação Java no Azure](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS
O seguinte mostra como enviar uma mensagem SMS utilizando a classe **Mensagem.** O número **de** **4155992671**, é fornecido pela Twilio para contas de teste para enviar mensagens SMS. O número **a** número deve ser verificado para a sua conta Twilio antes de executar o código.

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

Para obter mais informações sobre os parâmetros transmitidos ao método **Message.creator,** consulte [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas TwiML a partir do seu próprio Website
Quando a sua aplicação iniciar uma chamada para a API Twilio, por exemplo através do método **CallCreator.create,** twilio enviará o seu pedido para um URL que se espera que devolva uma resposta TwiML. O exemplo acima utiliza o URL [https://twimlets.com/message][twimlet_message_url]fornecido por Twilio . (Enquanto o TwiML é projetado para ser utilizado por serviços Web, pode ver o TwiML no seu navegador. Por exemplo, [https://twimlets.com/message][twimlet_message_url] clique para ver um elemento ** &lt;resposta&gt; ** vazio; como outro exemplo, clique [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] para ver um ** &lt;&gt; ** elemento resposta que contenha um ** &lt;&gt; ** elemento Say.)

Em vez de confiar no URL fornecido pelo Twilio, pode criar o seu próprio site de URL que devolve respostas http. Pode criar o site em qualquer idioma que derreta respostas HTTP; este tópico pressupõe que você vai hospedar o URL numa página JSP.

A página JSP seguinte resulta numa resposta TwiML que diz **Hello World!** na chamada.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

A página JSP seguinte resulta numa resposta TwiML que diz que algum texto, tem várias pausas, e diz informações sobre a versão Twilio API e o nome de papel Azure.

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

O parâmetro **ApiVersion** está disponível em pedidos de voz Twilio (não pedidos de SMS). Para ver os parâmetros de pedido disponíveis para pedidos de <https://www.twilio.com/docs/api/twiml/twilio_request> voz <https://www.twilio.com/docs/api/twiml/sms/twilio_request>Twilio e SMS, consulte e, respectivamente, os parâmetros de pedido disponíveis. A variável ambiente **RoleName** está disponível como parte de uma implantação azure. (Se quiser adicionar variáveis ambientais personalizadas para que possam ser recolhidas no **System.getenv,** consulte a secção de variáveis ambientais nas Definições de Configuração de [Funções Diversas][misc_role_config_settings].)

Assim que tiver a sua página JSP configurada para fornecer respostas TwiML, utilize o URL da página JSP à medida que o URL passou para o método **Call.creator.** Por exemplo, se tiver uma aplicação Web chamada MyTwiML implantada num serviço azure hospedado, e o nome da página JSP é mytwiml.jsp, o URL pode ser passado para **Call.creator** como mostrado no seguinte:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Outra opção para responder com o TwiML é através da classe **VoiceResponse,** que está disponível no pacote **com.twilio.twiml.**

Para obter informações adicionais sobre a utilização de Twilio em Azure com Java, consulte [Como fazer uma chamada telefónica usando Twilio numa aplicação Java no Azure][howto_phonecall_java].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Como: Utilizar Serviços Twilio Adicionais
Além dos exemplos aqui apresentados, o Twilio oferece APIs baseados na Web que pode utilizar para alavancar funcionalidades Twilio adicionais a partir da sua aplicação Azure. Para mais detalhes, consulte a documentação da [API twilio.][twilio_api_documentation]

## <a name="next-steps"></a><a id="NextSteps"></a>Próximos Passos
Agora que aprendeu o básico do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de Segurança twilio][twilio_security_guidelines]
* [Twilio HowTo's e Código exemplo][twilio_howtos]
* [Tutoriais Twilio Quickstart][twilio_quickstarts]
* [Twilio no GitHub][twilio_on_github]
* [Fale com o Apoio twilio][twilio_support]

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
