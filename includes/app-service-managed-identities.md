---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649107"
---
Uma identidade gerida do Azure Ative Directory (Azure AD) permite que a sua aplicação aceda facilmente a outros recursos protegidos por AD Azure, como o Azure Key Vault. A identidade é gerida pela plataforma Azure e não o obriga a fornecer ou rodar quaisquer segredos. Para saber mais sobre identidades geridas em Azure AD, consulte [identidades geridas para recursos Azure](../articles/active-directory/managed-identities-azure-resources/overview.md).

A sua aplicação pode ser concedida a dois tipos de identidades:

- Uma **identidade atribuída** ao sistema está ligada à sua aplicação e é eliminada se a sua aplicação for eliminada. Uma aplicação só pode ter uma identidade atribuída ao sistema.
- Uma **identidade atribuída ao utilizador** é um recurso Azure autónomo que pode ser atribuído à sua aplicação. Uma aplicação pode ter várias identidades atribuídas ao utilizador.