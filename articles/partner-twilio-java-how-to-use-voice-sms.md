---
title: Como usar o twilio para voz e SMS (Java) | Microsoft Docs
description: Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do twilio no Azure. Exemplos de código escritos em Java.
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
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637216"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Como usar o twilio para recursos de voz e SMS em Java
Este guia demonstra como executar tarefas comuns de programação com o serviço de API do twilio no Azure. Os cenários abordados incluem fazer uma chamada telefônica e enviar uma mensagem SMS (Short Message Service). Para obter mais informações sobre o twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [próximas etapas](#NextSteps) .

## <a id="WhatIs"></a>O que é o twilio?
O twilio é uma API de serviço Web de telefonia que permite que você use suas habilidades e linguagens da Web existentes para criar aplicativos de voz e de SMS. O twilio é um serviço de terceiros (não um recurso do Azure e não um produto da Microsoft).

O **twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio o SMS** permite que seus aplicativos façam e recebam mensagens SMS. O **twilio Client** permite que seus aplicativos habilitem a comunicação por voz usando conexões de Internet existentes, incluindo conexões móveis.

## <a id="Pricing"></a>Preços e ofertas especiais do twilio
Informações sobre preços do twilio estão disponíveis em [preços do twilio][twilio_pricing]. Os clientes do Azure recebem uma [oferta especial][special_offer]: um crédito gratuito de 1000 textos ou 1000 minutos de entrada. Para se inscrever nesta oferta ou obter mais informações, visite [https://ahoy.twilio.com/azure][special_offer].

## <a id="Concepts"></a>Principais
A API twilio é uma API RESTful que fornece a funcionalidade de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas; para obter uma lista, consulte [bibliotecas de API do twilio][twilio_libraries].

Os principais aspectos da API twilio são twilio verbos e TwiML (twilio Markup Language).

### <a id="Verbs"></a>Verbos twilio
A API utiliza verbos twilio; por exemplo, o  **&lt;verbo&gt; digamos** instrui o twilio a forma audível a entregar uma mensagem em uma chamada.

Veja a seguir uma lista de verbos do twilio.

* Discar:  **&lt;&gt;** Conecta o chamador a outro telefone.
* Reunir:  **&lt;&gt;** Coleta dígitos numéricos inseridos no teclado do telefone.
* Desligando: **&gt; &lt;** Termina uma chamada.
* Reproduzir:  **&lt;&gt;** Reproduz um arquivo de áudio.
* Fila:  **&lt;&gt;** Adicione o a uma fila de chamadores.
* Pausar:  **&lt;&gt;** Aguarda silenciosamente por um número especificado de segundos.
* Registro:  **&lt;&gt;** Registra a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* Redirecionar: **&gt; &lt;** Transfere o controle de uma chamada ou SMS para o TwiML em uma URL diferente.
* Rejeitar:  **&lt;&gt;** Rejeita uma chamada recebida para seu número de twilio sem cobrar por você.
* Digamos:  **&lt;&gt;** Converte o texto em fala que é feito em uma chamada.
* **&lt;Sms&gt;** : Envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseadas em XML com base nos verbos twilio que informam twilio de como processar uma chamada ou SMS.

Por exemplo, o TwiML a seguir converterá o texto **Olá, mundo!** para fala.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Quando o aplicativo chama a API twilio, um dos parâmetros de API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidas pelo twilio para fornecer as respostas do TwiML usadas por seus aplicativos. Você também pode hospedar suas próprias URLs para produzir as respostas TwiML e outra opção é usar o objeto **TwiMLResponse** .

Para obter mais informações sobre verbos twilio, seus atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API do twilio, consulte [API do twilio][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do twilio
Quando estiver pronto para obter uma conta do twilio, Inscreva-se em [experimentar o twilio][try_twilio]. Você pode começar com uma conta gratuita e atualizar sua conta mais tarde.

Ao se inscrever em uma conta do twilio, você receberá uma ID de conta e um token de autenticação. Ambos serão necessários para fazer chamadas à API do twilio. Para impedir o acesso não autorizado à sua conta, mantenha seu token de autenticação seguro. A ID da conta e o token de autenticação são visíveis no [console do twilio][twilio_console], nos campos ROTULAdos **SID da conta** e token de **autenticação**, respectivamente.

## <a id="create_app"></a>Criar um aplicativo Java
1. Obtenha o JAR twilio e adicione-o ao seu caminho de compilação Java e ao seu assembly de implantação WAR. Em [https://github.com/twilio/twilio-java][twilio_java], você pode baixar as fontes do GitHub e criar seu próprio jar ou baixar um jar predefinido (com ou sem dependências).
2. Verifique se o repositório de chaves **cacerts** do JDK contém o certificado de autoridade de certificado seguro Equifax com impressão digital MD5 67: CB: 9d: C0:13:24:8A: 82:9b: B2:17:1e: D1:1b: EC: D4 (o número de série é 35: de: F4: CF e a impressão digital SHA1 é D2:32:09: AD :D 3:14:23:21:74: E4:0D: 7F: 9D: 62:13:97:86:63:3A). Esse é o certificado de autoridade de certificação (CA) [https://api.twilio.com][twilio_api_service] para o serviço, que é chamado quando você usa APIs twilio. Para obter informações sobre como garantir que o repositório de chaves **cacerts** do JDK contenha o certificado de autoridade de certificação correto, consulte [adicionando um certificado ao repositório de certificados da autoridade de certificação Java][add_ca_cert].

Instruções detalhadas para usar a biblioteca de cliente do twilio para Java estão disponíveis em [como fazer uma chamada telefônica usando o twilio em um aplicativo Java no Azure][howto_phonecall_java].

## <a id="configure_app"></a>Configurar seu aplicativo para usar bibliotecas twilio
Dentro de seu código, você pode adicionar instruções de **importação** na parte superior dos seus arquivos de origem para os pacotes twilio ou classes que você deseja usar em seu aplicativo.

Para arquivos de origem Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Para arquivos de origem da página de servidor Java (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Dependendo de quais pacotes ou classes twilio você deseja usar, as instruções de **importação** podem ser diferentes.

## <a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída usando a classe **Call** . Esse código também usa um site fornecido pelo twilio para retornar a resposta TwiML (twilio Markup Language). Substitua os valores dos números **de telefone de** e **para** e verifique se você verificou o número **de** telefone de sua conta do twilio antes de executar o código.

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

Para obter mais informações sobre os parâmetros passados para o método **Call. Creator** , consulte [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Conforme mencionado, esse código usa um site fornecido pelo twilio para retornar a resposta TwiML. Em vez disso, você poderia usar seu próprio site para fornecer a resposta TwiML; para obter mais informações, consulte [como fornecer respostas de TwiML em um aplicativo Java no Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS
O seguinte mostra como enviar uma mensagem SMS usando a classe **Message** . O número **de** , **4155992671**, é fornecido pelo twilio para contas de avaliação para enviar mensagens SMS. O número **de para** deve ser verificado para sua conta do twilio antes da execução do código.

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

Para obter mais informações sobre os parâmetros passados para o método **Message. Creator** , consulte [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas de TwiML de seu próprio site
Quando seu aplicativo iniciar uma chamada para a API twilio, por exemplo, por meio do método **CallCreator. Create** , twilio enviará sua solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo acima usa a URL [https://twimlets.com/message][twimlet_message_url]fornecida pelo twilio. (Embora o TwiML seja projetado para ser usado pelos serviços Web, você pode exibir o TwiML em seu navegador. Por exemplo, clique [https://twimlets.com/message][twimlet_message_url] para ver um elemento  **&lt; de&gt; resposta** vazio; como outro exemplo, [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] clique para ver **&lt; um&gt;** elemento de resposta que contém um  **&lt; Elemento&gt; digamos** .)

Em vez de depender da URL fornecida pelo twilio, você pode criar seu próprio site de URL que retorna respostas HTTP. Você pode criar o site em qualquer idioma que retorne respostas HTTP; Este tópico pressupõe que você hospedará a URL em uma página JSP.

A página JSP a seguir resulta em uma resposta TwiML que diz **Olá, mundo!** na chamada.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

A página JSP a seguir resulta em uma resposta TwiML que diz algum texto, tem várias pausas e informa informações sobre a versão da API do twilio e o nome da função do Azure.

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

O parâmetro **ApiVersion** está disponível em solicitações de voz twilio (não em solicitações de SMS). Para ver os parâmetros de solicitação disponíveis para solicitações de voz e SMS do <https://www.twilio.com/docs/api/twiml/twilio_request> twilio <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, consulte e, respectivamente. A variável de ambiente **roleName** está disponível como parte de uma implantação do Azure. (Se você quiser adicionar variáveis de ambiente personalizadas para que elas possam ser selecionadas em **System. getenv**, consulte a seção de variáveis de ambiente em [diversos parâmetros de configuração de função][misc_role_config_settings].)

Depois que sua página JSP estiver configurada para fornecer respostas TwiML, use a URL da página JSP como a URL passada para o método **Call. Creator** . Por exemplo, se você tiver um aplicativo Web chamado MyTwiML implantado em um serviço hospedado do Azure e o nome da página JSP for MyTwiML. jsp, a URL poderá ser passada para **Call. Creator** , conforme mostrado a seguir:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Outra opção para responder com TwiML é por meio da classe **VoiceResponse** , que está disponível no pacote **com. twilio. TwiML** .

Para obter informações adicionais sobre como usar o twilio no Azure com Java, consulte [como fazer uma chamada telefônica usando o twilio em um aplicativo Java no Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Como: Usar serviços Twilios adicionais
Além dos exemplos mostrados aqui, o twilio oferece APIs baseadas na Web que você pode usar para aproveitar a funcionalidade adicional do twilio do seu aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API do twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Passos Seguintes
Agora que você aprendeu os conceitos básicos do serviço twilio, siga estes links para saber mais:

* [Diretrizes de segurança do twilio][twilio_security_guidelines]
* [Twilio HowTo e código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do twilio][twilio_quickstarts]
* [Twilio no GitHub][twilio_on_github]
* [Fale com o suporte do twilio][twilio_support]

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
