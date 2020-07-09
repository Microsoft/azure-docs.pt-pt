---
title: A minha página de ad AD Azure aceita as contas da Microsoft ? Microsoft Docs
description: Como as mensagens no ecrã refletem a pesquisa do nome de utilizador durante o pré-in
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81ba7f77bb3396f49c3cf41d2266567aa48a8257
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731363"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opções de inscrição para contas da Microsoft no Azure Ative Directory

A página de sindução microsoft 365 para Azure Ative Directory (Azure AD) suporta contas de trabalho ou escolas e contas microsoft, mas dependendo da situação do utilizador, pode ser um ou outro ou ambos. Por exemplo, o suporte da página de inscrição AZure AD:

* Aplicativos que aceitam ins-ins de ambos os tipos de conta
* Organizações que aceitam hóspedes

## <a name="identification"></a>Identificação
Pode dizer se a página de início de s nota que a sua organização utiliza suporta contas da Microsoft olhando para o texto sugestão no campo do nome de utilizador. Se o texto de sugestão diz "E-mail, telefone ou Skype", a página de inscrição suporta as contas da Microsoft.

![Diferença entre páginas de inscrição de conta](./media/signin-account-support/ui-prompt.png)

[As opções adicionais de inscrição funcionam apenas para contas pessoais da Microsoft,](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) mas não podem ser usadas para iniciar sessão no trabalho ou recursos de conta escolar.

## <a name="next-steps"></a>Passos seguintes

[Personalize a sua marca de sinalização](../fundamentals/add-custom-domain.md)