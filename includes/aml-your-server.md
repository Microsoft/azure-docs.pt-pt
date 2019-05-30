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
ms.openlocfilehash: ce8b117a3cbe0e3a5c4265729ccf5c0264241013
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391801"
---
1. Utilize as instruções em [criar uma área de trabalho do serviço do Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) para fazer o seguinte:
    * Criar um ambiente de Miniconda
    * Instalar o Azure Machine Learning SDK para Python
    * Criar uma área de trabalho
    * Gravar um arquivo de configuração da área de trabalho (**aml_config/config.json**).

1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```shell
    jupyter notebook
    ```