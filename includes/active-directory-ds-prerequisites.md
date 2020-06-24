---
title: ficheiro de inclusão
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "68426939"
---
> [!IMPORTANT]
> **Ative a sincronização de hash de palavra-passe nos Serviços de Domínio Azure AD, antes de completar as tarefas neste artigo.**
>
> Siga as instruções abaixo, dependendo do tipo de utilizadores no seu diretório AD Azure. Preencha ambos os conjuntos de instruções se tiver uma mistura de contas de utilizador sincronizadas e sincronizadas no seu diretório AD Azure. Poderá não conseguir realizar as seguintes operações caso esteja a tentar utilizar uma conta B2B Guest (por exemplo, o seu gmail ou MSA de um fornecedor de identidade diferente que permitimos) porque não temos a palavra-passe para estes utilizadores sincronizados para o domínio gerido, uma vez que estas são contas de hóspedes no diretório. As informações completas sobre estas contas, incluindo as suas palavras-passe, estariam fora do Azure AD e como esta informação não está no AD Azure, pelo que nem sequer se sincroniza com o domínio gerido. 
> - [Instruções para contas de utilizadores apenas na nuvem](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instruções para contas de utilizador sincronizadas a partir de um diretório no local](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
