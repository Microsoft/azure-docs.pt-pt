---
title: Como utilizar o Twilio para voz e SMS (Python) | Documentos da Microsoft
description: Saiba como fazer uma chamada telefónica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de códigos escritos em Python.
services: ''
documentationcenter: python
author: devinrader
manager: twilio
editor: ''
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: e6cfd9e72dc1a38e4ed0c11320336ccc4b44a2c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61457673"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Como utilizar o Twilio para voz e SMS capacidades em Python
Este guia demonstra como realizar tarefas comuns de programação com o serviço de Twilio API no Azure. Os cenários abrangidos incluem uma ligação telefônica e enviar uma mensagem de serviço de mensagens curtas (SMS). Para obter mais informações sobre o Twilio e voz e SMS a utilizar nas suas aplicações, consulte a [passos seguintes](#NextSteps) secção.

## <a id="WhatIs"></a>O que é o Twilio?
Twilio é que alimenta o futuro das comunicações empresariais, permitindo que os desenvolvedores incorporar voz, VoIP e de mensagens em aplicativos. Eles virtualizar toda a infraestrutura necessária num ambiente global, com base na cloud, expô-lo através da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Desfrute da flexibilidade com pay as you go de preços e se beneficiar de fiabilidade de cloud.

**Serviços de voz Twilio** permite que seus aplicativos fazer e receber chamadas telefónicas.
**Twilio SMS** permite que a sua aplicação enviar e receber mensagens de texto.
**Cliente do Twilio** permite-lhe efetuar chamadas VoIP, de qualquer telefone, tablet ou navegador e oferece suporte WebRTC.

## <a id="Pricing"></a>Preços do Twilio e ofertas especiais
Os clientes do Azure recebem uma [oferta especial] [ special_offer] US $10 do Twilio crédito quando atualiza a sua conta do Twilio. Este crédito Twilio pode ser aplicado a qualquer utilização do Twilio (crédito de US $10 equivalente ao enviar até 1000 mensagens SMS ou receber até 1000 entrados minutos de voz, consoante a localização do seu destino de número e a mensagem ou a chamada de telefone). Resgatar isso [Twilio crédito] [ special_offer] e começar a utilizar.

Twilio é um serviço pay as you go. Existem sem taxas de configuração e pode fechar a sua conta em qualquer altura. Pode encontrar mais detalhes [preços de Twilio][twilio_pricing].

## <a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful, que fornece funcionalidade SMS e de voz para aplicações. As bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [bibliotecas de APIS do Twilio][twilio_libraries].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação do Twilio (TwiML).

### <a id="Verbs"></a>Twilio Verbs
A API usa Twilio verbos; Por exemplo, o **&lt;Digamos&gt;** verbo instrui o Twilio para poder ouvi-lo entregar uma mensagem numa chamada.

Segue-se uma lista de verbos Twilio. Saiba mais sobre os outros verbos e capacidades através de [documentação da linguagem de marcação do Twilio][twiml].

* **&lt;Dial&gt;** : Liga-se o autor da chamada para outro telefone.
* **&lt;Gather&gt;** : Recolhe os dígitos numéricos introduzidos no teclado do telefone.
* **&lt;Hangup&gt;** : Termina uma chamada.
* **&lt;Pause&gt;** : Aguarda silenciosamente um número de segundos especificado.
* **&lt;Reproduzir&gt;** : Reproduz um arquivo de áudio.
* **&lt;Fila&gt;** : Adicionar à uma fila de chamadores.
* **&lt;Registo&gt;** : Regista a voz do chamador e retorna uma URL de um ficheiro que contém a gravação.
* **&lt;Redirect&gt;** : Transfere o controle de uma chamada ou SMS para o TwiML numa URL diferente.
* **&lt;Reject&gt;** : Rejeita uma chamada de entrada para o seu número do Twilio sem de faturação.
* **&lt;Say&gt;** : Converte texto em voz, que é efetuado numa chamada.
* **&lt;Sms&gt;** : Envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções com base em XML, com base nos verbos de Twilio informam Twilio de como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML seria converter o texto **Olá, mundo** para voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Quando seu aplicativo chama a API Twilio, um dos parâmetros de API é o URL que devolve a resposta de TwiML. Para fins de desenvolvimento, pode utilizar URLs fornecidos pelo Twilio para fornecer as respostas de TwiML utilizadas pelas suas aplicações. Também pudesse hospedar suas próprias URLs para produzir as respostas de TwiML e outra opção é usar o `TwiMLResponse` objeto.

Para obter mais informações sobre o Twilio verbos, os atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do Twilio
Quando estiver pronto para obter uma conta do Twilio, inscreva-se em [tente Twilio][try_twilio]. Pode começar com uma conta gratuita e atualize a sua conta mais tarde.

Quando se inscreve para uma conta do Twilio, receberá um SID da conta e um token de autenticação. Ambos serão necessários para fazer chamadas à API do Twilio. Para impedir acesso não autorizado à sua conta, manter o token de autenticação segura. O SID da conta e o token de autenticação são visíveis na [Twilio consola][twilio_console], nos campos nome **SID da conta** e **TOKEN de autenticação**, respectivamente.

## <a id="create_app"></a>Criar uma aplicação Python
Uma aplicação de Python que utiliza o serviço do Twilio e está em execução no Azure não é diferente de qualquer outra aplicação de Python que utiliza o serviço do Twilio. Embora Twilio serviços são baseados em REST e podem ser chamados a partir de Python de várias formas, este artigo focará sobre como utilizar os serviços com o Twilio [biblioteca de Twilio para Python a partir do GitHub][twilio_python]. Para obter mais informações sobre como utilizar a biblioteca do Twilio para o Python, veja [ https://www.twilio.com/docs/libraries/python ] [ twilio_lib_docs].

Primeiro, [configuração uma nova VM do Linux do Azure] [azure_vm_setup] para atuar como um anfitrião para a aplicação web do seu novo Python. Quando a Máquina Virtual estiver em execução, terá de expor seu aplicativo numa porta pública, conforme descrito abaixo.

### <a name="add-an-incoming-rule"></a>Adicionar uma regra de entrada
  1. Aceda à página [grupo de segurança] [azure_nsg].
  2. Selecione o grupo de segurança rede correspondente com a sua máquina Virtual.
  3. Adicionar e **regra de saída** para **a porta 80**. Certifique-se de que permitir a entrada de qualquer endereço.

### <a name="set-the-dns-name-label"></a>Defina a etiqueta de nome DNS
  1. Aceda à página [a endereços IP públicos] [azure_ips].
  2. Selecione o IP público que corresponde com a sua máquina Virtual.
  3. Definir o **etiqueta de nome DNS** no **configuração** secção. No caso deste exemplo ele será algo parecido com isto *your-domínio-label*. centralus.cloudapp.azure.com

Depois de se conseguir estabelecer ligação através de SSH à máquina Virtual pode instalar a estrutura de Web à sua escolha (os dois mais conhecidos em Python que está sendo [Flask](http://flask.pocoo.org/) e [Django](https://www.djangoproject.com)). Pode instalar qualquer um deles apenas executando o `pip install` comando.

Tenha em atenção que configurámos a Máquina Virtual para permitir tráfego apenas na porta 80. Por isso, certifique-se configurar a aplicação para utilizar esta porta.

## <a id="configure_app"></a>Configurar a sua aplicação utilizar bibliotecas do Twilio
Pode configurar a sua aplicação para utilizar a biblioteca do Twilio para o Python de duas formas:

* Instale a biblioteca do Twilio para o Python como um pacote do Pip. Pode ser instalado com os seguintes comandos:
   
        $ pip install twilio

    -OU-

* Transferir a biblioteca do Twilio para Python a partir do GitHub ([https://github.com/twilio/twilio-python][twilio_python]) e instale-o como este:

        $ python setup.py install

Depois de instalar a biblioteca do Twilio para o Python, pode então `import` -lo em seus arquivos de Python:

        import twilio

Para obter mais informações, consulte [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.rst).

## <a id="howto_make_call"></a>Como: Efetuar uma chamada de saída
O código a seguir mostra como fazer uma chamada de saída. Esse código também utiliza um site fornecida pelo Twilio para retornar a resposta de linguagem de marcação do Twilio (TwiML). Substitua os valores para o **from_number** e **to_number** números de telefone e certifique-se de que verificou o **from_number** número de telefone para sua conta do Twilio antes de executar o código.

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

Conforme mencionado, esse código usa um site fornecida pelo Twilio para retornar a resposta de TwiML. Em vez disso, poderia usar seu próprio site para fornecer a resposta de TwiML; Para obter mais informações, consulte [como a fornecer respostas de TwiML no seu próprio Web Site](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como: Envie uma mensagem SMS
O seguinte mostra como enviar uma mensagem SMS a utilizar o `TwilioRestClient` classe. O **from_number** número é fornecido pela Twilio para contas de avaliação para enviar mensagens SMS. O **to_number** número deve ser verificado para a sua conta do Twilio, antes de executar o código.

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

## <a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas TwiML do seu próprio Web site
Quando seu aplicativo inicia uma chamada à API do Twilio, o Twilio irá enviar o pedido para um URL que deverá devolver uma resposta de TwiML. O exemplo acima utiliza o URL fornecido pelo Twilio [ https://twimlets.com/message ] [ twimlet_message_url]. (Apesar TwiML foi concebido para utilização por Twilio, pode vê-lo no seu browser. Por exemplo, clique em [ https://twimlets.com/message ] [ twimlet_message_url] para ver vazio `<Response>` elemento; como outro exemplo, clique em [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ] [ twimlet_message_url_hello_world]para ver uma `<Response>` elemento que contém um `<Say>` elemento.)

Em vez de contar com o URL fornecido pelo Twilio, pode criar seu próprio site que retorna as respostas HTTP. Pode criar o site em qualquer linguagem que retorna as respostas XML; Este tópico pressupõe que irá utilizar Python para criar o TwiML.

Os exemplos seguintes produzirá uma resposta de TwiML diz **Olá, mundo** na chamada.

Com o Flask:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Com o Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Como pode ver no exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca do Twilio para Python contém classes que irão gerar TwiML para. O exemplo a seguir produz a resposta equivalente, conforme mostrado acima, mas utiliza o `twiml` módulo na biblioteca do Twilio para Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Para obter mais informações sobre TwiML, consulte [ https://www.twilio.com/docs/api/twiml ] [ twiml_reference].

Assim que tiver a sua aplicação de Python configurada para fornecer respostas TwiML, utilize o URL da aplicação como o URL transmitido para o `client.calls.create` método. Por exemplo, se tiver uma aplicação Web com o nome **MyTwiML** implementado a Azure alojado o serviço, pode utilizar o respetivo url como webhook, conforme mostrado no exemplo a seguir:

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

## <a id="AdditionalServices"></a>Como: Utilizar os serviços adicionais do Twilio
Para além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do Twilio através da aplicação do Azure. Para mais informações, consulte a [documentação da API do Twilio][twilio_api].

## <a id="NextSteps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço Twilio, siga estas ligações para saber mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [Guias de tutoriais sobre como do Twilio e código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do Twilio][twilio_quickstarts]
* [Twilio no GitHub][twilio_on_github]
* [Comunicar com o suporte do Twilio][twilio_support]

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
