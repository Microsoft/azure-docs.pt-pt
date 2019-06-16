---
title: Aplicações multi-inquilino e individuais no Azure Active Directory
description: Saiba mais sobre os recursos e as diferenças entre as aplicações de inquilino único e multi-inquilinos no Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9035cc629a11c125c1b6351bd4bff9f5576f7baf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111073"
---
# <a name="tenancy-in-azure-active-directory"></a>Inquilinos no Azure Active Directory

Azure Active Directory (Azure AD) organiza os objetos, como utilizadores e aplicações em grupos chamados *inquilinos*. Os inquilinos permitem ao administrador definir políticas nos utilizadores dentro da organização e as aplicações que a organização é proprietária para atender às suas políticas operacionais e de segurança. 

## <a name="who-can-sign-in-to-your-app"></a>Quem pode iniciar sessão sua aplicação?

Quando se trata de desenvolvimento de aplicativos, os desenvolvedores podem optar por configurar a sua aplicação para ser inquilino único ou vários inquilinos durante o registo de aplicação no [portal do Azure](https://portal.azure.com).
* Aplicações de inquilino único só estão disponíveis no inquilino que foram registados no, também conhecido como seu principal de inquilino.
* Aplicações de multi-inquilinos estão disponíveis para os utilizadores no seu inquilino principal e de outros inquilinos.

No portal do Azure, pode configurar a sua aplicação para ser o inquilino único ou vários inquilinos, definindo o público-alvo da seguinte forma.

| Audiência | Único/várias-tenant | Quem pode iniciar sessão | 
|----------|--------| ---------|
| Contas apenas neste diretório | Inquilino único | Todas as contas de convidado no seu diretório de utilizador e podem utilizar a sua aplicação ou API.<br>*Utilize esta opção se o seu público-alvo é interno para a sua organização.* |
| Contas de qualquer diretório do Azure AD | Multi-inquilino | Todos os utilizadores e convidados com uma conta escolar ou profissional da Microsoft podem utilizar a sua aplicação ou API. Isto inclui a instituições de ensino e empresas que utilizam o Office 365.<br>*Utilize esta opção se o seu público-alvo é empresas ou clientes de instituições de ensino.* |
| Contas em qualquer diretório do Azure AD e as contas Microsoft pessoais (por exemplo, o Skype, Xbox, Outlook.com) | Multi-inquilino | Todos os utilizadores com um trabalho ou escola ou conta Microsoft pessoal podem utilizar a sua aplicação ou API. Ele inclui a instituições de ensino e as empresas que utilizam o Office 365, bem como contas pessoais que são utilizadas para iniciar sessão em serviços como a Xbox e o Skype.<br>*Utilize esta opção para o conjunto mais ampla de contas Microsoft de destino.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Melhores práticas para aplicações multi-inquilino

Pode ser um desafio criação de excelentes aplicações multi-inquilino devido ao número de diferentes políticas que os administradores de TI podem definir no seus inquilinos. Se optar por criar uma aplicação multi-inquilino, siga estas práticas recomendadas:

* Testar a aplicação num inquilino que tenha configurado [políticas de acesso condicional](conditional-access-dev-guide.md).
* Acompanhar o princípio de, pelo menos, acesso de utilizador para se certificar de que a aplicação solicite apenas que ela realmente precisa de permissões. Evite a pedir permissões requerem consentimento de administrador, como isso pode impedir que os utilizadores a adquirir a sua aplicação em algumas organizações. 
* Fornece nomes adequados e descrições para quaisquer permissões que expor como parte da sua aplicação. Isto ajuda os utilizadores e os administradores sabem o que eles estão a aceitar quando tentarem utilizar as APIs da sua aplicação. Para obter mais informações, consulte a seção práticas recomendada a [guia de permissões](v1-permissions-and-consent.md).

## <a name="next-steps"></a>Passos Seguintes

* [Como converter um aplicativo de modo a multi-inquilino](howto-convert-app-to-be-multi-tenant.md)
