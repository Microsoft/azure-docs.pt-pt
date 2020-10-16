---
title: 'Tutorial: Implementar modelos ML com o designer'
titleSuffix: Azure Machine Learning
description: Este tutorial mostra-lhe como construir uma solução de análise preditiva no designer de Aprendizagem automática Azure. Treine, marque e implemente um modelo de aprendizagem automática utilizando módulos de arrasto e queda.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: a4923e48c890a50d642d937f014e466e998171cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90896641"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>Tutorial: Implementar um modelo de machine learning com o designer


Pode implementar o modelo preditivo desenvolvido na [parte um do tutorial](tutorial-designer-automobile-price-train-score.md) para dar a outros a oportunidade de usá-lo. Na primeira parte, treinaste o teu modelo. Agora, é hora de gerar novas previsões com base na entrada do utilizador. Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Criar um oleoduto de inferência em tempo real.
> * Criar um aglomerado de inferenculação.
> * Desdobre o ponto final em tempo real.
> * Teste o ponto final em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

Complete [a primeira parte do tutorial](tutorial-designer-automobile-price-train-score.md) para aprender a treinar e marcar um modelo de machine learning no designer.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Criar um pipeline de inferência em tempo real

Para implantar o seu oleoduto, tem primeiro de converter o gasoduto de treino num gasoduto de inferência em tempo real. Este processo remove módulos de formação e adiciona entradas e saídas de serviço web para lidar com pedidos.

### <a name="create-a-real-time-inference-pipeline"></a>Criar um pipeline de inferência em tempo real

1. Acima da tela do gasoduto, **selecione Criar o gasoduto**de  >  **inferência em tempo real**.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png"alt-text="Screenshot mostrando onde encontrar o botão de pipeline criar":::

    O seu oleoduto deve agora ser assim: 

   ![Screenshot mostrando a configuração esperada do gasoduto depois de prepará-lo para a implantação](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Quando seleciona **Criar o pipeline de inferência,** várias coisas acontecem:
    
    * O modelo treinado é armazenado como um módulo **Dataset** na paleta de módulos. Pode encontrá-lo nos **meus conjuntos de dados.**
    * São removidos módulos de treino como **o Train Model** e o Split **Data.**
    * O modelo treinado salvo é adicionado de volta ao oleoduto.
    * São adicionados módulos de entrada de **serviço web** e de saída de **serviço web.** Estes módulos mostram onde os dados dos utilizadores entram no pipeline e onde os dados são devolvidos.

    > [!NOTE]
    > Por predefinição, a Entrada de **Serviço Web** espera o mesmo esquema de dados que os dados de treino utilizados para criar o pipeline preditivo. Neste cenário, o preço está incluído no esquema. No entanto, o preço não é usado como um fator durante a previsão.
    >

1. **Selecione Enviar**, e use o mesmo alvo de cálculo e experimente que usou na primeira parte.

    Se for a primeira corrida, pode levar até 20 minutos para o seu oleoduto terminar de funcionar. As definições de computação padrão têm um tamanho mínimo de nó de 0, o que significa que o designer deve alocar recursos depois de estar inativo. As repetidas operações de gasoduto levarão menos tempo, uma vez que os recursos de computação já estão atribuídos. Além disso, o designer utiliza resultados em cache para cada módulo para melhorar ainda mais a eficiência.

1. Selecione **Implementar**.

## <a name="create-an-inferencing-cluster"></a>Criar um cluster de inferenculação

Na caixa de diálogo que aparece, pode selecionar a partir de quaisquer clusters existentes do Serviço Azure Kubernetes (AKS) para implantar o seu modelo. Se não tiver um cluster AKS, use os seguintes passos para criar um.

1. **Selecione Computar** na caixa de diálogo que parece ir para a página **compute.**

1. Na fita de navegação, selecione **Inference Clusters**  >  **+ New**.

    ![Screenshot mostrando como chegar ao novo painel de aglomerado de inferência](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. No painel de aglomerados de inferência, configuure um novo Serviço Kubernetes.

1. Introduza *aks-compute* para o **nome Compute**.
    
1. Selecione uma região próxima que esteja disponível para a **Região.**

1. Selecione **Criar**.

    > [!NOTE]
    > Leva aproximadamente 15 minutos para criar um novo serviço AKS. Pode verificar o estado de provisionamento na página **De Inference Clusters.**
    >

## <a name="deploy-the-real-time-endpoint"></a>Implementar o ponto de final em tempo real

Depois de o seu serviço AKS ter terminado o fornecimento, volte ao gasoduto de inferenculação em tempo real para completar a implementação.

1. Selecione **Implementar** acima da tela.

1. **Selecione Implementar novo ponto final em tempo real**. 

1. Selecione o cluster AKS que criou.

1. Selecione **Implementar**.
    
    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="Screenshot mostrando onde encontrar o botão de pipeline criar":::

    Uma notificação de sucesso acima da tela aparece após o fim da implantação. Pode levar alguns minutos.

## <a name="test-the-real-time-endpoint"></a>Teste o ponto final em tempo real

Após o fim da implementação, pode testar o seu ponto de chegada em tempo real indo para a página **Endpoints.**

1. Na página **Endpoints,** selecione o ponto final que implementou.

    ![Screenshot mostrando o separador pontos finais em tempo real com o ponto final recentemente criado em destaque](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Selecione **Teste**.

1. Pode inserir manualmente dados de teste ou utilizar os dados da amostra preenchidos automaticamente e selecionar **o Teste**.

    O portal submete um pedido de teste ao ponto final e mostra os resultados. Embora um valor de preço seja gerado para os dados de entrada, não é usado para gerar o valor de previsão.

    ![Screenshot mostrando como testar o ponto final em tempo real com a etiqueta pontuada para o preço realçado](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu os passos-chave na forma de criar, implementar e consumir um modelo de aprendizagem automática no designer. Para saber mais sobre como pode usar o designer para resolver outros tipos de problemas, consulte os outros pipelines de amostras.

> [!div class="nextstepaction"]
> [Amostras de designers](samples-designer.md)
