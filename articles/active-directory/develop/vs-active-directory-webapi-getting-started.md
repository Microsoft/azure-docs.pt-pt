---
title: Começar com Azure AD em projetos Visual Studio WebApi
description: Como começar a usar o Azure Ative Directory em projetos WebApi depois de ligar ou criar um AD Azure usando serviços conectados ao Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: c76aabbfce02d7e2bd8361dc99b4ba20d1f62f15
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88114494"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Começar com o Azure Ative Directory (projetos WebApi)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

Este artigo fornece orientações adicionais depois de ter adicionado Ative Directory a um projeto webAPI ASP.NET através do comando **de Serviços Conectados do Project > do** Visual Studio. Se ainda não adicionou o serviço ao seu projeto, pode fazê-lo a qualquer momento.

Veja [o que aconteceu ao meu projeto WebAPI?](vs-active-directory-webapi-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Exigindo autenticação para aceder aos controladores

Todos os controladores do seu projeto foram adornados com o `[Authorize]` atributo. Este atributo requer que o utilizador seja autenticado antes de aceder às APIs definidas por estes controladores. Para permitir o acesso do controlador de forma anónima, remova este atributo do controlador. Se pretender definir as permissões a um nível mais granular, aplique o atributo a cada método que requer autorização em vez de a aplicar à classe do controlador.

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para o Azure Ative Directory](./authentication-vs-authorization.md)
- [Adicione o s-in com a Microsoft a uma aplicação web ASP.NET](quickstart-v2-aspnet-webapp.md)