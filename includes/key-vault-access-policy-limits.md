---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 8c8c9563c954e3d1a84ea2b9f411187d5fb9f226
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026076"
---
O Key Vault suporta até 1024 entradas de política de acesso, com cada entrada a conceder um conjunto distinto de permissões para um principal de segurança específico. Devido a esta limitação, recomendamos a atribuição de políticas de acesso a grupos de utilizadores, sempre que possível, em vez de utilizadores individuais. A utilização de grupos torna muito mais fácil gerir permissões para várias pessoas na sua organização. Para obter mais informações, consulte [Gerir a aplicação e o acesso a recursos utilizando grupos de Diretório Ativo Azure](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

Para obter todos os detalhes sobre o controlo de acesso ao Cofre chave, consulte [a segurança do Cofre da Chave Azure: Gestão de identidade e acesso](../articles/key-vault/general/overview-security.md#identity-and-access-management).