---
title: Começar com a Azure AD em projetos .NET MVC / Rio Azure
description: Como começar a usar o Azure Ative Directory em projetos .NET MVC depois de ligar ou criar um AD Azure usando serviços conectados ao Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: 15a7c873e4d1e5c962a89b03f2a5cafc88843192
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165469"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Começar com O Diretório Ativo Azure (ASP.NET Projetos MVC)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)

Este artigo fornece orientações adicionais depois de ter adicionado Ative Directory a um projeto de MVC ASP.NET através do comando **de Serviços Conectados do Project > do** Visual Studio. Se ainda não adicionou o serviço ao seu projeto, pode fazê-lo a qualquer momento.

Veja [o que aconteceu ao meu projeto MVC?](vs-active-directory-dotnet-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Exigindo autenticação para aceder aos controladores

Todos os controladores do seu projeto foram adornados com o `[Authorize]` atributo. Este atributo requer que o utilizador seja autenticado antes de aceder a estes controladores. Para permitir o acesso do controlador de forma anónima, remova este atributo do controlador. Se pretender definir as permissões a um nível mais granular, aplique o atributo a cada método que requer autorização em vez de a aplicar à classe do controlador.

## <a name="adding-signin--signout-controls"></a>Adicionar controlos signin / signout

Para adicionar os controlos SignIn/SignOut à sua vista, pode utilizar a `_LoginPartial.cshtml` vista parcial para adicionar a funcionalidade a uma das suas vistas. Aqui está um exemplo da funcionalidade adicionada à `_Layout.cshtml` vista padrão. (Note o último elemento no div com o colapso de navbar de classe):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para o Azure Ative Directory](./authentication-vs-authorization.md)
- [Adicione o s-in com a Microsoft a uma aplicação web ASP.NET](quickstart-v2-aspnet-webapp.md)