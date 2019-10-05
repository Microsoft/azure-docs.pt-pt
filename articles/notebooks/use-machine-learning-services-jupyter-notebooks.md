---
title: Utilizar o Azure Machine Learning Services em blocos de notas do Azure
description: Uma visão geral dos blocos de notas de exemplo do Azure Machine Learning Services que pode utilizar com blocos de notas do Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: f591758fa6e51c420a090aa62d5160320fe15fe8
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973020"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Usar Azure Machine Learning serviço em um bloco de anotações

Azure Notebooks vem pré-configurado com o ambiente necessário para trabalhar com o [serviço Azure Machine Learning](/azure/machine-learning/service/). Pode facilmente clonar um projeto de exemplo para a sua conta de blocos de notas para explorar uma variedade de cenários de Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Clonar o exemplo na sua conta

1. Inicie sessão no [blocos de notas do Azure](https://notebooks.azure.com/).
1. Selecione **meus projetos** para ir para o painel projetos.
1. Selecione o botão Carregar repositório do **GitHub** (seta para cima) para abrir o pop-up de **upload do depósito** do github.
1. No pop-up, insira `Azure/MachineLearningNotebooks` no **repositório GitHub**, forneça um nome para o projeto no **nome do projeto** , como "Azure Machine Learning Service", forneça um identificador na ID do **projeto**, desmarque **público** se desejar e, em seguida, selecione **importar** .

    ![Importar exemplo do bloco de notas do Azure Machine Learning para sua conta de blocos de notas](media/azureml-import-project.png)

1. Depois de um minuto ou dois, blocos de notas do Azure automaticamente leva-o ao dashboard do projeto novo.

## <a name="run-a-sample-notebook"></a>Executar um bloco de notas de exemplo

1. Selecione **configuration.ipynb 00 -** para iniciar a seção de configuração do bloco de notas e siga as instruções para criar uma área de trabalho do Azure Machine Learning.

    - Como blocos de notas do Azure já contém os pacotes Python necessários, basta executar o fragmento de código no passo 2 dos pré-requisitos para verificar a versão do SDK do Azure ML.

1. Quando a configuração estiver concluída, selecione **01. Getting-Started** para abrir a pasta que contém treze blocos de anotações de amostra diferentes, cada um deles é auto-explicativo.

## <a name="next-steps"></a>Passos seguintes

A documentação do Azure Machine Learning Services contém uma variedade de outros recursos que guiá-lo a trabalhar com o serviço Machine Learning em blocos de notas:

- [Quickstart: Use o Python para começar a usar o Azure Machine Learning @ no__t-0
- #1 de @no__t 0Tutorial: Treinar um modelo de classificação de imagem com o serviço Azure Machine Learning @ no__t-0
- #2 de @no__t 0Tutorial: Implantar um modelo de classificação de imagem na ACI (instância de contêiner do Azure) ](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Tutorial: Treinar um modelo de classificação com o Machine Learning automatizado no serviço Azure Machine Learning @ no__t-0

Também consulte a documentação para o [do Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
