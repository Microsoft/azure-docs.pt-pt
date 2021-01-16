---
title: 'Tutorial: Implementar modelos ML com o designer'
titleSuffix: Azure Machine Learning
description: Construa uma solução de análise preditiva no designer de Aprendizagem automática Azure. Treine, marque e implemente um modelo de aprendizagem automática utilizando módulos de arrasto e queda.
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 01/15/2021
ms.custom: designer
ms.openlocfilehash: 6bba5ad17cbb6f1ed72d06b37c6d6af9ebd26495
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246473"
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

1. Acima da tela do gasoduto, **selecione Criar o gasoduto** de  >  **inferência em tempo real**.

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

    Se esta for a primeira corrida, pode levar até 20 minutos para o seu oleoduto terminar de funcionar. As definições de computação padrão têm um tamanho mínimo de nó de 0, o que significa que o designer deve alocar recursos depois de estar inativo. As repetidas operações de gasoduto levarão menos tempo, uma vez que os recursos de computação já estão atribuídos. Além disso, o designer utiliza resultados em cache para cada módulo para melhorar ainda mais a eficiência.

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

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="Screenshot mostrando como configurar um novo ponto final em tempo real":::

    Também pode alterar a definição **Avançada** para o seu ponto final em tempo real.
    
    |Definição avançada|Description|
    |---|---|
    |Ativar diagnósticos de Insights de Aplicações e recolha de dados| Se permitir que a Azure Application Ingishts recolha dados dos pontos finais implantados. </br> Por padrão: falso |
    |Tempo limite de pontuação| Um intervalo em milissegundos para impor chamadas de pontuação para o serviço web.</br>Por padrão: 60000|
    |Escala automática ativada|   Se permitir a autoscalagem para o serviço web.</br>Por padrão: verdadeiro|
    |Réplicas min| O número mínimo de recipientes a utilizar ao fazer a autoscalagem deste serviço web.</br>Por predefinição: 1|
    |Réplicas max| O número máximo de recipientes a utilizar ao fazer a autoscalagem deste serviço web.</br> Por predefinição: 10|
    |Utilização do alvo|A utilização do alvo (em percentagem em 100) que o autoscaler deve tentar manter para este serviço web.</br> Por predefinição: 70|
    |Período de atualização|Com que frequência (em segundos) o autoescalador tenta escalar este serviço web.</br> Por predefinição: 1|
    |Capacidade de reserva do CPU|O número de núcleos de CPU a atribuir a este serviço web.</br> Por predefinição: 0.1|
    |Capacidade de reserva de memória|A quantidade de memória (em GB) a atribuir a este serviço web.</br> Por predefinição: 0.5|
        

1. Selecione **Implementar**. 

    Uma notificação de sucesso acima da tela aparece após o fim da implantação. Pode levar alguns minutos.

> [!TIP]
> Também pode ser implantado no **Azure Container Instance** (ACI) se selecionar **Azure Container Instance** for **Compute type** na caixa de definição de ponto final em tempo real.
> A exemplo do recipiente Azure é utilizada para testes ou desenvolvimento. Utilize ACI para cargas de trabalho baseadas em CPU de baixa escala que exijam menos de 48 GB de RAM.

## <a name="view-the-real-time-endpoint"></a>Ver o ponto final em tempo real

Após o fim da implementação, pode ver o seu ponto de chegada em tempo real indo para a página **Endpoints.**

1. Na página **Endpoints,** selecione o ponto final que implementou.

1. No separador **Detalhes,** pode ver mais informações como o REST URI, status e tags.

1. No separador **Consumir,** pode encontrar chaves de segurança e definir métodos de autenticação.

1. No separador **Registos de Implementação,** pode encontrar os registos de implementação detalhados do seu ponto final em tempo real. 

Para obter mais informações sobre o consumo do seu serviço web, consulte [Consumir um modelo implementado como um webservice](how-to-consume-web-service.md)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu os passos-chave na forma de criar, implementar e consumir um modelo de aprendizagem automática no designer. Para saber mais sobre como pode usar o designer veja os seguintes links:

+ [Amostras de](samples-designer.md)design : Aprenda a usar o designer para resolver outros tipos de problemas.
+ [Utilize o estúdio Azure Machine Learning numa rede virtual Azure](how-to-enable-studio-virtual-network.md).