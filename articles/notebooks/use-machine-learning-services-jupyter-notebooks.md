---
title: Utilizar o Azure Machine Learning Services em blocos de notas do Azure
description: Uma visão geral dos blocos de notas de exemplo do Azure Machine Learning Services que pode utilizar com blocos de notas do Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: c99f815bd58b03dbc43ba742577259be5638fef9
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035740"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Usar Azure Machine Learning serviço em um bloco de anotações

Azure Notebooks vem pré-configurado com o ambiente necessário para trabalhar com o [serviço Azure Machine Learning](/azure/machine-learning/service/). Pode facilmente clonar um projeto de exemplo para a sua conta de blocos de notas para explorar uma variedade de cenários de Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Clonar o exemplo na sua conta

1. Inicie sessão no [blocos de notas do Azure](https://notebooks.azure.com/).
1. Selecione **meus projetos** para ir para o painel projetos.
1. Selecione o botão Carregar repositório do **GitHub** (seta para cima) para abrir o pop-up de **upload do depósito** do github.
1. No pop-up, `Azure/MachineLearningNotebooks` insira no **repositório GitHub**, forneça um nome para o projeto no **nome do projeto** , como "Azure Machine Learning Service", forneça um identificador na ID do **projeto**, desmarque **público** se desejar e, em seguida, selecione  **Importar**.

    ![Importar exemplo do bloco de notas do Azure Machine Learning para sua conta de blocos de notas](media/azureml-import-project.png)

1. Depois de um minuto ou dois, blocos de notas do Azure automaticamente leva-o ao dashboard do projeto novo.

## <a name="run-a-sample-notebook"></a>Executar um bloco de notas de exemplo

1. Selecione **configuration.ipynb 00 -** para iniciar a seção de configuração do bloco de notas e siga as instruções para criar uma área de trabalho do Azure Machine Learning.

    - Como blocos de notas do Azure já contém os pacotes Python necessários, basta executar o fragmento de código no passo 2 dos pré-requisitos para verificar a versão do SDK do Azure ML.

1. Quando a configuração estiver concluída, selecione **01. Getting-Started** para abrir a pasta que contém treze blocos de anotações de amostra diferentes, cada um deles é auto-explicativo.

## <a name="next-steps"></a>Passos Seguintes

A documentação do Azure Machine Learning Services contém uma variedade de outros recursos que guiá-lo a trabalhar com o serviço Machine Learning em blocos de notas:

- [Quickstart: Use o Python para começar a usar o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [#1 do tutorial: Treinar um modelo de classificação de imagem com o serviço Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [#2 do tutorial: Implantar um modelo de classificação de imagem na instância de contêiner do Azure (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Tutorial: Treinar um modelo de classificação com o Machine Learning automatizado no serviço Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Também consulte a documentação para o [do Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
