---
title: Como usar o twilio para voz e SMS (Python) | Microsoft Docs
description: Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do twilio no Azure. Exemplos de código escritos em Python.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.openlocfilehash: edbc9eef6b5f0af2e70152b66228cdf09ef31110
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242192"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Como usar o twilio para recursos de voz e SMS no Python
Este guia demonstra como executar tarefas comuns de programação com o serviço de API do twilio no Azure. Os cenários abordados incluem fazer uma chamada telefônica e enviar uma mensagem SMS (Short Message Service). Para obter mais informações sobre o twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [próximas etapas](#NextSteps) .

## <a id="WhatIs"></a>O que é o twilio?
O twilio está ligando o futuro das comunicações de negócios, permitindo que os desenvolvedores insiram voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infraestrutura necessária em um ambiente global baseado em nuvem, expondo-a por meio da plataforma de API de comunicações do twilio. Os aplicativos são simples de criar e escalonáveis. Aproveite a flexibilidade com os preços pagos conforme o uso e beneficie-se da confiabilidade da nuvem.

O **twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas.
O **SMS twilio** permite que seu aplicativo envie e receba mensagens de texto.
O **cliente twilio** permite fazer chamadas VoIP de qualquer telefone, Tablet ou navegador e dá suporte a WebRTC.

## <a id="Pricing"></a>Preços e ofertas especiais do twilio
Os clientes do Azure recebem uma [oferta especial][special_offer] $10 de crédito twilio quando você atualiza sua conta do twilio. Esse crédito twilio pode ser aplicado a qualquer uso de twilio ($10 de crédito equivalente a envio de até 1.000 mensagens SMS ou recebimento de até 1000 minutos de voz de entrada, dependendo do local do seu número de telefone e da mensagem ou do destino da chamada). Resgate este [crédito de twilio][special_offer] e comece.

Twilio é um serviço pago conforme o uso. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [preços de twilio][twilio_pricing].

## <a id="Concepts"></a>Principais
A API twilio é uma API RESTful que fornece a funcionalidade de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas; para obter uma lista, consulte [bibliotecas de API do twilio][twilio_libraries].

Os principais aspectos da API twilio são twilio verbos e TwiML (twilio Markup Language).

### <a id="Verbs"></a>Verbos twilio
A API utiliza verbos twilio; por exemplo, o **&lt;dizer&gt;** Verb instrui o twilio a forma audível a entregar uma mensagem em uma chamada.

Veja a seguir uma lista de verbos do twilio. Saiba mais sobre os outros verbos e funcionalidades por meio da [documentação da linguagem de marcação twilio][twiml].

* **&lt;&gt;de discagem** : conecta o chamador a outro telefone.
* **&lt;coletar&gt;** : coleta dígitos numéricos inseridos no teclado do telefone.
* **&lt;desligar&gt;** : encerra uma chamada.
* **&lt;pausar&gt;** : aguarda silenciosamente por um número especificado de segundos.
* **&lt;Play&gt;** : reproduz um arquivo de áudio.
* **&gt;de&lt;da fila** : Adicione o a uma fila de chamadores.
* **registro de&lt;&gt;** : registra a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* **&lt;Redirecionar&gt;** : transfere o controle de uma chamada ou SMS para o TwiML em uma URL diferente.
* **&lt;rejeitar&gt;** : rejeita uma chamada recebida para o número do twilio sem cobrar você.
* **&lt;digamos&gt;** : converte o texto em fala que é feito em uma chamada.
* **&lt;&gt;SMS** : envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseadas em XML com base nos verbos twilio que informam twilio de como processar uma chamada ou SMS.

Como exemplo, o TwiML a seguir converterá o texto **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API twilio, um dos parâmetros de API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidas pelo twilio para fornecer as respostas do TwiML usadas por seus aplicativos. Você também pode hospedar suas próprias URLs para produzir as respostas TwiML e outra opção é usar o objeto `TwiMLResponse`.

Para obter mais informações sobre verbos twilio, seus atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API do twilio, consulte [API do twilio][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do twilio
Quando você estiver pronto para obter uma conta do twilio, Inscreva-se em [experimentar o twilio][try_twilio]. Você pode começar com uma conta gratuita e atualizar sua conta mais tarde.

Ao se inscrever em uma conta do twilio, você receberá um SID de conta e um token de autenticação. Ambos serão necessários para fazer chamadas à API do twilio. Para impedir o acesso não autorizado à sua conta, mantenha seu token de autenticação seguro. O SID de sua conta e o token de autenticação são visíveis no [console do twilio][twilio_console], nos campos rotulados **conta SID** e **token de autenticação**, respectivamente.

## <a id="create_app"></a>Criar um aplicativo Python
Um aplicativo Python que usa o serviço twilio e está em execução no Azure não é diferente de qualquer outro aplicativo Python que usa o serviço twilio. Embora os serviços Twilios sejam baseados em REST e possam ser chamados do Python de várias maneiras, este artigo se concentrará em como usar os serviços do twilio com a [biblioteca twilio para Python do GitHub][twilio_python]. Para obter mais informações sobre como usar a biblioteca twilio para Python, consulte [https://www.twilio.com/docs/libraries/python][twilio_lib_docs].

Primeiro, [configure uma nova VM Linux do Azure] [azure_vm_setup] para atuar como um host para seu novo aplicativo Web do Python. Depois que a máquina virtual estiver em execução, você precisará expor seu aplicativo em uma porta pública, conforme descrito abaixo.

### <a name="add-an-incoming-rule"></a>Adicionar uma regra de entrada
  1. Vá para a página [grupo de segurança de rede] [azure_nsg].
  2. Selecione o grupo de segurança de rede que corresponde à sua máquina virtual.
  3. Adicionar e **regra de saída** para a **porta 80**. Certifique-se de permitir a entrada de qualquer endereço.

### <a name="set-the-dns-name-label"></a>Definir o rótulo de nome DNS
  1. Vá para a página [a Endereços IP Públicos] [azure_ips].
  2. Selecione o IP público que corresponde à sua máquina virtual.
  3. Defina o **rótulo de nome DNS** na seção **configuração** . No caso deste exemplo, ele será algo parecido *com seu-Domain-Label*. centralus.cloudapp.Azure.com

Depois de se conectar por meio do SSH à máquina virtual, você pode instalar a estrutura da Web de sua escolha (as duas mais conhecidas no Python são [Flask](http://flask.pocoo.org/) e [Django](https://www.djangoproject.com)). Você pode instalar qualquer um deles apenas executando o comando `pip install`.

Tenha em mente que configuramos a máquina virtual para permitir o tráfego somente na porta 80. Portanto, certifique-se de configurar o aplicativo para usar essa porta.

## <a id="configure_app"></a>Configurar seu aplicativo para usar bibliotecas twilio
Você pode configurar seu aplicativo para usar a biblioteca twilio para Python de duas maneiras:

* Instale a biblioteca twilio para Python como um pacote Pip. Ele pode ser instalado com os seguintes comandos:
   
        $ pip install twilio

    -OU-

* Baixe a biblioteca twilio para Python do GitHub ([https://github.com/twilio/twilio-python][twilio_python]) e instale-a da seguinte maneira:

        $ python setup.py install

Depois de instalar a biblioteca twilio para Python, você poderá `import`-la em seus arquivos Python:

        import twilio

Para obter mais informações, consulte [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a id="howto_make_call"></a>Como: fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída. Esse código também usa um site fornecido pelo twilio para retornar a resposta TwiML (twilio Markup Language). Substitua os valores para os números de telefone **from_number** e **TO_NUMBER** e verifique se você verificou o número de telefone **From_number** para sua conta do twilio antes de executar o código.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "https://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Conforme mencionado, esse código usa um site fornecido pelo twilio para retornar a resposta TwiML. Em vez disso, você poderia usar seu próprio site para fornecer a resposta TwiML; para obter mais informações, consulte [como fornecer respostas de TwiML de seu próprio site](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS
O seguinte mostra como enviar uma mensagem SMS usando a classe `TwilioRestClient`. O número de **from_number** é fornecido pelo twilio para contas de avaliação para enviar mensagens SMS. O número de **TO_NUMBER** deve ser verificado para sua conta do twilio antes de executar o código.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>Como: fornecer respostas de TwiML de seu próprio site
Quando seu aplicativo iniciar uma chamada para a API twilio, o twilio enviará sua solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo acima usa a URL fornecida pelo twilio [https://twimlets.com/message][twimlet_message_url]. (Embora o TwiML seja projetado para ser usado pelo twilio, você pode exibi-lo em seu navegador. Por exemplo, clique em [https://twimlets.com/message][twimlet_message_url] para ver um elemento de `<Response>` vazio; como outro exemplo, clique em [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] para ver um elemento `<Response>` que contém um elemento `<Say>`.)

Em vez de depender da URL fornecida pelo twilio, você pode criar seu próprio site que retorna respostas HTTP. Você pode criar o site em qualquer idioma que retorne respostas XML; Este tópico pressupõe que você usará o Python para criar o TwiML.

Os exemplos a seguir produzirão uma resposta TwiML que diz **Olá, mundo** na chamada.

Com Flask:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Com Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Como você pode ver no exemplo acima, a resposta TwiML é simplesmente um documento XML. A biblioteca twilio para Python contém classes que irão gerar TwiML para você. O exemplo a seguir produz a resposta equivalente conforme mostrado acima, mas usa o módulo `twiml` na biblioteca twilio para Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Para obter mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml][twiml_reference].

Depois de configurar seu aplicativo Python para fornecer respostas TwiML, use a URL do aplicativo como a URL passada para o método `client.calls.create`. Por exemplo, se você tiver um aplicativo Web chamado **MyTwiML** implantado em um serviço hospedado do Azure, poderá usar sua URL como webhook, conforme mostrado no exemplo a seguir:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>Como: usar serviços Twilios adicionais
Além dos exemplos mostrados aqui, o twilio oferece APIs baseadas na Web que você pode usar para aproveitar a funcionalidade adicional do twilio do seu aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API do twilio][twilio_api].

## <a id="NextSteps"></a>Passos Seguintes
Agora que você aprendeu os conceitos básicos do serviço twilio, siga estes links para saber mais:

* [Diretrizes de segurança do twilio][twilio_security_guidelines]
* [Twilio HowTo guias e código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do twilio][twilio_quickstarts]
* [Twilio no GitHub][twilio_on_github]
* [Fale com o suporte do twilio][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
