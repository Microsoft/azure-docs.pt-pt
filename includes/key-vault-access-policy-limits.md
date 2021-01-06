---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934551"
---
O Key Vault suporta até 1024 entradas de política de acesso, com cada entrada a conceder um conjunto distinto de permissões para um principal de segurança específico. Devido a esta limitação, recomendamos a atribuição de políticas de acesso a grupos de utilizadores, sempre que possível, em vez de utilizadores individuais. A utilização de grupos torna muito mais fácil gerir permissões para várias pessoas na sua organização. Para obter mais informações, consulte [Gerir a aplicação e o acesso a recursos utilizando grupos de Diretório Ativo Azure](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

Para obter todos os detalhes sobre o controlo de acesso ao Cofre chave, consulte [a segurança do Cofre da Chave Azure: Gestão de identidade e acesso](../articles/key-vault/general/security-overview.md#identity-management).