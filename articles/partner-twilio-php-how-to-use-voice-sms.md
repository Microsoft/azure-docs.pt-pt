---
title: Como utilizar o Twilio para voz e SMS (PHP) | Documentos da Microsoft
description: Saiba como fazer uma chamada telefónica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de código escritos em PHP.
documentationcenter: php
services: ''
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: a51cd589702320ecb55e6a2e3c5f0a6139e281fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422360"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Como utilizar o Twilio para obter capacidades SMS no PHP e de voz
Este guia demonstra como realizar tarefas comuns de programação com o serviço de Twilio API no Azure. Os cenários abrangidos incluem uma ligação telefônica e enviar uma mensagem de serviço de mensagens curtas (SMS). Para obter mais informações sobre o Twilio e voz e SMS a utilizar nas suas aplicações, consulte a [passos seguintes](#NextSteps) secção.

## <a id="WhatIs"></a>O que é o Twilio?
Twilio é que alimenta o futuro das comunicações empresariais, permitindo que os desenvolvedores incorporar voz, VoIP e de mensagens em aplicativos. Eles virtualizar toda a infraestrutura necessária num ambiente global, com base na cloud, expô-lo através da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Desfrute da flexibilidade com pay as you go de preços e se beneficiar de fiabilidade de cloud.

**Serviços de voz Twilio** permite que seus aplicativos fazer e receber chamadas telefónicas. **Twilio SMS** permite que a sua aplicação enviar e receber mensagens de texto. **Cliente do Twilio** permite-lhe efetuar chamadas VoIP, de qualquer telefone, tablet ou navegador e oferece suporte WebRTC.

## <a id="Pricing"></a>Preços do Twilio e ofertas especiais
Os clientes do Azure recebem uma [oferta especial](https://www.twilio.com/azure): gratuitos US $10 do Twilio crédito quando atualiza a sua conta do Twilio. Este crédito Twilio pode ser aplicado a qualquer utilização do Twilio (crédito de US $10 equivalente ao enviar até 1000 mensagens SMS ou receber até 1000 entrados minutos de voz, consoante a localização do seu destino de número e a mensagem ou a chamada de telefone). Resgatar este crédito do Twilio e comece a utilizar: [ https://ahoy.twilio.com/azure ](https://ahoy.twilio.com/azure).

Twilio é um serviço pay as you go. Existem sem taxas de configuração e pode fechar a sua conta em qualquer altura. Pode encontrar mais detalhes [preços de Twilio][twilio_pricing].

## <a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful, que fornece funcionalidade SMS e de voz para aplicações. As bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [bibliotecas de APIS do Twilio][twilio_libraries].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação do Twilio (TwiML).

### <a id="Verbs"></a>Twilio Verbs
A API usa Twilio verbos; Por exemplo, o **&lt;Digamos&gt;** verbo instrui o Twilio para poder ouvi-lo entregar uma mensagem numa chamada.

Segue-se uma lista de verbos Twilio. Saiba mais sobre os outros verbos e capacidades através de [documentação da linguagem de marcação do Twilio](https://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: Liga-se o autor da chamada para outro telefone.
* **&lt;Gather&gt;**: Recolhe os dígitos numéricos introduzidos no teclado do telefone.
* **&lt;Hangup&gt;**: Termina uma chamada.
* **&lt;Reproduzir&gt;**: Reproduz um arquivo de áudio.
* **&lt;Pause&gt;**: Aguarda silenciosamente um número de segundos especificado.
* **&lt;Registo&gt;**: Regista a voz do chamador e retorna uma URL de um ficheiro que contém a gravação.
* **&lt;Redirect&gt;**: Transfere o controle de uma chamada ou SMS para o TwiML numa URL diferente.
* **&lt;Reject&gt;**: Rejeita uma chamada de entrada para o seu número do Twilio sem de faturação
* **&lt;Say&gt;**: Converte texto em voz, que é efetuado numa chamada.
* **&lt;Sms&gt;**: Envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções com base em XML, com base nos verbos de Twilio informam Twilio de como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML seria converter o texto **Olá, mundo** para voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando seu aplicativo chama a API Twilio, um dos parâmetros de API é o URL que devolve a resposta de TwiML. Para fins de desenvolvimento, pode utilizar URLs fornecidos pelo Twilio para fornecer as respostas de TwiML utilizadas pelas suas aplicações. Também pudesse hospedar suas próprias URLs para produzir as respostas de TwiML e outra opção consiste em utilizar o **TwiMLResponse** objeto.

Para obter mais informações sobre o Twilio verbos, os atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do Twilio
Quando estiver pronto para obter uma conta do Twilio, inscreva-se em [tente Twilio][try_twilio]. Pode começar com uma conta gratuita e atualize a sua conta mais tarde.

Quando se inscreve para uma conta do Twilio, receberá um ID de conta e um token de autenticação. Ambos serão necessários para fazer chamadas à API do Twilio. Para impedir acesso não autorizado à sua conta, manter o token de autenticação segura. Sua ID de conta e a autenticação de token são visíveis no [página de conta do Twilio][twilio_account], nos campos nome **SID da conta** e **TOKEN de autenticação de**, respectivamente.

## <a id="create_app"></a>Criar uma aplicação PHP
Um aplicativo PHP que utiliza o serviço do Twilio e está em execução no Azure não é diferente de qualquer outro aplicativo PHP que utiliza o serviço do Twilio. Embora Twilio serviços são baseados em REST e podem ser chamados do PHP de várias formas, este artigo focará sobre como utilizar os serviços com o Twilio [biblioteca de Twilio para PHP a partir do GitHub][twilio_php]. Para obter mais informações sobre como utilizar a biblioteca do Twilio para PHP, consulte [ https://www.twilio.com/docs/libraries/php ] [ twilio_lib_docs].

Estão disponíveis instruções detalhadas para criar e implementar uma aplicação Twilio/PHP no Azure em [como tornar uma Twilio através de chamada telefónica num aplicativo PHP no Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configurar a sua aplicação utilizar bibliotecas do Twilio
Pode configurar a sua aplicação para utilizar a biblioteca do Twilio para PHP de duas formas:

1. Transferir a biblioteca do Twilio para PHP a partir do GitHub ([https://github.com/twilio/twilio-php][twilio_php]) e adicione o **serviços** diretório à sua aplicação.
   
    -OU-
2. Instale a biblioteca do Twilio para PHP como um pacote PEAR. Pode ser instalado com os seguintes comandos:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Depois de instalar a biblioteca do Twilio para PHP, em seguida, pode adicionar um **require_once** instrução na parte superior dos seus ficheiros PHP para a biblioteca de referência:

        require_once 'Services/Twilio.php';

Para obter mais informações, consulte [ https://github.com/twilio/twilio-php/blob/master/README.md ] [ twilio_github_readme].

## <a id="howto_make_call"></a>Como: Efetuar uma chamada de saída
O seguinte mostra como tornar uma saída chamar utilizando o **Services_Twilio** classe. Esse código também utiliza um site fornecida pelo Twilio para retornar a resposta de linguagem de marcação do Twilio (TwiML). Substitua os valores para o **partir** e **para** números de telefone e certifique-se de que verifique o **de** número de telefone para sua conta do Twilio antes de executar o código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "https://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Conforme mencionado, esse código usa um site fornecida pelo Twilio para retornar a resposta de TwiML. Em vez disso, poderia usar seu próprio site para fornecer a resposta de TwiML; Para obter mais informações, consulte [como a fornecer respostas de TwiML no seu próprio Web Site](#howto_provide_twiml_responses).

* **Nota**: Para resolver erros de validação do certificado SSL, consulte [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Como: Envie uma mensagem SMS
O seguinte mostra como enviar uma mensagem SMS através do **Services_Twilio** classe. O **partir** número é fornecido pela Twilio para contas de avaliação para enviar mensagens SMS. O **para** número deve ser verificado para a sua conta do Twilio antes de executar o código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas TwiML do seu próprio Web site
Quando seu aplicativo inicia uma chamada à API do Twilio, o Twilio irá enviar o pedido para um URL que deverá devolver uma resposta de TwiML. O exemplo acima utiliza o URL fornecido pelo Twilio [ https://twimlets.com/message ] [ twimlet_message_url]. (Apesar de TwiML foi concebido para utilização por Twilio, pode ver o it no seu browser. Por exemplo, clique em [ https://twimlets.com/message ] [ twimlet_message_url] para ver vazio `<Response>` elemento; como outro exemplo, clique em [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ] [ twimlet_message_url_hello_world]para ver uma `<Response>` elemento que contém um `<Say>` elemento.)

Em vez de contar com o URL fornecido pelo Twilio, pode criar seu próprio site que retorna as respostas HTTP. Pode criar o site em qualquer linguagem que retorna as respostas XML; Este tópico pressupõe que irá utilizar PHP para criar o TwiML.

A seguinte página PHP resulta numa resposta de TwiML diz **Olá, mundo** na chamada.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Como pode ver no exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca do Twilio para PHP contém classes que irão gerar TwiML para. O exemplo a seguir produz a resposta equivalente, conforme mostrado acima, mas utiliza a **serviços\_Twilio\_Twiml** classe na biblioteca do Twilio para PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Para obter mais informações sobre TwiML, consulte [ https://www.twilio.com/docs/api/twiml ] [ twiml_reference]. 

Assim que tiver a sua página PHP configurada para fornecer respostas TwiML, utilize o URL da página PHP como o URL transmitido para o `Services_Twilio->account->calls->create` método. Por exemplo, se tiver uma aplicação Web com o nome **MyTwiML** implementada para o serviço alojado do Azure e o nome da página PHP é **mytwiml.php**, o URL pode ser passado para **Services_Twilio -> conta -> chamadas -> criar** conforme mostrado no exemplo a seguir:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Para obter mais informações sobre como utilizar o Twilio no Azure com PHP, consulte [como tornar uma Twilio através de chamada telefónica num aplicativo PHP no Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Como: Utilizar os serviços adicionais do Twilio
Para além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do Twilio através da aplicação do Azure. Para mais informações, consulte a [documentação da API do Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço Twilio, siga estas ligações para saber mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [Twilio tutoriais sobre como e o código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do Twilio][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Comunicar com o suporte do Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
