---
title: Exemplo de cadernos Jupyter
titleSuffix: Azure Machine Learning
description: Encontre e use os cadernos Jupyter para explorar o Python de Aprendizagem automática Azure para SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 7242b82ee5c43878a33731bd1f02b685020f22b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78673633"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Explore a Aprendizagem automática Azure com os cadernos jupyter

O [exemplo do repositório](https://github.com/azure/machinelearningnotebooks) de cadernos de aprendizagem automática azure inclui as mais recentes amostras Azure Machine Learning Python SDK. Estes cadernos Juypter são projetados para ajudá-lo a explorar o SDK e servir como modelos para seus próprios projetos de aprendizagem automática.

Este artigo mostra-lhe como aceder ao repositório a partir dos seguintes ambientes:

- [Instância computacional de aprendizagem automática azure](#notebookvm)
- [Traga o seu próprio servidor de caderno](#byo)
- [Máquina Virtual de Ciência de Dados](#dsvm)

> [!NOTE]
> Depois de clonar o repositório, encontrará cadernos tutoriais na pasta dos **tutoriais** e cadernos específicos de funcionalidades na pasta **"Como usar-azureml".**

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Obtenha amostras no caso do cálculo do Azure Machine Learning

A maneira mais fácil de começar com as amostras é completar o [Tutorial: Ambiente de configuração e espaço de trabalho.](tutorial-1st-experiment-sdk-setup.md) Uma vez concluído, terá um servidor de portátil dedicado pré-carregado com o SDK e o repositório de amostras. Não são necessários downloads ou instalações.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Obtenha amostras no seu servidor de cadernos

Se quiser trazer o seu próprio servidor de cadernos para o desenvolvimento local, siga estes passos:

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

Estas instruções instalam as embalagens Base SDK necessárias para os cadernos de arranque rápido e tutoriais. Outros cadernos de amostrapodem exigir que instale componentes extras. Para mais informações, consulte [Instale o SDK de Aprendizagem automática Azure para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Obtenha amostras no DSVM

A Máquina Virtual da Ciência dos Dados (DSVM) é uma imagem VM personalizada construída especificamente para fazer ciência de dados. Se [criar um DSVM,](how-to-configure-environment.md#dsvm)o servidor SDK e portátil são instalados e configurados para si. No entanto, ainda terá de criar um espaço de trabalho e clonar o repositório de amostras.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os cadernos de [amostras](https://aka.ms/aml-notebooks) para descobrir o que o Azure Machine Learning pode fazer, ou experimente estes tutoriais:

- [Treine e implemente um modelo de classificação de imagem com MNIST](tutorial-train-models-with-aml.md)

- [Prepare dados e use machine learning automatizado para treinar um modelo de regressão com o conjunto de dados de táxi de NYC](tutorial-auto-train-models.md)
