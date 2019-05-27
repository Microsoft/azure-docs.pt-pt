---
title: incluir ficheiro
description: incluir ficheiro
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: 02ef0d6c7c8ddc7088938d9c8ea379e3b97f3045
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158731"
---
1. [Criar uma área de trabalho do serviço do Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md).

1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um ficheiro de configuração da área de trabalho para o diretório clonado com qualquer um dos seguintes métodos:

    * Na [portal do Azure](https://ms.portal.azure.com), selecione **transferir config** do **descrição geral** secção da área de trabalho. 

    ![Transferir o config.json](./media/aml-dsvm-server/download-config.png)

    * Criar uma nova área de trabalho com o código na [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) bloco de notas no seu diretório clonado.


1. Inicie o servidor de blocos de notas a partir do diretório clonado.
    
    ```shell
    jupyter notebook
    ```