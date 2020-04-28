---
title: Como usar o Twilio para voz e SMS (.NET) [ Microsoft Docs
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço Twilio API no Azure. Amostras de código escritas em .NET.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69876811"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Como usar twilio para voz e capacidades de SMS do Azure
Este guia demonstra como executar tarefas comuns de programação com o serviço Twilio API no Azure. Os cenários abordados incluem fazer uma chamada telefónica e enviar uma mensagem de Serviço de Mensagens Curtas (SMS). Para obter mais informações sobre twilio e usando voz e SMS nas suas aplicações, consulte a secção [Degraus Seguintes.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>O que é Twilio?
A Twilio está a potenciar o futuro das comunicações empresariais, permitindo aos desenvolvedores incorporarvoz, VoIP e mensagens em aplicações. Virtualizam todas as infraestruturas necessárias num ambiente global baseado em nuvem, expondo-a através da plataforma API de comunicações Twilio. As aplicações são simples de construir e escaláveis. Desfrute da flexibilidade com os preços pay-as-you-go, e beneficie da fiabilidade da nuvem.

**A Twilio Voice** permite que as suas aplicações façam e recebam chamadas telefónicas. **O SMS Twilio** permite que as suas aplicações enviem e recebam mensagens SMS. **O Cliente Twilio** permite-lhe fazer chamadas VoIP de qualquer telefone, tablet ou navegador e suporta webRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Preços twilio e ofertas especiais
Os clientes azure recebem uma [oferta especial:](https://www.twilio.com/azure)$10 gratuitos de Crédito Twilio ao atualizar a sua Conta Twilio. Este Crédito Twilio pode ser aplicado a qualquer utilização twilio (crédito de $10 equivalente a envio de até 1.000 mensagens SMS ou receber até 1000 minutos de voz de entrada, dependendo da localização do seu número de telefone e mensagem ou destino de chamada). Resgatar este crédito Twilio e começar a [twilio.com/azure.](https://twilio.com/azure)

Twilio é um serviço de pagamento. Não há taxas de configuração, e pode fechar a sua conta a qualquer momento. Pode encontrar mais detalhes no [Preço twilio.](https://www.twilio.com/voice/pricing)

## <a name="concepts"></a><a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece funcionalidade de voz e SMS para aplicações. As bibliotecas de clientes estão disponíveis em vários idiomas; para uma lista, consulte [bibliotecas Twilio API][twilio_libraries].

Os aspectos-chave da API Twilio são os verbos Twilio e a Linguagem Twilio Markup (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Verbos twilio
A API utiliza os verbos Twilio; por exemplo, o ** &lt;verbo Say&gt; ** instrui Twilio a entregar uma mensagem audível numa chamada.

Segue-se uma lista de verbos Twilio.  Conheça os outros verbos e capacidades através da [documentação da Linguagem Twilio Markup](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Liga o chamador a outro telefone.
* `<Gather>`: Recolhe os dígitos numéricos introduzidos no teclado do telefone.
* `<Hangup>`: Termina uma chamada.
* `<Play>`: Reproduz um ficheiro áudio.
* `<Pause>`: Aguarda silenciosamente um número determinado de segundos.
* `<Record>`: Grava a voz do autor da chamada e devolve um URL de um ficheiro que contém a gravação.
* `<Redirect>`: Transfere o controlo de uma chamada ou SMS para o TwiML num URL diferente.
* `<Reject>`: Rejeita uma chamada para o seu número Twilio sem cobrar
* `<Say>`: Converte texto em discurso que é feito numa chamada.
* `<Sms>`: Envia uma mensagem SMS.

### <a name="twiml"></a>TwiML
TwiML é um conjunto de instruções baseadas em XML baseadas nos verbos Twilio que informam Twilio de como processar uma chamada ou SMS.

Como exemplo, o seguinte TwiML converteria o texto **Hello World** em discurso.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Quando a sua aplicação chama a API Twilio, um dos parâmetros DaPI é o URL que devolve a resposta TwiML. Para fins de desenvolvimento, pode utilizar URLs fornecidos pela Twilio para fornecer as respostas TwiML utilizadas pelas suas aplicações. Também pode alojar os seus próprios URLs para produzir as respostas TwiML, e outra opção é utilizar o objeto **TwiMLResponse.**

Para mais informações sobre os verbos Twilio, os seus atributos, e o TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [a Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Criar uma conta Twilio
Quando estiver pronto para obter uma conta Twilio, inscreva-se no [Try Twilio.][try_twilio] Pode começar com uma conta gratuita e fazer upgrade da sua conta mais tarde.

Quando se inscrever numa conta Twilio, receberá uma identificação da conta e um símbolo de autenticação. Ambos serão necessários para fazer chamadas da API twilio. Para evitar o acesso não autorizado à sua conta, mantenha o seu símbolo de autenticação seguro. O id da sua conta e o token de autenticação são visualizados na página da [conta Twilio,][twilio_account]nos campos rotulados **DE IS e** **Token AUTH,** respectivamente.

## <a name="create-an-azure-application"></a><a id="create_app"></a>Criar uma aplicação Azure
Uma aplicação Azure que acolhe uma aplicação ativada por Twilio não é diferente de qualquer outra aplicação Azure. Adicione a biblioteca Twilio .NET e configure a função para utilizar as bibliotecas Twilio .NET.
Para obter informações sobre a criação de um projeto Azure inicial, consulte [Criar um projeto Azure com o Visual Studio.][vs_project]

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Configure a sua aplicação para utilizar bibliotecas Twilio
Twilio fornece um conjunto de bibliotecas de ajudantes .NET que envolvem vários aspetos da Twilio para fornecer formas simples e fáceis de interagir com a Twilio REST API e Twilio Client para gerar respostas TwiML.

Twilio fornece cinco bibliotecas para desenvolvedores .NET:

| Biblioteca | Descrição |
| --- | --- |
| Twilio.API | A biblioteca central de Twilio que envolve a Twilio REST API numa biblioteca amigável .NET. Esta biblioteca está disponível para .NET, Silverlight e Windows Phone 7. |
| Twilio.TwiML | Fornece uma forma amigável de .NET para gerar marcação TwiML. |
| Twilio.MVC | Para desenvolvedores que usam ASP.NET MVC, esta biblioteca inclui um TwilioController, TwiML ActionResult e atributo de validação de pedidos. |
| Twilio.WebMatrix | Para os desenvolvedores que usam a ferramenta de desenvolvimento gratuita da Microsoft WebMatrix, esta biblioteca contém ajudantes de sintaxe Razor para várias ações de Twilio. |
| Twilio.Client.Capability | Contém o gerador de token Capability para utilização com o Cliente Twilio JavaScript SDK. |

> [!Important]
> Todas as bibliotecas requerem .NET 3.5, Silverlight 4 ou Windows Phone 7 ou posteriormente.

As amostras fornecidas neste guia utilizam a biblioteca Twilio.API.

As bibliotecas podem ser [instaladas utilizando a extensão do gestor de pacotes NuGet](https://www.twilio.com/docs/csharp/install) disponível para visual studio 2010 até 2015.  O código fonte está hospedado no [GitHub,][twilio_github_repo]que inclui um Wiki que contém documentação completa para a utilização das bibliotecas.

Por padrão, o Microsoft Visual Studio 2010 instala a versão 1.2 do NuGet. A instalação das bibliotecas Twilio requer a versão 1.6 de NuGet ou superior. Para obter informações sobre a [https://nuget.org/][nuget]instalação ou atualização do NuGet, consulte .

> [!NOTE]
> Para instalar a versão mais recente do NuGet, primeiro tem de desinstalar a versão carregada utilizando o Visual Studio Extension Manager. Para tal, tem de gerir o Visual Studio como administrador. Caso contrário, o botão Desinstalar é desativado.
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>Para adicionar as bibliotecas Twilio ao seu projeto Visual Studio:
1. Abra a sua solução no Estúdio Visual.
2. **Referências**do clique direito .
3. Clique em **gerir pacotes NuGet...**
4. Clique **em Online**.
5. Na caixa online de pesquisa, *digite twilio*.
6. Clique em **Instalar** no pacote Twilio.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída utilizando a classe **CallResource.** Este código também utiliza um site fornecido por Twilio para devolver a resposta da Linguagem De Marcação Twilio (TwiML). Substitua os seus valores pelos números de telefone e de **e para** os números **de** telefone e certifique-se de que verifica o número de telefone **da** sua conta Twilio antes de executar o código.

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

Para obter mais informações sobre os parâmetros transmitidos [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]para o método **CallResource.Create,** consulte .

Como mencionado, este código utiliza um site fornecido por Twilio para devolver a resposta TwiML. Em vez disso, poderia utilizar o seu próprio site para fornecer a resposta TwiML. Para mais informações, consulte [Como: Fornecer respostas TwiML a partir do seu próprio website](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS
A imagem que se segue mostra como enviar uma mensagem SMS utilizando a classe **MessageResource.** O número **a partir** é fornecido pela Twilio para contas de teste para enviar mensagens SMS. O número **a** número deve ser verificado para a sua conta Twilio antes de executar o código.

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Como: Fornecer Respostas TwiML a partir do seu próprio site
Quando a sua aplicação inicia uma chamada para a API Twilio - por exemplo, através do método **CallResource.Create** - a Twilio envia o seu pedido para um URL que deverá devolver uma resposta TwiML. O exemplo em [Como: Fazer uma chamada de saída](#howto_make_call) [https://twimlets.com/message][twimlet_message_url] utiliza o URL fornecido por Twilio para devolver a resposta.

> [!NOTE]
> Enquanto o TwiML é projetado para ser utilizado por serviços web, pode ver o TwiML no seu navegador. Por exemplo, [https://twimlets.com/message][twimlet_message_url] clique para `<Response>` ver um elemento vazio; como outro exemplo, [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) clique `<Response>` para ver &lt;&gt; um elemento que contenha um elemento Say.
>

Em vez de confiar no URL fornecido pelo Twilio, pode criar o seu próprio site de URL que devolve respostas http. Pode criar o site em qualquer idioma que derreta respostas HTTP. Este tópico pressupõe que você vai hospedar o URL de um manipulador genérico ASP.NET.

O seguinte ASP.NET Handler faz uma resposta TwiML que diz **Hello World** na chamada.

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
    
Como pode ver pelo exemplo acima, a resposta TwiML é simplesmente um documento XML. A biblioteca Twilio.TwiML contém aulas que gerarão TwiML para si. O exemplo abaixo produz a resposta equivalente como mostrado acima, mas utiliza a classe **VoiceResponse.**

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

Para mais informações sobre [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml)o TwiML, consulte .

Uma vez configurado uma forma de fornecer respostas TwiML, pode passar esse URL para o método **CallResource.Create.** Por exemplo, se tiver uma aplicação web chamada MyTwiML implantada para um serviço de nuvem Azure, e o nome do seu ASP.NET Handler é mytwiml.ashx, o URL pode ser passado para **CallResource.Create** como mostrado na seguinte amostra de código:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Para obter informações adicionais sobre a utilização de Twilio em Azure com ASP.NET, consulte [como fazer uma chamada telefónica usando Twilio num papel web no Azure.][howto_phonecall_dotnet]

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
