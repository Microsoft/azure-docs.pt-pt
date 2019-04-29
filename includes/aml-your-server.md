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
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60753459"
---
1. Utilize as instruções em [criar uma área de trabalho do serviço do Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) para criar um ambiente de Miniconda, crie uma área de trabalho e gravar um arquivo de configuração da área de trabalho (**aml_config/config.json**) .

1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um ficheiro de configuração da área de trabalho com qualquer um dos seguintes métodos:
    * Copiar o **aml_config/config.json** ficheiro que criou com o guia de introdução de pré-requisitos para o diretório clonado.
    * Criar uma nova área de trabalho com o código na [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Inicie o servidor de blocos de notas a partir do diretório clonado.
    
    ```shell
    jupyter notebook
    ```