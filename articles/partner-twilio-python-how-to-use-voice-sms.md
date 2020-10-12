---
title: Como usar twilio para voz e SMS (Python) Microsoft Docs
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço API Twilio em Azure. Amostras de código escritas em Python.
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
ms.custom: devx-track-python
ms.openlocfilehash: ba93591ade730c4e9c9bdb6a42232e71e10d6469
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850156"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Como usar twilio para capacidades de voz e SMS em Python
Este guia demonstra como executar tarefas de programação comuns com o serviço Twilio API em Azure. Os cenários abrangidos incluem fazer uma chamada telefónica e enviar uma mensagem do Serviço de Mensagens Curtas (SMS). Para obter mais informações sobre Twilio e utilizando voz e SMS nas suas aplicações, consulte a secção [Etapas Seguintes.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>O que é Twilio?
A Twilio está a alimentar o futuro das comunicações empresariais, permitindo aos desenvolvedores incorporarem voz, VoIP e mensagens em aplicações. Virtualizam todas as infraestruturas necessárias num ambiente global baseado na nuvem, expondo-a através da plataforma API de comunicações Twilio. As aplicações são simples de construir e escaláveis. Desfrute da flexibilidade com o preço do pay-as-go e beneficie da fiabilidade da nuvem.

**O Twilio Voice** permite que as suas aplicações façam e recebam chamadas telefónicas.
**O Twilio SMS** permite que a sua aplicação envie e receba mensagens de texto.
**O Cliente Twilio** permite-lhe fazer chamadas VoIP a partir de qualquer telefone, tablet ou navegador e suporta o WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Preços twilio e ofertas especiais
Os clientes Azure recebem uma [oferta especial][special_offer] de $10 de Crédito Twilio quando atualiza a sua Conta Twilio. Este Crédito Twilio pode ser aplicado a qualquer utilização de Twilio (crédito de $10 equivalente ao envio de até 1.000 mensagens SMS ou receber até 1000 minutos de voz de entrada, dependendo da localização do seu número de telefone e mensagem ou destino de chamada). Resgatar este [crédito de Twilio][special_offer] e começar.

Twilio é um serviço de pagamento. Não existem taxas de configuração e pode fechar a sua conta a qualquer momento. Pode encontrar mais detalhes na [Twilio Pricing.][twilio_pricing]

## <a name="concepts"></a><a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece funcionalidade de voz e SMS para aplicações. As bibliotecas de clientes estão disponíveis em vários idiomas; para uma lista, consulte as [Bibliotecas Twilio API][twilio_libraries].

Os principais aspetos da API de Twilio são os verbos Twilio e a Língua de Marcação Twilio (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Verbos Twilio
A API faz uso de verbos twilio; por exemplo, o verbo ** &lt; Say &gt; ** instrui Twilio a entregar uma mensagem audivelmente numa chamada.

Segue-se uma lista de verbos de Twilio. Saiba mais sobre os outros verbos e capacidades através da [documentação da linguagem Twilio Markup][twiml].

* Marcação : Liga o chamador a outro telefone. ** &lt; &gt; **
* ** &lt; Recolha: &gt; **Coleciona dígitos numéricos introduzidos no teclado do telefone.
* ** &lt; Hangup &gt; **: Termina uma chamada.
* ** &lt; Pausa &gt; **: Aguarde silenciosamente um número especificado de segundos.
* ** &lt; Reproduzir: &gt; **Reproduz um ficheiro áudio.
* ** &lt; Fila &gt; **: Adicione a uma fila de chamadas.
* ** &lt; Record: &gt; **Grava a voz do autor da chamada e devolve um URL de um ficheiro que contém a gravação.
* ** &lt; Redirecionamento &gt; **: Transfere o controlo de uma chamada ou SMS para o TwiML num URL diferente.
* ** &lt; Rejeitar: &gt; **Rejeita uma chamada recebida para o seu número Twilio sem o cobrar.
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

Quando a sua aplicação chama a API Twilio, um dos parâmetros da API é o URL que devolve a resposta TwiML. Para fins de desenvolvimento, pode utilizar URLs fornecidos pela Twilio para fornecer as respostas TwiML utilizadas pelas suas aplicações. Também pode hospedar os seus próprios URLs para produzir as respostas TwiML, e outra opção é usar o `TwiMLResponse` objeto.

Para obter mais informações sobre verbos Twilio, seus atributos, e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [a API Twilio.][twilio_api]

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Criar uma Conta Twilio
Quando estiver pronto para obter uma conta Twilio, inscreva-se na [Try Twilio.][try_twilio] Pode começar com uma conta gratuita e atualizar a sua conta mais tarde.

Quando se inscreve numa conta Twilio, recebe uma conta SID e um token de autenticação. Ambos serão necessários para fazer chamadas da Twilio API. Para evitar o acesso não autorizado à sua conta, mantenha o seu símbolo de autenticação seguro. A sua conta SID e token de autenticação são visualizados na [Consola Twilio,][twilio_console]nos campos marcados **COMO CONTA SID** e **AUTH TOKEN,** respectivamente.

## <a name="create-a-python-application"></a><a id="create_app"></a>Criar uma aplicação Python
Uma aplicação Python que usa o serviço Twilio e está em execução em Azure não é diferente de qualquer outra aplicação Python que usa o serviço Twilio. Embora os serviços de Twilio sejam baseados em REST e possam ser chamados de Python de várias maneiras, este artigo irá focar-se em como usar os serviços Twilio com [a biblioteca Twilio para Python do GitHub.][twilio_python] Para obter mais informações sobre a utilização da biblioteca Twilio para Python, [https://www.twilio.com/docs/libraries/python][twilio_lib_docs] consulte.

Primeiro, [configurar um novo Azure Linux VM][azure_vm_setup] para atuar como anfitrião da sua nova aplicação web Python. Uma vez que a Máquina Virtual esteja em funcionamento, terá de expor a sua aplicação numa porta pública, conforme descrito abaixo.

### <a name="add-an-incoming-rule"></a>Adicionar uma regra de entrada
  1. Vá para a página [Grupo de Segurança da Rede][azure_nsg].
  2. Selecione o Grupo de Segurança da Rede que corresponde à sua Máquina Virtual.
  3. Adicionar e **sair regra** para a **porta 80**. Certifique-se de permitir a entrada de qualquer endereço.

### <a name="set-the-dns-name-label"></a>Desa estação a etiqueta de nome DNS
  1. Aceda à página [Endereços IP][azure_ips].
  2. Selecione o IP público que corresponde à sua Máquina Virtual.
  3. Desaver a **etiqueta de nome DNS** na secção **configuração.** No caso deste exemplo, será algo parecido com este *o seu rótulo de domínio*.centralus.cloudapp.azure.com

Uma vez que você é capaz de ligar através de SSH à Máquina Virtual você pode instalar a Estrutura Web da sua escolha (os dois mais conhecidos em Python sendo [Flask](http://flask.pocoo.org/) e [Django).](https://www.djangoproject.com) Pode instalar qualquer um deles apenas executando o `pip install` comando.

Tenha em mente que configuramos a Máquina Virtual para permitir o tráfego apenas na porta 80. Por isso, certifique-se de configurar a aplicação para utilizar esta porta.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Configure a sua aplicação para utilizar bibliotecas Twilio
Pode configurar a sua aplicação para utilizar a biblioteca Twilio para Python de duas maneiras:

* Instale a biblioteca Twilio para Python como um pacote Pip. Pode ser instalado com os seguintes comandos:
   
  `$ pip install twilio`

    -OU-

* Descarregue a biblioteca Twilio para Python do GitHub e [https://github.com/twilio/twilio-python][twilio_python] instale-a desta forma:

  `$ python setup.py install`

Uma vez instalada a biblioteca Twilio para Python, pode então `import` nos seus ficheiros Python:

  `import twilio`

Para mais informações, consulte [twilio_github_readme.](https://github.com/twilio/twilio-python/blob/master/README.md)

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída. Este código também utiliza um site fornecido por Twilio para devolver a resposta Twilio Markup Language (TwiML). Substitua os seus valores pelos números de telefone **from_number** e **to_number** e certifique-se de que verificou o número de telefone **from_number** da sua conta Twilio antes de executar o código.

```python
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
```

Como mencionado, este código utiliza um site fornecido por Twilio para devolver a resposta TwiML. Em vez disso, pode utilizar o seu próprio site para fornecer a resposta TwiML; para obter mais informações, consulte [Como Fornecer Respostas TwiML a partir do seu próprio Web Site](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS
O seguinte mostra como enviar uma mensagem SMS utilizando a `TwilioRestClient` classe. O **número from_number** é fornecido pela Twilio para contas de teste para enviar mensagens SMS. O **número to_number** deve ser verificado para a sua conta Twilio antes de executar o código.

```python
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
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas TwiML a partir do seu próprio website
Quando a sua aplicação iniciar uma chamada para a API Twilio, twilio enviará o seu pedido para um URL que deverá devolver uma resposta TwiML. O exemplo acima utiliza o URL fornecido por [https://twimlets.com/message][twimlet_message_url] Twilio. (Enquanto o TwiML foi concebido para ser utilizado pela Twilio, pode vê-lo no seu navegador. Por exemplo, clique [https://twimlets.com/message][twimlet_message_url] para ver um elemento `<Response>` vazio; como outro exemplo, clique [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] para ver um elemento que `<Response>` contenha um `<Say>` elemento.)

Em vez de confiar no URL fornecido por Twilio, pode criar o seu próprio site que devolve respostas HTTP. Pode criar o site em qualquer idioma que devolva respostas XML; este tópico pressupõe que você vai usar Python para criar o TwiML.

Os exemplos a seguir irão dar uma resposta TwiML que diz **Hello World** na chamada.

Com Frasco:

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

Com Django:

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

Como pode ver pelo exemplo acima, a resposta TwiML é simplesmente um documento XML. A biblioteca Twilio para Python contém aulas que gerarão TwiML para si. O exemplo abaixo produz a resposta equivalente como mostrado acima, mas utiliza o `twiml` módulo na biblioteca Twilio para Python:

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

Para obter mais informações sobre o TwiML, consulte [https://www.twilio.com/docs/api/twiml][twiml_reference] .

Assim que tiver a sua aplicação Python configurada para fornecer respostas TwiML, utilize o URL da aplicação à medida que o URL passou para o `client.calls.create`  método. Por exemplo, se tiver uma aplicação Web chamada **MyTwiML** implantada num serviço hospedado no Azure, pode utilizar o seu url como webhook como mostrado no seguinte exemplo:

```python
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
```

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Como: Utilizar serviços Twilio adicionais
Além dos exemplos mostrados aqui, o Twilio oferece APIs baseados na Web que pode utilizar para alavancar a funcionalidade Twilio adicional a partir da sua aplicação Azure. Para mais detalhes, consulte a documentação da [API Twilio.][twilio_api]

## <a name="next-steps"></a><a id="NextSteps"></a>Próximos Passos
Agora que aprendeu o básico do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de Segurança Twilio][twilio_security_guidelines]
* [Twilio Como Guias e Código de Exemplo][twilio_howtos]
* [Twilio Quickstart Tutorials][twilio_quickstarts]
* [Twilio no GitHub][twilio_on_github]
* [Fale com o Apoio Twilio][twilio_support]

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
