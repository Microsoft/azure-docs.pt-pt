---
title: Como usar o twilio para voz e SMS (Ruby) | Microsoft Docs
description: Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do twilio no Azure. Exemplos de código escritos em Ruby.
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
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637204"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Como usar o twilio para recursos de voz e SMS no Ruby
Este guia demonstra como executar tarefas comuns de programação com o serviço de API do twilio no Azure. Os cenários abordados incluem fazer uma chamada telefônica e enviar uma mensagem SMS (Short Message Service). Para obter mais informações sobre o twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [próximas etapas](#NextSteps) .

## <a id="WhatIs"></a>O que é o twilio?
O twilio é uma API de serviço Web de telefonia que permite que você use suas habilidades e linguagens da Web existentes para criar aplicativos de voz e de SMS. O twilio é um serviço de terceiros (não um recurso do Azure e não um produto da Microsoft).

O **twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio o SMS** permite que seus aplicativos façam e recebam mensagens SMS. O **twilio Client** permite que seus aplicativos habilitem a comunicação por voz usando conexões de Internet existentes, incluindo conexões móveis.

## <a id="Pricing"></a>Preços e ofertas especiais do twilio
Informações sobre preços do twilio estão disponíveis em [preços do twilio][twilio_pricing]. Os clientes do Azure recebem uma [oferta especial][special_offer]: um crédito gratuito de 1000 textos ou 1000 minutos de entrada. Para se inscrever nesta oferta ou obter mais informações, visite [https://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Principais
A API twilio é uma API RESTful que fornece a funcionalidade de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas; para obter uma lista, consulte [bibliotecas de API do twilio][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseadas em XML que informam twilio sobre como processar uma chamada ou SMS.

Como exemplo, o TwiML a seguir converterá o texto **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos os documentos TwiML `<Response>` têm como seu elemento raiz. A partir daí, você usa verbos twilio para definir o comportamento do seu aplicativo.

### <a id="Verbs"></a>Verbos TwiML
Os verbos twilio são marcas XML que dizem ao twilio o que **fazer**. Por exemplo, o  **&lt;verbo&gt; digamos** instrui o twilio a forma audível a entregar uma mensagem em uma chamada. 

Veja a seguir uma lista de verbos do twilio.

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

Para obter mais informações sobre verbos twilio, seus atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API do twilio, consulte [API do twilio][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do twilio
Quando estiver pronto para obter uma conta do twilio, Inscreva-se em [experimentar o twilio][try_twilio]. Você pode começar com uma conta gratuita e atualizar sua conta mais tarde.

Ao se inscrever em uma conta do twilio, você obterá um número de telefone gratuito para seu aplicativo. Você também receberá um SID de conta e um token de autenticação. Ambos serão necessários para fazer chamadas à API do twilio. Para impedir o acesso não autorizado à sua conta, mantenha seu token de autenticação seguro. O SID de sua conta e o token de autenticação são visíveis na [página da conta do twilio][twilio_account], nos campos rotulados **conta SID** e **token de autenticação**, respectivamente.

### <a id="VerifyPhoneNumbers"></a>Verificar números de telefone
Além do número que você recebe por twilio, também é possível verificar os números que você controla (ou seja, o telefone celular ou o número de telefone residencial) para uso em seus aplicativos. 

Para obter informações sobre como verificar um número de telefone, consulte [gerenciar números][verify_phone].

## <a id="create_app"></a>Criar um aplicativo Ruby
Um aplicativo Ruby que usa o serviço twilio e está em execução no Azure não é diferente de qualquer outro aplicativo Ruby que usa o serviço twilio. Embora os serviços Twilios sejam RESTful e possam ser chamados do Ruby de várias maneiras, este artigo se concentrará em como usar os serviços do twilio com a [biblioteca auxiliar do twilio para Ruby][twilio_ruby].

Primeiro, [Configure uma nova VM Linux do Azure][azure_vm_setup] para atuar como um host para seu novo aplicativo Web Ruby. Ignore as etapas que envolvem a criação de um aplicativo Rails, basta configurar a VM. Certifique-se de criar um ponto de extremidade com uma porta externa de 80 e uma porta interna de 5000.

Nos exemplos a seguir, usaremos o [Sinatra][sinatra], uma estrutura da Web muito simples para o Ruby. Mas você certamente pode usar a biblioteca auxiliar do twilio para Ruby com qualquer outra estrutura da Web, incluindo Ruby on Rails.

Use SSH em sua nova VM e crie um diretório para o novo aplicativo. Dentro desse diretório, crie um arquivo chamado Gemfile e copie o seguinte código para ele:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na linha de comando, `bundle install`execute. Isso instalará as dependências acima. Em seguida, crie um `web.rb`arquivo chamado. Isso será onde o código para seu aplicativo Web reside. Cole o código a seguir nele:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Neste ponto, você deve ser capaz de executar o comando `ruby web.rb -p 5000`. Isso fará com que um servidor Web pequeno na porta 5000 seja girado. Você deve ser capaz de navegar até esse aplicativo em seu navegador visitando a URL que você configurou para sua VM do Azure. Depois que você puder acessar seu aplicativo Web no navegador, estará pronto para começar a criar um aplicativo twilio.

## <a id="configure_app"></a>Configurar seu aplicativo para usar o twilio
Você pode configurar seu aplicativo Web para usar a biblioteca twilio atualizando o `Gemfile` para incluir esta linha:

    gem 'twilio-ruby'

Na linha de comando, execute `bundle install`. Agora, `web.rb` abra e inclua essa linha na parte superior:

    require 'twilio-ruby'

Agora você está pronto para usar a biblioteca auxiliar do twilio para Ruby em seu aplicativo Web.

## <a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída. Os conceitos principais incluem o uso da biblioteca auxiliar do twilio para Ruby para fazer chamadas à API REST e renderizar TwiML. Substitua os valores dos números **de telefone de** e **para** e verifique se você verificou o número **de** telefone de sua conta do twilio antes de executar o código.

Adicione esta função a `web.md`:

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

Se você abrir `http://yourdomain.cloudapp.net/make_call` em um navegador, isso irá disparar a chamada para a API twilio para fazer a chamada telefônica. Os dois primeiros parâmetros no `client.account.calls.create` são bastante auto-explicativos: o número da chamada é `from` e o número da chamada é `to`. 

O terceiro parâmetro (`url`) é a URL que o twilio solicita para obter instruções sobre o que fazer quando a chamada é conectada. Nesse caso, configuramos uma URL (`http://yourdomain.cloudapp.net`) que retorna um documento simples de TwiML e usa o `<Say>` verbo para fazer alguma conversão de texto em fala e dizer "macaco Hello" para a pessoa que recebe a chamada.

## <a id="howto_receive_sms"></a>Como: Receber uma mensagem SMS
No exemplo anterior, iniciamos uma chamada telefônica de **saída** . Desta vez, vamos usar o número de telefone que o twilio nos forneceu durante a inscrição para processar uma mensagem SMS de **entrada** .

Primeiro, faça logon no seu [painel do twilio][twilio_account]. Clique em "números" na barra de navegação superior e, em seguida, clique no número de twilio que você forneceu. Você verá duas URLs que podem ser configuradas. Uma URL de solicitação de voz e uma URL de solicitação de SMS. Essas são as URLs que o twilio chama sempre que uma chamada telefônica é feita ou um SMS é enviado ao seu número. As URLs também são conhecidas como "ganchos da Web".

Gostaríamos de processar mensagens SMS de entrada, então, vamos atualizar a URL para `http://yourdomain.cloudapp.net/sms_url`. Vá em frente e clique em salvar alterações na parte inferior da página. Agora, de `web.rb` volta, vamos programar nosso aplicativo para lidar com isso:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Depois de fazer a alteração, certifique-se de reiniciar seu aplicativo Web. Agora, retire seu telefone e envie um SMS para seu número twilio. Você deve receber imediatamente uma resposta SMS que diz "Ei, obrigado pelo ping! Twilio e Azure rock! ".

## <a id="additional_services"></a>Como: Usar serviços Twilios adicionais
Além dos exemplos mostrados aqui, o twilio oferece APIs baseadas na Web que você pode usar para aproveitar a funcionalidade adicional do twilio do seu aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API do twilio][twilio_api_documentation].

### <a id="NextSteps"></a>Passos Seguintes
Agora que você aprendeu os conceitos básicos do serviço twilio, siga estes links para saber mais:

* [Diretrizes de segurança do twilio][twilio_security_guidelines]
* [Twilio HowTo e código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do twilio][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Fale com o suporte do twilio][twilio_support]

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
