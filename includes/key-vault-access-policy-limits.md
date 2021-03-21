---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934551"
---
O cofre-chave suporta até 1024 entradas na política de acesso, com cada entrada a conceder um conjunto distinto de permissões a um determinado diretor de segurança. Devido a esta limitação, recomendamos a atribuição de políticas de acesso a grupos de utilizadores, sempre que possível, em vez de utilizadores individuais. A utilização de grupos torna muito mais fácil gerir permissões para várias pessoas na sua organização. Para obter mais informações, consulte [Gerir a aplicação e o acesso a recursos utilizando grupos de Diretório Ativo Azure](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

Para obter todos os detalhes sobre o controlo de acesso ao Cofre chave, consulte [a segurança do Cofre da Chave Azure: Gestão de identidade e acesso](../articles/key-vault/general/security-overview.md#identity-management).