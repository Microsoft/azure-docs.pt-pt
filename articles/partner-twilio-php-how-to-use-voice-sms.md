---
title: Como usar o Twilio para voz e SMS (PHP) [ Microsoft Docs
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço Twilio API no Azure. Amostras de código escritas em PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 34057f1962338927a252011dccc56ed6a77bec47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69636026"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Como usar twilio para capacidades de voz e SMS em PHP
Este guia demonstra como executar tarefas comuns de programação com o serviço Twilio API no Azure. Os cenários abordados incluem fazer uma chamada telefónica e enviar uma mensagem de Serviço de Mensagens Curtas (SMS). Para obter mais informações sobre twilio e usando voz e SMS nas suas aplicações, consulte a secção [Next Steps.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>O que é Twilio?
A Twilio está a potenciar o futuro das comunicações empresariais, permitindo aos desenvolvedores incorporarvoz, VoIP e mensagens em aplicações. Virtualizam todas as infraestruturas necessárias num ambiente global baseado em nuvem, expondo-a através da plataforma API de comunicações Twilio. As aplicações são simples de construir e escaláveis. Desfrute da flexibilidade com os preços pay-as-you go, e beneficie da fiabilidade da nuvem.

**A Twilio Voice** permite que as suas aplicações façam e recebam chamadas telefónicas. **O SMS Twilio** permite que a sua aplicação envie e receba mensagens de texto. **O Cliente Twilio** permite-lhe fazer chamadas VoIP de qualquer telefone, tablet ou navegador e suporta webRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Preços twilio e ofertas especiais
Os clientes azure recebem uma [oferta especial:](https://www.twilio.com/azure)$10 gratuitos de Crédito Twilio ao atualizar a sua Conta Twilio. Este Crédito Twilio pode ser aplicado a qualquer utilização twilio (crédito de $10 equivalente a envio de até 1.000 mensagens SMS ou receber até 1000 minutos de voz de entrada, dependendo da localização do seu número de telefone e mensagem ou destino de chamada). Resgatar este crédito Twilio e [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure)começar a: .

Twilio é um serviço de pagamento. Não existem taxas de configuração e pode fechar a sua conta a qualquer momento. Pode encontrar mais detalhes no [Preço twilio.][twilio_pricing]

## <a name="concepts"></a><a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece funcionalidade de voz e SMS para aplicações. As bibliotecas de clientes estão disponíveis em vários idiomas; para uma lista, consulte [bibliotecas Twilio API][twilio_libraries].

Os aspectos-chave da API Twilio são os verbos Twilio e a Linguagem Twilio Markup (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Verbos
A API utiliza os verbos Twilio; por exemplo, o ** &lt;verbo Say&gt; ** instrui Twilio a entregar uma mensagem audível numa chamada.

Segue-se uma lista de verbos Twilio. Conheça os outros verbos e capacidades através da [documentação da Linguagem Twilio Markup](https://www.twilio.com/docs/api/twiml).

* **Marcação&gt;: Liga o chamador a outro &lt;** telefone.
* Recolha : Recolhe os dígitos numéricos introduzidos no teclado do telefone. ** &lt;&gt;**
* **Hangup&gt;: Termina uma &lt;** chamada.
* **Reprodução&gt;: Reproduz um ficheiro &lt;** áudio.
* Pausa : Aguarda silenciosamente um número determinado de segundos. ** &lt;&gt;**
* **Gravar:&gt;Grava a voz do chamador e devolve um URL de um ficheiro que contém a &lt;** gravação.
* **Redirecionamento&gt;: Transfere o controlo de uma chamada ou SMS para o TwiML num URL diferente. &lt;**
* **Rejeitar:&gt;Rejeita uma chamada para o seu número Twilio sem &lt;** cobrar
* Diga : Converte texto em discurso que é feito numa chamada. ** &lt;&gt;**
* **SMS&gt;: Envia uma mensagem SMS. &lt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseadas em XML baseadas nos verbos Twilio que informam Twilio de como processar uma chamada ou SMS.

Como exemplo, o seguinte TwiML converteria o texto **Hello World** em discurso.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando a sua aplicação chama a API Twilio, um dos parâmetros DaPI é o URL que devolve a resposta TwiML. Para fins de desenvolvimento, pode utilizar URLs fornecidos pela Twilio para fornecer as respostas TwiML utilizadas pelas suas aplicações. Também pode alojar os seus próprios URLs para produzir as respostas TwiML, e outra opção é utilizar o objeto **TwiMLResponse.**

Para mais informações sobre os verbos Twilio, os seus atributos, e o TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [a Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Criar uma conta Twilio
Quando estiver pronto para obter uma conta Twilio, inscreva-se no [Try Twilio.][try_twilio] Pode começar com uma conta gratuita e fazer upgrade da sua conta mais tarde.

Quando se inscrever numa conta Twilio, receberá uma identificação da conta e um símbolo de autenticação. Ambos serão necessários para fazer chamadas da API twilio. Para evitar o acesso não autorizado à sua conta, mantenha o seu símbolo de autenticação seguro. O id da sua conta e o token de autenticação são visualizados na página da [conta Twilio,][twilio_account]nos campos rotulados **DE IS e** **Token AUTH,** respectivamente.

## <a name="create-a-php-application"></a><a id="create_app"></a>Criar uma aplicação PHP
Uma aplicação PHP que utiliza o serviço Twilio e está a funcionar em Azure não é diferente de qualquer outra aplicação PHP que utilize o serviço Twilio. Embora os serviços twilio sejam baseados em REST e possam ser chamados de PHP de várias maneiras, este artigo irá focar-se em como usar os serviços Twilio com [a biblioteca Twilio para PHP do GitHub.][twilio_php] Para mais informações sobre a utilização da [https://www.twilio.com/docs/libraries/php][twilio_lib_docs]biblioteca Twilio para PHP, consulte .

Instruções detalhadas para a construção e implementação de uma aplicação Twilio/PHP para o Azure estão disponíveis em [Como Fazer uma Chamada Telefónica Utilizando Twilio numa aplicação PHP no Azure][howto_phonecall_php].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Configure a sua aplicação para usar bibliotecas Twilio
Pode configurar a sua aplicação para utilizar a biblioteca Twilio para PHP de duas formas:

1. Descarregue a biblioteca Twilio para[https://github.com/twilio/twilio-php][twilio_php]PHP a partir de GitHub e adicione o diretório de **Serviços** à sua aplicação.
   
    -OU-
2. Instale a biblioteca Twilio para PHP como um pacote PEAR. Pode ser instalado com os seguintes comandos:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Depois de ter instalado a biblioteca Twilio para PHP, pode adicionar uma declaração **require_once** no topo dos seus ficheiros PHP para fazer referência à biblioteca:

        require_once 'Services/Twilio.php';

Para mais informações, consulte [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída usando a classe **Services_Twilio.** Este código também utiliza um site fornecido por Twilio para devolver a resposta da Linguagem De Marcação Twilio (TwiML). Substitua os seus valores pelos números de telefone **De** e **Para** e certifique-se de que verifica o número de telefone **do From** para a sua conta Twilio antes de executar o código.

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

Como mencionado, este código utiliza um site fornecido por Twilio para devolver a resposta TwiML. Em vez disso, poderia utilizar o seu próprio site para fornecer a resposta TwiML; para mais informações, consulte [Como Fornecer Respostas TwiML a partir do seu próprio Web Site](#howto_provide_twiml_responses).

* **Nota:** Para resolver problemas de erro de validação de certificados SSL, consulte[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS
O seguinte mostra como enviar uma mensagem SMS utilizando a classe **Services_Twilio.** O número **From** é fornecido pela Twilio para contas de teste para enviar mensagens SMS. O número **To** deve ser verificado para a sua conta Twilio antes de executar o código.

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas TwiML a partir do seu próprio Website
Quando a sua aplicação iniciar uma chamada para a API Twilio, a Twilio enviará o seu pedido para um URL que deverá devolver uma resposta TwiML. O exemplo acima utiliza o URL [https://twimlets.com/message][twimlet_message_url]fornecido por Twilio . (Enquanto o TwiML foi projetado para ser utilizado pela Twilio, pode ver o mesmo no seu navegador. Por exemplo, [https://twimlets.com/message][twimlet_message_url] clique para `<Response>` ver um elemento vazio; como outro exemplo, [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] clique `<Response>` para ver `<Say>` um elemento que contenha um elemento.)

Em vez de confiar no URL fornecido pelo Twilio, pode criar o seu próprio site que devolve respostas http. Pode criar o site em qualquer idioma que destitude respostas XML; este tópico pressupõe que você estará usando PHP para criar o TwiML.

A página PHP seguinte resulta numa resposta TwiML que diz **Hello World** na chamada.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Como pode ver pelo exemplo acima, a resposta TwiML é simplesmente um documento XML. A biblioteca Twilio para PHP contém aulas que gerarão TwiML para si. O exemplo abaixo produz a resposta equivalente, como mostrado acima, mas utiliza a classe **Services\_Twilio\_Twiml** na biblioteca Twilio para PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Para mais informações sobre [https://www.twilio.com/docs/api/twiml][twiml_reference]o TwiML, consulte . 

Assim que tiver a sua página PHP configurada para fornecer respostas TwiML, utilize `Services_Twilio->account->calls->create` o URL da página PHP à medida que o URL passou para o método. Por exemplo, se tiver uma aplicação Web chamada **MyTwiML** implantada num serviço azure hospedado, e o nome da página PHP é **mytwiml.php,** o URL pode ser passado para **Services_Twilio->>chamadas->criar** como mostrado no exemplo seguinte:

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

Para obter informações adicionais sobre a utilização do Twilio em Azure com PHP, consulte [como fazer uma chamada telefónica utilizando o Twilio numa aplicação PHP no Azure][howto_phonecall_php].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Como: Utilizar Serviços Twilio Adicionais
Além dos exemplos aqui apresentados, o Twilio oferece APIs baseados na Web que pode utilizar para alavancar funcionalidades Twilio adicionais a partir da sua aplicação Azure. Para mais detalhes, consulte a documentação da [API twilio.][twilio_api_documentation]

## <a name="next-steps"></a><a id="NextSteps"></a>Próximos Passos
Agora que aprendeu o básico do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de Segurança twilio][twilio_security_guidelines]
* [Twilio HowTo's e Código exemplo][twilio_howtos]
* [Tutoriais Twilio Quickstart][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Fale com o Apoio twilio][twilio_support]

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
