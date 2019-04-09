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
ms.date: 04/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a62c4d56fbfca34ff6291863149b078f7ddc6680
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288595"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opções de início de sessão das contas da Microsoft no Azure Active Directory

A página de início de sessão do Microsoft 365 para o Azure Active Directory (Azure AD) oferece suporte a uma ou ambas contas escolar ou profissional e contas da Microsoft, dependendo da situação, para oferecer suporte a:

* Aplicações que aceitam inícios de sessão de ambos os tipos de conta
* Organizações que aceitam convidados

## <a name="identification"></a>Identificação
Pode informar se a página de início de sessão na que sua organização utiliza suporta contas Microsoft examinando o texto de sugestão no campo de nome de utilizador. Se o texto de sugestão diz "E-Mail, telefone ou Skype", a página de início de sessão suporta contas Microsoft.

![Diferença entre as páginas de início de sessão de conta](./media/signin-account-support/ui-prompt.png)

[Opções de início de sessão adicionais trabalham apenas para contas Microsoft pessoais](index.yml) , mas não pode ser utilizado para iniciar sessão a profissional ou escolar recursos da conta.

## <a name="next-steps"></a>Passos Seguintes

[Personalizar o seu início de sessão de imagem corporativa](../fundamentals/add-custom-domain.md)