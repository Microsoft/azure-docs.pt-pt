---
title: Será que a minha página de entrada de Anúncio saque a Azure aceita contas da Microsoft / Microsoft Docs
description: Como as mensagens no ecrã refletem a procura de nome de utilizador durante o iniciar o sessão
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74024289"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opções de entrada para contas da Microsoft no Diretório Ativo do Azure

A página de inscrição do Microsoft 365 para o Azure Ative Directory (Azure AD) suporta contas de trabalho ou de escola e contas da Microsoft, mas dependendo da situação do utilizador, pode ser uma ou outra ou ambas. Por exemplo, a página de entrada da AD Azure suporta:

* Apps que aceitam inscrições de ambos os tipos de conta
* Organizações que aceitam hóspedes

## <a name="identification"></a>Identificação
Pode dizer se a página de início de sessão que a sua organização utiliza suporta as contas da Microsoft, olhando para o texto de sugestão no campo do nome de utilizador. Se o texto da dica diz "E-mail, telefone ou Skype", a página de inscrição suporta as contas da Microsoft.

![Diferença entre as páginas de inscrição da conta](./media/signin-account-support/ui-prompt.png)

[Opções adicionais de inscrição funcionam apenas para contas pessoais](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) da Microsoft, mas não podem ser usadas para iniciar sessão para trabalhar ou recursos de conta escolar.

## <a name="next-steps"></a>Passos seguintes

[Personalize a sua marca de sessão](../fundamentals/add-custom-domain.md)