---
title: Portal de inscrição de self-service para colaboração B2B - Azure AD
description: A colaboração do B2B Azure Active Directory suporta as relações entre empresas, permitindo a parceiros de negócios acederem, seletivamente, às suas aplicações empresariais
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff18d3d9cae6e887abbd9fb1845de62386062b2b
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85386712"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Self-service para inscrição de colaboração Azure AD B2B

Os clientes podem fazer muito mais com as funcionalidades incorporadas expostas através do [portal do Azure](https://portal.azure.com) e do [Painel de Acesso à Aplicação](https://myapps.microsoft.com) para utilizadores finais. No entanto, poderá ter de personalizar o fluxo de trabalho de implementação para utilizadores B2B de forma a satisfazer as necessidades da sua organização.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Gestão de direitos Azure AD para inscrição de utilizadores de hóspedes B2B

Como uma organização convidativa, pode não saber com antecedência quem são os colaboradores externos individuais que precisam de acesso aos seus recursos. Precisa de uma forma de os utilizadores de empresas parceiras se inscreverem com políticas que controlam. Se pretender permitir que utilizadores de outras organizações solicitem acesso, e após aprovação seja a provisionado com contas de hóspedes e atribuído a grupos, apps e sites SharePoint Online, pode utilizar a [gestão de direitos AD AZure](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) para configurar políticas que [gerem o acesso a utilizadores externos.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users)

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Ative Directy B2B convite API

As organizações podem usar o [gestor de convites microsoft Graph API](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) para construir as suas próprias experiências de embarque para utilizadores convidados B2B. Quando pretender oferecer inscrição de utilizador de hóspedes B2B self-service, recomendamos que utilize [a gestão de direitos AD da Azure](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Mas se quiser construir a sua própria experiência, pode utilizar o [convite de criação API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) para enviar automaticamente o seu e-mail de convite personalizado diretamente para o utilizador B2B, por exemplo. Ou a sua aplicação pode usar o conviteRedeemUrl devolvido na resposta de criação para criar o seu próprio convite (através do seu mecanismo de comunicação de escolha) para o utilizador convidado.

## <a name="next-steps"></a>Passos seguintes

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Licenciamento de colaboração B2B do Azure AD](licensing-guidance.md)
* [Perguntas mais frequentes (FAQ) sobre a colaboração B2B do Azure Active Directory](faq.md)
