---
title: Aplicativos individuais e multi-inquilinos em Azure AD
titleSuffix: Microsoft identity platform
description: Conheça as funcionalidades e diferenças entre aplicações de inquilino único e multi-inquilinos em Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: ec59383f9ca2b71ec9f4b6df3ab2e24c6b52473b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80881371"
---
# <a name="tenancy-in-azure-active-directory"></a>Arrendamento em Diretório Ativo Azure

O Azure Ative Directory (Azure AD) organiza objetos como utilizadores e aplicações em *grupos chamados inquilinos.* Os inquilinos permitem que um administrador estabeleça políticas sobre os utilizadores dentro da organização e as aplicações que a organização detém para atender às suas políticas de segurança e operacionais. 

## <a name="who-can-sign-in-to-your-app"></a>Quem pode iniciar sessão na sua aplicação?

No que diz respeito ao desenvolvimento de apps, os desenvolvedores podem optar por configurar a sua app para serem inquilinos únicos ou multi-inquilinos durante o registo de aplicações no [portal Azure.](https://portal.azure.com)
* As aplicações de inquilino único só estão disponíveis no inquilino onde estavam registadas, também conhecidas como sua casa de inquilino.
* Aplicações multi-inquilinos estão disponíveis para os utilizadores tanto no seu inquilino doméstico como em outros inquilinos.

No portal Azure, pode configurar a sua app para ser um inquilino único ou multi-inquilino, definindo o público da seguinte forma.

| Audiência | Inquilino único/multi-inquilino | Quem pode assinar | 
|----------|--------| ---------|
| Contas neste diretório apenas | Inquilino único | Todas as contas de utilizador e hóspedes do seu diretório podem utilizar a sua aplicação ou API.<br>*Use esta opção se o seu público-alvo for interno para a sua organização.* |
| Contas em qualquer diretório da AD Azure | Multi-inquilino | Todos os utilizadores e hóspedes com uma conta de trabalho ou escola da Microsoft podem usar a sua aplicação ou API. Isto inclui escolas e empresas que usam o Escritório 365.<br>*Utilize esta opção se o seu público-alvo for clientes empresariais ou educativos.* |
| Contas em qualquer diretório da AD Azure e contas pessoais da Microsoft (como Skype, Xbox, Outlook.com) | Multi-inquilino | Todos os utilizadores com uma conta de trabalho ou escola, ou conta pessoal da Microsoft, podem utilizar a sua aplicação ou API. Inclui escolas e empresas que usam o Office 365, bem como contas pessoais que são usadas para iniciar sessão em serviços como Xbox e Skype.<br>*Utilize esta opção para direcionar o conjunto mais amplo das contas da Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Boas práticas para aplicações multi-inquilinos

A construção de grandes aplicações multi-arrendatárias pode ser um desafio devido ao número de políticas diferentes que os administradores de TI podem definir nos seus inquilinos. Se optar por construir uma aplicação multi-inquilinos, siga estas boas práticas:

* Teste a sua aplicação num inquilino que tenha configurado as políticas de [Acesso Condicional.](../azuread-dev/conditional-access-dev-guide.md)
* Siga o princípio do menor acesso ao utilizador para garantir que a sua aplicação apenas solicita permissões de que realmente necessita. Evite solicitar permissões que exijam consentimento administrativo, uma vez que isso pode impedir que os utilizadores adquiram a sua app em algumas organizações. 
* Forneça nomes e descrições apropriados para quaisquer permissões que exponha como parte da sua aplicação. Isto ajuda os utilizadores e administradores a saberem com o que estão a concordar quando tentam utilizar as APIs da sua aplicação. Para mais informações, consulte a secção de boas práticas no guia de [permissões](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Passos seguintes

* [Como converter uma app para ser multi-inquilino](howto-convert-app-to-be-multi-tenant.md)
