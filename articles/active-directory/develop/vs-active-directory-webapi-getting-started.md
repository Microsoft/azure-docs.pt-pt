---
title: Começar com azure AD em projetos WebApi do Estúdio Visual
description: Como começar a usar o Diretório Ativo Azure em projetos WebApi após ligar ou criar um Anúncio Azure usando serviços conectados do Estúdio Visual
author: ghogen
manager: jillfra
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: e2216d1f489acea27bbc788c6ded21c6d9835915
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699942"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Começar com o Diretório Ativo Azure (projetos WebApi)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

Este artigo fornece orientação adicional depois de ter adicionado O Diretório Ativo a um projeto ASP.NET WebAPI através do comando **do Project > Connected Services** do Visual Studio. Se ainda não adicionou o serviço ao seu projeto, pode fazê-lo a qualquer momento.

Veja [o que aconteceu ao meu projeto WebAPI?](vs-active-directory-webapi-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Exigindo autenticação para aceder aos controladores

Todos os controladores do seu projeto foram adornados com o atributo `[Authorize]`. Este atributo exige que o utilizador seja autenticado antes de aceder às APIs definidas por estes controladores. Para permitir que o controlador seja acedido de forma anónima, remova este atributo do controlador. Se pretender definir as permissões a um nível mais granular, aplique o atributo a cada método que requer autorização em vez de a aplicar à classe de controlador.

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para o Diretório Ativo do Azure](authentication-scenarios.md)
- [Adicione o sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v1-aspnet-webapp.md)
