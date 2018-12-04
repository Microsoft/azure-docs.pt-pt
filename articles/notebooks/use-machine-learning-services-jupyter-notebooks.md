---
title: Utilizar o Azure Machine Learning Services em blocos de notas do Azure | Documentos da Microsoft
description: Uma visão geral dos blocos de notas de exemplo do Azure Machine Learning Services que pode utilizar com blocos de notas do Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: fabd89f7591abafd68b318921fb7723f3eb7373d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856453"
---
# <a name="use-azure-machine-learning-services-in-a-notebook"></a>Utilizar o Azure Machine Learning Services num bloco de notas

Blocos de notas do Azure está pré-configurada com o ambiente necessário para trabalhar com [serviços do Azure Machine Learning](/azure/machine-learning/service/). Pode facilmente clonar um projeto de exemplo para a sua conta de blocos de notas para explorar uma variedade de cenários de Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Clonar o exemplo na sua conta

1. Inicie sessão no [blocos de notas do Azure](https://notebooks.azure.com/).
1. Selecione **meus projetos** para navegar para o dashboard de projetos.
1. Selecione o **carregar repositório do GitHub** (a seta para cima) botão abrir o **carregar repositório do Github** pop-up.
1. No pop-up, introduza `Azure/MachineLearningNotebooks` no **repositório do GitHub**, forneça um nome para o projeto no **nome do projeto** como "Azure ML Services", fornecer um identificador no **ID do projeto**, desmarque **pública** se desejar, em seguida, selecione **importação**.

    ![Importar exemplo do bloco de notas do Azure Machine Learning para sua conta de blocos de notas](media/azureml-import-project.png)

1. Depois de um minuto ou dois, blocos de notas do Azure automaticamente leva-o ao dashboard do projeto novo.

## <a name="run-a-sample-notebook"></a>Executar um bloco de notas de exemplo

1. Selecione **configuration.ipynb 00 -** para iniciar a seção de configuração do bloco de notas e siga as instruções para criar uma área de trabalho do Azure Machine Learning.

    - Como blocos de notas do Azure já contém os pacotes Python necessários, basta executar o fragmento de código no passo 2 dos pré-requisitos para verificar a versão do SDK do Azure ML.

1. Depois de concluída a configuração, selecione **01.getting-started** para navegar para a pasta que contém treze blocos de notas de exemplo diferentes, cada uma delas é auto-explicativa.

## <a name="next-steps"></a>Passos Seguintes

A documentação do Azure Machine Learning Services contém uma variedade de outros recursos que guiá-lo a trabalhar com o serviço Machine Learning em blocos de notas:

- [Início rápido: Utilizar o Python para começar a utilizar com o Azure Machine Learning](/azure/machine-learning/service/quickstart-create-workspace-with-python.md)
- [Tutorial #1: Preparar um modelo de classificação de imagem com o serviço Azure Machine Learning](/azure/machine-learning/service/tutorial-train-models-with-aml.md)
- [Tutorial #2: Implementar um modelo de classificação de imagem na instância de contentor do Azure (ACI)](/azure/machine-learning/service/tutorial-deploy-models-with-aml.md)
- [Tutorial: Preparar um modelo de classificação com aprendizagem automática no serviço Azure Machine Learning](/azure/machine-learning/service/tutorial-auto-train-models.md)

Também consulte a documentação para o [do Azure Machine Learning SDK para Python](/python/api/overview/azure/ml/intro.md?view=azure-ml-py).
