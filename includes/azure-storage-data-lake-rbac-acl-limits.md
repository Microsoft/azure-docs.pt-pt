---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017691"
---
| Mecanismo | Âmbito |Limites | Nível de permissão suportado |
|---|---|---|---|
| RBAC do Azure | Contas de armazenamento, contentores. <br>Designação de funções de recurso cross azure a nível de subscrição ou grupo de recursos. | Atribuições de funções Azure 2000 numa subscrição | Funções Azure (incorporado ou personalizado) |
| ACL| Diretório, arquivo |32 entradas ACL (efetivamente 28 entradas ACL) por ficheiro e por diretório. Os ACLs de acesso e padrão têm cada um o seu próprio limite de entrada de 32 ACL. |Permissão ACL|
