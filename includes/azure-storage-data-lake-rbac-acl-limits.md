---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131637"
---
| Mecanismo | Âmbito |Limites | Nível de permissão suportado |
|---|---|---|---|
| RBAC | Contas de armazenamento, contentores. <br>Atribuições de funções de rbac de recurso transversal a nível de subscrição ou grupo de recursos. | Atribuições de funções de 2000 RBAC numa subscrição | Funções RBAC (incorporado ou personalizado) |
| ACL| Diretório, arquivo |32 entradas ACL (efetivamente 28 entradas ACL) por ficheiro e por diretório. Os ACLs de acesso e padrão têm cada um o seu próprio limite de entrada de 32 ACL. |Permissão ACL|
