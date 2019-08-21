---
title: Como usar o twilio para voz e SMS (PHP) | Microsoft Docs
description: Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do twilio no Azure. Exemplos de código escritos em PHP.
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
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636026"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Como usar o twilio para recursos de voz e SMS no PHP
Este guia demonstra como executar tarefas comuns de programação com o serviço de API do twilio no Azure. Os cenários abordados incluem fazer uma chamada telefônica e enviar uma mensagem SMS (Short Message Service). Para obter mais informações sobre o twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [próximas etapas](#NextSteps) .

## <a id="WhatIs"></a>O que é o twilio?
O twilio está ligando o futuro das comunicações de negócios, permitindo que os desenvolvedores insiram voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infraestrutura necessária em um ambiente global baseado em nuvem, expondo-a por meio da plataforma de API de comunicações do twilio. Os aplicativos são simples de criar e escalonáveis. Aproveite a flexibilidade com os preços pagos conforme o uso e beneficie-se da confiabilidade da nuvem.

O **twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. O **SMS twilio** permite que seu aplicativo envie e receba mensagens de texto. O **cliente twilio** permite fazer chamadas VoIP de qualquer telefone, Tablet ou navegador e dá suporte a WebRTC.

## <a id="Pricing"></a>Preços e ofertas especiais do twilio
Os clientes do Azure recebem uma [oferta especial](https://www.twilio.com/azure): grátis $10 de crédito twilio ao atualizar sua conta do twilio. Esse crédito twilio pode ser aplicado a qualquer uso de twilio ($10 de crédito equivalente a envio de até 1.000 mensagens SMS ou recebimento de até 1000 minutos de voz de entrada, dependendo do local do seu número de telefone e da mensagem ou do destino da chamada). Resgate este crédito de twilio e comece em: [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure).

Twilio é um serviço pago conforme o uso. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [preços de twilio][twilio_pricing].

## <a id="Concepts"></a>Principais
A API twilio é uma API RESTful que fornece a funcionalidade de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas; para obter uma lista, consulte [bibliotecas de API do twilio][twilio_libraries].

Os principais aspectos da API twilio são twilio verbos e TwiML (twilio Markup Language).

### <a id="Verbs"></a>Verbos twilio
A API utiliza verbos twilio; por exemplo, o  **&lt;verbo&gt; digamos** instrui o twilio a forma audível a entregar uma mensagem em uma chamada.

Veja a seguir uma lista de verbos do twilio. Saiba mais sobre os outros verbos e funcionalidades por meio da [documentação da linguagem de marcação twilio](https://www.twilio.com/docs/api/twiml).

* Discar:  **&lt;&gt;** Conecta o chamador a outro telefone.
* Reunir:  **&lt;&gt;** Coleta dígitos numéricos inseridos no teclado do telefone.
* Desligando: **&gt; &lt;** Termina uma chamada.
* Reproduzir:  **&lt;&gt;** Reproduz um arquivo de áudio.
* Pausar:  **&lt;&gt;** Aguarda silenciosamente por um número especificado de segundos.
* Registro:  **&lt;&gt;** Registra a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* Redirecionar: **&gt; &lt;** Transfere o controle de uma chamada ou SMS para o TwiML em uma URL diferente.
* Rejeitar:  **&lt;&gt;** Rejeita uma chamada recebida para o número do twilio sem cobrança
* Digamos:  **&lt;&gt;** Converte o texto em fala que é feito em uma chamada.
* **&lt;Sms&gt;** : Envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseadas em XML com base nos verbos twilio que informam twilio de como processar uma chamada ou SMS.

Como exemplo, o TwiML a seguir converterá o texto **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API twilio, um dos parâmetros de API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidas pelo twilio para fornecer as respostas do TwiML usadas por seus aplicativos. Você também pode hospedar suas próprias URLs para produzir as respostas TwiML e outra opção é usar o objeto **TwiMLResponse** .

Para obter mais informações sobre verbos twilio, seus atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API do twilio, consulte [API do twilio][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do twilio
Quando estiver pronto para obter uma conta do twilio, Inscreva-se em [experimentar o twilio][try_twilio]. Você pode começar com uma conta gratuita e atualizar sua conta mais tarde.

Ao se inscrever em uma conta do twilio, você receberá uma ID de conta e um token de autenticação. Ambos serão necessários para fazer chamadas à API do twilio. Para impedir o acesso não autorizado à sua conta, mantenha seu token de autenticação seguro. A ID da conta e o token de autenticação são visíveis na [página da conta do twilio][twilio_account], nos campos ROTULAdos SID da **conta** e **token de autenticação**, respectivamente.

## <a id="create_app"></a>Criar um aplicativo PHP
Um aplicativo PHP que usa o serviço twilio e está em execução no Azure não é diferente de qualquer outro aplicativo PHP que usa o serviço twilio. Embora os serviços Twilios sejam baseados em REST e possam ser chamados do PHP de várias maneiras, este artigo se concentrará em como usar os serviços do twilio com a [biblioteca twilio para php do GitHub][twilio_php]. Para obter mais informações sobre como usar a biblioteca twilio para PHP [https://www.twilio.com/docs/libraries/php][twilio_lib_docs], consulte.

Instruções detalhadas para criar e implantar um aplicativo twilio/PHP no Azure estão disponíveis em [como fazer uma chamada telefônica usando o twilio em um aplicativo PHP no Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configurar seu aplicativo para usar bibliotecas twilio
Você pode configurar seu aplicativo para usar a biblioteca twilio para PHP de duas maneiras:

1. Baixe a biblioteca twilio para php do GitHub ([https://github.com/twilio/twilio-php][twilio_php]) e adicione o diretório de **Serviços** ao seu aplicativo.
   
    -OU-
2. Instale a biblioteca twilio para PHP como um pacote do Pera. Ele pode ser instalado com os seguintes comandos:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Depois de instalar a biblioteca twilio para PHP, você pode adicionar uma instrução **require_once** na parte superior dos arquivos PHP para fazer referência à biblioteca:

        require_once 'Services/Twilio.php';

Para mais informações, consulte [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada externa usando a classe **Services_Twilio** . Esse código também usa um site fornecido pelo twilio para retornar a resposta TwiML (twilio Markup Language). Substitua os valores dos números **de telefone de** e **para** e verifique se você verificou o número **de** telefone de sua conta do twilio antes de executar o código.

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

Conforme mencionado, esse código usa um site fornecido pelo twilio para retornar a resposta TwiML. Em vez disso, você poderia usar seu próprio site para fornecer a resposta TwiML; para obter mais informações, consulte [como fornecer respostas de TwiML de seu próprio site](#howto_provide_twiml_responses).

* **Nota**: Para solucionar erros de validação de certificado SSL, consulte[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS
O seguinte mostra como enviar uma mensagem SMS usando a classe **Services_Twilio** . O número **de** é fornecido pelo twilio para contas de avaliação para enviar mensagens SMS. O número **de para** deve ser verificado para sua conta do twilio antes da execução do código.

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

## <a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas de TwiML de seu próprio site
Quando seu aplicativo iniciar uma chamada para a API twilio, o twilio enviará sua solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo acima usa a URL [https://twimlets.com/message][twimlet_message_url]fornecida pelo twilio. (Embora o TwiML seja projetado para ser usado pelo twilio, você pode exibi-lo em seu navegador. Por exemplo, clique [https://twimlets.com/message][twimlet_message_url] para ver um elemento `<Response>` vazio; como outro exemplo, clique [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] para ver um `<Response>` elemento que contém um `<Say>` elemento.)

Em vez de depender da URL fornecida pelo twilio, você pode criar seu próprio site que retorna respostas HTTP. Você pode criar o site em qualquer idioma que retorne respostas XML; Este tópico pressupõe que você usará o PHP para criar o TwiML.

A página PHP a seguir resulta em uma resposta TwiML que diz **Olá, mundo** na chamada.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Como você pode ver no exemplo acima, a resposta TwiML é simplesmente um documento XML. A biblioteca twilio para PHP contém classes que irão gerar TwiML para você. O exemplo a seguir produz a resposta equivalente conforme mostrado acima, mas usa **a\_classe\_Services twilio Twiml** na biblioteca twilio para php:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Para obter mais informações sobre TwiML, [https://www.twilio.com/docs/api/twiml][twiml_reference]consulte. 

Depois que a página do PHP estiver configurada para fornecer respostas TwiML, use a URL da página PHP como a URL passada para `Services_Twilio->account->calls->create` o método. Por exemplo, se você tiver um aplicativo Web chamado **MyTwiML** implantado em um serviço hospedado do Azure, e o nome da página php for **MyTwiML. php**, a URL poderá ser passada para **Services_Twilio-> conta > chamadas-> criar** conforme mostrado no exemplo a seguir:

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

Para obter informações adicionais sobre como usar o twilio no Azure com PHP, consulte [como fazer uma chamada telefônica usando o twilio em um aplicativo PHP no Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Como: Usar serviços Twilios adicionais
Além dos exemplos mostrados aqui, o twilio oferece APIs baseadas na Web que você pode usar para aproveitar a funcionalidade adicional do twilio do seu aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API do twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Passos Seguintes
Agora que você aprendeu os conceitos básicos do serviço twilio, siga estes links para saber mais:

* [Diretrizes de segurança do twilio][twilio_security_guidelines]
* [Twilio HowTo e código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do twilio][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Fale com o suporte do twilio][twilio_support]

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
