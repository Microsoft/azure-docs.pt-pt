---
title: Minha página de entrada do Azure AD aceita contas da Microsoft | Microsoft Docs
description: Como as mensagens na tela refletem a pesquisa de nome de usuário durante a entrada
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024289"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opções de entrada para contas da Microsoft no Azure Active Directory

A página de entrada Microsoft 365 do Azure Active Directory (AD do Azure) dá suporte a contas corporativas ou de estudante e contas da Microsoft, mas dependendo da situação do usuário, ela pode ser uma ou outra ou ambas. Por exemplo, a página de entrada do Azure AD dá suporte a:

* Aplicativos que aceitam entradas de ambos os tipos de conta
* Organizações que aceitam convidados

## <a name="identification"></a>ID
Você pode saber se a página de entrada que sua organização usa dá suporte a contas da Microsoft examinando o texto de dica no campo username. Se o texto de dica diz "email, telefone ou Skype", a página de entrada dá suporte a contas da Microsoft.

![Diferença entre as páginas de entrada da conta](./media/signin-account-support/ui-prompt.png)

[As opções de entrada adicionais só funcionam para contas pessoais da Microsoft](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , mas não podem ser usadas para entrar em recursos de conta corporativa ou de estudante.

## <a name="next-steps"></a>Passos seguintes

[Personalizar sua identidade visual de entrada](../fundamentals/add-custom-domain.md)