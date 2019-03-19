---
title: Registar uma aplicação com o ponto final do Azure AD v2.0| Microsoft Docs
description: Saiba como registar uma aplicação com a Microsoft para ativar o início de sessão e aceder aos serviços da Microsoft com o ponto final do Azure AD v2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6221fb7575fc267030929b449cba48cff8563f27
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122559"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v20-endpoint"></a>Início rápido: Registar uma aplicação com o ponto de final de v2.0 do Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Para criar uma aplicação que aceite o início de sessão de conta pessoal da Microsoft (MSA), e profissional ou escolar (Azure AD), terá de registar uma aplicação com o ponto final do Azure Active Directory (Azure AD) v2.0. Neste momento, não poderá utilizar as aplicações existentes que possa ter com o Azure AD ou MSA - terá de criar uma nova.

Observe que todos os cenários e funcionalidades do Azure AD são compatíveis com o ponto final do v2.0. Para determinar se deve utilizar o ponto final do v2.0, leia sobre as [limitações do v2.0](active-directory-v2-limitations.md).

> [!NOTE]
> Registar uma nova aplicação? Experimente a nova experiência **Registos de aplicações (Pré-visualização)** no portal do Azure. Veja [Registar uma aplicação (Pré-visualização)](quickstart-register-app.md) para começar.

## <a name="step-1-sign-in-to-the-microsoft-application-registration-portal"></a>Passo 1: Inicie sessão no portal de registo de aplicação do Microsoft

1. Navegue para o portal de registo de aplicações da Microsoft em [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).
1. Inicie sessão com uma conta pessoal, escolar ou profissional da Microsoft. Se não tiver uma, inscreva-se numa nova conta pessoal.
1. Feito? Agora deve procurar a sua lista de aplicações da Microsoft, que provavelmente está vazia. Vamos mudar isso.

## <a name="step-2-register-an-app"></a>Passo 2: Registar uma aplicação

1. Selecione **Adicionar uma aplicação** e atribua um nome.
    O portal irá atribuir a aplicação a um ID de Aplicação globalmente exclusivo, que irá utilizar mais tarde no seu código. Se a sua aplicação inclui um componente do lado do servidor que tem os tokens de acesso para chamar APIs (pensar: Office, Azure ou o seu próprio web API), vai querer criar uma **segredo de aplicação** aqui também.
1. Em seguida, adicione as **Plataformas** que a sua aplicação irá utilizar.
    * Para aplicações baseadas na Web, indique um **URI de Redirecionamento** onde as mensagens de início de sessão possam ser enviadas.
    * Para as aplicações móveis, copie o URI de redirecionamento predefinido criado automaticamente para si.
    * Para APIs Web, é criado automaticamente um âmbito predefinido para aceder à API Web.
        Pode adicionar âmbitos adicionais com o botão **Adicionar Âmbito**. Também pode adicionar todas as aplicações que já estão pré-autorizadas para utilizar a sua API Web com o formulário **Aplicações pré-autorizadas**.
1. Opcionalmente, personalize o aspeto e a funcionalidade da sua página de início de sessão na secção **Perfil**. 
1. **Guarde** as suas alterações antes de continuar.

> [!NOTE]
> Quando registar uma aplicação com [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), a aplicação será registada no inquilino principal da conta que utiliza para iniciar sessão no portal. Isto significa que não pode registar uma aplicação no seu inquilino do Azure AD com uma conta Microsoft pessoal. Se desejar registar explicitamente uma aplicação num inquilino específico, inicie sessão com uma conta criada originalmente nesse inquilino.

## <a name="next-steps"></a>Passos Seguintes

Agora que tem uma aplicação da Microsoft, pode concluir um dos quickstart2 do v2.0. Seguem-se algumas recomendações:

[!INCLUDE [active-directory-v2-quickstart-table](~/includes/active-directory-v2-quickstart-table.md)]
