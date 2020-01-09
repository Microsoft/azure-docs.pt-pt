---
title: Usar Azure Machine Learning na visualização Azure Notebooks
description: Uma visão geral dos blocos de anotações de exemplo para Azure Machine Learning que você pode usar com Azure Notebooks visualização.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 3c0e0e9ccea079d7e8f4e35e9af2a0e1c1ec5051
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646957"
---
# <a name="use-azure-machine-learning-in-azure-notebooks-preview"></a>Usar Azure Machine Learning na visualização Azure Notebooks

Azure Notebooks vem pré-configurado com o ambiente necessário para trabalhar com [Azure Machine Learning](/azure/machine-learning/service/). Pode facilmente clonar um projeto de exemplo para a sua conta de blocos de notas para explorar uma variedade de cenários de Machine Learning.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="clone-the-sample-into-your-account"></a>Clonar o exemplo na sua conta

1. Inicie sessão no [blocos de notas do Azure](https://notebooks.azure.com/).
1. Selecione **meus projetos** para ir para o painel projetos.
1. Selecione o botão Carregar repositório do **GitHub** (seta para cima) para abrir o pop-up de **upload do depósito** do github.
1. No pop-up, insira `Azure/MachineLearningNotebooks` no **repositório GitHub**, forneça um nome para o projeto **no nome do projeto** , como "Azure Machine Learning", forneça um identificador na ID do **projeto**, desmarque **público** se desejar e, em seguida, selecione **importar**.

    ![Importar exemplo do bloco de notas do Azure Machine Learning para sua conta de blocos de notas](media/azureml-import-project.png)

1. Depois de um minuto ou dois, blocos de notas do Azure automaticamente leva-o ao dashboard do projeto novo.

## <a name="run-a-sample-notebook"></a>Executar um bloco de notas de exemplo

1. Selecione **configuration.ipynb 00 -** para iniciar a seção de configuração do bloco de notas e siga as instruções para criar uma área de trabalho do Azure Machine Learning.

    - Como blocos de notas do Azure já contém os pacotes Python necessários, basta executar o fragmento de código no passo 2 dos pré-requisitos para verificar a versão do SDK do Azure ML.

1. Quando a configuração estiver concluída, selecione **01. Getting-Started** para abrir a pasta que contém treze blocos de anotações de amostra diferentes, cada um deles é auto-explicativo.

## <a name="next-steps"></a>Passos seguintes

A documentação Azure Machine Learning contém uma variedade de outros recursos que orientam você durante o trabalho com Machine Learning em blocos de anotações:

- [Início rápido: Utilizar o Python para começar a utilizar com o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Tutorial #1: treinar um modelo de classificação de imagem com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Tutorial #2: Implementar um modelo de classificação de imagem na instância de contentor do Azure (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Tutorial: treinar um modelo de classificação com o aprendizado de máquina automatizado no Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Também consulte a documentação para o [do Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
