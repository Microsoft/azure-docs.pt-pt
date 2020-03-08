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
ms.openlocfilehash: 4bce52ba3320506b85949493407dded1d52415a3
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673623"
---
1. Utilize as instruções do [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para instalar o SDK de Aprendizagem automática Azure para Python

1. Crie um espaço de [trabalho azure machine learning.](../articles/machine-learning/how-to-manage-workspace.md)

1. Escreva um ficheiro de [configuração](../articles/machine-learning/how-to-configure-environment.md#workspace) **(aml_config/config.json**).

1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```shell
    jupyter notebook
    ```
