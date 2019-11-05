---
title: Exemplo de notebooks Jupyter
titleSuffix: Azure Machine Learning
description: Encontre e use os notebooks Jupyter de exemplo para explorar o Azure Machine Learning Python para SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 145ab994bbd2ac2445947968ccdeadbb730eb76a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476095"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Explorar Azure Machine Learning com notebooks Jupyter

O [repositório do exemplo Azure Machine Learning notebooks](https://github.com/azure/machinelearningnotebooks) inclui os exemplos mais recentes do SDK do Python Azure Machine Learning. Esses blocos de anotações do Juypter foram projetados para ajudá-lo a explorar o SDK e servir como modelos para seus próprios projetos de Machine Learning.

Este artigo mostra como acessar o repositório nos seguintes ambientes:

- [Azure Machine Learning instância de computação](#notebookvm)
- [Traga seu próprio servidor de notebook](#byo)
- [Máquina Virtual de Ciência de Dados](#dsvm)

> [!NOTE]
> Depois de clonar o repositório, você encontrará os notebooks do tutorial na pasta **tutoriais** e nos notebooks específicos do recurso na pasta **como usar-azureml** .

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Obter exemplos em Azure Machine Learning instância de computação

A maneira mais fácil de começar a usar os exemplos é concluir o [tutorial: configuração de ambiente e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md). Depois de concluído, você terá um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo. Não é necessário nenhum download ou instalação.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Obter exemplos em seu servidor de notebook

Se você quiser colocar seu próprio servidor de notebook para o desenvolvimento local, siga estas etapas:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Estas instruções instalam os pacotes do SDK base necessários para os notebooks de início rápido e de tutorial. Outros blocos de anotações de exemplo podem exigir a instalação de componentes adicionais. Para obter mais informações, consulte [instalar o SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Obter exemplos em DSVM

O Máquina Virtual de Ciência de Dados (DSVM) é uma imagem de VM personalizada criada especificamente para a ciência de dados. Se você [criar um DSVM](how-to-configure-environment.md#dsvm), o SDK e o servidor de notebook serão instalados e configurados para você. No entanto, você ainda precisará criar um espaço de trabalho e clonar o repositório de exemplo.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os [notebooks de exemplo](https://aka.ms/aml-notebooks) para descobrir o que Azure Machine Learning pode fazer ou Experimente estes tutoriais:

- [Treinar e implantar um modelo de classificação de imagem com MNIST](tutorial-train-models-with-aml.md)

- [Preparar dados e usar o aprendizado de máquina automatizado para treinar um modelo de regressão com o conjunto de dados de táxi de NYC](tutorial-auto-train-models.md)
