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
ms.openlocfilehash: a882a874574395095e98079cd0f8aa4a4987c749
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391804"
---
1. [Criar uma área de trabalho do serviço do Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md).

1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um ficheiro de configuração da área de trabalho para o diretório clonado com qualquer um dos seguintes métodos:

    * Na [portal do Azure](https://ms.portal.azure.com), selecione **transferir config** do **descrição geral** secção da área de trabalho. 

    ![Baixe o JSON](./media/aml-dsvm-server/download-config.png)

    * Criar uma nova área de trabalho com o código na [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) bloco de notas no seu diretório clonado.

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```shell
    jupyter notebook
    ```