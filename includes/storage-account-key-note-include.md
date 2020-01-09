---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460542"
---
## <a name="protect-your-access-keys"></a>Proteger suas chaves de acesso

As chaves de acesso da conta de armazenamento são semelhantes a uma senha raiz para sua conta de armazenamento. Sempre tenha cuidado para proteger suas chaves de acesso. Use Azure Key Vault para gerenciar e girar suas chaves com segurança. Evite distribuir chaves de acesso para outros usuários, embuti-las em código ou salvá-las em qualquer lugar em texto sem formatação que seja acessível a outras pessoas. Gire suas chaves se acreditar que elas podem ter sido comprometidas.

Se possível, use Azure Active Directory (Azure AD) para autorizar solicitações para o armazenamento de BLOBs e de filas em vez da chave compartilhada. O Azure AD fornece segurança superior e facilidade de uso sobre chave compartilhada. Para obter mais informações sobre como autorizar o acesso a dados com o Azure AD, consulte [autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
