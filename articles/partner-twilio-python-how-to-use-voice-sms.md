---
title: Como usar o Twilio para voz e SMS (Python) [ Microsoft Docs
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço Twilio API no Azure. Amostras de código escritas em Python.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242192"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Como usar twilio para capacidades de voz e SMS em Python
Este guia demonstra como executar tarefas comuns de programação com o serviço Twilio API no Azure. Os cenários abordados incluem fazer uma chamada telefónica e enviar uma mensagem de Serviço de Mensagens Curtas (SMS). Para obter mais informações sobre twilio e usando voz e SMS nas suas aplicações, consulte a secção [Next Steps.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>O que é Twilio?
A Twilio está a potenciar o futuro das comunicações empresariais, permitindo aos desenvolvedores incorporarvoz, VoIP e mensagens em aplicações. Virtualizam todas as infraestruturas necessárias num ambiente global baseado em nuvem, expondo-a através da plataforma API de comunicações Twilio. As aplicações são simples de construir e escaláveis. Desfrute da flexibilidade com os preços pay-as-you go, e beneficie da fiabilidade da nuvem.

**A Twilio Voice** permite que as suas aplicações façam e recebam chamadas telefónicas.
**O SMS Twilio** permite que a sua aplicação envie e receba mensagens de texto.
**O Cliente Twilio** permite-lhe fazer chamadas VoIP de qualquer telefone, tablet ou navegador e suporta webRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Preços twilio e ofertas especiais
Os clientes azure recebem uma [oferta especial][special_offer] de $10 do Crédito Twilio quando atualizar a sua Conta Twilio. Este Crédito Twilio pode ser aplicado a qualquer utilização twilio (crédito de $10 equivalente a envio de até 1.000 mensagens SMS ou receber até 1000 minutos de voz de entrada, dependendo da localização do seu número de telefone e mensagem ou destino de chamada). Resgatar este [crédito Twilio][special_offer] e começar.

Twilio é um serviço de pagamento. Não existem taxas de configuração e pode fechar a sua conta a qualquer momento. Pode encontrar mais detalhes no [Preço twilio.][twilio_pricing]

## <a name="concepts"></a><a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece funcionalidade de voz e SMS para aplicações. As bibliotecas de clientes estão disponíveis em vários idiomas; para uma lista, consulte [bibliotecas Twilio API][twilio_libraries].

Os aspectos-chave da API Twilio são os verbos Twilio e a Linguagem Twilio Markup (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Verbos
A API utiliza os verbos Twilio; por exemplo, o ** &lt;verbo Say&gt; ** instrui Twilio a entregar uma mensagem audível numa chamada.

Segue-se uma lista de verbos Twilio. Conheça os outros verbos e capacidades através da [documentação da Linguagem Twilio Markup][twiml].

* **Marcação&gt;: Liga o chamador a outro &lt;** telefone.
* Recolha : Recolhe os dígitos numéricos introduzidos no teclado do telefone. ** &lt;&gt;**
* **Hangup&gt;: Termina uma &lt;** chamada.
* Pausa : Aguarda silenciosamente um número determinado de segundos. ** &lt;&gt;**
* **Reprodução&gt;: Reproduz um ficheiro &lt;** áudio.
* Fila : Adicione o a uma fila de chamadas. ** &lt;&gt;**
* **Gravar:&gt;Grava a voz do chamador e devolve um URL de um ficheiro que contém a &lt;** gravação.
* **Redirecionamento&gt;: Transfere o controlo de uma chamada ou SMS para o TwiML num URL diferente. &lt;**
* Rejeitar : Rejeita uma chamada para o seu número Twilio sem cobrar. ** &lt;&gt;**
* Diga : Converte texto em discurso que é feito numa chamada. ** &lt;&gt;**
* **SMS&gt;: Envia uma mensagem SMS. &lt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseadas em XML baseadas nos verbos Twilio que informam Twilio de como processar uma chamada ou SMS.

Como exemplo, o seguinte TwiML converteria o texto **Hello World** em discurso.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Quando a sua aplicação chama a API Twilio, um dos parâmetros DaPI é o URL que devolve a resposta TwiML. Para fins de desenvolvimento, pode utilizar URLs fornecidos pela Twilio para fornecer as respostas TwiML utilizadas pelas suas aplicações. Também pode hospedar os seus próprios URLs para produzir as respostas TwiML, e outra opção é usar o `TwiMLResponse` objeto.

Para mais informações sobre os verbos Twilio, os seus atributos, e o TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [a Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Criar uma conta Twilio
Quando estiver pronto para obter uma conta Twilio, inscreva-se no [Try Twilio][try_twilio]. Pode começar com uma conta gratuita e fazer upgrade da sua conta mais tarde.

Quando se inscreve numa conta Twilio, recebe uma conta SID e um símbolo de autenticação. Ambos serão necessários para fazer chamadas da API twilio. Para evitar o acesso não autorizado à sua conta, mantenha o seu símbolo de autenticação seguro. O SID da sua conta e o token de autenticação são visualizados na [Consola Twilio,][twilio_console]nos campos rotulados **DE IS e** **TOKEN AUTH,** respectivamente.

## <a name="create-a-python-application"></a><a id="create_app"></a>Criar uma aplicação Python
Uma aplicação Python que utiliza o serviço Twilio e está a funcionar em Azure não é diferente de qualquer outra aplicação Python que utilize o serviço Twilio. Embora os serviços twilio sejam baseados em REST e possam ser chamados de Python de várias maneiras, este artigo irá focar-se em como usar os serviços twilio com [a biblioteca Twilio para Python do GitHub.][twilio_python] Para mais informações sobre a utilização da [https://www.twilio.com/docs/libraries/python][twilio_lib_docs]biblioteca Twilio para Python, consulte .

Primeiro, [cria ção de um novo Azure Linux VM][azure_vm_setup] para atuar como anfitrião da sua nova aplicação web Python. Assim que a Máquina Virtual estiver a funcionar, terá de expor a sua aplicação numa porta pública, conforme descrito abaixo.

### <a name="add-an-incoming-rule"></a>Adicione uma regra de entrada
  1. Vá à página [Network Security Group][azure_nsg].
  2. Selecione o Grupo de Segurança da Rede que corresponde à sua Máquina Virtual.
  3. Regra de adição e **saída** para **a porta 80**. Certifique-se de permitir a entrada de qualquer endereço.

### <a name="set-the-dns-name-label"></a>Definir a etiqueta de nome DNS
  1. Vá à página [The Public IP Addresss][azure_ips].
  2. Selecione o IP público que corresponde à sua Máquina Virtual.
  3. Coloque a etiqueta de **nome DNS** na secção **configuração.** No caso deste exemplo, será parecido com este *rótulo de domínio*.centralus.cloudapp.azure.com

Uma vez que você é capaz de ligar através de SSH à Máquina Virtual, você pode instalar o Quadro Web da sua escolha (os dois mais conhecidos em Python sendo [Flask](http://flask.pocoo.org/) e [Django).](https://www.djangoproject.com) Pode instalar qualquer um deles `pip install` apenas com o comando.

Tenha em mente que configuramos a Máquina Virtual para permitir o tráfego apenas na porta 80. Por isso, certifique-se de configurar a aplicação para utilizar esta porta.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Configure a sua aplicação para usar bibliotecas Twilio
Pode configurar a sua aplicação para utilizar a biblioteca Twilio para Python de duas formas:

* Instale a biblioteca Twilio para Python como um pacote Pip. Pode ser instalado com os seguintes comandos:
   
        $ pip install twilio

    -OU-

* Descarregue a biblioteca Twilio para[https://github.com/twilio/twilio-python][twilio_python]Python a partir do GitHub e instale-a assim:

        $ python setup.py install

Depois de ter instalado a biblioteca Twilio `import` para python, pode então nos seus ficheiros Python:

        import twilio

Para mais informações, consulte [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída. Este código também utiliza um site fornecido por Twilio para devolver a resposta da Linguagem De Marcação Twilio (TwiML). Substitua os seus valores pelos **números de telefone from_number** e **to_number** e certifique-se de que verificou o **from_number** número de telefone da sua conta Twilio antes de executar o código.

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

Como mencionado, este código utiliza um site fornecido por Twilio para devolver a resposta TwiML. Em vez disso, poderia utilizar o seu próprio site para fornecer a resposta TwiML; para mais informações, consulte [Como Fornecer Respostas TwiML a partir do seu próprio Web Site](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS
O seguinte mostra como enviar uma mensagem `TwilioRestClient` SMS usando a classe. O número **from_number** é fornecido pela Twilio para contas de teste para enviar mensagens SMS. O número **to_number** deve ser verificado para a sua conta Twilio antes de executar o código.

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas TwiML a partir do seu próprio Website
Quando a sua aplicação iniciar uma chamada para a API Twilio, a Twilio enviará o seu pedido para um URL que deverá devolver uma resposta TwiML. O exemplo acima utiliza o URL [https://twimlets.com/message][twimlet_message_url]fornecido por Twilio . (Enquanto o TwiML é projetado para ser usado pela Twilio, pode vê-lo no seu navegador. Por exemplo, [https://twimlets.com/message][twimlet_message_url] clique para `<Response>` ver um elemento vazio; como outro exemplo, [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] clique `<Response>` para ver `<Say>` um elemento que contenha um elemento.)

Em vez de confiar no URL fornecido pelo Twilio, pode criar o seu próprio site que devolve respostas http. Pode criar o site em qualquer idioma que destitude respostas XML; este tópico pressupõe que você estará usando Python para criar o TwiML.

Os seguintes exemplos irão dar uma resposta TwiML que diz **Hello World** na chamada.

Com O Balão:

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

Como pode ver pelo exemplo acima, a resposta TwiML é simplesmente um documento XML. A biblioteca Twilio para Python contém aulas que gerarão TwiML para si. O exemplo abaixo produz a resposta equivalente como `twiml` mostrado acima, mas utiliza o módulo na biblioteca Twilio para Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Para mais informações sobre [https://www.twilio.com/docs/api/twiml][twiml_reference]o TwiML, consulte .

Assim que tiver a sua aplicação Python configurada para fornecer respostas TwiML, utilize o URL da aplicação à medida que o URL passou para o `client.calls.create` método. Por exemplo, se tiver uma aplicação Web chamada **MyTwiML** implantada num serviço azure hospedado, pode utilizar o seu url como webhook, como mostrado no exemplo seguinte:

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

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Como: Utilizar Serviços Twilio Adicionais
Além dos exemplos aqui apresentados, o Twilio oferece APIs baseados na Web que pode utilizar para alavancar funcionalidades Twilio adicionais a partir da sua aplicação Azure. Para mais detalhes, consulte a documentação da [API twilio.][twilio_api]

## <a name="next-steps"></a><a id="NextSteps"></a>Próximos Passos
Agora que aprendeu o básico do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de Segurança twilio][twilio_security_guidelines]
* [Twilio HowTo Guias e Código de Exemplo][twilio_howtos]
* [Tutoriais Twilio Quickstart][twilio_quickstarts]
* [Twilio no GitHub][twilio_on_github]
* [Fale com o Apoio twilio][twilio_support]

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
