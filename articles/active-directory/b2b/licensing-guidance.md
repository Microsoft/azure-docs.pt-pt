---
title: Diretrizes de licenciamento de colaboração B2B-Azure Active Directory | Microsoft Docs
description: Azure Active Directory colaboração B2B não exige licenças pagas do Azure AD, mas você também pode obter recursos pagos para usuários convidados B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 081061eae07fa3765d032ad155e59ebf5aa3cbc9
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512557"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Orientação de licenciamento de colaboração do Azure Active Directory B2B

Com a colaboração B2B (Business-to-Business) do Azure Active Directory (Azure AD), você pode convidar usuários externos (ou "usuários convidados") para usar seus serviços pagos do Azure AD. Alguns recursos são gratuitos, mas para todos os recursos pagos do Azure AD, você pode convidar até cinco usuários convidados para cada licença de edição do Azure AD que você possui para um funcionário ou um usuário não convidado em seu locatário.

> [!NOTE]
> Consulte [Azure Active Directory preços](https://azure.microsoft.com/pricing/details/active-directory/) para obter detalhes sobre os preços do Azure AD e recursos de colaboração B2B.

O licenciamento de usuário convidado B2B é calculado e relatado automaticamente com base na proporção de 1:5. Atualmente, não é possível atribuir licenças de usuário convidado B2B diretamente aos usuários convidados.

Além disso, os usuários convidados podem usar recursos gratuitos do Azure AD sem requisitos de licenciamento adicionais. Os usuários convidados têm acesso aos recursos gratuitos do Azure AD, mesmo que você não tenha nenhuma licença paga do Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Exemplos: Calculando licenças de usuário convidado
Depois de determinar quantos usuários convidados precisam acessar seus serviços pagos do Azure AD, verifique se você tem licenças pagas do Azure AD suficientes para cobrir os usuários convidados na proporção 1:5 necessária. Eis alguns exemplos:

- Você deseja convidar usuários convidados 100 para seus aplicativos ou serviços do Azure AD e deseja atribuir gerenciamento e provisionamento de acesso a todos os usuários convidados. Você também deseja exigir MFA e acesso condicional para 50 desses usuários convidados. Para abordar essa combinação, você precisará de 10 licenças de Azure AD Basic e 10 Azure AD Premium licenças P1. Se você planeja usar recursos de proteção de identidade com seus usuários convidados, precisará de Azure AD Premium licenças P2 na mesma proporção de 1:5 para cobrir os usuários convidados.
- Você deseja convidar os usuários convidados 60 que precisam de MFA, portanto, você deve ter pelo menos 12 licenças do P1 Azure AD Premium. Você tem 10 funcionários com licenças Azure AD Premium P1, que permitiriam até 50 usuários convidados sob a proporção de licenciamento 1:5. Você precisará comprar duas licenças Premium P1 adicionais para cobrir 10 usuários convidados adicionais.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes recursos na colaboração B2B do Azure AD:

* [Preços de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Perguntas mais frequentes (FAQ) sobre a colaboração B2B do Azure Active Directory](faq.md)
