---
title: 'Tutorial: Implementar um modelo de machine learning com a interface visual'
titleSuffix: Azure Machine Learning service
description: Saiba como criar uma solução de Análise Preditiva na interface visual de serviço do Azure Machine Learning. Formar, Pontuar e implementar um modelo de aprendizagem automática com arrastar e largar módulos. Este tutorial é a parte dois de uma série de duas partes sobre prever preços de automóveis com regressão linear.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: dd28fb51a4fc3fbf3dfc893f2f5f159ccafdb4b3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839312"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Tutorial: Implementar um modelo de machine learning com a interface visual

Para atribuir a outras pessoas oportunidade de se usar o modelo de previsão desenvolvido em [primeira parte do tutorial](ui-tutorial-automobile-price-train-score.md), pode implementá-la como um serviço web do Azure. Até agora, já foi fazer experiências com seu modelo de formação. Agora, chegou a hora para gerar novas predições com base na entrada do usuário. Nesta parte do tutorial,:

> [!div class="checklist"]
> * Preparar um modelo de implementação
> * Implementar serviços Web
> * Testar um serviço web
> * Gerir um serviço web
> * Consumir o serviço web

## <a name="prerequisites"></a>Pré-requisitos

Concluída [primeira parte do tutorial](ui-tutorial-automobile-price-train-score.md) para saber como dar formação e Pontuar o modelo de machine learning na interface do visual.

## <a name="prepare-for-deployment"></a>Preparar para a implementação

Antes de implementar a sua experimentação como um serviço web, primeiro tem de converter sua *experimentação de preparação* num *experimentação preditiva*.

1. Selecione **criar a experimentação preditiva*** na parte inferior da tela de experimentação.

    ![Animados gif que mostra a conversão automática de uma experimentação de preparação para uma experimentação preditiva](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Quando seleciona **experimentação preditiva criar**, várias coisas acontecem:
    
    * O modelo preparado é armazenado como uma **modelo preparado** módulo na paleta do módulo. Pode encontrá-lo sob **modelos de formação**.
    * Módulos que eram usados para treinamento são removidos; especificamente:
      * Preparar Modelo
      * Dividir Dados
      * Avaliar Modelo
    * O modelo treinado guardado é adicionado novamente para a experimentação.
    * **Entrada do serviço da Web** e **saída de serviço da Web** módulos são adicionados. Estes módulos de identificam em que os dados de utilizador irão introduzir o modelo e, em que os dados são retornados.

    O **experimentação de preparação** ainda é guardado em novos separadores na parte superior da tela de experimentação.

1. **Execute** a experimentação.

1. Selecione a saída do **modelo de pontuação** módulo e selecione **ver resultados** para verificar o modelo ainda está a funcionar. Pode ver que os dados originais, juntamente com o preço previsto ("etiquetas classificadas"), são apresentados.

A experimentação deverá agora ser semelhante a esta:  

![Captura de ecrã que mostra a configuração esperada da experimentação depois de prepará-lo para a implementação](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Implementar o serviço web

1. Selecione **implementar serviço Web** abaixo da tela.

1. Selecione o **destino de computação** que gostaria de executar o serviço web.

    Atualmente, a interface visual só suporta a implementação para destinos de computação do Azure Kubernetes Service (AKS). Pode escolher entre destinos de computação AKS disponíveis na sua área de trabalho do serviço de aprendizagem ou configurar um novo ambiente do AKS utilizando os passos na caixa de diálogo que aparece.

    ![Captura de ecrã que mostra uma configuração possível para um novo destino de computação](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Selecione **implementar serviço Web**. Verá a seguinte notificação quando a implementação estiver concluída. Implementação pode demorar alguns minutos.

    ![Captura de ecrã que mostra a mensagem de confirmação de uma implantação bem-sucedida.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Testar o serviço web

Pode testar e gerir os seus serviços de web de visual interface ao navegar para o **serviços da Web** separador.

1. Aceda à secção de serviço web. Verá que o serviço web implementado com o nome **Tutorial - prever o preço do automóvel [Exp preditiva]** .

     ![Captura de ecrã que mostra o separador de serviço da web com o serviço web recentemente criada realçado](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Selecione o nome do serviço web para ver detalhes adicionais.

     ![Ver a captura de ecrã que mostra os detalhes adicionais disponíveis no serviço web](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Selecione **teste**.

    ![Captura de ecrã que mostra o página de teste do serviço de web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Teste de dados de entrada ou utilizar os dados de exemplo autofilled e selecione **teste**.

    O pedido de teste foi submetido para o serviço web e os resultados são mostrados na página. Embora um valor do preço é gerado para os dados de entrada, não é utilizado para gerar o valor de previsão.

## <a name="consume-the-web-service"></a>Consumir o serviço web

Os utilizadores agora podem enviar pedidos de API para o seu serviço web do Azure e receber os resultados para prever o preço de suas automóveis novos.

**Pedido/resposta** -o utilizador envia uma ou mais linhas de dados dos automóveis para o serviço utilizando um protocolo HTTP. O serviço responde com um ou mais conjuntos de resultados.

Pode encontrar o exemplo chama o REST no **Consume** separador da página de detalhes do serviço web.

   ![Captura de ecrã que mostra um exemplo do REST chama que os usuários podem encontrar no separador Consume](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Navegue para o **API Doc** guia, para obter mais informações de API.

  ![Captura de ecrã que mostra detalhes adicionais da API que os usuários podem encontrar no separador de documentos de API](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments"></a>Gerir implementações e modelos

Os modelos e implementações de serviços da web que criar na interface do visual também podem ser geridas da área de trabalho de serviço do Azure Machine Learning.

1. Abra a área de trabalho do [portal do Azure](https://portal.azure.com/).  

1. Na área de trabalho, selecione **modelos**. Em seguida, selecione a experimentação que criou.

    ![Captura de ecrã que mostra como navegar para experimentações no portal do Azure](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Nesta página, verá detalhes adicionais sobre o modelo.

    ![Descrição geral do captura de ecrã que mostra estatísticas de experimentação no portal do Azure](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. Selecione **implementações**, este irá listar quaisquer serviços da web que utilizam o modelo. Selecione o nome do serviço web, é enviado para a página de detalhes do serviço web. Nesta página, pode obter informações mais detalhadas do serviço web.

    ![Execute o relatório de detalhes de captura de ecrã](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu que as principais etapas da criação, implantação e consumir um modelo de machine learning na interface do visual. Para saber mais sobre como pode utilizar a interface visual para resolver outros tipos de problemas, consulte os nossos outras experimentações de exemplo.

> [!div class="nextstepaction"]
> [Exemplo de classificação de risco de crédito](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
