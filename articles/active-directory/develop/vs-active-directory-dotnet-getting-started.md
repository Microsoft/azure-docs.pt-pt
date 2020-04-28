---
title: Começar com a Azure AD em projetos de MVC .NET [ Azure
description: Como começar a usar o Azure Ative Directory em projetos .NET MVC após ligar ou criar um Azure AD utilizando serviços conectados ao Estúdio Visual
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: fe408e965c528db1d82b73ee7b20bbe3b3933657
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886131"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Começar com o Diretório Ativo Azure (ASP.NET Projetos MVC)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)

Este artigo fornece orientação adicional depois de ter adicionado O Diretório Ativo a um projeto ASP.NET MVC através do **comando de Serviços Conectados do Project > Connected Services** do Visual Studio. Se ainda não adicionou o serviço ao seu projeto, pode fazê-lo a qualquer momento.

Veja [o que aconteceu ao meu projeto mVC?](vs-active-directory-dotnet-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Exigindo autenticação para aceder aos controladores

Todos os controladores do seu `[Authorize]` projeto foram adornados com o atributo. Este atributo exige que o utilizador seja autenticado antes de aceder a estes controladores. Para permitir que o controlador seja acedido de forma anónima, remova este atributo do controlador. Se pretender definir as permissões a um nível mais granular, aplique o atributo a cada método que requer autorização em vez de a aplicar à classe de controlador.

## <a name="adding-signin--signout-controls"></a>Adicionar controlos signin /signout

Para adicionar os controlos SignIn/SignOut à `_LoginPartial.cshtml` sua vista, pode utilizar a vista parcial para adicionar a funcionalidade a uma das suas opiniões. Aqui está um exemplo da funcionalidade `_Layout.cshtml` adicionada à vista padrão. (Note o último elemento no div com o colapso da navbar de classe):

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

- [Cenários de autenticação para o Diretório Ativo do Azure](authentication-scenarios.md)
- [Adicione o sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v2-aspnet-webapp.md)
