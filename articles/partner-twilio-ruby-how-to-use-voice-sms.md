---
title: Como usar twilio para voz e SMS (Ruby) | Microsoft Docs
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço API Twilio em Azure. Amostras de código escritas na Ruby.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 49203195bf7746d0bff1b9543d1641f69ab23359
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95542682"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Como usar twilio para capacidades de voz e SMS em Ruby
Este guia demonstra como executar tarefas de programação comuns com o serviço Twilio API em Azure. Os cenários abrangidos incluem fazer uma chamada telefónica e enviar uma mensagem do Serviço de Mensagens Curtas (SMS). Para obter mais informações sobre Twilio e utilizando voz e SMS nas suas aplicações, consulte a secção [Etapas Seguintes.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>O que é Twilio?
Twilio é uma API de serviço web de telefonia que permite usar os seus idiomas e habilidades web existentes para construir aplicações de voz e SMS. Twilio é um serviço de terceiros (não é uma funcionalidade Azure e não um produto Microsoft).

**O Twilio Voice** permite que as suas aplicações façam e recebam chamadas telefónicas. **O Twilio SMS** permite que as suas aplicações façam e recebam mensagens SMS. **O Twilio Client** permite que as suas aplicações permitam a comunicação por voz utilizando as ligações existentes na Internet, incluindo ligações móveis.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Preços twilio e ofertas especiais
Informações sobre preços de Twilio estão disponíveis na [Twilio Pricing.][twilio_pricing] Os clientes Azure recebem uma [oferta especial:][special_offer]um crédito gratuito de 1000 textos ou 1000 minutos de entrada. Para se inscrever nesta oferta ou obter mais informações, [https://ahoy.twilio.com/azure][special_offer] visite.  

## <a name="concepts"></a><a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece funcionalidade de voz e SMS para aplicações. As bibliotecas de clientes estão disponíveis em vários idiomas; para uma lista, consulte as [Bibliotecas Twilio API][twilio_libraries].

### <a name="twiml"></a><a id="TwiML"></a>TWIML
TwiML é um conjunto de instruções baseadas em XML que informam Twilio de como processar uma chamada ou SMS.

Como exemplo, o seguinte TwiML converteria o texto **Hello World** em discurso.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Todos os documentos TwiML têm `<Response>` como elemento de raiz. A partir daí, usa twilio Verbs para definir o comportamento da sua aplicação.

### <a name="twiml-verbs"></a><a id="Verbs"></a>Verbos TwiML
Twilio Verbs são tags XML que dizem a Twilio o que **fazer.** Por exemplo, o verbo **&lt; Say &gt;** instrui Twilio a entregar uma mensagem audivelmente numa chamada. 

Segue-se uma lista de verbos de Twilio.

* Marcação : Liga o chamador a outro telefone. **&lt; &gt;**
* **&lt; Recolha: &gt;** Coleciona dígitos numéricos introduzidos no teclado do telefone.
* **&lt; Hangup &gt;**: Termina uma chamada.
* **&lt; Reproduzir: &gt;** Reproduz um ficheiro áudio.
* **&lt; Pausa &gt;**: Aguarde silenciosamente um número especificado de segundos.
* **&lt; Record: &gt;** Grava a voz do autor da chamada e devolve um URL de um ficheiro que contém a gravação.
* **&lt; Redirecionamento &gt;**: Transfere o controlo de uma chamada ou SMS para o TwiML num URL diferente.
* **&lt; Rejeitar: &gt;** Rejeita uma chamada recebida para o seu número Twilio sem o cobrar
* **&lt; Diga: &gt;** Converte texto para discurso que é feito numa chamada.
* **&lt; Sms &gt;**: Envia uma mensagem SMS.

Para obter mais informações sobre verbos Twilio, seus atributos, e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [a API Twilio.][twilio_api]

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Criar uma Conta Twilio
Quando estiver pronto para obter uma conta Twilio, inscreva-se na [Try Twilio.][try_twilio] Pode começar com uma conta gratuita e atualizar a sua conta mais tarde.

Quando se inscreve numa conta Twilio, receberá um número de telefone gratuito para a sua candidatura. Você também receberá uma conta SID e um token auth. Ambos serão necessários para fazer chamadas da Twilio API. Para evitar o acesso não autorizado à sua conta, mantenha o seu símbolo de autenticação seguro. A sua conta SID e auth token são visualizadas na página da [conta Twilio,][twilio_account]nos campos marcados **COMO CONTA SID** e **AUTH TOKEN,** respectivamente.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Verificar números de telefone
Além do número que lhe é dado pela Twilio, também pode verificar os números que controla (isto é, o seu telemóvel ou número de telefone doméstico) para utilização nas suas aplicações. 

Para obter informações sobre como verificar um número de telefone, consulte ['Gerir números'.][verify_phone]

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Criar uma aplicação Ruby
Uma aplicação Ruby que usa o serviço Twilio e está em execução em Azure não é diferente de qualquer outra aplicação Ruby que usa o serviço Twilio. Enquanto os serviços Twilio são RESTful e podem ser chamados da Ruby de várias maneiras, este artigo vai focar-se em como usar os serviços Twilio com [a biblioteca de ajudantes Twilio para a Ruby.][twilio_ruby]

Primeiro, [crie um novo Azure Linux VM][azure_vm_setup] para atuar como anfitrião da sua nova aplicação web Ruby. Ignore os passos que envolvem a criação de uma app Rails, basta configurar o VM. Certifique-se de criar um Ponto Final com uma porta externa de 80 e uma porta interna de 5000.

Nos exemplos abaixo, estaremos a usar [o Sinatra,][sinatra]uma estrutura web muito simples para a Ruby. Mas você certamente pode usar a biblioteca de ajudantes Twilio para Ruby com qualquer outra estrutura web, incluindo Ruby on Rails.

SSH no seu novo VM e crie um diretório para a sua nova aplicação. Dentro desse diretório, crie um ficheiro chamado Gemfile e copie o seguinte código nele:

```bash
source 'https://rubygems.org'
gem 'sinatra'
gem 'thin'
```

Na linha de `bundle install` comando. Isto instalará as dependências acima. Em seguida, crie um ficheiro chamado `web.rb` . Este será o lugar onde o código para a sua aplicação web vive. Cole-lhe o seguinte código:

```ruby
require 'sinatra'

get '/' do
    "Hello Monkey!"
end
```

Neste momento, deverá ser capaz de executar o `ruby web.rb -p 5000` comando. Isto irá spin-up um pequeno servidor web na porta 5000. Você deve ser capaz de navegar para esta aplicação no seu navegador visitando o URL que configura para o seu Azure VM. Assim que conseguir chegar à sua aplicação web no navegador, está pronto para começar a construir uma aplicação Twilio.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Configure a sua aplicação para usar o Twilio
Pode configurar a sua aplicação web para utilizar a biblioteca Twilio atualizando a sua `Gemfile` linha para incluir esta linha:

```bash
gem 'twilio-ruby'
```

Na linha de comando, `bundle install` corra. Agora abra `web.rb` e inclua esta linha no topo:

```ruby
require 'twilio-ruby'
```

Está pronto para usar a biblioteca de ajudantes Twilio para ruby na sua aplicação web.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída. Os conceitos-chave incluem a utilização da biblioteca de ajudantes Twilio para ruby para fazer chamadas REST API e renderização TwiML. Substitua os seus valores pelos números de telefone **de** e **para para telefone** e certifique-se de que verifica o número de telefone **da** sua conta Twilio antes de executar o código.

Adicione esta função `web.md` a:

```ruby
# Set your account ID and authentication token.
sid = "your_twilio_account_sid";
token = "your_twilio_authentication_token";

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from = "NNNNNNNNNNN";

# The number of the phone receiving call.
to = "NNNNNNNNNNN";

# Use the Twilio-provided site for the TwiML response.
url = "http://yourdomain.cloudapp.net/voice_url";

get '/make_call' do
    # Create the call client.
    client = Twilio::REST::Client.new(sid, token);

    # Make the call
    client.account.calls.create(to: to, from: from, url: url)
end

post '/voice_url' do
    "<Response>
        <Say>Hello Monkey!</Say>
    </Response>"
end
```

Se abrir `http://yourdomain.cloudapp.net/make_call` num browser, isso irá desencadear a chamada para a API Twilio para fazer a chamada. Os dois primeiros parâmetros `client.account.calls.create` são bastante autoexplicativos: o número que a chamada é `from` e o número da chamada é `to` . 

O terceiro parâmetro `url` () é o URL que Twilio pede para obter instruções sobre o que fazer uma vez que a chamada esteja ligada. Neste caso, configuramos um URL `http://yourdomain.cloudapp.net` () que devolve um documento TwiML simples e usa o `<Say>` verbo para fazer algum texto-a-discurso e dizer "Olá Macaco" à pessoa que recebe a chamada.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Como: Receber uma mensagem SMS
No exemplo anterior, iniciámos um telefonema **de saída.** Desta vez, vamos usar o número de telefone que o Twilio nos deu durante a inscrição para processar uma mensagem SMS **recebida.**

Primeiro, faça login no seu [painel Twilio.][twilio_account] Clique em "Numbers" na navegação superior e, em seguida, clique no número Twilio que lhe foi fornecido. Verá dois URLs que pode configurar. Um URL de Pedido de Voz e um URL de pedido de SMS. Estes são os URLs que twilio liga sempre que uma chamada telefónica é feita ou um SMS é enviado para o seu número. Os URLs também são conhecidos como "web hooks".

Gostaríamos de processar mensagens SMS recebidas, por isso vamos atualizar o URL para `http://yourdomain.cloudapp.net/sms_url` . Vá em frente e clique em Guardar Alterações na parte inferior da página. Agora, de volta, `web.rb` vamos programar a nossa candidatura para lidar com isto:

```ruby
post '/sms_url' do
    "<Response>
        <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
    </Response>"
end
```

Depois de escoar a alteração, certifique-se de reiniciar a sua aplicação web. Agora, tire o telefone e envie uma SMS para o seu número Twilio. Você deve prontamente obter uma resposta SMS que diz "Ei, obrigado pelo ping! Twilio e Azure rock!".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Como: Utilizar serviços Twilio adicionais
Além dos exemplos mostrados aqui, o Twilio oferece APIs baseados na Web que pode utilizar para alavancar a funcionalidade Twilio adicional a partir da sua aplicação Azure. Para mais detalhes, consulte a documentação da [API Twilio.][twilio_api_documentation]

### <a name="next-steps"></a><a id="NextSteps"></a>Próximos Passos
Agora que aprendeu o básico do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de Segurança Twilio][twilio_security_guidelines]
* [Twilio HowTos e Código de Exemplo][twilio_howtos]
* [Twilio Quickstart Tutorials][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Fale com o Apoio Twilio][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: /previous-versions/azure/virtual-machines/linux/classic/ruby-rails-web-app