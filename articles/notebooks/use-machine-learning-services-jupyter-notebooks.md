---
title: Usar Azure Machine Learning no Azure Notebooks
description: Uma visão geral dos blocos de anotações de exemplo para Azure Machine Learning que você pode usar com Azure Notebooks.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: e3c4fbdf35bf7ea1f4dddbceb9d7235c67ed13a4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277448"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Usar Azure Machine Learning em um bloco de anotações

Azure Notebooks vem pré-configurado com o ambiente necessário para trabalhar com [Azure Machine Learning](/azure/machine-learning/service/). Pode facilmente clonar um projeto de exemplo para a sua conta de blocos de notas para explorar uma variedade de cenários de Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Clonar o exemplo na sua conta

1. Entre [Azure notebooks](https://notebooks.azure.com/).
1. Selecione **meus projetos** para ir para o painel projetos.
1. Selecione o botão Carregar repositório do **GitHub** (seta para cima) para abrir o pop-up de **upload do depósito** do github.
1. No pop-up, insira `Azure/MachineLearningNotebooks` no **repositório GitHub**, forneça um nome para o projeto **no nome do projeto** , como "Azure Machine Learning", forneça um identificador na ID do **projeto**, desmarque **público** se desejar e, em seguida, selecione **importar**.

    ![Importar exemplo do bloco de notas do Azure Machine Learning para sua conta de blocos de notas](media/azureml-import-project.png)

1. Depois de um minuto ou dois, blocos de notas do Azure automaticamente leva-o ao dashboard do projeto novo.

## <a name="run-a-sample-notebook"></a>Executar um bloco de notas de exemplo

1. Selecione **00-Configuration. ipynb** para iniciar a seção de configuração do bloco de anotações e siga as instruções para criar um Workspace do Azure Machine Learning.

    - Como blocos de notas do Azure já contém os pacotes Python necessários, basta executar o fragmento de código no passo 2 dos pré-requisitos para verificar a versão do SDK do Azure ML.

1. Quando a configuração estiver concluída, selecione **01. Getting-Started** para abrir a pasta que contém treze blocos de anotações de amostra diferentes, cada um deles é auto-explicativo.

## <a name="next-steps"></a>Passos seguintes

A documentação Azure Machine Learning contém uma variedade de outros recursos que orientam você durante o trabalho com Machine Learning em blocos de anotações:

- [Início rápido: Use o Python para começar a usar o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Tutorial #1: treinar um modelo de classificação de imagem com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Tutorial #2: implantar um modelo de classificação de imagem na instância de contêiner do Azure (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Tutorial: treinar um modelo de classificação com o aprendizado de máquina automatizado no Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Consulte também a documentação do [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
