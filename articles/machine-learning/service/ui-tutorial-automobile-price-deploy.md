---
title: 'Tutorial: Implementar um modelo de machine learning com a interface visual'
titleSuffix: Azure Machine Learning service
description: Saiba como criar uma solução de Análise Preditiva na interface visual de serviço do Azure Machine Learning. Formar, Pontuar e implementar um modelo de aprendizagem automática com arrastar e largar módulos. Este tutorial é a parte dois de uma série de duas partes sobre prever preços de automóveis com regressão linear.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 5f29e3820416686b42167fa278c4b7d0f9a58f1f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190908"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Tutorial: Implementar um modelo de machine learning com a interface visual

Neste tutorial, fazer uma observação expandida ao desenvolver uma solução preditiva na interface visual de serviço do Azure Machine Learning. Este tutorial é a **segunda parte de uma série composta por duas partes**. Na [primeira parte do tutorial](ui-tutorial-automobile-price-train-score.md), treinados, classificadas e avaliado um modelo para prever preços de automóveis. Nesta parte do tutorial,:

> [!div class="checklist"]
> * Preparar um modelo de implementação
> * Implementar serviços Web
> * Testar um serviço web
> * Gerir um serviço web
> * Consumir o serviço web

## <a name="prerequisites"></a>Pré-requisitos

Concluída [primeira parte do tutorial](ui-tutorial-automobile-price-train-score.md).

## <a name="prepare-for-deployment"></a>Preparar para a implementação

Para atribuir a outras pessoas oportunidade de se usar o modelo de previsão desenvolvido neste tutorial, pode implementá-la como um serviço web do Azure.

Até agora, já foi fazer experiências com seu modelo de formação. Agora, chegou a hora para gerar novas predições com base na entrada do usuário.

Preparação para a implantação é um processo de dois passos:  

1. Converter os *experimentação de preparação* que criou num *experimentação preditiva*
1. Implementar a experimentação preditiva como um serviço web

Talvez queira fazer uma cópia da experimentação primeiro selecionando **guardar como** na parte inferior da tela de experimentação.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experimentação de preparação para uma experimentação preditiva

Para obter esse modelo pronto para implantação, converta esta experimentação de preparação para uma experimentação preditiva. Normalmente, isto envolve três passos:

1. Guarde o modelo Treinou e substituir os módulos de treinamento
1. Cortar a experimentação para remover módulos que eram necessários apenas para formação
1. Definir em que o serviço web irá aceitar dados de entrada e em que gera a saída

Pode executar estes passos manualmente ou pode selecionar **no serviço de Web** na parte inferior da tela de experimentação tê-los feito automaticamente.

