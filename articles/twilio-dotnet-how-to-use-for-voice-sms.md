---
title: Como utilizar o Twilio para voz e SMS (.NET) | Documentos da Microsoft
description: Saiba como fazer uma chamada telefónica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de códigos escritos no .NET.
services: ''
documentationcenter: .net
author: devinrader
manager: twilio
editor: ''
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 3b8b21de9664a969e8b1ce5699034aa9ab41d0f1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762899"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Como utilizar o Twilio para voz e capacidades SMS do Azure
Este guia demonstra como realizar tarefas comuns de programação com o serviço de Twilio API no Azure. Os cenários abrangidos incluem uma ligação telefônica e enviar uma mensagem de serviço de mensagens curtas (SMS). Para obter mais informações sobre o Twilio e voz e SMS a utilizar nas suas aplicações, consulte a [próximos passos](#NextSteps) secção.

## <a id="WhatIs"></a>O que é o Twilio?
Twilio é que alimenta o futuro das comunicações empresariais, permitindo que os desenvolvedores incorporar voz, VoIP e de mensagens em aplicativos. Eles virtualizar toda a infraestrutura necessária num ambiente global, com base na cloud, expô-lo através da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Desfrute da flexibilidade com pay as you go, preços e se beneficiar de fiabilidade de cloud.

**Serviços de voz Twilio** permite que seus aplicativos fazer e receber chamadas telefónicas. **Twilio SMS** permite que seus aplicativos enviar e receber mensagens SMS. **Cliente do Twilio** permite-lhe efetuar chamadas VoIP, de qualquer telefone, tablet ou navegador e oferece suporte WebRTC.

## <a id="Pricing"></a>Preços do Twilio e ofertas especiais
Os clientes do Azure recebem uma [oferta especial](https://www.twilio.com/azure): gratuitos US $10 do Twilio crédito quando atualiza a sua conta do Twilio. Este crédito Twilio pode ser aplicado a qualquer utilização do Twilio (crédito de US $10 equivalente ao enviar até 1000 mensagens SMS ou receber até 1000 entrados minutos de voz, consoante a localização do seu destino de número e a mensagem ou a chamada de telefone). Resgatar este crédito do Twilio e comece a utilizar [twilio.com/azure](https://twilio.com/azure).

Twilio é um serviço pay as you go. Existem sem taxas de configuração e pode fechar a sua conta em qualquer altura. Pode encontrar mais detalhes [Twilio preços](https://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful, que fornece funcionalidade SMS e de voz para aplicações. As bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [bibliotecas de APIS do Twilio][twilio_libraries].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação do Twilio (TwiML).

### <a id="Verbs"></a>Verbos de Twilio
A API usa Twilio verbos; Por exemplo, o **&lt;Digamos&gt;** verbo instrui o Twilio para poder ouvi-lo entregar uma mensagem numa chamada.

Segue-se uma lista de verbos Twilio.  Saiba mais sobre os outros verbos e capacidades através de [documentação da linguagem de marcação do Twilio](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Liga-se o autor da chamada para outro telefone.
* `<Gather>`: Recolhe os dígitos numéricos introduzidos no teclado do telefone.
* `<Hangup>`: Termina uma chamada.
* `<Play>`: Reproduz um arquivo de áudio.
* `<Pause>`: Aguarda silenciosamente um número de segundos especificado.
* `<Record>`: Regista a voz do chamador e retorna um URL de um ficheiro que contém a gravação.
* `<Redirect>`: Transfere o controle de uma chamada ou SMS para o TwiML numa URL diferente.
* `<Reject>`: Rejeita uma chamada de entrada para o seu número do Twilio sem de faturação
* `<Say>`: Converte texto em voz, que é efetuado numa chamada.
* `<Sms>`: Envia uma mensagem SMS.

### <a name="twiml"></a>TwiML
TwiML é um conjunto de instruções com base em XML, com base nos verbos de Twilio informam Twilio de como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML seria converter o texto **Olá, mundo** para voz.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Quando seu aplicativo chama a API Twilio, um dos parâmetros de API é o URL que devolve a resposta de TwiML. Para fins de desenvolvimento, pode utilizar URLs fornecidos pelo Twilio para fornecer as respostas de TwiML utilizadas pelas suas aplicações. Também pudesse hospedar suas próprias URLs para produzir as respostas de TwiML e outra opção consiste em utilizar o **TwiMLResponse** objeto.

Para obter mais informações sobre o Twilio verbos, os atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API Twilio, consulte [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do Twilio
Quando estiver pronto para obter uma conta do Twilio, inscreva-se em [tente Twilio][try_twilio]. Pode começar com uma conta gratuita e atualize a sua conta mais tarde.

Quando se inscreve para uma conta do Twilio, receberá um ID de conta e um token de autenticação. Ambos serão necessários para fazer chamadas à API do Twilio. Para impedir acesso não autorizado à sua conta, manter o token de autenticação segura. Sua ID de conta e a autenticação de token são visíveis no [página de conta do Twilio][twilio_account], nos campos nome **SID da conta** e **TOKEN de autenticação de**, respectivamente.

## <a id="create_app"></a>Criar uma aplicação do Azure
Uma aplicação do Azure que aloja uma aplicação Twilio ativada não é diferente de qualquer outra aplicação do Azure. Adicionar a biblioteca .NET do Twilio e configurar a função a utilizar as bibliotecas .NET do Twilio.
Para obter informações sobre como criar um projeto do Azure inicial, consulte [criar um projeto do Azure com o Visual Studio][vs_project].

## <a id="configure_app"></a>Configurar a sua aplicação utilizar bibliotecas do Twilio
Twilio fornece um conjunto de bibliotecas de programa auxiliar de .NET que encapsulam vários aspectos do Twilio para fornecer formas simples e fácil de interagir com a API de REST do Twilio e o cliente do Twilio para gerar TwiML respostas.

Twilio dispõe de bibliotecas de cinco para programadores .NET:

| Biblioteca | Descrição |
| --- | --- |
| Twilio.API | A biblioteca principal do Twilio que encapsula a API de REST do Twilio numa biblioteca .NET amigável. Esta biblioteca está disponível para .NET, o Silverlight e Windows Phone 7. |
| Twilio.TwiML | Fornece uma forma amigável do .NET para gerar a marcação de TwiML. |
| Twilio.MVC | Para desenvolvedores que usam o ASP.NET MVC, esta biblioteca inclui um TwilioController, TwiML ActionResult e o atributo de validação do pedido. |
| Twilio.WebMatrix | Para os desenvolvedores usando a ferramenta de desenvolvimento de WebMatrix gratuita da Microsoft, esta biblioteca contém auxiliares de sintaxe do Razor para várias ações do Twilio. |
| Twilio.Client.Capability | Contém o gerador do token de recurso para utilização com o JavaScript SDK de cliente do Twilio. |

> [!Important]
> Todas as bibliotecas requerem o .NET 3.5, o Silverlight 4 ou o Windows Phone 7 ou posterior.

Os exemplos fornecidos neste guia, utilize a biblioteca de Twilio.API.

As bibliotecas podem ser [instalado usando a extensão de Gestor de pacote NuGet](https://www.twilio.com/docs/csharp/install) disponíveis para o Visual Studio 2010 até 2015.  O código-fonte está alojado numa [GitHub][twilio_github_repo], que inclui um Wiki que contém a documentação completa para utilizar as bibliotecas.

Por predefinição, o Microsoft Visual Studio 2010 instala a versão 1.2 do NuGet. Instalar as bibliotecas do Twilio requer a versão 1.6 do NuGet ou superior. Para informações sobre como instalar ou atualizar o NuGet, consulte [ https://nuget.org/ ] [ nuget].

> [!NOTE]
> Para instalar a versão mais recente do NuGet, primeiro tem de desinstalar a versão de carregá-lo utilizando o Gestor de extensões do Visual Studio. Para tal, tem de executar o Visual Studio como administrador. Caso contrário, o botão de desinstalação está desativado.
>
>

### <a id="use_nuget"></a>Para adicionar as bibliotecas do Twilio para seu projeto do Visual Studio:
1. Abra a solução no Visual Studio.
2. Com o botão direito **referências**.
3. Clique em **gerir pacotes NuGet...**
4. Clique em **Online**.
5. Na caixa de pesquisa online, escreva *twilio*.
6. Clique em **instalar** no pacote do Twilio.

## <a id="howto_make_call"></a>Como: Efetuar uma chamada de saída
O seguinte mostra como tornar uma saída chamar utilizando o **CallResource** classe. Esse código também utiliza um site fornecida pelo Twilio para retornar a resposta de linguagem de marcação do Twilio (TwiML). Substitua os valores para o **para** e **partir** números de telefone e certifique-se de que verifique o **de** número de telefone para sua conta do Twilio antes de executar o código.

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

Para obter mais informações sobre os parâmetros transmitidos para o **CallResource.Create** método, consulte [ https://www.twilio.com/docs/api/rest/making-calls ] [ twilio_rest_making_calls].

Conforme mencionado, esse código usa um site fornecida pelo Twilio para retornar a resposta de TwiML. Em vez disso, poderia usar seu próprio site para fornecer a resposta de TwiML. Para obter mais informações, consulte [como: Fornecer respostas TwiML do seu próprio Web site](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como: Envie uma mensagem SMS
Captura de ecrã seguinte mostra como enviar uma mensagem SMS através do **MessageResource** classe. O **partir** número é fornecido pela Twilio para contas de avaliação para enviar mensagens SMS. O **para** número deve ser verificado para a sua conta do Twilio, antes de executar o código.

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

## <a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas TwiML do seu próprio Web site
Quando a aplicação inicia uma chamada para a API Twilio - por exemplo, através da **CallResource.Create** método - Twilio envia o pedido para um URL que deverá devolver uma resposta de TwiML. O exemplo na [como: Efetuar uma chamada de saída](#howto_make_call) utiliza o URL fornecido pelo Twilio [ https://twimlets.com/message ] [ twimlet_message_url] para retornar a resposta.

> [!NOTE]
> Apesar de TwiML foi concebido para utilização pelos serviços web, pode ver o TwiML no seu browser. Por exemplo, clique em [ https://twimlets.com/message ] [ twimlet_message_url] para ver vazio `<Response>` elemento; como outro exemplo, clique em [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ](https://twimlets.com/message?Message%5B0%5D=Hello%20World) para ver um `<Response>` elemento que contém um &lt;Digamos&gt; elemento.
>

Em vez de contar com o URL fornecido pelo Twilio, pode criar seu próprio site URL que devolve as respostas HTTP. É possível criar o site em qualquer linguagem que retorna as respostas HTTP. Este tópico parte do princípio de que irá alojar o URL a partir de um manipulador de genérico do ASP.NET.

O seguinte processador do ASP.NET cria uma resposta de TwiML diz **Olá, mundo** na chamada.

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
    
Como pode ver no exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca de Twilio.TwiML contém classes que irão gerar TwiML para. O exemplo a seguir produz a resposta equivalente, conforme mostrado acima, mas utiliza a **VoiceResponse** classe.

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

Para obter mais informações sobre TwiML, consulte [ https://www.twilio.com/docs/api/twiml ](https://www.twilio.com/docs/api/twiml).

Assim que tiver configurado a uma forma de fornecer respostas TwiML, pode passar esse URL para o **CallResource.Create** método. Por exemplo, se tiver uma aplicação web com o nome MyTwiML implementada para um serviço cloud do Azure e o nome do seu manipulador do ASP.NET é mytwiml.ashx, o URL pode ser passado para **CallResource.Create** conforme mostrado no exemplo de código a seguir:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Para obter mais informações sobre como utilizar o Twilio no Azure com o ASP.NET, consulte [como fazer uma chamada telefónica através do Twilio numa função da web no Azure][howto_phonecall_dotnet].

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
