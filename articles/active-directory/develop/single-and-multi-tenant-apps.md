---
title: Aplicativos individuais e multi-inquilinos em Azure AD
titleSuffix: Microsoft identity platform
description: Conheça as características e diferenças entre aplicações de inquilino único e multi-inquilinos em Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2020
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 0868d87d977b15a552b04d5dbd6d19de6931f0ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91395911"
---
# <a name="tenancy-in-azure-active-directory"></a>Inquilinos no Azure Active Directory

O Azure Ative Directory (Azure AD) organiza objetos como utilizadores e aplicações em grupos *chamados inquilinos.* Os inquilinos permitem que um administrador estabeleça políticas sobre os utilizadores dentro da organização e as aplicações que a organização detém para atender às suas políticas de segurança e operacional. 

## <a name="who-can-sign-in-to-your-app"></a>Quem pode entrar na sua aplicação?

No que diz respeito ao desenvolvimento de apps, os desenvolvedores podem optar por configurar a sua app para serem inquilinos únicos ou multi-inquilinos durante o registo de aplicações no [portal Azure.](https://portal.azure.com)
* As aplicações de inquilino único só estão disponíveis no inquilino em que estavam registados, também conhecidos como seu inquilino de casa.
* Aplicações multi-arrendatários estão disponíveis para utilizadores em seu inquilino de casa e outros inquilinos.

No portal Azure, pode configurar a sua app para ser um inquilino único ou multi-inquilino, definindo o público da seguinte forma.

| Audiência | Solteiro/multi-inquilino | Quem pode entrar | 
|----------|--------| ---------|
| Contas neste diretório apenas | Inquilino único | Todas as contas de utilizador e de hóspedes do seu diretório podem utilizar a sua aplicação ou API.<br>*Use esta opção se o seu público-alvo for interno para a sua organização.* |
| Contas em qualquer diretório AD Azure | Multi-inquilino | Todos os utilizadores e hóspedes com uma conta de trabalho ou escola da Microsoft podem usar a sua aplicação ou API. Isto inclui escolas e empresas que usam o Microsoft 365.<br>*Use esta opção se o seu público-alvo for clientes empresariais ou educativos.* |
| Contas em qualquer diretório AD AZure e contas pessoais da Microsoft (como Skype, Xbox, Outlook.com) | Multi-inquilino | Todos os utilizadores com trabalho ou escola ou conta pessoal da Microsoft podem utilizar a sua aplicação ou API. Inclui escolas e empresas que usam o Microsoft 365, bem como contas pessoais que são usadas para iniciar sôms em serviços como Xbox e Skype.<br>*Utilize esta opção para direcionar o conjunto mais amplo de contas da Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Melhores práticas para aplicativos multi-inquilinos

Construir grandes aplicativos multi-inquilinos pode ser um desafio devido ao número de políticas diferentes que os administradores de TI podem definir nos seus inquilinos. Se optar por construir uma app multi-arrendatário, siga estas boas práticas:

* Teste a sua aplicação num inquilino que tenha configurado [políticas de Acesso Condicional.](../azuread-dev/conditional-access-dev-guide.md)
* Siga o princípio do menor acesso ao utilizador para garantir que a sua app apenas solicita permissões de que necessita. 
* Forneça nomes e descrições apropriados para quaisquer permissões que exponha como parte da sua aplicação. Isto ajuda os utilizadores e administradores a saber em que estão a concordar quando tentam usar as APIs da sua aplicação. Para mais informações, consulte a secção de boas práticas no guia de [permissões.](v2-permissions-and-consent.md)

## <a name="next-steps"></a>Passos seguintes

* [Como converter uma app para ser multi-inquilino](howto-convert-app-to-be-multi-tenant.md)
