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
ms.openlocfilehash: e289cf7aea6e0ea46ff049f3ea8bf9e1517e8aaf
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673605"
---
1. [Crie um espaço de trabalho azure machine learning.](../articles/machine-learning/how-to-manage-workspace.md)

1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um ficheiro de configuração do espaço de trabalho ao diretório clonado utilizando qualquer um destes métodos:

    * No [portal Azure,](https://ms.portal.azure.com)selecione **Download config.json** da secção **de visão geral** do seu espaço de trabalho. 

    ![Baixar config.json](./media/aml-dsvm-server/download-config.png)

    * Crie um novo espaço de trabalho utilizando código no caderno [configuração.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) no seu diretório clonado.

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```shell
    jupyter notebook
    ```