---
title: Introdução ao Azure AD em projetos do Visual Studio WebApi
description: Serviços ligados de como começar a utilizar o Azure Active Directory em projetos WebApi após a conexão ou criação de um Azure AD com o Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 6ddc1e8f7520135092eefbcbf2b4a2b3f1026e66
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494718"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Introdução ao Azure Active Directory (aos projetos WebApi)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

Este artigo fornece orientação adicional depois de adicionar do Active Directory a um projeto de ASP.NET WebAPI através da **projeto > Serviços ligados** comando do Visual Studio. Se ainda não tiver adicionado o serviço ao seu projeto, pode fazê-lo em qualquer altura.

Ver [o que aconteceu ao meu projeto WebAPI?](vs-active-directory-webapi-what-happened.md) as alterações feitas ao seu projeto ao adicionar o serviço ligado.

## <a name="requiring-authentication-to-access-controllers"></a>Exigir a autenticação para os controladores de acesso

Todos os controladores no seu projeto foram adornados com o `[Authorize]` atributo. Este atributo requer que o utilizador ser autenticada antes de acessar as APIs definidas por estes controladores. Para permitir que o controlador de ser acedidas anonimamente, remova este atributo a partir do controlador. Se pretender definir as permissões num nível mais granular, aplica o atributo para cada método que requer autorização em vez de aplicá-lo para a classe de controlador.

## <a name="next-steps"></a>Passos Seguintes

- [Cenários de autenticação do Azure Active Directory](authentication-scenarios.md)
- [Adicionar início de sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v1-aspnet-webapp.md)
