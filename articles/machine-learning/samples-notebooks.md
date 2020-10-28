---
title: Exemplo Cadernos Jupyter
titleSuffix: Azure Machine Learning
description: Encontre e use exemplo os cadernos jupyter para explorar o Azure Machine Learning Python para SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8b4256f33a143c711a23a3564c2a46c69e0783b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675761"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Explore a aprendizagem da máquina de Azure com cadernos Jupyter

> [!NOTE] 
> Um repositório de exemplos orientado pela comunidade pode ser encontrado em https://github.com/Azure/azureml-examples .

O [exemplo do repositório de cadernos de aprendizagem de máquinas Azure](https://github.com/azure/machinelearningnotebooks) inclui as mais recentes amostras de Python SDK de aprendizagem de máquinas Azure. Estes cadernos Juypter são projetados para ajudá-lo a explorar o SDK e servir como modelos para seus próprios projetos de machine learning.

Este artigo mostra-lhe como aceder ao repositório a partir dos seguintes ambientes:

- [Instância de computação do Azure Machine Learning](#notebookvm)
- [Traga o seu próprio servidor de cadernos](#byo)
- [Máquina Virtual de Ciência de Dados](#dsvm)

> [!NOTE]
> Depois de clonado o repositório, encontrará cadernos tutoriais na pasta **dos tutoriais** e cadernos específicos de funcionalidades na pasta **como usar-azureml.**

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Obtenha amostras em Azure Machine Learning caso

A maneira mais fácil de começar com as amostras é completar o [Tutorial: Ambiente de configuração e espaço de trabalho.](tutorial-1st-experiment-sdk-setup.md) Uma vez concluído, terá um servidor de portátil dedicado pré-carregado com o SDK e o repositório de amostras. Não são necessários downloads ou instalações.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Obtenha amostras no seu servidor de cadernos

Se quiser trazer o seu próprio servidor de cadernos para o desenvolvimento local, siga estes passos:

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

Estas instruções instalam os pacotes SDK base necessários para os cadernos de arranque rápido e tutorial. Outros cadernos de amostras podem exigir que instale componentes extras. Para obter mais informações, consulte [instalar o Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Obtenha amostras em DSVM

A Máquina Virtual de Ciência de Dados (DSVM) é uma imagem de VM personalizada criada especificamente para a ciência de dados. Se [criar um DSVM,](how-to-configure-environment.md#dsvm)o SDK e o servidor de portátil estão instalados e configurados para si. No entanto, ainda terá de criar um espaço de trabalho e clonar o repositório de amostras.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os cadernos de [amostras](https://github.com/Azure/MachineLearningNotebooks) para descobrir o que a Azure Machine Learning pode fazer.

Para mais projetos e exemplos de amostras do GitHub, consulte estes repos:
+ [Exemplos Azure/azureml](https://github.com/Azure/azureml-examples)
+ [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
+ [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

Experimente estes tutoriais:

- [Treine e implemente um modelo de classificação de imagem com o MNIST](tutorial-train-models-with-aml.md)

- [Prepare dados e use machine learning automatizado para treinar um modelo de regressão com o conjunto de dados de táxi nyc](tutorial-auto-train-models.md)
