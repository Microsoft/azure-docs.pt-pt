---
title: 'Tutorial: implantar um modelo de aprendizado de máquina com o designer'
titleSuffix: Azure Machine Learning
description: Este tutorial mostra como criar uma solução de análise preditiva no designer de Azure Machine Learning. Treine, pontuate e implante um modelo de aprendizado de máquina usando os módulos arrastar e soltar.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 89fdac5e59b59c60d7e20b94799d78b029d236d6
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76315002"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>Tutorial: implantar um modelo de aprendizado de máquina com o designer
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Você pode implantar o modelo de previsão desenvolvido na [parte um do tutorial](tutorial-designer-automobile-price-train-score.md) para dar aos outros a oportunidade de usá-lo. Na parte um, você treinou seu modelo. Agora, é hora de gerar novas previsões com base na entrada do usuário. Nesta parte do tutorial, você vai:

> [!div class="checklist"]
> * Crie um pipeline de inferência em tempo real.
> * Crie um cluster inferência.
> * Implante o ponto de extremidade em tempo real.
> * Teste o ponto de extremidade em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

Conclua a [parte um do tutorial](tutorial-designer-automobile-price-train-score.md) para aprender a treinar e pontuar um modelo de aprendizado de máquina no designer.

## <a name="create-a-real-time-inference-pipeline"></a>Criar um pipeline de inferência em tempo real

Para implantar seu pipeline, você deve primeiro converter o pipeline de treinamento em um pipeline de inferência em tempo real. Esse processo remove módulos de treinamento e adiciona entradas e saídas do serviço Web para manipular solicitações.

### <a name="create-a-real-time-inference-pipeline"></a>Criar um pipeline de inferência em tempo real

1. Acima da tela do pipeline, selecione **criar pipeline de inferência** > **pipeline de inferência em tempo real**.

    Seu pipeline agora deve ser assim: 

   ![Captura de tela mostrando a configuração esperada do pipeline depois de prepará-lo para implantação](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Quando você seleciona **criar pipeline de inferência**, várias coisas acontecem:
    
    * O modelo treinado é armazenado como um módulo de **conjunto** de armazenamento na paleta de módulos. Você pode encontrá-lo em **meus conjuntos de valores**.
    * Módulos de treinamento como **modelo** de treinamento e **dados divididos** são removidos.
    * O modelo treinado salvo é adicionado de volta ao pipeline.
    * Os módulos **entrada de serviço Web** e **saída de serviço Web** são adicionados. Esses módulos mostram onde os dados do usuário entram no pipeline e onde os dados são retornados.

    > [!NOTE]
    > Por padrão, a **entrada do serviço Web** esperará o mesmo esquema de dados que os dados de treinamento usados para criar o pipeline de previsão. Nesse cenário, o preço é incluído no esquema. No entanto, o preço não é usado como um fator durante a previsão.
    >

1. Selecione **executar**e use o mesmo destino de computação e teste que você usou na parte um.

1. Selecione o módulo **modelo de Pontuação** .

1. No painel Propriedades, selecione **saídas** > **Visualizar** para verificar se o modelo ainda está funcionando. Você pode ver que os dados originais são exibidos junto com o preço previsto ("rótulos pontuados").

1. Selecione **Implementar**.

## <a name="create-an-inferencing-cluster"></a>Criar um cluster inferência

Na caixa de diálogo que aparece, você pode selecionar entre todos os clusters do AKS (serviço kubernetes do Azure) existentes para implantar o modelo. Se você não tiver um cluster AKS, use as etapas a seguir para criar um.

1. Selecione **computação** na caixa de diálogo que aparece para ir para a página de **computação** .

1. Na faixa de opções de navegação, selecione **clusters de inferência** >  **+ novo**.

    ![Captura de tela mostrando como chegar ao novo painel de cluster de inferência](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. No painel cluster de inferência, configure um novo serviço kubernetes.

1. Digite *AKs-Compute* para o **nome de computação**.
    
1. Selecione uma região próxima que esteja disponível para a **região**.

1. Selecione **Criar**.

    > [!NOTE]
    > Leva aproximadamente 15 minutos para criar um novo serviço AKS. Você pode verificar o estado de provisionamento na página **clusters de inferência** .
    >

## <a name="deploy-the-real-time-endpoint"></a>Implantar o ponto de extremidade em tempo real

Depois que o serviço AKS tiver concluído o provisionamento, retorne ao pipeline inferência em tempo real para concluir a implantação.

1. Selecione **implantar** acima da tela.

1. Selecione **implantar novo ponto de extremidade em tempo real**. 

1. Selecione o cluster AKS que você criou.

1. Selecione **Implementar**.

    ![Captura de tela mostrando como configurar um novo ponto de extremidade em tempo real](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    Uma notificação de êxito acima da tela aparece após a conclusão da implantação. Pode levar alguns minutos.

## <a name="test-the-real-time-endpoint"></a>Testar o ponto de extremidade em tempo real

Após a conclusão da implantação, você pode testar seu ponto de extremidade em tempo real indo para a página **pontos** de extremidade.

1. Na página **pontos** de extremidade, selecione o ponto de extremidade que você implantou.

    ![Captura de tela mostrando a guia pontos de extremidade em tempo real com o ponto de extremidades recentemente criado realçado](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Selecione **testar**.

1. Você pode inserir dados de teste manualmente ou usar os dados de exemplo preenchidos automaticamente e selecionar **testar**.

    O portal envia uma solicitação de teste para o ponto de extremidade e mostra os resultados. Embora um valor de preço seja gerado para os dados de entrada, ele não é usado para gerar o valor de previsão.

    ![Captura de tela mostrando como testar o ponto de extremidade em tempo real com o rótulo pontuado para o preço realçado](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu as principais etapas em como criar, implantar e consumir um modelo de aprendizado de máquina no designer. Para saber mais sobre como você pode usar o designer para resolver outros tipos de problemas, consulte nossos outros pipelines de exemplo.

> [!div class="nextstepaction"]
> [Exemplo de classificação de risco de crédito](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
