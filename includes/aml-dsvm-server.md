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
ms.openlocfilehash: ac5656a8efe1dee932bfe753f8bdc06b787e20af
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529797"
---
1. [Crie um espaço de trabalho Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um arquivo de configuração do espaço de trabalho ao diretório clonado usando um destes métodos:

    * No [portal do Azure](https://ms.portal.azure.com), selecione **baixar config. JSON** na seção **visão geral** do seu espaço de trabalho. 

    ![Baixar config. JSON](./media/aml-dsvm-server/download-config.png)

    * Crie um novo espaço de trabalho usando código no bloco de anotações [Configuration. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) no diretório clonado.

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```shell
    jupyter notebook
    ```