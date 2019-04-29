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
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60753470"
---
1. Concluir o [guia de início rápido do Azure Machine Learning Python](../articles/machine-learning/service/quickstart-create-workspace-with-python.md) para criar uma área de trabalho.  Fique à vontade ignorar a **utilizar o bloco de notas** secção se desejar.
1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. Adicione um ficheiro de configuração da área de trabalho com qualquer um dos seguintes métodos:
    * Copiar o **aml_config\config.json** ficheiro que criou com o guia de introdução de pré-requisitos para o diretório clonado.
    * Criar uma nova área de trabalho com o código na [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) bloco de notas no seu diretório clonado.
1. Inicie o servidor de blocos de notas a partir do diretório clonado.
    
    ```shell
    jupyter notebook
    ```