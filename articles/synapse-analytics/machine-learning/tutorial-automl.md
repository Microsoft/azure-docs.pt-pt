---
title: 'Tutorial: Treine um modelo utilizando machine learning automatizado'
description: Tutorial sobre como treinar um modelo de aprendizagem automática sem código no Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: aaf0aab2ef600b269b9b47182aeb096ca13c7a87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943518"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Tutorial: Treine um modelo de aprendizagem automática sem código

Pode enriquecer os seus dados em tabelas Spark com novos modelos de machine learning que treina utilizando [machine learning automatizado.](../../machine-learning/concept-automated-ml.md) No Azure Synapse Analytics, pode selecionar uma tabela Spark no espaço de trabalho para usar como conjunto de dados de formação para a construção de modelos de aprendizagem automática, e pode fazê-lo numa experiência sem código.

Neste tutorial, você aprende a treinar modelos de machine learning usando uma experiência sem código no Synapse Studio. O Synapse Studio é uma característica da Azure Synapse Analytics. 

Você usará machine learning automatizado em Azure Machine Learning, em vez de codificar a experiência manualmente. O tipo de modelo que treinas depende do problema que estás a tentar resolver.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Um [espaço de trabalho Azure Synapse Analytics](../get-started-create-workspace.md). Certifique-se de que dispõe de uma conta de armazenamento Azure Data Lake Gen2 configurada como o armazenamento predefinido. Para o sistema de ficheiros Data Lake Storage Gen2 com o qual trabalha, certifique-se de que é o *contribuinte de dados da blob de armazenamento.*
- Uma piscina Apache Spark no seu espaço de trabalho Azure Synapse Analytics. Para mais detalhes, consulte [Quickstart: Crie uma piscina SQL dedicada utilizando o Synapse Studio](../quickstart-create-sql-pool-studio.md).
- Um serviço de aprendizagem automática Azure no seu espaço de trabalho Azure Synapse Analytics. Para mais detalhes, consulte [Quickstart: Crie um novo serviço ligado à Azure Machine Learning em Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-spark-table-for-the-training-dataset"></a>Criar uma tabela Spark para o conjunto de dados de treino

Para este tutorial, precisa de uma mesa de faíscas. O seguinte caderno cria um:

1. Descarregue o portátil [Create-Spark-Table-NYCTaxi-Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Importe o caderno para o Estúdio Synapse.
![Screenshot da Azure Synapse Analytics, com a opção Import em destaque.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Selecione a piscina Spark que pretende utilizar e, em seguida, selecione **Executar tudo**. Este passo obtém dados de táxi de Nova Iorque a partir do conjunto de dados aberto e guarda os dados para a sua base de dados padrão Spark.
![Screenshot de Azure Synapse Analytics, com a base de dados Run all e Spark em destaque.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Depois de concluída a execução do portátil, vê uma nova tabela Spark sob a base de dados padrão do Spark. A partir de **Data,** encontre a tabela com o nome **nyc_taxi**.
![Screenshot do separador Azure Synapse Analytics Data, com a nova tabela em destaque.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="open-the-automated-machine-learning-wizard"></a>Abra o assistente automatizado de aprendizagem automática

Para abrir o assistente:

1. Clique com o botão direito na tabela Spark que criou no passo anterior. Em seguida, selecione **Machine Learning** Enrich com  >  **novo modelo**.
![Screenshot da tabela Spark, com Machine Learning e Enrich com novo modelo em destaque.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. Forneça detalhes de configuração para criar uma experiência automatizada de aprendizagem automática executada em Azure Machine Learning. Esta corrida treina vários modelos. O melhor modelo de uma corrida bem sucedida está registado no registo do modelo Azure Machine Learning.

   ![Screenshot das especificações de configuração para treinar um modelo de aprendizagem automática.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Espaço de trabalho Azure Machine Learning**: É necessário um espaço de trabalho para a aprendizagem automática de máquinas Azure para criar uma experiência automatizada de aprendizagem automática. Também precisa de ligar o seu espaço de trabalho Azure Synapse Analytics ao espaço de trabalho Azure Machine Learning utilizando um [serviço ligado.](quickstart-integrate-azure-machine-learning.md) Depois de ter cumprido todos os pré-requisitos, pode especificar o espaço de trabalho Azure Machine Learning que pretende utilizar para esta execução automatizada.

    - **Nome da experiência**: Especificar o nome da experiência. Quando submete uma corrida automatizada de aprendizagem automática, fornece um nome de experiência. A informação para a execução é armazenada no âmbito dessa experiência no espaço de trabalho Azure Machine Learning. Esta experiência cria uma nova experiência por padrão e gera um nome proposto, mas também pode fornecer o nome de uma experiência existente.

    - **Melhor nome do modelo**: Especifique o nome do melhor modelo da execução automatizada. O melhor modelo é dado este nome e guardado no registo do modelo Azure Machine Learning automaticamente após esta execução. Uma corrida automatizada de aprendizagem automática cria muitos modelos de aprendizagem automática. Com base na métrica primária que seleciona num passo posterior, estes modelos podem ser comparados e o melhor modelo pode ser selecionado.

    - **Coluna-alvo**: Isto é o que o modelo é treinado para prever. Escolha a coluna que pretende prever. (Neste tutorial, selecionamos a coluna numérica `fareAmount` como coluna-alvo.)

    - **Piscina de faíscas**: Especifique a piscina Spark que pretende utilizar para a experiência automatizada. Os cálculos são executados na piscina que você especifica.

    - **Detalhes da configuração** da faísca : Além da piscina Spark, tem a opção de fornecer detalhes de configuração da sessão.

1. Selecione **Continuar**.

## <a name="choose-a-task-type"></a>Escolha um tipo de tarefa

Selecione o tipo de modelo de aprendizagem automática para a experiência, com base na pergunta que está a tentar responder. Porque `fareAmount` é a coluna alvo, e é um valor numérico, recomendamos que selecione **Regressão** aqui. Em seguida, selecione **Continuar**.

![Screenshot de Enrich com novo modelo, com Regressão em destaque.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Configurações adicionais

Se selecionou **a Regressão** ou **classificação** como o seu tipo de modelo na secção anterior, estão disponíveis as seguintes configurações:

- **Métrica primária**: Introduza a métrica que mede o quão bem o modelo está a correr. Utiliza esta métrica para comparar diferentes modelos criados na execução automatizada e determinar qual o modelo que melhor desempenhou.

- **Tempo de trabalho de formação (horas)**: Especifique o tempo máximo, em horas, para uma experiência para executar e treinar modelos. Note que também pode fornecer valores inferiores a 1 (por exemplo, **0,5**).

- **Iterações máximas simultâneas**: Escolha o número máximo de iterações que funcionam em paralelo.

- **Compatibilidade do modelo ONNX**: Se ativar esta opção, os modelos treinados por machine learning automatizados são convertidos para o formato ONNX. Isto é particularmente relevante se quiser utilizar o modelo para pontuar nas piscinas Azure Synapse Analytics SQL.

Todas estas definições têm um valor predefinido que pode personalizar.
![Screenshot de configurações adicionais para configurar um modelo de regressão.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Depois de todas as configurações necessárias, pode iniciar a sua execução automatizada. Pode escolher **Criar run**, que inicia a sua execução diretamente, sem código. Alternativamente, se preferir o código, pode selecionar **Abrir no bloco de notas.** Esta opção permite-lhe ver o código que cria a execução e, em seguida, executar o caderno.

>[!NOTE]
>Se selecionou **a previsão da série time** como o seu tipo de modelo na secção anterior, deve esporar configurações adicionais. A previsão também não suporta a compatibilidade do modelo ONNX.

### <a name="create-a-run-directly"></a>Criar uma corrida diretamente

Para iniciar a sua aprendizagem automática de máquinas, selecione **Start Run**. Vê uma notificação que indica que a execução está a começar. Depois vê outra notificação que indica sucesso. Também pode verificar o estado em Azure Machine Learning selecionando o link na notificação.
![Screenshot de notificação bem sucedida.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-a-run-with-a-notebook"></a>Criar uma corrida com um caderno

Para gerar um caderno, selecione **Abrir No Caderno.** Em seguida, **selecione Executar tudo**. Isto também lhe dá a oportunidade de adicionar configurações à sua aprendizagem automática de máquinas.

![Screenshot de um caderno, com Run tudo em destaque.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Depois de ter submetido com sucesso a execução, vê um link para a experiência executada no espaço de trabalho Azure Machine Learning na saída do caderno. Selecione o link para monitorizar a sua execução automatizada em Azure Machine Learning.
![Screenshot de Azure Synapse Analytics com um link realçado. ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Assistente de pontuação de modelo de machine learning (pré-visualização) para piscinas SQL dedicadas](tutorial-sql-pool-model-scoring-wizard.md)
- [Quickstart: Criar um novo serviço de aprendizagem automática Azure em Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md)
- [Capacidades de aprendizagem automática em Azure Synapse Analytics](what-is-machine-learning.md)