---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649107"
---
Uma identidade gerida a partir do Azure Ative Directory (Azure AD) permite que a sua aplicação aceda facilmente a outros recursos protegidos por Azure, como o Azure Key Vault. A identidade é gerida pela plataforma Azure e não requer que forneça ou rode quaisquer segredos. Para obter mais sobre identidades geridas em Azure AD, consulte [identidades geridas para recursos Azure](../articles/active-directory/managed-identities-azure-resources/overview.md).

O seu pedido pode ser concedido dois tipos de identidades:

- Uma **identidade atribuída ao sistema** está ligada à sua aplicação e é eliminada se a sua aplicação for eliminada. Uma aplicação só pode ter uma identidade atribuída ao sistema.
- Uma **identidade atribuída ao utilizador** é um recurso autónomo da Azure que pode ser atribuído à sua aplicação. Uma aplicação pode ter várias identidades atribuídas ao utilizador.