---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: 6e9f349f2fd90fdb12d8d26ca904e504b75d9db2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445179"
---
* Ao criar um novo espaço de trabalho, pode criar automaticamente serviços necessários pelo espaço de trabalho ou utilizar os serviços existentes. Se pretender utilizar __os serviços existentes a partir de uma subscrição Azure diferente__ do espaço de trabalho, tem de registar o espaço de nomeS Azure Machine Learning na subscrição que contém esses serviços. Por exemplo, criar um espaço de trabalho na subscrição A que utiliza uma conta de armazenamento a partir da subscrição B, o espaço de nomeS Azure Machine Learning deve ser registado na subscrição B antes de poder utilizar a conta de armazenamento com o espaço de trabalho.

    O fornecedor de recursos para Azure Machine Learning é __o Microsoft.MachineLearningService__. Para obter informações sobre como ver se está registado e como registá-lo, consulte os [fornecedores e tipos de recursos da Azure.](../articles/azure-resource-manager/management/resource-providers-and-types.md)

    > [!IMPORTANT]
    > Isto aplica-se apenas aos recursos fornecidos durante a criação do espaço de trabalho; Contas de armazenamento Azure, Registo de Contentores Azure, Cofre de Chaves Azure e Insights de Aplicação.