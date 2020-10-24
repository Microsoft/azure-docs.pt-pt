---
author: baanders
description: incluir ficheiro para as permissões de acesso passo na configuração Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: cbcaf4b4ad1b6c00f8c452582b986b6ee3b2806e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478872"
---
A Azure Digital Twins usa [o Azure Ative Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) para controlo de acesso baseado em funções (RBAC). Isto significa que antes de um utilizador poder fazer chamadas de avião de dados para a sua instância Azure Digital Twins, esse utilizador precisa de ser atribuído a uma função com permissões apropriadas para o mesmo.

Para a Azure Digital Twins, este papel é _**Azure Digital Twins Data Owner**_. Pode ler mais sobre papéis e segurança em [*Soluções Concepts: Security for Azure Digital Twins.*](../articles/digital-twins/concepts-security.md)

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]

Esta secção irá mostrar-lhe como criar uma atribuição de papel para um utilizador na sua instância Azure Digital Twins, utilizando o e-mail desse utilizador no inquilino AZure AD na sua subscrição Azure. Dependendo do seu papel na sua organização, você pode configurar esta permissão para si mesmo, ou instalá-la em nome de outra pessoa que estará gerindo o exemplo de Azure Digital Twins.

### <a name="assign-the-role"></a>Atribuir o papel

Para dar permissão a um utilizador para gerir uma instância Azure Digital Twins, deve atribuir-lhes o papel _**de Proprietário de Dados Azure Digital Twins**_ dentro do caso.

> [!NOTE]
> Note que esta função é diferente da função Azure AD *Owner,* que também pode ser atribuída no âmbito da instância Azure Digital Twins. Estas são duas funções de gestão distintas, e a Azure AD *Owner* não concede acesso a funcionalidades de plano de dados que são concedidas com *o Azure Digital Twins Data Owner*.