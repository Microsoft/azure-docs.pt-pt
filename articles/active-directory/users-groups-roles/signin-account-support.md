---
title: Como saber se uma página de início de sessão do Azure AD aceita contas da Microsoft | Documentos da Microsoft
description: Como no ecrã de mensagens refletem a pesquisa de nome de utilizador durante o início de sessão
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d26ff0f9259e3531259673f94fe477444cc786b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491600"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opções de início de sessão das contas da Microsoft no Azure Active Directory

A página de início de sessão do Microsoft 365 para o Azure Active Directory (Azure AD) suporta contas escolar ou profissional e contas Microsoft, mas dependendo da situação do usuário, é possível um ou outro ou ambos. Por exemplo, a página de início de sessão do Azure AD suporta:

* Aplicações que aceitam inícios de sessão de ambos os tipos de conta
* Organizações que aceitam convidados

## <a name="identification"></a>Identificação
Pode informar se a página de início de sessão na que sua organização utiliza suporta contas Microsoft examinando o texto de sugestão no campo de nome de utilizador. Se o texto de sugestão diz "E-Mail, telefone ou Skype", a página de início de sessão suporta contas Microsoft.

![Diferença entre as páginas de início de sessão de conta](./media/signin-account-support/ui-prompt.png)

[Opções de início de sessão adicionais trabalham apenas para contas Microsoft pessoais](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , mas não pode ser utilizado para iniciar sessão a profissional ou escolar recursos da conta.

## <a name="next-steps"></a>Passos Seguintes

[Personalizar o seu início de sessão de imagem corporativa](../fundamentals/add-custom-domain.md)