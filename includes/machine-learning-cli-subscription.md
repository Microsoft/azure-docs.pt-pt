---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81616850"
---
> [!TIP]
> Após o início do sessão, consulte uma lista de subscrições associadas à sua conta Azure. A informação `isDefault: true` de subscrição é a subscrição ativada atualmente para comandos Azure CLI. Esta subscrição deve ser a mesma que contém o seu espaço de trabalho Azure Machine Learning. Pode encontrar o ID de subscrição do [portal Azure](https://portal.azure.com) visitando a página de visão geral para o seu espaço de trabalho. Também pode utilizar o SDK para obter o ID de subscrição do objeto espaço de trabalho. Por exemplo, `Workspace.from_config().subscription_id`.
> 
> Para selecionar outra subscrição, utilize o `az account set -s <subscription name or ID>` comando e especifique o nome de subscrição ou id para mudar. Para obter mais informações sobre a seleção de subscrições, consulte [Utilize várias Assinaturas Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).