---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623332"
---
* Ao criar um novo espaço de trabalho, pode criar automaticamente serviços necessários pelo espaço de trabalho ou utilizar os serviços existentes. Se pretender utilizar __os serviços existentes a partir de uma subscrição Azure diferente__ do espaço de trabalho, tem de registar o espaço de nomeS Azure Machine Learning na subscrição que contém esses serviços. Por exemplo, criar um espaço de trabalho na subscrição A que utiliza uma conta de armazenamento a partir da subscrição B, o espaço de nomeS Azure Machine Learning deve ser registado na subscrição B antes de poder utilizar a conta de armazenamento com o espaço de trabalho.

    O fornecedor de recursos para Azure Machine Learning é __o Microsoft.MachineLearningServices__. Para obter informações sobre como ver se está registado e como registá-lo, consulte os [fornecedores e tipos de recursos da Azure.](../articles/azure-resource-manager/management/resource-providers-and-types.md)

    > [!IMPORTANT]
    > Isto aplica-se apenas aos recursos fornecidos durante a criação do espaço de trabalho; Contas de armazenamento Azure, Registo de Contentores Azure, Cofre de Chaves Azure e Insights de Aplicação.
