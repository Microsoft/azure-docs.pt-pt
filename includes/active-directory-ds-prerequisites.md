---
title: incluir ficheiro
description: incluir arquivo com pré-requisitos
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426939"
---
> [!IMPORTANT]
> **Habilite a sincronização de hash de senha para Azure AD Domain Services, antes de concluir as tarefas neste artigo.**
>
> Siga as instruções abaixo, dependendo do tipo de usuário em seu diretório do Azure AD. Conclua os dois conjuntos de instruções se você tiver uma mistura de contas de usuário sincronizadas somente na nuvem e em seu diretório do Azure AD. Talvez você não consiga realizar as seguintes operações caso esteja tentando usar uma conta de convidado B2B (exemplo, seu Gmail ou MSA de um provedor de identidade diferente que permitimos) porque não temos a senha para esses usuários sincronizados com o domínio gerenciado como estes são contas de convidado no diretório. As informações completas sobre essas contas, incluindo suas senhas, estão fora do Azure AD e, como essas informações não estão no Azure AD, portanto, nem mesmo são sincronizadas com o domínio gerenciado. 
> - [Instruções para contas de usuário somente em nuvem](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instruções para contas de usuário sincronizadas de um diretório local](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
