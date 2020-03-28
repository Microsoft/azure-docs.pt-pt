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
ms.date: 03/05/2020
ms.openlocfilehash: 2504245a14eee5308e046148f8073cf34ffa8143
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485989"
---
1. [Crie um espaço de trabalho azure machine learning.](../articles/machine-learning/how-to-manage-workspace.md)

1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um ficheiro de configuração do espaço de trabalho ao diretório clonado utilizando qualquer um destes métodos:

    * No [portal Azure,](https://ms.portal.azure.com)selecione **Download config.json** da secção **de visão geral** do seu espaço de trabalho. 

    ![Baixar config.json](./media/aml-dsvm-server/download-config.png)

    * Crie um novo espaço de trabalho utilizando código no caderno [configuração.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) no seu diretório clonado.

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```bash
    jupyter notebook
    ```