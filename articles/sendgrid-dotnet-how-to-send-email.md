---
title: Como utilizar o serviço de e-mail SendGrid (.NET) | Microsoft Docs
description: Saiba como enviar e-mail com o serviço de e-mail SendGrid no Azure. Amostras de código escritas em C# e utilize a API .NET.
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.custom: devx-track-dotnet
ms.openlocfilehash: ae816d2be592ab774500d1cfe8f2f6a7b7905b91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98196559"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Como enviar e-mail usando SendGrid com Azure
## <a name="overview"></a>Descrição Geral
Este guia demonstra como executar tarefas de programação comuns com o serviço de e-mail SendGrid em Azure. As amostras são escritas em C \# e suportam .NET Standard 1.3. Os cenários abrangidos incluem a construção de e-mails, envio de e-mail, adição de anexos e ativação de várias configurações de correio e rastreamento. Para obter mais informações sobre SendGrid e enviar e-mail, consulte a secção [etapas seguintes.][Next steps]

## <a name="what-is-the-sendgrid-email-service"></a>O que é o Serviço de E-mail SendGrid?
SendGrid é um [serviço de e-mail baseado na nuvem] que fornece entrega de [e-mail transacional]fiável, escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os casos comuns de utilização da SendGrid incluem:

* Envio automaticamente de recibos ou confirmações de compra para os clientes.
* Administração de listas de distribuição para envio de clientes mensais e promoções.
* Colecionando métricas em tempo real para coisas como e-mail bloqueado e envolvimento com o cliente.
* Encaminhamento de inquéritos de clientes.
* Processamento de e-mails recebidos.

