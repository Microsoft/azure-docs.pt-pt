---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79486120"
---
1. Use as instruções na [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)  para instalar o Azure Machine Learning SDK para Python

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