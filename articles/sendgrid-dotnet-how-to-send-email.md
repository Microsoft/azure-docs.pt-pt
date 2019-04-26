---
title: Como utilizar o serviço de correio eletrónico do SendGrid (.NET) | Documentos da Microsoft
description: Saiba como enviar um e-mail com o serviço de correio eletrónico do SendGrid no Azure. Exemplos de código escrito em c# e utilizam a API .NET.
services: ''
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
ms.openlocfilehash: 91d28802b4af23da5b8060fa7c8f9a7e843a7dab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444884"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Como enviar E-mails com o SendGrid com o Azure
## <a name="overview"></a>Descrição geral
Este guia demonstra como realizar tarefas comuns de programação com o serviço de correio eletrónico do SendGrid no Azure. Os exemplos são escritos em C\# e suporta 1.3 padrão do .NET. Os cenários abrangidos incluem a construção de e-mail, o envio de e-mail, adicionar anexos e permitindo que vários emails e definições de registo. Para obter mais informações sobre o SendGrid e o envio de e-mail, consulte a [passos seguintes] [ Next steps] secção.

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de correio eletrónico do SendGrid?
O SendGrid é um [serviço de e-mail com base na cloud] que fornece fiável [entrega de e-mail transacional], escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Casos de utilização do SendGrid comuns incluem:

* Enviar automaticamente os recibos ou confirmações de compra aos clientes.
* Administrar a distribuição de lista para o envio de clientes Panfleto mensal e promoções.
* A coleta de métricas em tempo real para coisas como o e-mail bloqueada e envolvimento do cliente.
* Consultas de cliente de reencaminhamento.
* Processar e-mails recebidos.