Para mais informações, visite [https://sendgrid.com](https://sendgrid.com) ou a [biblioteca C#][sendgrid-csharp] da SendGrid, GitHub repo.

## <a name="create-a-sendgrid-account"></a>Criar uma conta SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referência da Biblioteca de Classe SendGrid .NET
O [pacote SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) é a forma mais fácil de obter a API SendGrid e configurar a sua aplicação com todas as dependências. NuGet é uma extensão do Visual Studio incluída com o Microsoft Visual Studio 2015 e acima que facilita a instalação e atualização de bibliotecas e ferramentas.

> [!NOTE]
> Para instalar o NuGet se estiver a executar uma versão do Visual Studio mais cedo do que o Visual Studio 2015, visite [https://www.nuget.org](https://www.nuget.org) e clique no botão Instalar **NuGet.**
>
>

Para instalar o pacote SendGrid NuGet na sua aplicação, faça o seguinte:

1. Clique em **Novo Projeto** e selecione um **modelo.**

   ![Criar um novo projeto][create-new-project]
2. No **Solution Explorer,** clique à direita **Referências** e, em seguida, clique em **Gerir Pacotes NuGet**.

   ![Pacote SendGrid NuGet][SendGrid-NuGet-package]
3. Procure **por SendGrid** e selecione o item **SendGrid** na lista de resultados.
4. Selecione a versão mais recente estável do pacote Nuget a partir do dropdown da versão para poder trabalhar com o modelo de objeto e APIs demonstrados neste artigo.

   ![Pacote SendGrid][sendgrid-package]
5. Clique **em Instalar** para concluir a instalação e, em seguida, fechar este diálogo.

A biblioteca da classe SendGrid .NET chama-se **SendGrid**. Contém os seguintes espaços de nome:

* **SendGrid** para comunicar com a API da SendGrid.
* **SendGrid.Helpers.Mail** para métodos de ajuda para criar facilmente objetos SendGridMessage que especificam como enviar e-mails.

Adicione as seguintes declarações de espaço de nome de código ao topo de qualquer ficheiro C# no qual pretende aceder programáticamente ao serviço de e-mail SendGrid.

```csharp
using SendGrid;
using SendGrid.Helpers.Mail;
```

## <a name="how-to-create-an-email"></a>Como: Criar um email
Utilize o objeto **SendGridMessage** para criar uma mensagem de correio eletrónico. Uma vez criado o objeto de mensagem, pode definir propriedades e métodos, incluindo o remetente de e-mail, o destinatário do e-mail, e o sujeito e corpo do e-mail.

O exemplo a seguir demonstra como criar um objeto de e-mail totalmente povoado:

```csharp
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
```

Para obter mais informações sobre todas as propriedades e métodos suportados pelo tipo **SendGrid,** consulte [sendgrid-csharp][sendgrid-csharp] no GitHub.

## <a name="how-to-send-an-email"></a>Como: Enviar um e-mail
Depois de criar uma mensagem de e-mail, pode enviá-la usando a API da SendGrid. Em alternativa, pode [utilizar. A NET é construída na biblioteca.][NET-library]

O envio de e-mail requer que forneça a sua Chave API SendGrid. Se precisar de detalhes sobre como configurar chaves API, visite a [documentação][documentation]da API Keys da SendGrid .

Pode armazenar estas credenciais através do seu portal Azure clicando nas definições de Aplicação e adicionando os pares chave/valor nas definições de App.

![Definições de aplicativos Azure][azure_app_settings]

Em seguida, pode acessá-los da seguinte forma:

```csharp
var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
var client = new SendGridClient(apiKey);
```

Os exemplos a seguir mostram como enviar uma mensagem de e-mail usando a API da Web SendGrid com uma aplicação de consola.

```csharp
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
```

## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Como: Enviar e-mail da ASP .NET Core API usando a classe MailHelper

O exemplo abaixo pode ser usado para enviar um único e-mail para várias pessoas da ASP .NET Core API usando a `MailHelper` classe de espaço de `SendGrid.Helpers.Mail` nome. Para este exemplo, estamos a utilizar ASP .NET Core 1.0. 

Neste exemplo, a tecla API foi armazenada no `appsettings.json` ficheiro que pode ser ultrapassado a partir do portal Azure, como mostram os exemplos acima.

O conteúdo do `appsettings.json` ficheiro deve ser semelhante a:

```json
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
```

Em primeiro lugar, precisamos adicionar o código abaixo no `Startup.cs` ficheiro do projeto API .NET Core. Isto é necessário para que possamos aceder ao `SENDGRID_API_KEY` ficheiro utilizando a injeção de dependência no controlador `appsettings.json` API. A `IConfiguration` interface pode ser injetada no construtor do controlador depois de a adicionar no método `ConfigureServices` abaixo. O conteúdo do `Startup.cs` ficheiro parece o seguinte após a adição do código necessário:

```csharp
    public IConfigurationRoot Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        // Add mvc here
        services.AddMvc();
        services.AddSingleton<IConfiguration>(Configuration);
    }
```

No controlador, depois de injetar a `IConfiguration` interface, podemos usar o `CreateSingleEmailToMultipleRecipients` método da classe para enviar um único `MailHelper` e-mail para vários destinatários. O método aceita um parâmetro booleano adicional chamado `showAllRecipients` . Este parâmetro pode ser usado para controlar se os destinatários de e-mail serão capazes de ver o endereço de e-mail uns dos outros na secção To do cabeçalho de e-mail. O código de amostra para controlador deve ser como abaixo 

```csharp
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
```

## <a name="how-to-add-an-attachment"></a>Como: Adicionar um anexo
Os anexos podem ser adicionados a uma mensagem chamando o método **AddAttachment** e especificando minimamente o nome do ficheiro e o conteúdo codificado Base64 que pretende anexar. Pode incluir vários anexos ligando para este método uma vez para cada ficheiro que deseja anexar ou utilizando o método **AddAttachments.** O exemplo a seguir demonstra a adição de um anexo a uma mensagem:

```csharp
var banner2 = new Attachment()
{
    Content = Convert.ToBase64String(raw_content),
    Type = "image/png",
    Filename = "banner2.png",
    Disposition = "inline",
    ContentId = "Banner 2"
};
msg.AddAttachment(banner2);
```

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Como: Utilizar as definições de correio para permitir rodapés, rastreios e análises
A SendGrid fornece uma funcionalidade adicional de e-mail através da utilização de definições de correio e definições de rastreio. Estas definições podem ser adicionadas a uma mensagem de e-mail para permitir funcionalidades específicas, tais como rastreio de cliques, análise do Google, rastreio de subscrição, e assim por diante. Para obter uma lista completa de aplicações, consulte a [Documentação de Definições.][settings-documentation]

As aplicações podem ser aplicadas a mensagens de correio **enviargrid** usando métodos implementados como parte da classe **SendGridMessage.** Os seguintes exemplos demonstram o rodapé e clicam nos filtros de rastreio:

Os seguintes exemplos demonstram o rodapé e clicam nos filtros de rastreio:

### <a name="footer-settings"></a>Configurações do rodapé

```csharp
    msg.SetFooterSetting(
        true,
        "Some Footer HTML",
        "<strong>Some Footer Text</strong>");
```

### <a name="click-tracking"></a>Clique no rastreio

```csharp
msg.SetClickTracking(true);
```

## <a name="how-to-use-additional-sendgrid-services"></a>Como: Utilizar serviços adicionais de SendGrid
A SendGrid oferece várias APIs e webhooks que pode utilizar para alavancar funcionalidades adicionais dentro da sua aplicação Azure. Para mais detalhes, consulte a Referência API da [SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico do serviço de email SendGrid, siga estes links para saber mais.

* Repo da biblioteca SendGrid \# C: [sendgrid-csharp][sendgrid-csharp]
* Documentação da SendGrid API: <https://sendgrid.com/docs>

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
[SendGrid API documentation]: https://sendgrid.com/docs/api-reference/
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[serviço de e-mail baseado na nuvem]: https://sendgrid.com/solutions
[entrega de e-mail transacional]: https://sendgrid.com/use-cases/transactional-email

