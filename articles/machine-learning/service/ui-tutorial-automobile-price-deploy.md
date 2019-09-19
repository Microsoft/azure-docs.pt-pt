---
title: 'Tutorial: Implantar um modelo de aprendizado de máquina com a interface visual'
titleSuffix: Azure Machine Learning
description: Saiba como criar uma solução de análise preditiva na interface visual do Azure Machine Learning. Treine, pontuate e implante um modelo de aprendizado de máquina usando os módulos arrastar e soltar. Este tutorial é a parte dois de uma série de duas partes sobre a previsão de preços de automóvel usando a regressão linear.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: 9378c6a14c3b755a6456ef68ecd73730cb77fc79
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128972"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Tutorial: Implantar um modelo de aprendizado de máquina com a interface visual

Para dar aos outros a oportunidade de usar o modelo de previsão desenvolvido na [parte um do tutorial](ui-tutorial-automobile-price-train-score.md), você pode implantá-lo como um serviço Web do Azure. Até agora, você experimentou treinar seu modelo. Agora, é hora de gerar novas previsões com base na entrada do usuário. Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Preparar um modelo para implantação
> * Implementar serviços Web
> * Testar um serviço Web
> * Gerenciar um serviço Web
> * Consumir o serviço Web

## <a name="prerequisites"></a>Pré-requisitos

Conclua a [parte um do tutorial](ui-tutorial-automobile-price-train-score.md) para aprender a treinar e pontuar um modelo de aprendizado de máquina na interface visual.

## <a name="prepare-for-deployment"></a>Preparar para a implementação

Antes de implantar seu experimento como um serviço Web, primeiro você precisa converter seu *teste de treinamento* em um *experimento de previsão*.

1. Selecione **criar experimento preditiva*** na parte inferior da tela do experimento.

    ![GIF animado mostrando a conversão automática de um teste de treinamento em um experimento de previsão](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Quando você seleciona **criar teste de previsão**, várias coisas acontecem:
    
    * O modelo treinado é armazenado como um módulo de **modelo treinado** na paleta de módulos. Você pode encontrá-lo em **modelos treinados**.
    * Módulos que eram usados para treinamento são removidos; especificamente:
      * Preparar Modelo
      * Dividir Dados
      * Avaliar Modelo
    * O modelo treinado salvo é adicionado de volta ao experimento.
    * Os módulos **entrada de serviço Web** e **saída de serviço Web** são adicionados. Esses módulos identificam onde os dados de usuário entrarão no modelo e onde os dados são retornados.

    O **teste de treinamento** ainda é salvo nas novas guias na parte superior da tela do experimento.

1. **Execute** a experimentação.

1. Selecione a saída do módulo **modelo de Pontuação** e selecione **exibir resultados** para verificar se o modelo ainda está funcionando. Você pode ver que os dados originais são exibidos, juntamente com o preço previsto ("rótulos pontuados").

Seu experimento agora deve ser assim:  

![Captura de tela mostrando a configuração esperada do experimento depois de prepará-la para implantação](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Implementar o serviço web

1. Selecione **implantar serviço Web** abaixo da tela.

1. Selecione o **destino de computação** no qual você gostaria de executar o serviço Web.

    Atualmente, a interface visual só dá suporte à implantação para destinos de computação do AKS (serviço kubernetes do Azure). Você pode escolher entre os destinos de computação disponíveis do AKS em seu espaço de trabalho do serviço de aprendizado de máquina ou configurar um novo ambiente AKS usando as etapas na caixa de diálogo que aparece.

    ![Captura de tela mostrando uma possível configuração para um novo destino de computação](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Selecione **implantar serviço Web**. Você verá a notificação a seguir quando a implantação for concluída. A implantação pode levar alguns minutos.

    ![Captura de tela mostrando a mensagem de confirmação para uma implantação bem-sucedida.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Testar o serviço web

Você pode testar e gerenciar seus serviços Web da interface visual navegando até a guia **Serviços Web** .

1. Vá para a seção serviço Web. Você verá o serviço Web implantado com o nome **tutorial – prever o preço do automóvel [custo de previsão]** .

     ![Captura de tela mostrando a guia serviço Web com o serviço Web recentemente criado realçado](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Selecione o nome do serviço Web para exibir detalhes adicionais.

1. Selecione **testar**.

    [![Captura de tela mostrando a página de teste do serviço Web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)](./media/ui-tutorial-automobile-price-deploy/web-service-test.png#lightbox)

1. Dados de teste de entrada ou use os dados de exemplo preenchidos com preenchimento automática e selecione **teste**.

    A solicitação de teste é enviada para o serviço Web e os resultados são mostrados na página. Embora um valor de preço seja gerado para os dados de entrada, ele não é usado para gerar o valor de previsão.

## <a name="consume-the-web-service"></a>Consumir o serviço Web

Agora, os usuários podem enviar solicitações de API para o serviço Web do Azure e receber resultados para prever o preço de seus novos automóveis.

**Solicitação/resposta** – o usuário envia uma ou mais linhas de dados do automóvel para o serviço usando um protocolo http. O serviço responde com um ou mais conjuntos de resultados.

Você pode encontrar as chamadas REST de exemplo na guia **consumir** da página de detalhes do serviço Web.

   ![Captura de tela mostrando uma chamada REST de exemplo que os usuários podem encontrar na guia consumir](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Navegue até a guia **Doc da API** para encontrar mais detalhes sobre a API.

## <a name="manage-models-and-deployments"></a>Gerenciar modelos e implantações

Os modelos e as implantações de serviço Web criadas na interface visual também podem ser gerenciados no espaço de trabalho Azure Machine Learning.

1. Abra seu espaço de trabalho no [portal do Azure](https://portal.azure.com/).  

1. Em seu espaço de trabalho, selecione **modelos**. Em seguida, selecione o experimento que você criou.

    ![Captura de tela mostrando como navegar até experimentos na portal do Azure](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Nessa página, você verá detalhes adicionais sobre o modelo.

1. Selecione **implantações**, e todos os serviços Web que usam o modelo serão listadas. Selecione o nome do serviço Web, ele vai para a página de detalhes do serviço Web. Nesta página, você pode obter informações mais detalhadas do serviço Web.

    [![Relatório de execução detalhada de captura de tela](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)](./media/ui-tutorial-automobile-price-deploy/deployment-details.png#lightbox)

Você também pode encontrar esses modelos e implantações nas seções **modelos** e **pontos de extremidade** da página de [aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com).

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu as principais etapas na criação, implantação e consumo de um modelo de aprendizado de máquina na interface visual. Para saber mais sobre como você pode usar a interface visual para resolver outros tipos de problemas, Confira nossos outros experimentos de exemplo.

> [!div class="nextstepaction"]
> [Exemplo de classificação de risco de crédito](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
