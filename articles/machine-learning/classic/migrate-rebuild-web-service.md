---
title: 'Estúdio ML (clássico): Migrar para Azure Machine Learning - Reconstruir serviço web'
description: Reconstruir serviços web (clássicos) como pontos finais de pipeline em Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 5e467d22cc3230bd9945fb276dc16cf1fa765bb6
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565506"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Reconstruir um serviço web studio (clássico) em Azure Machine Learning

Neste artigo, você aprende a reconstruir um serviço web Studio (clássico) como **um ponto final** em Azure Machine Learning.

Utilize pontos finais do gasoduto Azure Machine Learning para fazer previsões, retreinar modelos ou executar qualquer gasoduto genérico. O ponto final REST permite-lhe executar gasodutos a partir de qualquer plataforma. 

Este artigo faz parte da série de migração Studio (clássico) para Azure Machine Learning. Para obter mais informações sobre a migração para a Azure Machine Learning, consulte o [artigo visão geral da migração.](migrate-overview.md)

> [!NOTE]
> Esta série de migração centra-se no designer de arrastar e largar. Para obter mais informações sobre a implementação de modelos programáticamente, consulte [os modelos de machine learning implementar em Azure](../how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma área de trabalho do Azure Machine Learning. Criar um espaço de trabalho para [aprendizagem automática Azure.](../how-to-manage-workspace.md#create-a-workspace)
- Um oleoduto de treino de aprendizagem automática Azure. Para obter mais informações, consulte [a experiência Rebuild a Studio (classic) em Azure Machine Learning](migrate-rebuild-experiment.md).

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>Ponto final em tempo real vs ponto final do gasoduto

Os serviços web do estúdio (clássicos) foram substituídos por **pontos finais** em Azure Machine Learning. Utilize a seguinte tabela para escolher qual o tipo de ponto final a utilizar:

|Serviço web de estúdio (clássico)| Substituição de aprendizagem automática Azure
|---|---|
|Serviço web de pedido/resposta (previsão em tempo real)|Ponto final em tempo real|
|Serviço web de lote (previsão do lote)|Ponto final do gasoduto|
|Serviço web de reciclagem (reconversão)|Ponto final do gasoduto| 


## <a name="deploy-a-real-time-endpoint"></a>Implementar um ponto final em tempo real

No Studio (clássico), usou um **serviço web REQUEST/RESPOND** para implementar um modelo para previsões em tempo real. Em Azure Machine Learning, você usa um **ponto final em tempo real**.

Existem várias formas de implementar um modelo no Azure Machine Learning. Uma das formas mais simples é usar o designer para automatizar o processo de implantação. Utilize os seguintes passos para implantar um modelo como ponto final em tempo real:

1. Executar o seu oleoduto de treino completo pelo menos uma vez.
1. Depois de concluída a execução, na parte superior da tela, **selecione Criar o gasoduto** de  >  **inferência em tempo real**.

    ![criar pipeline de inferência em tempo real](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    O designer converte o gasoduto de treino num oleoduto de inferência em tempo real. Uma conversão semelhante também ocorre em Studio (clássico).

    No designer, o passo de conversão também [regista o modelo treinado para o seu espaço de trabalho Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. **Selecione Submeter-se** para executar o pipeline de inferência em tempo real e verificar se funciona com sucesso.

1. Depois de verificar o gasoduto de inferência, selecione **Implementar**.

1. Insira um nome para o seu ponto final e um tipo de cálculo.

    A tabela a seguir descreve as suas opções de computação de implantação no designer:

    | Destino de computação | Utilizado para | Descrição | Criação |
    | ----- |  ----- | ----- | -----  |
    |[Azure Kubernetes Service (AKS)](../how-to-deploy-azure-kubernetes-service.md) |Inferência em tempo real|Implantações de produção em larga escala. Tempo de resposta rápida e autoscalagem de serviço.| Criado pelo utilizador. Para obter mais informações, consulte [Criar metas de computação](../how-to-create-attach-compute-studio.md#inference-clusters). |
    |[Instâncias de contentores Azure ](../how-to-deploy-azure-container-instance.md)|Teste ou desenvolvimento | Cargas de trabalho em pequena escala, baseadas em CPU que requerem menos de 48 GB de RAM.| Criada automaticamente por Azure Machine Learning.

### <a name="test-the-real-time-endpoint"></a>Teste o ponto final em tempo real

Após a implementação concluída, pode ver mais detalhes e testar o seu ponto final:

1. Vá ao **separador Endpoints.**
1. Selecione o ponto final.
1. Selecione o separador **Teste**.
    
    ![Screenshot mostrando o separador Endpoints com o botão de ponto final de teste](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>Publicar um ponto final de gasoduto para previsão ou reconversão de lotes

Também pode utilizar o seu pipeline de treino para criar um **ponto final** de gasoduto em vez de um ponto final em tempo real. Utilize **pontos finais do gasoduto** para efetuar a previsão do lote ou a reconversão.

Os pontos finais do pipeline substituem **os pontos finais de execução de lote (clássicos)** e **os serviços web de reconversão.**

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>Publique um ponto final de pipeline para previsão de lote

Publicar um ponto final de previsão de lote é semelhante ao ponto final em tempo real.

Utilize as seguintes etapas para publicar um ponto final do gasoduto para a previsão do lote:

1. Executar o seu oleoduto de treino completo pelo menos uma vez.

1. Depois de concluída a execução, na parte superior da tela, **selecione Criar pipeline de**  >  **inferência Pipeline pipeline**

    ![Screenshot mostrando o botão de pipeline de inferência de criação em um oleoduto de treino](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    O designer converte o gasoduto de treino num pipeline de inferência de lote. Uma conversão semelhante também ocorre em Studio (clássico).

    No designer, este passo [também regista o modelo treinado para o seu espaço de trabalho Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. **Selecione Submeter-se** para executar o pipeline de inferência do lote e verificar se completa com sucesso.

1. Depois de verificar o gasoduto de inferência, **selecione Publicar**.
 
1. Crie um novo ponto final do gasoduto ou selecione um existente.
    
    Um novo ponto final do gasoduto cria um novo ponto final REST para o seu oleoduto. 

    Se selecionar um ponto final de gasoduto existente, não substitui o gasoduto existente. Em vez disso, a Azure Machine Learning versa cada oleoduto no ponto final. Pode especificar qual a versão a executar na sua chamada REST. Também deve definir um pipeline predefinido se a chamada REST não especificar uma versão.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>Publique um ponto final de pipeline para a reconversão

Para publicar um ponto final de oleoduto para reciclagem, já deve ter um rascunho de gasoduto que treine um modelo. Para obter mais informações sobre a construção de um pipeline de formação, consulte [a experiência Rebuild a Studio (clássica).](migrate-rebuild-experiment.md)

Para reutilizar o seu ponto final do pipeline para a reconversão, tem de criar um **parâmetro de pipeline** para o conjunto de dados de entrada. Isto permite-lhe definir dinamicamente o conjunto de dados de treino, para que possa voltar a treinar o seu modelo.

Utilize as seguintes medidas para publicar o ponto final do gasoduto de reconversão:

1. Executar o seu oleoduto de treino pelo menos uma vez.
1. Após a execução concluída, selecione o módulo de conjunto de dados.
1. No painel de detalhes do módulo, selecione **Definir como parâmetro de pipeline**.
1. Forneça um nome descritivo como "InputDataset".    

    ![Screenshot realçando como criar um parâmetro de pipeline](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    Isto cria um parâmetro de pipeline para o seu conjunto de dados de entrada. Quando ligar para o seu ponto final do pipeline para o treino, pode especificar um novo conjunto de dados para treinar o modelo.

1. Selecione **Publicar**.

    ![Screenshot realçando o botão Publicar em um pipeline de treino](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>Ligue para o seu ponto final do pipeline a partir do estúdio

Depois de criar o seu ponto final de inferência ou retreinamento do pipeline, pode ligar diretamente para o seu ponto final a partir do seu navegador.

1. Vá ao **separador Pipelines** e selecione **pontos finais do Pipeline**.
1. Selecione o ponto final do gasoduto que pretende executar.
1. Selecione **Submeter**.

    Pode especificar quaisquer parâmetros de pipeline depois de selecionar **Enviar por isso.**

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a reconstruir um serviço web Studio (clássico) em Azure Machine Learning. O próximo passo é [integrar o seu serviço web com aplicações de clientes.](migrate-rebuild-integrate-with-client-app.md)


Veja os outros artigos na série de migração Studio (clássica):

1. [Visão geral da migração](migrate-overview.md).
1. [Conjunto de dados migrar](migrate-register-dataset.md).
1. [Reconstruir um pipeline de treino studio (clássico).](migrate-rebuild-experiment.md)
1. **Reconstruir um serviço web Studio (clássico).**
1. [Integre um serviço web Azure Machine Learning com aplicações para clientes.](migrate-rebuild-integrate-with-client-app.md)
1. [Migrar Executar O Guião R](migrate-execute-r-script.md).
