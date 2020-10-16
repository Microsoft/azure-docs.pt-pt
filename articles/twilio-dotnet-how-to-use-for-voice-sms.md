---
title: Como usar twilio para voz e SMS (.NET) Microsoft Docs
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço API Twilio em Azure. Amostras de código escritas em .NET.
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
ms.custom: devx-track-dotnet
ms.openlocfilehash: 104f969f5e27ef36ad43eb10e19176a4bcfd6648
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021141"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Como usar twilio para capacidades de voz e SMS do Azure
Este guia demonstra como executar tarefas de programação comuns com o serviço Twilio API em Azure. Os cenários abrangidos incluem fazer uma chamada telefónica e enviar uma mensagem do Serviço de Mensagens Curtas (SMS). Para obter mais informações sobre Twilio e utilizando voz e SMS nas suas aplicações, consulte a secção [etapas seguintes.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>O que é Twilio?
A Twilio está a alimentar o futuro das comunicações empresariais, permitindo aos desenvolvedores incorporarem voz, VoIP e mensagens em aplicações. Virtualizam todas as infraestruturas necessárias num ambiente global baseado na nuvem, expondo-a através da plataforma API de comunicações Twilio. As aplicações são simples de construir e escaláveis. Desfrute da flexibilidade com preços pay-as-you-go e beneficie da fiabilidade da nuvem.

**O Twilio Voice** permite que as suas aplicações façam e recebam chamadas telefónicas. **O Twilio SMS** permite que as suas aplicações enviem e recebam mensagens SMS. **O Cliente Twilio** permite-lhe fazer chamadas VoIP a partir de qualquer telefone, tablet ou navegador e suporta o WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Preços twilio e ofertas especiais
Os clientes Azure recebem uma [oferta especial:](https://www.twilio.com/azure)$10 de Crédito Twilio quando atualiza a sua Conta Twilio. Este Crédito Twilio pode ser aplicado a qualquer utilização de Twilio (crédito de $10 equivalente ao envio de até 1.000 mensagens SMS ou receber até 1000 minutos de voz de entrada, dependendo da localização do seu número de telefone e mensagem ou destino de chamada). Resgatar este crédito Twilio e começar a [twilio.com/azure.](https://twilio.com/azure)

Twilio é um serviço de pagamento. Não há taxas de configuração, e pode fechar a sua conta a qualquer momento. Pode encontrar mais detalhes na [Twilio Pricing.](https://www.twilio.com/voice/pricing)

## <a name="concepts"></a><a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece funcionalidade de voz e SMS para aplicações. As bibliotecas de clientes estão disponíveis em vários idiomas; para uma lista, consulte as [Bibliotecas Twilio API][twilio_libraries].

Os principais aspetos da API de Twilio são os verbos Twilio e a Língua de Marcação Twilio (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Verbos twilio
A API faz uso de verbos twilio; por exemplo, o verbo ** &lt; Say &gt; ** instrui Twilio a entregar uma mensagem audivelmente numa chamada.

Segue-se uma lista de verbos de Twilio.  Saiba mais sobre os outros verbos e capacidades através da [documentação da linguagem Twilio Markup](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Liga o chamador a outro telefone.
* `<Gather>`: Recolhe dígitos numéricos introduzidos no teclado do telefone.
* `<Hangup>`: Termina uma chamada.
* `<Play>`: Reproduz um ficheiro áudio.
* `<Pause>`: Aguarde silenciosamente um número especificado de segundos.
* `<Record>`: Grava a voz do ouvinte e devolve um URL de um ficheiro que contém a gravação.
* `<Redirect>`: Transfere o controlo de uma chamada ou SMS para o TwiML num URL diferente.
* `<Reject>`: Rejeita uma chamada recebida para o seu número Twilio sem o cobrar
* `<Say>`: Converte texto para discurso que é feito numa chamada.
* `<Sms>`: Envia uma mensagem SMS.

### <a name="twiml"></a>TWIML
TwiML é um conjunto de instruções baseadas em XML com base nos verbos Twilio que informam Twilio de como processar uma chamada ou SMS.

Como exemplo, o seguinte TwiML converteria o texto **Hello World** em discurso.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Quando a sua aplicação chama a API Twilio, um dos parâmetros da API é o URL que devolve a resposta TwiML. Para fins de desenvolvimento, pode utilizar URLs fornecidos pela Twilio para fornecer as respostas TwiML utilizadas pelas suas aplicações. Também pode hospedar os seus próprios URLs para produzir as respostas TwiML, e outra opção é usar o objeto **TwiMLResponse.**

Para obter mais informações sobre verbos Twilio, seus atributos, e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [a API Twilio.][twilio_api]

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Criar uma Conta Twilio
Quando estiver pronto para obter uma conta Twilio, inscreva-se na [Try Twilio.][try_twilio] Pode começar com uma conta gratuita e atualizar a sua conta mais tarde.

Quando se inscreve numa conta Twilio, receberá uma identificação de conta e um token de autenticação. Ambos serão necessários para fazer chamadas da Twilio API. Para evitar o acesso não autorizado à sua conta, mantenha o seu símbolo de autenticação seguro. O seu ID de conta e token de autenticação são visualizados na página da [conta Twilio,][twilio_account]nos campos **marcados COMO CONTA SID** e **AUTH TOKEN,** respectivamente.

## <a name="create-an-azure-application"></a><a id="create_app"></a>Criar uma aplicação Azure
Uma aplicação Azure que acolhe uma aplicação ativada por Twilio não é diferente de qualquer outra aplicação Azure. Adicione a biblioteca Twilio .NET e configuure o papel de utilização das bibliotecas Twilio .NET.
Para obter informações sobre a criação de um projeto Azure inicial, consulte [criar um projeto Azure com o Visual Studio.][vs_project]

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Configure a sua aplicação para utilizar bibliotecas Twilio
Twilio fornece um conjunto de bibliotecas de ajuda .NET que envolvem vários aspetos do Twilio para fornecer formas simples e fáceis de interagir com o Twilio REST API e O Cliente Twilio para gerar respostas TwiML.

Twilio fornece cinco bibliotecas para desenvolvedores .NET:

| Biblioteca | Descrição |
| --- | --- |
| Twilio.API | A biblioteca central de Twilio que envolve a API Twilio REST numa simpática biblioteca .NET. Esta biblioteca está disponível para .NET, Silverlight e Windows Phone 7. |
| Twilio.TwiML | Fornece uma forma amigável .NET de gerar marcação TwiML. |
| Twilio.MVC | Para desenvolvedores que usam ASP.NET MVC, esta biblioteca inclui um TwilioController, TwiML ActionResult e atributo de validação de pedidos. |
| Twilio.WebMatrix | Para desenvolvedores que usam a ferramenta de desenvolvimento webMatrix gratuita da Microsoft, esta biblioteca contém ajudantes de sintaxe Razor para várias ações de Twilio. |
| Twilio.Cliente.Capacidade | Contém o gerador de fichas de capacidade para utilização com o Cliente Twilio JavaScript SDK. |

> [!Important]
> Todas as bibliotecas requerem .NET 3.5, Silverlight 4 ou Windows Phone 7 ou mais tarde.

As amostras fornecidas neste guia utilizam a biblioteca Twilio.API.

As bibliotecas podem ser [instaladas usando a extensão do gestor de pacotes NuGet](https://www.twilio.com/docs/csharp/install) disponível para o Visual Studio 2010 até 2015.  O código fonte está hospedado no [GitHub,][twilio_github_repo]que inclui um Wiki que contém documentação completa para a utilização das bibliotecas.

Por padrão, o Microsoft Visual Studio 2010 instala a versão 1.2 do NuGet. A instalação das bibliotecas Twilio requer a versão 1.6 de NuGet ou superior. Para obter informações sobre a instalação ou atualização do NuGet, consulte [https://nuget.org/][nuget] .

> [!NOTE]
> Para instalar a versão mais recente do NuGet, primeiro tem de desinstalar a versão carregada utilizando o Gestor de Extensão do Estúdio Visual. Para tal, tem de executar o Visual Studio como administrador. Caso contrário, o botão Desinstalar está desativado.
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>Para adicionar as bibliotecas Twilio ao seu projeto Visual Studio:
1. Abra a sua solução no Visual Studio.
2. **Referências**de clique à direita.
3. Clique **em Gerir Pacotes NuGet...**
4. Clique **em 'Online'.**
5. Na caixa online de pesquisa, escreva *twilio.*
6. Clique em **Instalar** no pacote Twilio.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Como: Fazer uma chamada de saída
O seguinte mostra como fazer uma chamada de saída usando a classe **CallResource.** Este código também utiliza um site fornecido por Twilio para devolver a resposta Twilio Markup Language (TwiML). Substitua os seus valores **pelos** números de telefone e certifique-se de que verifica o número de telefone **da** sua conta Twilio antes **de** executar o código.

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

Para obter mais informações sobre os parâmetros transmitidos para o método **CallResource.Create,** consulte [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] .

Como mencionado, este código utiliza um site fornecido por Twilio para devolver a resposta TwiML. Em vez disso, pode utilizar o seu próprio site para fornecer a resposta TwiML. Para mais informações, consulte [Como: Fornecer respostas TwiML a partir do seu próprio site.](#howto_provide_twiml_responses)

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS
A imagem que se segue mostra como enviar uma mensagem SMS utilizando a classe **MessageResource.** O **from** número é fornecido pela Twilio para contas de teste para enviar mensagens SMS. O número deve ser verificado para **a** sua conta Twilio antes de executar o código.

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas TwiML a partir do seu próprio site
Quando a sua aplicação inicia uma chamada para a API Twilio - por exemplo, através do método **CallResource.Create** - Twilio envia o seu pedido para um URL que se espera que devolva uma resposta TwiML. O exemplo em [Como: Fazer uma chamada de saída](#howto_make_call) utiliza o URL fornecido por Twilio para devolver a [https://twimlets.com/message][twimlet_message_url] resposta.

> [!NOTE]
> Enquanto o TwiML é projetado para ser usado por serviços web, você pode ver o TwiML no seu navegador. Por exemplo, clique [https://twimlets.com/message][twimlet_message_url] para ver um elemento `<Response>` vazio; como outro exemplo, clique [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) para ver um elemento que contém um elemento `<Response>` &lt; &gt; Say.
>

Em vez de confiar no URL fornecido por Twilio, pode criar o seu próprio site URL que devolve respostas HTTP. Pode criar o site em qualquer idioma que devolva respostas HTTP. Este tópico pressupõe que você vai hospedar o URL de um manipulador genérico ASP.NET.

O seguinte ASP.NET Handler cria uma resposta TwiML que diz **Hello World** na chamada.

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

Para obter mais informações sobre o TwiML, consulte [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml) .

Uma vez configurado uma forma de fornecer respostas TwiML, pode passar esse URL para o método **CallResource.Create.** Por exemplo, se tiver uma aplicação web chamada MyTwiML implantada num serviço de nuvem Azure, e o nome do seu ASP.NET Handler é mytwiml.ashx, o URL pode ser transmitido para **CallResource.Create** como mostrado na seguinte amostra de código:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Para obter informações adicionais sobre a utilização de Twilio em Azure com ASP.NET, consulte [Como fazer uma chamada telefónica usando Twilio numa função web no Azure][howto_phonecall_dotnet].

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
