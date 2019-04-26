---
title: Introdução ao Azure AD em projetos do Visual Studio .NET MVC
description: Serviços ligados de como começar a utilizar o Azure Active Directory em projetos .NET MVC após a conexão ou criação de um Azure AD com o Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
origin.date: 03/12/2018
ms.date: 09/03/2018
ms.author: v-junlch
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 6bbbef3f48eb55c863fdd286113297d79f9b9e02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60296848"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Introdução ao Azure Active Directory (projetos de MVC do ASP.NET)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)

Este artigo fornece orientação adicional depois de adicionar do Active Directory para um projeto de MVC do ASP.NET através da **projeto > Serviços ligados** comando do Visual Studio. Se ainda não tiver adicionado o serviço ao seu projeto, pode fazê-lo em qualquer altura.

Ver [o que aconteceu ao meu projeto MVC?](vs-active-directory-dotnet-what-happened.md) as alterações feitas ao seu projeto ao adicionar o serviço ligado.

## <a name="requiring-authentication-to-access-controllers"></a>Exigir a autenticação para os controladores de acesso

Todos os controladores no seu projeto foram adornados com o `[Authorize]` atributo. Este atributo requer que o utilizador ser autenticada antes de aceder a estes controladores. Para permitir que o controlador de ser acedidas anonimamente, remova este atributo a partir do controlador. Se pretender definir as permissões num nível mais granular, aplica o atributo para cada método que requer autorização em vez de aplicá-lo para a classe de controlador.

## <a name="adding-signin--signout-controls"></a>Adicionar início de sessão / controla a fim de sessão

Para adicionar os controles de início de sessão/fim de sessão à sua exibição, pode usar o `_LoginPartial.cshtml` exibição parcial para adicionar a funcionalidade a uma das suas exibições. Eis um exemplo da funcionalidade adicionada ao padrão `_Layout.cshtml` vista. (Tenha em atenção o último elemento no div com classe barra de navegação-fechar):

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

## <a name="next-steps"></a>Passos Seguintes

- [Cenários de autenticação do Azure Active Directory](authentication-scenarios.md)
- [Adicionar início de sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v1-aspnet-webapp.md)

<!-- Update_Description: link update -->