Para obter mais informações, visite [ https://sendgrid.com ](https://sendgrid.com) ou do SendGrid [biblioteca C#] [ sendgrid-csharp] repositório do GitHub.

## <a name="create-a-sendgrid-account"></a>Criar uma conta de SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referência da biblioteca de classes do .NET de SendGrid
O [pacote NuGet do SendGrid](https://www.nuget.org/packages/Sendgrid) é a forma mais fácil de obter a API de SendGrid e para configurar a sua aplicação com todas as dependências. O NuGet é uma extensão do Visual Studio incluída com o Microsoft Visual Studio 2015 e acima que torna mais fácil instalar e atualizar bibliotecas e ferramentas.

> [!NOTE]
> Para instalar o NuGet, se estiver a executar uma versão do Visual Studio anteriores ao Visual Studio 2015, visite [ https://www.nuget.org ](https://www.nuget.org)e clique nas **instalar NuGet** botão.
>
>

Para instalar o pacote NuGet do SendGrid em seu aplicativo, faça o seguinte:

1. Clique em **novo projeto** e selecione um **modelo**.

   ![Criar um novo projeto][create-new-project]
2. Na **Explorador de soluções**, clique com botão direito **referências**, em seguida, clique em **gerir pacotes NuGet**.

   ![Pacote NuGet do SendGrid][SendGrid-NuGet-package]
3. Procure **SendGrid** e selecione o **SendGrid** item na lista de resultados.
4. Selecione a versão estável mais recente do pacote Nuget na lista pendente versão para poder trabalhar com o modelo de objeto e APIs demonstrado neste artigo.

   ![Pacote de SendGrid][sendgrid-package]
5. Clique em **instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.

Denomina-se a biblioteca de classes do .NET do SendGrid **SendGrid**. Contém os espaços de nomes seguintes:

* **O SendGrid** para comunicação com a API do SendGrid.
* **SendGrid.Helpers.Mail** para métodos auxiliares para facilmente criar objetos de SendGridMessage que especificam como enviar e-mails.

Adicione as seguintes declarações de espaço de nomes de código na parte superior de qualquer arquivo do c# no qual pretende aceder programaticamente o serviço de correio eletrónico SendGrid.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Como: Criar uma mensagem de E-Mail
Utilize o **SendGridMessage** objeto para criar uma mensagem de e-mail. Depois de criar o objeto de mensagem, pode definir propriedades e métodos, incluindo o remetente de e-mail, o destinatário de e-mail e o assunto e corpo do e-mail.

O exemplo seguinte demonstra como criar um objeto totalmente preenchida de e-mail:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

Para obter mais informações sobre todas as propriedades e métodos suportados pelos **SendGrid** escreva, consulte [sendgrid-csharp] [ sendgrid-csharp] no GitHub.

## <a name="how-to-send-an-email"></a>Como: Enviar um E-Mail
Depois de criar uma mensagem de e-mail, pode enviá-lo a utilizar a API do SendGrid. Como alternativa, pode usar [. NET criadas na biblioteca][NET-library].

Enviar e-mail requer que forneça a chave de API do SendGrid. Se precisar de obter detalhes sobre como configurar as chaves de API, visite as chaves de API do SendGrid [documentação][documentation].

Pode armazenar estas credenciais através do Portal do Azure ao clicar em configurações de aplicativo e adicionar os pares de chave/valor em definições da aplicação.

 ![Definições da aplicação do Azure][azure_app_settings]

 Em seguida, pode acessá-los da seguinte forma:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Os exemplos seguintes mostram como enviar uma mensagem de e-mail com a API de Web do SendGrid com um aplicativo de console.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Como: Enviar e-mail de ASP .NET Core API usando a classe de MailHelper

O exemplo abaixo, pode servir para enviar um e-mail único para várias pessoas do ASP .NET Core API com o `MailHelper` classe de `SendGrid.Helpers.Mail` espaço de nomes. Neste exemplo utilizamos ASP .NET Core 1.0. 

Neste exemplo, a chave de API foi armazenada no `appsettings.json` arquivo que pode ser substituído no portal do Azure, conforme mostrado nos exemplos acima.

O conteúdo do `appsettings.json` ficheiro deve ter um aspeto semelhante a:

    {
       "Logging": {
       "IncludeScopes": false,
       "LogLevel": {
       "Default": "Debug",
       "System": "Information",
       "Microsoft": "Information"
         }
       },
     "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }

Em primeiro lugar, precisamos de adicionar o abaixo o código no `Startup.cs` arquivo do projeto .NET Core API. Isto é necessário para que nós possam aceder a `SENDGRID_API_KEY` do `appsettings.json` ficheiro com a injeção de dependência no controlador de API. O `IConfiguration` interface pode ser injetada ao construtor do controlador de depois de adicioná-lo no `ConfigureServices` método abaixo. O conteúdo do `Startup.cs` arquivo semelhante ao seguinte depois de adicionar o código necessário:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

No controlador de, depois de injetar o `IConfiguration` interface, podemos usar o `CreateSingleEmailToMultipleRecipients` método da `MailHelper` classe para enviar um e-mail único para vários destinatários. O método aceita um parâmetro booleano adicional com o nome `showAllRecipients`. Este parâmetro pode ser utilizado para controlar se os destinatários de e-mail será capazes de ver uns dos outros endereço na seção de cabeçalho de e-mail em de e-mail. O código de exemplo para o controlador deve ser, como abaixo 

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using SendGrid;
    using SendGrid.Helpers.Mail;
    using Microsoft.Extensions.Configuration;

    namespace SendgridMailApp.Controllers
    {
        [Route("api/[controller]")]
        public class NotificationController : Controller
        {
           private readonly IConfiguration _configuration;

           public NotificationController(IConfiguration configuration)
           {
             _configuration = configuration;
           }      
        
           [Route("SendNotification")]
           public async Task PostMessage()
           {
              var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
              var client = new SendGridClient(apiKey);
              var from = new EmailAddress("test1@example.com", "Example User 1");
              List<EmailAddress> tos = new List<EmailAddress>
              {
                  new EmailAddress("test2@example.com", "Example User 2"),
                  new EmailAddress("test3@example.com", "Example User 3"),
                  new EmailAddress("test4@example.com","Example User 4")
              };
            
              var subject = "Hello world email from Sendgrid ";
              var htmlContent = "<strong>Hello world with HTML content</strong>";
              var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
              var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
              var response = await client.SendEmailAsync(msg);
          }
       }
    }
    
## <a name="how-to-add-an-attachment"></a>Como: Adicionar um anexo
Anexos podem ser adicionados a uma mensagem ao chamar o **AddAttachment** método e minimamente especificando o nome de ficheiro e codificada em Base64 de conteúdo desejam anexar. Pode incluir múltiplos anexos ao chamar este método, uma vez para cada ficheiro que pretende anexar ou utilizando o **AddAttachments** método. O exemplo seguinte demonstra a adição de um anexo a uma mensagem:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Como: Utilize as definições de correio para ativar rodapés, rastreamento e análise
O SendGrid disponibiliza a funcionalidade de e-mail adicionais através da utilização de definições de correio e configurações de rastreamento. Estas definições podem ser adicionadas a uma mensagem de e-mail para ativar a funcionalidade específica, como o controlo de cliques em, do Google analytics, subscrição de controlo e assim por diante. Para obter uma lista completa de aplicações, consulte a [documentação de definições][settings-documentation].

Aplicações que podem ser aplicadas a **SendGrid** usando métodos implementados como parte de mensagens de e-mail a **SendGridMessage** classe. Os exemplos seguintes demonstram o rodapé e clique em filtros de controlo:

Os exemplos seguintes demonstram o rodapé e clique em filtros de controlo:

### <a name="footer-settings"></a>Definições de rodapé
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Clique em controle
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Como: Utilizar os serviços adicionais do SendGrid
O SendGrid oferece várias APIs e webhooks que pode utilizar para tirar partido das funcionalidades adicionais na sua aplicação do Azure. Para obter mais detalhes, consulte a [referência da API de SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço de correio eletrónico SendGrid, siga estas ligações para saber mais.

* C do SendGrid\# repositório de biblioteca: [csharp do sendgrid][sendgrid-csharp]
* Documentação da API de SendGrid: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[serviço de e-mail com base na cloud]: https://sendgrid.com/solutions
[entrega de e-mail transacional]: https://sendgrid.com/use-cases/transactional-email

