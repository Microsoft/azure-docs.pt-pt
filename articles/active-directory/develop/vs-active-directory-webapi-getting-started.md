---
title: Introdução ao Azure AD em projetos do Visual Studio WebApi
description: Como começar a usar o Azure Active Directory em projetos WebApi depois de se conectar ou criar um AD do Azure usando os serviços conectados do Visual Studio
services: active-directory
ms.service: active-directory
ms.subservice: develop
author: ghogen
manager: douge
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 662e0a3e85684287c636b8d0b512b072c978dd3f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320739"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Introdução ao Azure Active Directory (projetos WebApi)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

Este artigo fornece diretrizes adicionais depois que você adicionou Active Directory a um projeto ASP.NET WebAPI por meio do comando **project > Connected Services** do Visual Studio. Se você ainda não adicionou o serviço ao seu projeto, você pode fazer isso a qualquer momento.

Veja [o que aconteceu com meu projeto do WebAPI?](vs-active-directory-webapi-what-happened.md) para as alterações feitas ao seu projeto ao adicionar o serviço conectado.

## <a name="requiring-authentication-to-access-controllers"></a>Exigindo autenticação para acessar controladores

Todos os controladores em seu projeto foram adornados com `[Authorize]` o atributo. Esse atributo requer que o usuário seja autenticado antes de acessar as APIs definidas por esses controladores. Para permitir que o controlador seja acessado anonimamente, remova esse atributo do controlador. Se você quiser definir as permissões em um nível mais granular, aplique o atributo a cada método que requer autorização em vez de aplicá-lo à classe do controlador.

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para Azure Active Directory](authentication-scenarios.md)
- [Adicionar entrada com a Microsoft a um aplicativo Web ASP.NET](quickstart-v1-aspnet-webapp.md)
