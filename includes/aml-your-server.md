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
ms.date: 09/26/2019
ms.openlocfilehash: e11b1c8c19db73ace069d4bfda3516cdbbceaa02
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476071"
---
1. Use as instruções em [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para instalar o sdk do Azure Machine Learning para Python

1. Crie um [espaço de trabalho Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Gravar um arquivo de [configuração](../articles/machine-learning/service/how-to-configure-environment.md#workspace) (**aml_config/config. JSON**).

1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```shell
    jupyter notebook
    ```
