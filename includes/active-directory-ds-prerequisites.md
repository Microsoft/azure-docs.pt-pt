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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426939"
---
> [!IMPORTANT]
> **Ative a sincronização de hash de palavra-passe para os Serviços de Domínio AD Azure, antes de completar as tarefas neste artigo.**
>
> Siga as instruções abaixo, dependendo do tipo de utilizadores no seu diretório Azure AD. Complete ambos os conjuntos de instruções se tiver uma mistura de contas de utilizador apenas em nuvem e sincronizadas no seu diretório Azure AD. Pode não ser capaz de realizar as seguintes operações no caso de estar a tentar utilizar uma conta De hóspedes B2B (exemplo, o seu gmail ou MSA de um fornecedor de identidade diferente que permitimos) porque não temos a palavra-passe para estes utilizadores sincronizados com o domínio gerido como estes são contas de hóspedes no diretório. A informação completa sobre estas contas, incluindo as suas palavras-passe, estaria fora da AD Azure e como esta informação não está em Azure AD, por isso nem sequer é sincronizada com o domínio gerido. 
> - [Instruções para contas de utilizadores apenas na nuvem](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instruções para contas de utilizador sincronizadas a partir de um diretório no local](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
