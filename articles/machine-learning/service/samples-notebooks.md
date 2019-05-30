---
title: Blocos de notas Jupyter do exemplo
titleSuffix: Azure Machine Learning service
description: Encontre e utilize blocos de notas do Jupyter exemplo para explorar o SDK de Python do serviço do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 05/29/2019
ms.custom: seodec18
ms.openlocfilehash: ea4d5a807c25ea0406b49dac8a83ef1a34e0e8b3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391810"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Utilizar blocos de notas do Jupyter para explorar o serviço Azure Machine Learning

O [repositório de blocos de notas do Azure Machine Learning](https://github.com/azure/machinelearningnotebooks) inclui as mais recentes amostras de SDK de Python do Azure Machine Learning. Estes blocos de notas Juypter foram concebidos para ajudar a explorar o SDK e servem como modelos para seu próprios projetos de aprendizagem.

Este artigo mostra como acessar o repositório de seguintes ambientes:

- [VM do bloco de notas do Azure Machine Learning](#azure-machine-learning-notebook-vm)
- [Traga seu próprio servidor de bloco de notas](#bring-your-own-jupyter-notebook-server)
- [Máquina de Virtual de ciência de dados](#data-science-virtual-machine)
- [Azure Notebooks](#azure-notebooks)

> [!NOTE]
> Uma vez que já clonou o repositório, encontrará tutoriais blocos de notas na **tutoriais** pasta e blocos de notas específicos de funcionalidades na **procedimentos-to-use-azureml** pasta.

## <a name="azure-machine-learning-notebook-vm"></a>VM do bloco de notas do Azure Machine Learning

A maneira mais fácil para começar a utilizar com os exemplos é concluir a [guia de introdução do bloco de notas com base na cloud](quickstart-run-cloud-notebook.md). Depois de concluído, terá um servidor dedicado de bloco de notas pré-carregados com o SDK e o repositório de exemplo. Nenhuma downloads ou instalação necessária.

## <a name="bring-your-own-jupyter-notebook-server"></a>Traga seu próprio servidor de bloco de notas do Jupyter

Se gostaria de trazer o seu próprio servidor de bloco de notas para o desenvolvimento local, siga estes passos:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Estas instruções de instalação de pacotes SDK bases necessários para os blocos de notas do guia de introdução e tutorial. Outros blocos de notas de exemplo podem exigir a instalação de componentes adicionais. Para obter mais informações, consulte [instalar o SDK do Azure Machine Learning para o Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="data-science-virtual-machine"></a>Máquina Virtual de Ciência de Dados

A Máquina Virtual de ciência de dados (DSVM) é uma imagem VM personalizada, criada especificamente para fazer ciência de dados. Se [criar uma DSVM](how-to-configure-environment.md#dsvm), o servidor SDK e o bloco de notas instalados e configurados para. No entanto, ainda precisará criar uma área de trabalho e clone o repositório de exemplo.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="azure-notebooks"></a>Azure Notebooks

No [blocos de notas do Azure](https://notebooks.azure.com/), o servidor SDK e o bloco de notas instalados e configurados para. Blocos de notas do Azure fornece um ambiente de bloco de notas totalmente gerido e leve para que possa explorar.

Para acessar o repositório de exemplo nos blocos de notas do Azure, navegue até à sua área de trabalho do Azure Machine Learning através da [portal do Azure](https://portal.azure.com). Partir do **descrição geral** secção, selecione **começar em blocos de notas do Azure**.

## <a name="next-steps"></a>Passos Seguintes

Explore os [blocos de notas de exemplo](https://aka.ms/aml-notebooks) para detetar que o Azure Machine Learning serviço pode fazer ou Use esses tutoriais:

- [Preparar e implementar um modelo de classificação de imagem com MNIST](tutorial-train-models-with-aml.md)

- [Preparar dados e utilizar automatizada de machine learning para preparar um modelo de regressão com o conjunto de dados de táxis NYC](tutorial-data-prep.md)