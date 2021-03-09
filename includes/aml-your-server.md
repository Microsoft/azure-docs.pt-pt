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
ms.openlocfilehash: 0d7622217d192a100fd809c32c9e85970cf61fd7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511036"
---
1. Use as instruções na [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)  para instalar o Azure Machine Learning SDK para Python

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