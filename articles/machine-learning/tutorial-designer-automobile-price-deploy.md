---
title: 'Tutorial: Implementar um modelo de machine learning com o designer'
titleSuffix: Azure Machine Learning
description: Este tutorial mostra-lhe como construir uma solução de análise preditiva no designer de Machine Learning Azure (pré-visualização). Treine, marque e implemente um modelo de aprendizagem automática utilizando módulos de arrasto e queda.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: c3ca37fd47b6551a95f9a491053ec7863acd1eeb
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389397"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Tutorial: Implementar um modelo de machine learning com o designer (pré-visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Pode implementar o modelo preditivo desenvolvido na [primeira parte do tutorial](tutorial-designer-automobile-price-train-score.md) para dar aos outros a oportunidade de usá-lo. Na primeira parte, treinou o seu modelo. Agora, é hora de gerar novas previsões com base na entrada do utilizador. Nesta parte do tutorial, irá:

> [!div class="checklist"]
> * Criar um oleoduto de inferência em tempo real.
> * Criar um aglomerado de inferência.
> * Implante o ponto final em tempo real.
> * Teste o ponto final em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

Complete a [primeira parte do tutorial](tutorial-designer-automobile-price-train-score.md) para aprender a treinar e a marcar um modelo de aprendizagem automática no designer.

## <a name="create-a-real-time-inference-pipeline"></a>Criar um oleoduto de inferência em tempo real

Para implantar o seu oleoduto, primeiro tem de converter o gasoduto de treino num gasoduto de inferência em tempo real. Este processo remove módulos de treino e adiciona inputs e saídas de serviço web para lidar com pedidos.

### <a name="create-a-real-time-inference-pipeline"></a>Criar um oleoduto de inferência em tempo real

1. Acima da tela do gasoduto, selecione Criar gasoduto > de **inferência**Em**tempo real**.

    ![Screenshot mostrando onde encontrar o botão de criar pipeline](./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png)

    O seu oleoduto deve agora ser assim: 

   ![Screenshot mostrando a configuração esperada do pipeline após prepará-lo para a implantação](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Quando seleciona **O pipeline de inferência Criar,** várias coisas acontecem:
    
    * O modelo treinado é armazenado como um módulo **Dataset** na paleta de módulos. Pode encontrá-lo sob **os meus conjuntos**de dados.
    * São removidos módulos de treino como **o Modelo de Comboio** e os Dados **Divididos.**
    * O modelo treinado guardado é adicionado de volta ao oleoduto.
    * Os módulos de entrada de serviço **web** e de saída do **serviço web** são adicionados. Estes módulos mostram onde os dados do utilizador entram no pipeline e onde os dados são devolvidos.

    > [!NOTE]
    > Por padrão, a Entrada do **Serviço Web** espera o mesmo esquema de dados que os dados de treino utilizados para criar o pipeline preditivo. Neste cenário, o preço está incluído no esquema. No entanto, o preço não é usado como fator durante a previsão.
    >

1. Selecione **Submeter**, e use o mesmo alvo de cálculo e experimente que usou na primeira parte.

    Se for a primeira corrida, pode levar até 20 minutos para o seu oleoduto terminar de funcionar. As definições de computação padrão têm um tamanho mínimo de nó de 0, o que significa que o designer deve alocar recursos depois de estar inativo. As repetidas corridas de gasodutos demorarão menos tempo, uma vez que os recursos da computação já estão atribuídos. Além disso, o designer utiliza resultados em cache para cada módulo para melhorar ainda mais a eficiência.

1. Selecione **Implementar**.

## <a name="create-an-inferencing-cluster"></a>Criar um cluster de inferência

Na caixa de diálogo que aparece, pode selecionar entre quaisquer clusters de Serviço Azure Kubernetes (AKS) existentes para implementar o seu modelo para. Se não tiver um cluster AKS, use os seguintes passos para criar um.

1. Selecione **Compute** na caixa de diálogo que parece ir para a página **Compute.**

1. Na fita de navegação, selecione **Clusters** > de Inferência **+ Novo**.

    ![Screenshot mostrando como chegar ao novo painel de cluster de inferência](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. No painel de cluster de inferência, configure um novo Serviço Kubernetes.

1. Introduza *aks-compute* para o **nome Compute**.
    
1. Selecione uma região próxima que esteja disponível para a **Região.**

1. Selecione **Criar**.

    > [!NOTE]
    > Leva aproximadamente 15 minutos para criar um novo serviço AKS. Pode verificar o estado de provisionamento na página **Inference Clusters.**
    >

## <a name="deploy-the-real-time-endpoint"></a>Implementar o ponto final em tempo real

Depois do seu serviço AKS ter terminado o fornecimento, volte ao oleoduto de inferência em tempo real para completar a implantação.

1. Selecione **Implementar** acima da tela.

1. Selecione **Implementar novo ponto final em tempo real**. 

1. Selecione o cluster AKS que criou.

1. Selecione **Implementar**.

    ![Screenshot mostrando como configurar um novo ponto final em tempo real](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    Uma notificação de sucesso acima da tela aparece após acabamentos de implantação. Pode levar alguns minutos.

## <a name="test-the-real-time-endpoint"></a>Teste o ponto final em tempo real

Após os acabamentos de implementação, pode testar o seu ponto final em tempo real indo para a página **Endpoints.**

1. Na página **Endpoints,** selecione o ponto final que implementou.

    ![Screenshot mostrando o separador de pontos finais em tempo real com o ponto final recentemente criado em destaque](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Selecione **Teste**.

1. Pode introduzir manualmente dados de teste ou utilizar os dados da amostra auto-preenchidos e selecionar **Test**.

    O portal submete um pedido de teste ao ponto final e mostra os resultados. Embora um valor de preço seja gerado para os dados de entrada, não é usado para gerar o valor de previsão.

    ![Screenshot mostrando como testar o ponto final em tempo real com o rótulo pontuado para o preço realçado](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu os passos-chave na forma de criar, implantar e consumir um modelo de machine learning no designer. Para saber mais sobre como pode usar o designer para resolver outros tipos de problemas, consulte os nossos outros pipelines de amostra.

> [!div class="nextstepaction"]
> [Amostras de designer](samples-designer.md)
