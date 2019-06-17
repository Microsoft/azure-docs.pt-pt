---
title: Como utilizar o Twilio para voz e SMS (Ruby) | Documentos da Microsoft
description: Saiba como fazer uma chamada telefónica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de códigos escritos em Ruby.
services: ''
documentationcenter: ruby
author: devinrader
manager: twilio
editor: ''
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 40b633c4e51a34e6640a9557be49bbe30543daf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61457656"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Como utilizar o Twilio para voz e SMS capacidades em Ruby
Este guia demonstra como realizar tarefas comuns de programação com o serviço de Twilio API no Azure. Os cenários abrangidos incluem uma ligação telefônica e enviar uma mensagem de serviço de mensagens curtas (SMS). Para obter mais informações sobre o Twilio e voz e SMS a utilizar nas suas aplicações, consulte a [passos seguintes](#NextSteps) secção.

## <a id="WhatIs"></a>O que é o Twilio?
Twilio é uma API de serviço web de telefonia que permite que use suas habilidades e linguagens da web existentes para criar aplicativos de SMS e de voz. Twilio é um serviço de terceiros (não uma funcionalidade do Azure e não um produto da Microsoft).

**Serviços de voz Twilio** permite que seus aplicativos fazer e receber chamadas telefónicas. **Twilio SMS** permite que seus aplicativos fazer e receber mensagens SMS. **Cliente do Twilio** permite que seus aplicativos permitir a comunicação de voz através de ligações de Internet existentes, incluindo ligações móveis.

## <a id="Pricing"></a>Preços do Twilio e ofertas especiais
Estão disponíveis em informações sobre os preços do Twilio [preços de Twilio][twilio_pricing]. Os clientes do Azure recebem uma [oferta especial][special_offer]: um crédito gratuito de textos de 1000 ou 1000 em minutos de entrada. Inscrever-se para a oferta ou obter mais informações, visite [ https://ahoy.twilio.com/azure ] [ special_offer].  

## <a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful, que fornece funcionalidade SMS e de voz para aplicações. As bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [bibliotecas de APIS do Twilio][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções com base em XML que o informam Twilio de como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML seria converter o texto **Olá, mundo** para voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos os documentos de TwiML têm `<Response>` como o elemento raiz. A partir daí, utilize verbos de Twilio para definir o comportamento da sua aplicação.

### <a id="Verbs"></a>TwiML Verbs
Twilio verbos são marcas XML que informam ao Twilio o que **fazer**. Por exemplo, o **&lt;Digamos&gt;** verbo instrui o Twilio para poder ouvi-lo entregar uma mensagem numa chamada. 

Segue-se uma lista de verbos Twilio.

* **&lt;Dial&gt;** : Liga-se o autor da chamada para outro telefone.
* **&lt;Gather&gt;** : Recolhe os dígitos numéricos introduzidos no teclado do telefone.
* **&lt;Hangup&gt;** : Termina uma chamada.
* **&lt;Reproduzir&gt;** : Reproduz um arquivo de áudio.
* **&lt;Pause&gt;** : Aguarda silenciosamente um número de segundos especificado.
* **&lt;Registo&gt;** : Regista a voz do chamador e retorna uma URL de um ficheiro que contém a gravação.
* **&lt;Redirect&gt;** : Transfere o controle de uma chamada ou SMS para o TwiML numa URL diferente.
* **&lt;Reject&gt;** : Rejeita uma chamada de entrada para o seu número do Twilio sem de faturação
* **&lt;Say&gt;** : Converte texto em voz, que é efetuado numa chamada.
* **&lt;Sms&gt;** : Envia uma mensagem SMS.

Para obter mais informações sobre o Twilio verbos, os atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do Twilio
Quando estiver pronto para obter uma conta do Twilio, inscreva-se em [tente Twilio][try_twilio]. Pode começar com uma conta gratuita e atualize a sua conta mais tarde.

Quando se inscreve para uma conta do Twilio, obterá um número de telefone gratuita para a sua aplicação. Também receberá um SID da conta e um token de autenticação. Ambos serão necessários para fazer chamadas à API do Twilio. Para impedir acesso não autorizado à sua conta, manter o token de autenticação segura. O SID da conta e o token de autenticação são visíveis no [página de conta do Twilio][twilio_account], nos campos nome **SID da conta** e **TOKEN de autenticação de**, respectivamente.

### <a id="VerifyPhoneNumbers"></a>Verificar números de telefone
Para além do número é-lhe dada por Twilio, também pode verificar números de controlo (ou seja, o número de telefone do telefone celular ou página inicial) para uso em seus aplicativos. 

Para obter informações sobre como verificar um número de telefone, consulte [gerir números][verify_phone].

## <a id="create_app"></a>Criar uma aplicação Ruby
Uma aplicação Ruby que utiliza o serviço Twilio e está em execução no Azure não é diferente de qualquer outro aplicativo Ruby que utiliza o serviço do Twilio. Embora Twilio serviços são RESTful e podem ser chamados do Ruby de várias formas, este artigo focará sobre como utilizar os serviços com o Twilio [biblioteca de auxiliar do Twilio para Ruby][twilio_ruby].

Primeiro, [configurar uma nova VM do Linux do Azure] [ azure_vm_setup] para atuar como um anfitrião para a sua nova aplicação Ruby web. Ignore as etapas que envolvem a criação de uma aplicação Rails, configuração apenas a VM. Certifique-se de que criar um ponto final com uma porta externa de 80 e uma porta interna de 5000.

Nos exemplos abaixo, usaremos [Sinatra][sinatra], uma estrutura web muito simples para Ruby. Mas pode usar a biblioteca de programa auxiliar do Twilio para Ruby com qualquer outra estrutura de web, como Ruby on Rails.

SSH para a nova VM e criar um diretório para a nova aplicação. Dentro desse diretório, crie um ficheiro chamado Gemfile e copie o seguinte código para o mesmo:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na linha de comandos, execute `bundle install`. Esta ação irá instalar as dependências acima. Em seguida, crie um arquivo chamado `web.rb`. Este será onde reside o código para a sua aplicação web. Cole o seguinte código para o mesmo:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Neste momento deve ser capaz de executar o comando `ruby web.rb -p 5000`. Isto irá girar-até um servidor de pequena web na porta 5000. Deve ser capaz de procurar a esta aplicação no seu browser ao visitar o URL configurar para a sua VM do Azure. Assim que pode entrar a sua aplicação web no browser, está pronto para começar a criar uma aplicação do Twilio.

## <a id="configure_app"></a>Configurar a sua aplicação para utilizar o Twilio
Pode configurar a aplicação web para utilizar a biblioteca do Twilio, atualizando seu `Gemfile` para incluir esta linha:

    gem 'twilio-ruby'

Na linha de comandos, execute `bundle install`. Agora abra `web.rb` e essa linha na parte superior, incluindo:

    require 'twilio-ruby'

Agora pronto para utilizar a biblioteca de programa auxiliar do Twilio para Ruby na sua aplicação web.

## <a id="howto_make_call"></a>Como: Efetuar uma chamada de saída
O código a seguir mostra como fazer uma chamada de saída. Conceitos-chave incluem usando a biblioteca de programa auxiliar do Twilio para Ruby para fazer chamadas de REST API e composição TwiML. Substitua os valores para o **partir** e **para** números de telefone e certifique-se de que verifique o **de** número de telefone para sua conta do Twilio antes de executar o código.

Adicione esta função para `web.md`:

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

Se abrir-up `http://yourdomain.cloudapp.net/make_call` num browser, que irá disparar a chamada para a API Twilio para fazer a chamada telefónica. Os primeiros dois parâmetros na `client.account.calls.create` são bastante auto-explicativo: o número a chamada é `from` e o número a chamada é `to`. 

O terceiro parâmetro (`url`) é o URL que solicita a Twilio para obter instruções sobre o que fazer depois da chamada está ligada. Neste caso, vamos configurar um URL (`http://yourdomain.cloudapp.net`) que devolve um documento de TwiML simple e utiliza o `<Say>` verbo fazer algumas voz e dizer "Hello Monkey" para a pessoa receber a chamada.

## <a id="howto_receive_sms"></a>Como: Receber uma mensagem SMS
No exemplo anterior, iniciou um **saída** chamada telefónica. Desta vez, vamos utilizar o número de telefone Twilio deu-nos durante Inscreva-se ao processo de um **recebidos** mensagem SMS.

Em primeiro lugar, iniciar sessão na sua [dashboard do Twilio][twilio_account]. Clique em "Números de" na barra de navegação superior e, em seguida, clique no número do Twilio, foram fornecidos. Verá dois URLs que pode configurar. URL do pedido de um URL de pedido de voz e uma SMS. Estes são os URLs que o Twilio chama sempre que for feita uma chamada telefónica ou um SMS é enviado para o seu número. Os URLs também são conhecidos como "webhooks".

Gostaríamos de processar mensagens SMS de entrada, então, vamos atualizar o URL para `http://yourdomain.cloudapp.net/sms_url`. Vá em frente e clique em Guardar alterações na parte inferior da página. Agora, efetuar uma cópia no `web.rb` vamos programar nosso aplicativo para lidar com isso:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Depois de fazer a alteração, lembre-se de que reinicie a aplicação web. Agora, remova o telemóvel e enviar um SMS para o seu número do Twilio. Deve obter imediatamente uma resposta SMS que diz "EI, obrigado pelo ping! Twilio e o Azure rock! ".

## <a id="additional_services"></a>Como: Utilizar os serviços adicionais do Twilio
Para além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do Twilio através da aplicação do Azure. Para mais informações, consulte a [documentação da API do Twilio][twilio_api_documentation].

### <a id="NextSteps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço Twilio, siga estas ligações para saber mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [Twilio HowTos e código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do Twilio][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Comunicar com o suporte do Twilio][twilio_support]

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