![Animados gif que mostra a conversão automática de uma experimentação de preparação para uma experimentação preditiva](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

Quando seleciona **no serviço de Web**, várias coisas acontecem:

* O modelo preparado é convertido numa única **modelo preparado** módulo. Ele é armazenado na paleta do módulo para a esquerda da tela de experimentação. Pode encontrá-lo sob **modelos de formação**.
* Módulos que eram usados para treinamento são removidos; especificamente:
  * Preparar Modelo
  * Dividir Dados
  * Avaliar Modelo
* O modelo treinado guardado é adicionado novamente para a experimentação
* **Entrada do serviço da Web** e **saída de serviço da Web** módulos são adicionados. Estes módulos de identificam em que os dados do utilizador irão introduzir o modelo e, em que os dados são retornados.

Pode ver que a experimentação é guardada em duas partes em novos separadores na parte superior da tela de experimentação. A experimentação de preparação original é no separador **experimentação de preparação**, e a experimentação preditiva recentemente criada está sob **experimentação preditiva**. A experimentação preditiva é o que vai implementar como um serviço web.

A experimentação deverá agora ser semelhante a esta:  

![Captura de ecrã que mostra a configuração esperada da experimentação depois de prepará-lo para a implementação](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

Execute a experimentação pela última vez (selecione **executar**). Escolha o destino de computação que pretende que a experimentação para execução na caixa de diálogo pop-up. Para verificar se o modelo ainda está a funcionar, selecione a saída do módulo do modelo de pontuação e selecione **ver resultados**. Pode ver que os dados originais, juntamente com o preço previsto ("etiquetas classificadas"), são apresentados.

## <a name="deploy-the-web-service"></a>Implementar o serviço web

Para implementar um novo serviço web derivado de sua experiência:

1. Selecione **implementar serviço Web** abaixo da tela.
1. Selecione o **destino de computação** que gostaria de executar o serviço web.

    Atualmente, a interface visual só suporta a implementação para destinos de computação do Azure Kubernetes Service (AKS). Pode escolher entre destinos de computação AKS disponíveis na sua área de trabalho do serviço de aprendizagem ou configurar um novo ambiente do AKS utilizando os passos na caixa de diálogo que aparece.

    ![Captura de ecrã que mostra uma configuração possível para um novo destino de computação](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Selecione **implementar serviço Web**. Verá a seguinte notificação quando a implementação estiver concluída. Implementação pode demorar alguns minutos.

    ![Captura de ecrã que mostra a mensagem de confirmação de uma implantação bem-sucedida.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Testar o serviço web

Dados de entrada do usuário inserem seu modelo implementado por meio da **entrada de serviço da Web** módulo. A entrada é classificada, em seguida, o **modelo de pontuação** módulo. A forma como configurou a experimentação preditiva, o modelo espera que os dados no mesmo formato que o conjunto de dados original do preço de automóveis. Por fim, os resultados são devolvidos para o utilizador através da **saída de serviço da Web** módulo.

Pode testar um serviço web no guia de serviço da web na interface do visual.

1. Aceda à secção de serviço web. Verá que o serviço web implementado com o nome **Tutorial - prever o preço do automóvel [Exp preditiva]**.

     ![Captura de ecrã que mostra o separador de serviço da web com o serviço web recentemente criada realçado](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Selecione o nome do serviço web para ver detalhes adicionais.

     ![Ver a captura de ecrã que mostra os detalhes adicionais disponíveis no serviço web](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Selecione **teste**.

    ![Captura de ecrã que mostra o página de teste do serviço de web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Teste de dados de entrada ou utilizar os dados de exemplo autofilled e selecione **teste** na parte inferior. O pedido de teste foi submetido para o serviço web e os resultados são mostrados na página.

## <a name="manage-the-web-service"></a>Gerir o serviço web

Assim que tiver implantado o serviço da web, pode gerenciá-lo a partir da **serviços da Web** guia na interface do visual.

Pode eliminar um serviço da web, selecionando **eliminar** na página de detalhes do serviço da web.

   ![Captura de ecrã que mostra a localização do botão Delete web service na parte inferior da janela](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>Consumir o serviço web

Nos passos anteriores deste tutorial, implementou um modelo de predição de automóveis como um serviço web do Azure. Agora, os utilizadores podem enviar dados para o mesmo e receber resultados através da REST API.

**Pedido/resposta** -o utilizador envia uma ou mais linhas de dados dos automóveis para o serviço utilizando um protocolo HTTP. O serviço responde com um ou mais conjuntos de resultados.

Pode encontrar o exemplo chama o REST no **Consume** separador da página de detalhes do serviço web.

   ![Captura de ecrã que mostra um exemplo do REST chama que os usuários podem encontrar no separador Consume](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Navegue para o **API Doc** guia, para obter mais informações de API.

  ![Captura de ecrã que mostra detalhes adicionais da API que os usuários podem encontrar no separador de documentos de API](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>Gerir modelos e implementações na área de trabalho de serviço de Azure Machine Learning

Os modelos e implementações de serviços da web que criar na interface do visual podem ser geridas a partir da área de trabalho do serviço do Azure Machine Learning.

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

Neste tutorial, aprendeu que as principais etapas da criação, implantação e consumir um modelo de machine learning na interface do visual. Para saber mais sobre como pode utilizar a interface visual para resolver outros tipos de problemas, consulte as experimentações de exemplo.

> [!div class="nextstepaction"]
> [Exemplo de classificação de risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
