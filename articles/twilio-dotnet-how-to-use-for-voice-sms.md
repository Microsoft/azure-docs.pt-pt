---
title: Como usar o twilio para voz e SMS (.NET) | Microsoft Docs
description: Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do twilio no Azure. Exemplos de código escritos em .NET.
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.openlocfilehash: 22b33d7b4b0ff69a2e751cadff70453f73ed4f8e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876811"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Como usar o twilio para recursos de voz e SMS do Azure
Este guia demonstra como executar tarefas comuns de programação com o serviço de API do twilio no Azure. Os cenários abordados incluem fazer uma chamada telefônica e enviar uma mensagem SMS (Short Message Service). Para obter mais informações sobre o twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [próximas etapas](#NextSteps) .

## <a id="WhatIs"></a>O que é o twilio?
O twilio está ligando o futuro das comunicações de negócios, permitindo que os desenvolvedores insiram voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infraestrutura necessária em um ambiente global baseado em nuvem, expondo-a por meio da plataforma de API de comunicações do twilio. Os aplicativos são simples de criar e escalonáveis. Aproveite a flexibilidade com os preços pagos conforme o uso e beneficie-se da confiabilidade da nuvem.

O **twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. O **TWILIO SMS** permite que seus aplicativos enviem e recebam mensagens SMS. O **cliente twilio** permite fazer chamadas VoIP de qualquer telefone, Tablet ou navegador e dá suporte a WebRTC.

## <a id="Pricing"></a>Preços e ofertas especiais do twilio
Os clientes do Azure recebem uma [oferta especial](https://www.twilio.com/azure): grátis $10 de crédito twilio ao atualizar sua conta do twilio. Esse crédito twilio pode ser aplicado a qualquer uso de twilio ($10 de crédito equivalente a envio de até 1.000 mensagens SMS ou recebimento de até 1000 minutos de voz de entrada, dependendo do local do seu número de telefone e da mensagem ou do destino da chamada). Resgate este crédito de twilio e comece a usar o [twilio.com/Azure](https://twilio.com/azure).

Twilio é um serviço pago conforme o uso. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [preços de twilio](https://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Principais
A API twilio é uma API RESTful que fornece a funcionalidade de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas; para obter uma lista, consulte [bibliotecas de API do twilio][twilio_libraries].

Os principais aspectos da API twilio são twilio verbos e TwiML (twilio Markup Language).

### <a id="Verbs"></a>Verbos twilio
A API utiliza verbos twilio; por exemplo, o  **&lt;verbo&gt; digamos** instrui o twilio a forma audível a entregar uma mensagem em uma chamada.

Veja a seguir uma lista de verbos do twilio.  Saiba mais sobre os outros verbos e funcionalidades por meio da [documentação da linguagem de marcação twilio](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Conecta o chamador a outro telefone.
* `<Gather>`: Coleta dígitos numéricos inseridos no teclado do telefone.
* `<Hangup>`: Termina uma chamada.
* `<Play>`: Reproduz um arquivo de áudio.
* `<Pause>`: Aguarda silenciosamente por um número especificado de segundos.
* `<Record>`: Registra a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* `<Redirect>`: Transfere o controle de uma chamada ou SMS para o TwiML em uma URL diferente.
* `<Reject>`: Rejeita uma chamada recebida para o número do twilio sem cobrança
* `<Say>`: Converte o texto em fala que é feito em uma chamada.
* `<Sms>`: Envia uma mensagem SMS.

### <a name="twiml"></a>TwiML
TwiML é um conjunto de instruções baseadas em XML com base nos verbos twilio que informam twilio de como processar uma chamada ou SMS.

Como exemplo, o TwiML a seguir converterá o texto **Olá, mundo** em fala.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Quando o aplicativo chama a API twilio, um dos parâmetros de API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidas pelo twilio para fornecer as respostas do TwiML usadas por seus aplicativos. Você também pode hospedar suas próprias URLs para produzir as respostas TwiML e outra opção é usar o objeto **TwiMLResponse** .

Para obter mais informações sobre verbos twilio, seus atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API do twilio, consulte [API do twilio][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do twilio
Quando estiver pronto para obter uma conta do twilio, Inscreva-se em [experimentar o twilio][try_twilio]. Você pode começar com uma conta gratuita e atualizar sua conta mais tarde.

Ao se inscrever em uma conta do twilio, você receberá uma ID de conta e um token de autenticação. Ambos serão necessários para fazer chamadas à API do twilio. Para impedir o acesso não autorizado à sua conta, mantenha seu token de autenticação seguro. A ID da conta e o token de autenticação são visíveis na [página da conta do twilio][twilio_account], nos campos ROTULAdos SID da **conta** e **token de autenticação**, respectivamente.

## <a id="create_app"></a>Criar um Aplicativo Azure
Um aplicativo do Azure que hospeda um aplicativo habilitado para twilio não é diferente de qualquer outro aplicativo do Azure. Você adiciona a biblioteca do .NET twilio e configura a função para usar as bibliotecas .NET do twilio.
Para obter informações sobre como criar um projeto inicial do Azure, consulte [criando um projeto do Azure com o Visual Studio][vs_project].

## <a id="configure_app"></a>Configurar seu aplicativo para usar bibliotecas twilio
O twilio fornece um conjunto de bibliotecas auxiliares .NET que encapsulam vários aspectos de twilio para fornecer maneiras simples e fáceis de interagir com a API REST do twilio e o cliente twilio para gerar respostas TwiML.

O twilio fornece cinco bibliotecas para desenvolvedores .NET:

| Biblioteca | Descrição |
| --- | --- |
| Twilio. API | A biblioteca twilio básica que encapsula a API REST do twilio em uma biblioteca .NET amigável. Essa biblioteca está disponível para .NET, Silverlight e Windows Phone 7. |
| Twilio.TwiML | Fornece uma maneira amigável do .NET para gerar marcação TwiML. |
| Twilio. MVC | Para os desenvolvedores que usam o ASP.NET MVC, essa biblioteca inclui um TwilioController, TwiML ActionResult e o atributo de validação de solicitação. |
| Twilio.WebMatrix | Para os desenvolvedores que usam a ferramenta de desenvolvimento do WebMatrix gratuita da Microsoft, essa biblioteca contém sintaxe Razor auxiliares para várias ações de twilio. |
| Twilio.Client.Capability | Contém o gerador de token de funcionalidade para uso com o SDK de JavaScript do cliente twilio. |

> [!Important]
> Todas as bibliotecas exigem o .NET 3,5, o Silverlight 4 ou o Windows Phone 7 ou posterior.

Os exemplos fornecidos neste guia usam a biblioteca twilio. API.

As bibliotecas podem ser [instaladas usando a extensão do Gerenciador de pacotes NuGet](https://www.twilio.com/docs/csharp/install) disponível para o Visual Studio 2010 até 2015.  O código-fonte é hospedado no [GitHub][twilio_github_repo], que inclui um wiki que contém a documentação completa para usar as bibliotecas.

Por padrão, Microsoft Visual Studio 2010 instala a versão 1,2 do NuGet. A instalação das bibliotecas twilio requer a versão 1,6 do NuGet ou superior. Para obter informações sobre como instalar ou atualizar o [https://nuget.org/][nuget]NuGet, consulte.

> [!NOTE]
> Para instalar a versão mais recente do NuGet, você deve primeiro desinstalar a versão carregada usando o Gerenciador de extensões do Visual Studio. Para fazer isso, você deve executar o Visual Studio como administrador. Caso contrário, o botão desinstalar será desabilitado.
>
>

### <a id="use_nuget"></a>Para adicionar as bibliotecas twilio ao seu projeto do Visual Studio:
1. Abra sua solução no Visual Studio.
2. Clique com o botão direito do mouse em **referências**.
3. Clique em **gerenciar pacotes NuGet...**
4. Clique em **online**.
5. Na caixa Pesquisar online, digite *twilio*.
6. Clique em **instalar** no pacote twilio.

## <a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada externa usando a classe **CallResource** . Esse código também usa um site fornecido pelo twilio para retornar a resposta TwiML (twilio Markup Language). Substitua os valores para os números de telefone de e **para** e certifique-se de verificar o número **de telefone de** para sua conta do twilio antes de executar o código.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

Para obter mais informações sobre os parâmetros passados para o método **CallResource. Create** , consulte [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Conforme mencionado, esse código usa um site fornecido pelo twilio para retornar a resposta TwiML. Em vez disso, você poderia usar seu próprio site para fornecer a resposta TwiML. Para obter mais informações, consulte [como: Forneça respostas de TwiML de seu próprio](#howto_provide_twiml_responses)site.

## <a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS
A captura de tela a seguir mostra como enviar uma mensagem SMS usando a classe **MessageResource** . O número **de** é fornecido pelo twilio para contas de avaliação para enviar mensagens SMS. O número **de para** deve ser verificado para sua conta do twilio antes de executar o código.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

try
{
    // Send an SMS message.
    var message = MessageResource.Create(
        to: new PhoneNumber("+12069419717"),
        from: new PhoneNumber("+14155992671"),
        body: "This is my SMS message.");
}
catch (TwilioException ex)
{
    // An exception occurred making the REST call
    Console.WriteLine(ex.Message);
}
```

## <a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas de TwiML de seu próprio site
Quando seu aplicativo inicia uma chamada para a API twilio, por exemplo, por meio do método **CallResource. Create** -twilio envia sua solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo de [como: Fazer uma chamada](#howto_make_call) de saída usa a URL [https://twimlets.com/message][twimlet_message_url] fornecida pelo twilio para retornar a resposta.

> [!NOTE]
> Embora o TwiML seja projetado para ser usado pelos serviços Web, você pode exibir o TwiML em seu navegador. Por exemplo, clique [https://twimlets.com/message][twimlet_message_url] para ver um elemento `<Response>` vazio; como outro exemplo, clique [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) para ver um `<Response>` elemento que contém um &lt; elemento&gt; de digamos.
>

Em vez de depender da URL fornecida pelo twilio, você pode criar seu próprio site de URL que retorna respostas HTTP. Você pode criar o site em qualquer idioma que retorne respostas HTTP. Este tópico pressupõe que você hospedará a URL de um manipulador genérico ASP.NET.

O manipulador ASP.NET a seguir mostra uma resposta TwiML que diz **Olá, mundo** na chamada.

```csharp
using System.Text;
using System.Web;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {
        public void ProcessRequest(HttpContext context)
        {
            const string twiMLResponse =
                "<Response><Say>Hello World.</Say></Response>";

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.ContentEncoding = Encoding.UTF8;
            context.Response.Write(twiMLResponse);
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```
    
Como você pode ver no exemplo acima, a resposta TwiML é simplesmente um documento XML. A biblioteca twilio. TwiML contém classes que irão gerar TwiML para você. O exemplo a seguir produz a resposta equivalente conforme mostrado acima, mas usa a classe **VoiceResponse** .

```csharp
using System.Web;
using Twilio.TwiML;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {

        public void ProcessRequest(HttpContext context)
        {
            var twiml = new VoiceResponse();
            twiml.Say("Hello World.");

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.Write(twiml.ToString());
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

Para obter mais informações sobre TwiML, [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml)consulte.

Depois de configurar uma maneira de fornecer respostas TwiML, você pode passar essa URL para o método **CallResource. Create** . Por exemplo, se você tiver um aplicativo Web chamado MyTwiML implantado em um serviço de nuvem do Azure e o nome do manipulador ASP.NET for MyTwiML. ashx, a URL poderá ser passada para **CallResource. Create** , conforme mostrado no exemplo de código a seguir:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Para obter informações adicionais sobre como usar o twilio no Azure com ASP.NET, consulte [como fazer uma chamada telefônica usando o twilio em uma função Web no Azure][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
