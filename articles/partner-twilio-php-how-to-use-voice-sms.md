---
title: Como usar twilio para voz e SMS (PHP) Microsoft Docs
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço API Twilio em Azure. Amostras de código escritas em PHP.
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
ms.openlocfilehash: 2ce0e34032d8f0d07af3a7dcd3c47558814be7bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826813"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Como usar twilio para capacidades de voz e SMS em PHP
Este guia demonstra como executar tarefas de programação comuns com o serviço Twilio API em Azure. Os cenários abrangidos incluem fazer uma chamada telefónica e enviar uma mensagem do Serviço de Mensagens Curtas (SMS). Para obter mais informações sobre Twilio e utilizando voz e SMS nas suas aplicações, consulte a secção [Etapas Seguintes.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>O que é Twilio?
A Twilio está a alimentar o futuro das comunicações empresariais, permitindo aos desenvolvedores incorporarem voz, VoIP e mensagens em aplicações. Virtualizam todas as infraestruturas necessárias num ambiente global baseado na nuvem, expondo-a através da plataforma API de comunicações Twilio. As aplicações são simples de construir e escaláveis. Desfrute da flexibilidade com o preço do pay-as-go e beneficie da fiabilidade da nuvem.

**O Twilio Voice** permite que as suas aplicações façam e recebam chamadas telefónicas. **O Twilio SMS** permite que a sua aplicação envie e receba mensagens de texto. **O Cliente Twilio** permite-lhe fazer chamadas VoIP a partir de qualquer telefone, tablet ou navegador e suporta o WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Preços twilio e ofertas especiais
Os clientes Azure recebem uma [oferta especial:](https://www.twilio.com/azure)$10 de Crédito Twilio quando atualiza a sua Conta Twilio. Este Crédito Twilio pode ser aplicado a qualquer utilização de Twilio (crédito de $10 equivalente ao envio de até 1.000 mensagens SMS ou receber até 1000 minutos de voz de entrada, dependendo da localização do seu número de telefone e mensagem ou destino de chamada). Resgatar este crédito Twilio e começar em: [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure) .

Twilio é um serviço de pagamento. Não existem taxas de configuração e pode fechar a sua conta a qualquer momento. Pode encontrar mais detalhes na [Twilio Pricing.][twilio_pricing]

## <a name="concepts"></a><a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece funcionalidade de voz e SMS para aplicações. As bibliotecas de clientes estão disponíveis em vários idiomas; para uma lista, consulte as [Bibliotecas Twilio API][twilio_libraries].

Os principais aspetos da API de Twilio são os verbos Twilio e a Língua de Marcação Twilio (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Verbos Twilio
A API faz uso de verbos twilio; por exemplo, o verbo ** &lt; Say &gt; ** instrui Twilio a entregar uma mensagem audivelmente numa chamada.

Segue-se uma lista de verbos de Twilio. Saiba mais sobre os outros verbos e capacidades através da [documentação da linguagem Twilio Markup](https://www.twilio.com/docs/api/twiml).

* Marcação : Liga o chamador a outro telefone. ** &lt; &gt; **
* ** &lt; Recolha: &gt; **Coleciona dígitos numéricos introduzidos no teclado do telefone.
* ** &lt; Hangup &gt; **: Termina uma chamada.
* ** &lt; Reproduzir: &gt; **Reproduz um ficheiro áudio.
* ** &lt; Pausa &gt; **: Aguarde silenciosamente um número especificado de segundos.
* ** &lt; Record: &gt; **Grava a voz do autor da chamada e devolve um URL de um ficheiro que contém a gravação.
* ** &lt; Redirecionamento &gt; **: Transfere o controlo de uma chamada ou SMS para o TwiML num URL diferente.
* ** &lt; Rejeitar: &gt; **Rejeita uma chamada recebida para o seu número Twilio sem o cobrar
* ** &lt; Diga: &gt; **Converte texto para discurso que é feito numa chamada.
* ** &lt; Sms &gt; **: Envia uma mensagem SMS.

### <a name="twiml"></a><a id="TwiML"></a>TWIML
TwiML é um conjunto de instruções baseadas em XML com base nos verbos Twilio que informam Twilio de como processar uma chamada ou SMS.

Como exemplo, o seguinte TwiML converteria o texto **Hello World** em discurso.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Quando a sua aplicação chama a API Twilio, um dos parâmetros da API é o URL que devolve a resposta TwiML. Para fins de desenvolvimento, pode utilizar URLs fornecidos pela Twilio para fornecer as respostas TwiML utilizadas pelas suas aplicações. Também pode hospedar os seus próprios URLs para produzir as respostas TwiML, e outra opção é usar o objeto **TwiMLResponse.**

Para obter mais informações sobre verbos Twilio, seus atributos, e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [a API Twilio.][twilio_api]

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Criar uma Conta Twilio
Quando estiver pronto para obter uma conta Twilio, inscreva-se na [Try Twilio.][try_twilio] Pode começar com uma conta gratuita e atualizar a sua conta mais tarde.

Quando se inscreve numa conta Twilio, receberá uma identificação de conta e um token de autenticação. Ambos serão necessários para fazer chamadas da Twilio API. Para evitar o acesso não autorizado à sua conta, mantenha o seu símbolo de autenticação seguro. O seu ID de conta e token de autenticação são visualizados na página da [conta Twilio,][twilio_account]nos campos **marcados COMO CONTA SID** e **AUTH TOKEN,** respectivamente.

## <a name="create-a-php-application"></a><a id="create_app"></a>Criar uma aplicação PHP
Uma aplicação PHP que utiliza o serviço Twilio e está em execução em Azure não é diferente de qualquer outra aplicação PHP que utiliza o serviço Twilio. Embora os serviços Twilio sejam baseados em REST e possam ser chamados de PHP de várias maneiras, este artigo irá focar-se em como usar os serviços Twilio com [a biblioteca Twilio para PHP do GitHub.][twilio_php] Para obter mais informações sobre a utilização da biblioteca Twilio para PHP, consulte [https://www.twilio.com/docs/libraries/php][twilio_lib_docs] .

Instruções detalhadas para a construção e implantação de uma aplicação Twilio/PHP para a Azure estão disponíveis em [Como Fazer uma Chamada Telefónica Usando twilio numa aplicação PHP em Azure][howto_phonecall_php].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Configure a sua aplicação para utilizar bibliotecas Twilio
Pode configurar a sua aplicação para utilizar a biblioteca Twilio para PHP de duas formas:

1. Faça o download da biblioteca Twilio para PHP do GitHub e [https://github.com/twilio/twilio-php][twilio_php] adicione o diretório **de Serviços** à sua aplicação.
   
    -OU-
2. Instale a biblioteca Twilio para PHP como um pacote PEAR. Pode ser instalado com os seguintes comandos:

    ```bash
    $ pear channel-discover twilio.github.com/pear
    $ pear install twilio/Services_Twilio
    ```

Depois de ter instalado a biblioteca Twilio para PHP, pode adicionar uma declaração **require_once** no topo dos seus ficheiros PHP para fazer referência à biblioteca:

```php
require_once 'Services/Twilio.php';
```

Para obter mais informações, veja [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída usando a classe **Services_Twilio.** Este código também utiliza um site fornecido por Twilio para devolver a resposta Twilio Markup Language (TwiML). Substitua os seus valores pelos números de telefone **de** e **para para telefone** e certifique-se de que verifica o número de telefone **da** sua conta Twilio antes de executar o código.

```php
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
```

Como mencionado, este código utiliza um site fornecido por Twilio para devolver a resposta TwiML. Em vez disso, pode utilizar o seu próprio site para fornecer a resposta TwiML; para obter mais informações, consulte [Como Fornecer Respostas TwiML a partir do seu próprio Web Site](#howto_provide_twiml_responses).

* **Nota**: Para resolver problemas erros de validação de certificados TLS/SSL, consulte [https://www.twilio.com/docs/api/errors][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS
O seguinte mostra como enviar uma mensagem SMS utilizando a classe **Services_Twilio.** O número **De** é fornecido pela Twilio para contas de teste para enviar mensagens SMS. O número **Para** deve ser verificado para a sua conta Twilio antes de executar o código.

```php
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
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas TwiML a partir do seu próprio website
Quando a sua aplicação iniciar uma chamada para a API Twilio, twilio enviará o seu pedido para um URL que deverá devolver uma resposta TwiML. O exemplo acima utiliza o URL fornecido por [https://twimlets.com/message][twimlet_message_url] Twilio. (Enquanto o TwiML foi concebido para ser utilizado pela Twilio, pode vê-lo no seu navegador. Por exemplo, clique [https://twimlets.com/message][twimlet_message_url] para ver um elemento `<Response>` vazio; como outro exemplo, clique [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] para ver um elemento que `<Response>` contenha um `<Say>` elemento.)

Em vez de confiar no URL fornecido por Twilio, pode criar o seu próprio site que devolve respostas HTTP. Pode criar o site em qualquer idioma que devolva respostas XML; este tópico pressupõe que vai usar PHP para criar o TwiML.

A página php seguinte resulta numa resposta TwiML que diz **Hello World** na chamada.

```xml
<?php    
    header("content-type: text/xml");    
    echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
?>
<Response>    
    <Say>Hello world.</Say>
</Response>
```

Como pode ver pelo exemplo acima, a resposta TwiML é simplesmente um documento XML. A biblioteca Twilio para PHP contém aulas que gerarão TwiML para si. O exemplo abaixo produz a resposta equivalente como mostrado acima, mas utiliza a classe **Serviços \_ Twilio \_ Twiml** na biblioteca Twilio para PHP:

```php
require_once('Services/Twilio.php');

$response = new Services_Twilio_Twiml();
$response->say("Hello world.");
print $response;
```

Para obter mais informações sobre o TwiML, consulte [https://www.twilio.com/docs/api/twiml][twiml_reference] . 

Assim que tiver a sua página PHP configurada para fornecer respostas TwiML, utilize o URL da página PHP à medida que o URL passou para o  `Services_Twilio->account->calls->create`  método. Por exemplo, se tiver uma aplicação Web chamada **MyTwiML** implantada num serviço hospedado em Azure, e o nome da página PHP for **mytwiml.php,** o URL pode ser transmitido para  **Services_Twilio->chamadas >>>criar**  como mostrado no seguinte exemplo:

```php
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
```

Para obter informações adicionais sobre a utilização de Twilio em Azure com PHP, consulte [Como Fazer uma chamada telefónica usando twilio numa aplicação PHP em Azure][howto_phonecall_php].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Como: Utilizar serviços Twilio adicionais
Além dos exemplos mostrados aqui, o Twilio oferece APIs baseados na Web que pode utilizar para alavancar a funcionalidade Twilio adicional a partir da sua aplicação Azure. Para mais detalhes, consulte a documentação da [API Twilio.][twilio_api_documentation]

## <a name="next-steps"></a><a id="NextSteps"></a>Próximos Passos
Agora que aprendeu o básico do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de Segurança Twilio][twilio_security_guidelines]
* [Twilio HowTo's e Código de Exemplo][twilio_howtos]
* [Twilio Quickstart Tutorials][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Fale com o Apoio Twilio][twilio_support]

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
