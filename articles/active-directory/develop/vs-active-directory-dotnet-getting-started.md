---
title: Introdução ao Azure AD em projetos do Visual Studio .NET MVC
description: Como começar a usar o Azure Active Directory em projetos do .NET MVC depois de se conectar ou criar um AD do Azure usando os serviços conectados do Visual Studio
services: active-directory
ms.subservice: develop
author: ghogen
manager: douge
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab641aa93d926f64216694c689d8d28e407195d0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326086"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Introdução com Azure Active Directory (projetos ASP.NET MVC)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)

Este artigo fornece diretrizes adicionais depois de você ter adicionado Active Directory a um projeto MVC do ASP.NET por meio do comando **project > Connected Services** do Visual Studio. Se você ainda não adicionou o serviço ao seu projeto, você pode fazer isso a qualquer momento.

Veja o [que aconteceu com meu projeto do MVC?](vs-active-directory-dotnet-what-happened.md) para as alterações feitas ao seu projeto ao adicionar o serviço conectado.

## <a name="requiring-authentication-to-access-controllers"></a>Exigindo autenticação para acessar controladores

Todos os controladores em seu projeto foram adornados com `[Authorize]` o atributo. Este atributo requer que o usuário seja autenticado antes de acessar esses controladores. Para permitir que o controlador seja acessado anonimamente, remova esse atributo do controlador. Se você quiser definir as permissões em um nível mais granular, aplique o atributo a cada método que requer autorização em vez de aplicá-lo à classe do controlador.

## <a name="adding-signin--signout-controls"></a>Adicionando controles de entrada/saída

Para adicionar os controles de entrada/saída à exibição, você pode usar a `_LoginPartial.cshtml` exibição parcial para adicionar a funcionalidade a uma de suas exibições. Aqui está um exemplo da funcionalidade adicionada à exibição padrão `_Layout.cshtml` . (Observe o último elemento no div com a barra de navegação de classe-Collapse):

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

- [Cenários de autenticação para Azure Active Directory](authentication-scenarios.md)
- [Adicionar entrada com a Microsoft a um aplicativo Web ASP.NET](quickstart-v1-aspnet-webapp.md)
