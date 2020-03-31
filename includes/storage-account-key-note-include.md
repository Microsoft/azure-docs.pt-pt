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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460542"
---
## <a name="protect-your-access-keys"></a>Proteja as suas chaves de acesso

As chaves de acesso à sua conta de armazenamento são semelhantes a uma palavra-passe raiz para a sua conta de armazenamento. Tenha sempre cuidado para proteger as suas chaves de acesso. Utilize o Cofre de Chaves Azure para gerir e rodar as chaves de forma segura. Evite distribuir chaves de acesso a outros utilizadores, codificando-as com força ou guarde-as em qualquer lugar em texto simples que seja acessível a outros. Rode as chaves se acreditar que podem ter sido comprometidas.

Se possível, utilize o Azure Ative Directory (Azure AD) para autorizar pedidos de armazenamento blob e fila em vez de Chave Partilhada. A Azure AD proporciona uma segurança superior e facilidade de utilização sobre a Chave Partilhada. Para obter mais informações sobre a autorização de acesso a dados com a AD Azure, consulte [Autorizar o acesso a blobs e filas Azure utilizando o Diretório Ativo Azure](../articles/storage/common/storage-auth-aad.md).
