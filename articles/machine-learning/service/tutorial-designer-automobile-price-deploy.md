---
title: 'Tutorial: implantar um modelo de aprendizado de máquina com o designer'
titleSuffix: Azure Machine Learning
description: Saiba como criar uma solução de análise preditiva no designer de Azure Machine Learning (versão prévia). Treine, pontuate e implante um modelo de aprendizado de máquina usando os módulos arrastar e soltar.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 69fba508eac4b778dcd72371fd1471625ecb8c1a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501604"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Tutorial: implantar um modelo de aprendizado de máquina com o designer (versão prévia)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Para dar aos outros a oportunidade de usar o modelo de previsão desenvolvido na [parte um do tutorial](tutorial-designer-automobile-price-train-score.md), você pode implantá-lo como um ponto de extremidade em tempo real. Na parte 1, você treinou seu modelo. Agora, é hora de gerar novas previsões com base na entrada do usuário. Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Implantar um ponto de extremidade em tempo real
> * Criar um cluster inferência
> * Testar um ponto de extremidade em tempo real

## <a name="prerequisites"></a>Pré-requisitos

Conclua a [parte um do tutorial](tutorial-designer-automobile-price-train-score.md) para aprender a treinar e pontuar um modelo de aprendizado de máquina no designer.

## <a name="deploy-a-real-time-endpoint"></a>Implantar um ponto de extremidade em tempo real

Para implantar seu pipeline, você deve:

1. Converta o pipeline de treinamento em um pipeline de inferência em tempo real, que remove módulos de treinamento e adiciona entradas e saídas para solicitações inferência.
1. Implante o pipeline de inferência.

### <a name="create-a-real-time-inference-pipeline"></a>Criar um pipeline de inferência em tempo real

1. Na parte superior da tela do pipeline, selecione **criar pipeline de inferência** > **pipeline de indução em tempo real**

    Quando você seleciona **criar pipeline de inferência**, várias coisas acontecem:
    
    * O modelo treinado é armazenado como um módulo de **conjunto** de armazenamento na paleta de módulos. Você pode encontrá-lo em **meus conjuntos de valores**.
    * Módulos, como **treinar modelo** e **dividir dados**, que foram usados para treinamento, são removidos.
    * O modelo treinado salvo é adicionado de volta ao pipeline.
    * Os módulos **entrada de serviço Web** e **saída de serviço Web** são adicionados. Esses módulos identificam onde os dados de usuário entrarão no modelo e onde os dados são retornados.

    > [!Note]
    > O **pipeline de treinamento** é salvo na nova guia na parte superior da tela do pipeline. Ele também pode ser encontrado como um pipeline publicado no designer.
    >

    Seu pipeline agora deve ser assim:  

   ![Captura de tela mostrando a configuração esperada do pipeline depois de prepará-lo para implantação](./media/ui-tutorial-automobile-price-deploy/real-time-inference-pipeline.png)

1. Selecione **executar** e use o mesmo destino de computação e teste que você usou na parte 1.

1. Selecione o módulo **modelo de Pontuação** .

1. No painel Propriedades, selecione **saídas** > **Visualizar** para verificar se o modelo ainda está funcionando. Você pode ver que os dados originais são exibidos junto com o preço previsto ("rótulos pontuados").

1. Selecione **Implementar**.

### <a name="create-an-inferencing-cluster"></a>Criar um cluster inferência

Na caixa de diálogo exibida, você pode selecionar entre os clusters do AKS (serviço kubernetes do Azure) existentes no espaço de trabalho para implantar seu modelo. Se você não tiver um cluster AKS, use as etapas a seguir para criar um.

1. Selecione **computação** na caixa de diálogo para navegar até a página de **computação** .

1. Na faixa de opções de navegação, selecione **clusters de inferência** >  **+ novo**.

    ![Captura de tela mostrando como navegar até o novo painel de cluster de inferência](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. No painel cluster de inferência, configure um novo serviço kubernetes.

1. Insira "AKS-Compute" para o **nome de computação**.
    
1. Selecione uma **região**disponível próxima.

1. Selecione **Criar**.

    > [!Note]
    > Leva aproximadamente 15 minutos para criar um novo serviço AKS. Você pode verificar o estado de provisionamento na página **clusters de inferência**
    >

### <a name="deploy-the-real-time-endpoint"></a>Implantar o ponto de extremidade em tempo real

Depois que o serviço AKS tiver concluído o provisionamento, retorne ao pipeline inferência em tempo real para concluir a implantação.

1. Selecione **implantar** acima da tela.

1. Selecione **implantar novo ponto de extremidade em tempo real**. 

1. Selecione o cluster AKS que você criou.

1. Selecione **Implementar**.

    ![Captura de tela mostrando como configurar um novo ponto de extremidade em tempo real](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Uma notificação de êxito acima da tela será exibida quando a implantação for concluída, pode levar alguns minutos.

## <a name="test-the-real-time-endpoint"></a>Testar o ponto de extremidade em tempo real

Você pode testar seu ponto de extremidade em tempo real navegando até a página **pontos de extremidade** no painel de navegação do espaço de trabalho à esquerda.

1. Na página **pontos** de extremidade, selecione o ponto de extremidade que você implantou.

    ![Captura de tela mostrando a guia pontos de extremidade em tempo real com o ponto de extremidades recentemente criado realçado](./media/ui-tutorial-automobile-price-deploy/endpoints.png)

1. Selecione **testar**.

1. Dados de teste de entrada ou use os dados de exemplo preenchidos com preenchimento automática e selecione **teste**.

    A solicitação de teste é enviada para o ponto de extremidade e os resultados são mostrados na página. Embora um valor de preço seja gerado para os dados de entrada, ele não é usado para gerar o valor de previsão.

    ![Captura de tela mostrando como testar o ponto de extremidade em tempo real com o rótulo pontuado para o preço realçado](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu as principais etapas na criação, implantação e consumo de um modelo de aprendizado de máquina no designer. Para saber mais sobre como você pode usar o designer para resolver outros tipos de problemas, Confira nossos outros pipelines de exemplo.

> [!div class="nextstepaction"]
> [Exemplo de classificação de risco de crédito](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
