---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 1fde2947719079e411bc233bcce426feec8fa996
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631053"
---
> [!IMPORTANT]
> A instância de Coss DB é criada num grupo de recursos gerido pela Microsoft na __sua subscrição__ , juntamente com todos os recursos de que necessita. Isto significa que és acusado por este caso de Cosmos DB. O grupo de recursos geridos está nomeado no `<AML Workspace Resource Group Name><GUID>` formato. Se o seu espaço de trabalho Azure Machine Learning utiliza um ponto final privado, uma rede virtual também é criada para a instância DeSB cosmos. Este VNet é usado para garantir a comunicação entre Cosmos DB e Azure Machine Learning.
> 
> * __Não elimine o grupo de recursos__ que contém esta instância Descs DB cosmos, ou qualquer dos recursos automaticamente criados neste grupo. Se precisar de eliminar o grupo de recursos, instância Cosmos DB, etc., deve eliminar o espaço de trabalho Azure Machine Learning que o utiliza. O grupo de recursos, a instância de Cosmos DB e outros recursos automaticamente criados são eliminados quando o espaço de trabalho associado é eliminado.
> * As [__Unidades de Pedido__](../articles/cosmos-db/request-units.md) predefinidos para esta conta Cosmos DB estão definidas em __8000__ . __Alterar este valor não é suportado.__
> * Não __é possível fornecer o seu próprio VNet para uso com a instância de Cosmos DB__ que é criada. Também __não é possível modificar a rede virtual.__ Por exemplo, não é possível alterar o intervalo de endereço IP que utiliza.