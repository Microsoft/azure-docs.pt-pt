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
ms.openlocfilehash: 6e7580abfd113a279e2223b9cc9665f6a0b86bc8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562129"
---
1. Use as instruções na [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?view=azure-ml-py)  para instalar o Azure Machine Learning SDK para Python

1. Criar um espaço de trabalho para [aprendizagem automática Azure.](../articles/machine-learning/how-to-manage-workspace.md)

1. Escreva um ficheiro [de configuração](../articles/machine-learning/how-to-configure-environment.md#workspace) **(aml_config/config.js).**

1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```bash
    jupyter notebook
    ```