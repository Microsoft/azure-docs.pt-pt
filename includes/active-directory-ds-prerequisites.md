---
title: incluir ficheiro
description: incluir o ficheiro com a pré-requisitos
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
ms.openlocfilehash: 1fba8cc9ae40cf5539016bbd73de65f557a64136
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184841"
---
> [!IMPORTANT]
> **Ative a sincronização de hash de palavra-passe para o Azure AD Domain Services, antes de concluir as tarefas neste artigo.**
>
> Siga as instruções abaixo, dependendo do tipo de utilizadores no diretório do Azure AD. Conclua os dois conjuntos de instruções se tiver uma mistura de contas de utilizador apenas na cloud e sincronizadas no diretório do Azure AD. Pode não ser capaz de realizar as seguintes operações no caso de que está a tentar utilizar uma conta de convidado de B2B (exemplo, o gmail ou MSA de um fornecedor de identidade diferente que permitimos) porque não temos a palavra-passe para estes utilizadores sincronizados com o domínio gerido como estes são contas de convidado no diretório. As informações completas sobre estas contas, incluindo as palavras-passe seria fora do Azure AD e como essas informações não estão no Azure AD, por conseguinte, ele não até mesmo obter sincronizado com o domínio gerido. 
> - [Instruções para contas de utilizador apenas na cloud](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instruções para contas de utilizador sincronizadas a partir de um diretório no local](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
