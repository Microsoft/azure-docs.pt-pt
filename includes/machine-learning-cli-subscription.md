---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296890"
---
Após o início do sessão, consulte uma lista de subscrições associadas à sua conta Azure. A informação `isDefault: true` de subscrição é a subscrição ativada atualmente para comandos Azure CLI. Esta subscrição deve ser a mesma que contém o seu espaço de trabalho Azure Machine Learning. Pode encontrar o ID de subscrição do [portal Azure](https://portal.azure.com) visitando a página de visão geral para o seu espaço de trabalho. Também pode utilizar o SDK para obter o ID de subscrição do objeto espaço de trabalho. Por exemplo, `Workspace.from_config().subscription_id`.
    
Para selecionar outra subscrição, utilize o comando conjunto de [contas az](https://docs.microsoft.com/cli/azure/account#az-account-set) com o ID de subscrição para mudar. Para obter mais informações sobre a seleção de subscrições, consulte [Utilize várias Assinaturas Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).