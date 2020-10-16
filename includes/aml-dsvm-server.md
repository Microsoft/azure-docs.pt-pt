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
ms.openlocfilehash: 2504245a14eee5308e046148f8073cf34ffa8143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79485989"
---
1. Criar um espaço de trabalho para [aprendizagem automática Azure.](../articles/machine-learning/how-to-manage-workspace.md)

1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um ficheiro de configuração do espaço de trabalho ao diretório clonado utilizando qualquer um destes métodos:

    * No [portal Azure,](https://ms.portal.azure.com)selecione  **Baixar config.jsna** secção **Visão Geral** do seu espaço de trabalho. 

    ![Baixar config.jsem](./media/aml-dsvm-server/download-config.png)

    * Crie um novo espaço de trabalho utilizando código no caderno [configuração.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) no seu diretório clonado.

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```bash
    jupyter notebook
    ```