---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ba715d510dc296ffa8f9c0ee58841f284416a118
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86027341"
---
## <a name="protect-your-access-keys"></a>Proteja as suas chaves de acesso

As chaves de acesso à sua conta de armazenamento são semelhantes a uma senha de raiz para a sua conta de armazenamento. Tenha sempre cuidado para proteger as suas chaves de acesso. Utilize o Cofre da Chave Azure para gerir e rodar as chaves de forma segura. Evite distribuir chaves de acesso a outros utilizadores, codificando-as duramente ou guardando-as em qualquer lugar em texto simples que seja acessível a outros. Rode as chaves se acredita que podem ter sido comprometidas.

> [!NOTE]
> A Microsoft recomenda a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos contra dados de blob e fila, se possível, em vez de "Shared Key". A Azure AD proporciona segurança superior e facilidade de utilização sobre a Chave Partilhada. Para obter mais informações sobre a autorização de acesso aos dados com a Azure AD, consulte [o Acesso Autorizado a blobs e filas Azure através do Azure Ative Directory](../articles/storage/common/storage-auth-aad.md).
