---
title: Como usar o Twilio para voz e SMS (Ruby) [ Microsoft Docs
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço Twilio API no Azure. Amostras de código escritas em Ruby.
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
ms.openlocfilehash: 4822e6feb29f5a17c653a60937b895ec584e0ee4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637204"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Como usar twilio para capacidades de voz e SMS em Ruby
Este guia demonstra como executar tarefas comuns de programação com o serviço Twilio API no Azure. Os cenários abordados incluem fazer uma chamada telefónica e enviar uma mensagem de Serviço de Mensagens Curtas (SMS). Para obter mais informações sobre twilio e usando voz e SMS nas suas aplicações, consulte a secção [Next Steps.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>O que é Twilio?
Twilio é um API de serviço web de telefonia que permite usar as suas línguas e habilidades web existentes para construir aplicações de voz e SMS. Twilio é um serviço de terceiros (não uma funcionalidade Azure e não um produto Microsoft).

**A Twilio Voice** permite que as suas aplicações façam e recebam chamadas telefónicas. **O SMS Twilio** permite que as suas aplicações eçam e recebam mensagens SMS. **O Twilio Client** permite que as suas aplicações permitam a comunicação por voz utilizando as ligações de Internet existentes, incluindo ligações móveis.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Preços twilio e ofertas especiais
Informações sobre preços Twilio estão disponíveis na [Twilio Pricing][twilio_pricing]. Os clientes azure recebem uma [oferta especial:][special_offer]um crédito gratuito de 1000 textos ou 1000 minutos de entrada. Para se inscrever para esta oferta ou [https://ahoy.twilio.com/azure][special_offer]obter mais informações, visite .  

## <a name="concepts"></a><a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece funcionalidade de voz e SMS para aplicações. As bibliotecas de clientes estão disponíveis em vários idiomas; para uma lista, consulte [bibliotecas Twilio API][twilio_libraries].

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseadas em XML que informam Twilio de como processar uma chamada ou SMS.

Como exemplo, o seguinte TwiML converteria o texto **Hello World** em discurso.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos os documentos `<Response>` TwiML têm como elemento raiz. A partir daí, usas Twilio Verbs para definir o comportamento da tua aplicação.

### <a name="twiml-verbs"></a><a id="Verbs"></a>Verbos TwiML
Twilio Verbs são tags XML que dizem a Twilio o que **fazer.** Por exemplo, ** &lt;&gt; ** o verbo Say instrui Twilio a entregar uma mensagem audivelmente numa chamada. 

Segue-se uma lista de verbos Twilio.

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

Para mais informações sobre os verbos Twilio, os seus atributos, e o TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [a Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Criar uma conta Twilio
Quando estiver pronto para obter uma conta Twilio, inscreva-se no [Try Twilio.][try_twilio] Pode começar com uma conta gratuita e fazer upgrade da sua conta mais tarde.

Quando se inscrever para uma conta Twilio, receberá um número de telefone gratuito para a sua inscrição. Também receberá uma conta SID e um símbolo de auth. Ambos serão necessários para fazer chamadas da API twilio. Para evitar o acesso não autorizado à sua conta, mantenha o seu símbolo de autenticação seguro. A sua conta SID e auth token são visualizadas na página da [conta Twilio,][twilio_account]nos campos rotulados **DE IS** e **Token AUTH,** respectivamente.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Verificar números de telefone
Além do número que lhe é dado pela Twilio, também pode verificar os números que controla (ou seja, o seu telemóvel ou número de telefone doméstico) para utilização nas suas aplicações. 

Para obter informações sobre como verificar um número de telefone, consulte [Gerir números][verify_phone].

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Criar uma aplicação Ruby
Uma aplicação Ruby que utiliza o serviço Twilio e está a funcionar em Azure não é diferente de qualquer outra aplicação Ruby que utilize o serviço Twilio. Embora os serviços de Twilio sejam RESTful e possam ser chamados da Ruby de várias maneiras, este artigo irá focar-se em como usar os serviços twilio com a biblioteca de [ajudantes Twilio para ruby.][twilio_ruby]

Primeiro, [configurar um novo Azure Linux VM][azure_vm_setup] para atuar como anfitrião da sua nova aplicação web Ruby. Ignore os passos que envolvem a criação de uma app Rails, basta configurar o VM. Certifique-se de criar um Ponto Final com uma porta externa de 80 e um porto interno de 5000.

Nos exemplos abaixo, vamos usar o [Sinatra,][sinatra]um quadro web muito simples para a Ruby. Mas você certamente pode usar a biblioteca de ajudantes Twilio para Ruby com qualquer outra estrutura web, incluindo Ruby on Rails.

SSH no seu novo VM e crie um diretório para a sua nova aplicação. Dentro desse diretório, crie um ficheiro chamado Gemfile e copie o seguinte código no mesmo:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na linha de `bundle install`comando. Isto irá instalar as dependências acima. Em seguida, `web.rb`criar um ficheiro chamado . Este será o local onde vive o código para a sua aplicação web. Colá-lo o seguinte código:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Neste ponto deve ser capaz de `ruby web.rb -p 5000`executar o comando . Isto irá girar um pequeno servidor web na porta 5000. Deverá poder navegar para esta aplicação no seu navegador visitando o URL configurado para o seu Azure VM. Assim que conseguir chegar à sua aplicação web no navegador, está pronto para começar a construir uma aplicação Twilio.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Configure a sua aplicação para usar o Twilio
Pode configurar a sua aplicação web para utilizar `Gemfile` a biblioteca Twilio atualizando a sua para incluir esta linha:

    gem 'twilio-ruby'

Na linha de `bundle install`comando, corra. Agora `web.rb` aberto e incluindo esta linha no topo:

    require 'twilio-ruby'

Agora estão prontos para usar a biblioteca de ajudantes Twilio para a Ruby na sua aplicação web.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída. Conceitos-chave incluem a utilização da biblioteca de ajudantes Twilio para a Ruby para fazer chamadas rest API e renderização TwiML. Substitua os seus valores pelos números de telefone **De** e **Para** e certifique-se de que verifica o número de telefone **do From** para a sua conta Twilio antes de executar o código.

Adicione esta `web.md`função a:

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

Se abrir `http://yourdomain.cloudapp.net/make_call` num navegador, isso irá desencadear a chamada para a API Twilio para fazer a chamada. Os dois primeiros `client.account.calls.create` parâmetros são bastante autoexplicativos: o `from` número da chamada `to`é e o número da chamada é . 

O terceiro parâmetro`url`é o URL que Twilio pede para obter instruções sobre o que fazer uma vez que a chamada esteja ligada. Neste caso, configuramos um`http://yourdomain.cloudapp.net`URL que devolve um simples documento `<Say>` TwiML e usa o verbo para fazer algum texto-a-fala e dizer "Hello Monkey" à pessoa que recebe a chamada.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Como: Receber uma mensagem SMS
No exemplo anterior, iniciámos um telefonema **de saída.** Desta vez, vamos usar o número de telefone que twilio **incoming** nos deu durante a inscrição para processar uma mensagem SMS.

Primeiro, faça login no seu [painel twilio.][twilio_account] Clique em "Numbers" na navegação superior e clique no número Twilio que lhe foi fornecido. Verá dois URLs que pode configurar. Um URL de Pedido de Voz e um URL de Pedido de SMS. Estes são os URLs que Twilio liga sempre que uma chamada é feita ou um SMS é enviado para o seu número. Os URLs também são conhecidos como "web hooks".

Gostaríamos de processar mensagens SMS que chegam, por `http://yourdomain.cloudapp.net/sms_url`isso vamos atualizar o URL para . Vá em frente e clique em Guardar Alterações na parte inferior da página. Agora, `web.rb` de volta, vamos programar a nossa aplicação para lidar com isto:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Depois de então fazer a mudança, certifique-se de reiniciar a sua aplicação web. Agora, tira o telemóvel e envia um SMS para o teu número Twilio. Deve receber prontamente uma resposta SMS que diga "Obrigado pelo ping! Twilio e Azure rock!".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Como: Utilizar Serviços Twilio Adicionais
Além dos exemplos aqui apresentados, o Twilio oferece APIs baseados na Web que pode utilizar para alavancar funcionalidades Twilio adicionais a partir da sua aplicação Azure. Para mais detalhes, consulte a documentação da [API twilio.][twilio_api_documentation]

### <a name="next-steps"></a><a id="NextSteps"></a>Próximos Passos
Agora que aprendeu o básico do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de Segurança twilio][twilio_security_guidelines]
* [Twilio HowTos e Código de Exemplo][twilio_howtos]
* [Tutoriais Twilio Quickstart][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Fale com o Apoio twilio][twilio_support]

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
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
