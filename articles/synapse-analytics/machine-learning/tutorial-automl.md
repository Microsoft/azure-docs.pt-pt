---
title: 'Tutorial: Formação de modelo de aprendizagem automática utilizando o AutoML'
description: Tutorial para treinar um modelo de aprendizagem automática em Azure Synapse usando AutoML.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 8dd99b60a548e3c392bbe468ddde484081e6eb8b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468411"
---
# <a name="tutorial-code-free-machine-learning-model-training-in-azure-synapse-with-automl-preview"></a>Tutorial: Formação de modelo de aprendizagem automática livre de códigos em Azure Synapse com AutoML (Pré-visualização)

Saiba como enriquecer facilmente os seus dados em tabelas Spark com novos modelos de machine learning que treina utilizando [o AutoML em Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml).  Um utilizador em Synapse pode simplesmente selecionar uma tabela Spark no espaço de trabalho Azure Synapse para usar como conjunto de dados de formação para construir modelos de aprendizagem automática numa experiência livre de códigos.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> - Modelos de aprendizagem de máquinas de treinamento usando uma experiência sem código no estúdio Azure Synapse que usa ML automatizado em Azure ML. O tipo de modelo que treina depende do problema que está a tentar resolver.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Espaço de trabalho Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento ADLS Gen2 configurada como o armazenamento padrão. Tem de ser o colaborador de dados da **Blob** de Armazenamento do sistema de ficheiros ADLS Gen2 com o que trabalha.
- Piscina de faíscas no seu espaço de trabalho Azure Synapse Analytics. Para mais detalhes, consulte [Criar uma piscina de faíscas em Azure Synapse.](../quickstart-create-sql-pool-studio.md)
- Serviço de aprendizagem automática Azure no seu espaço de trabalho Azure Synapse Analytics. Para mais detalhes, consulte [criar um serviço ligado à Aprendizagem automática Azure em Azure Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="create-a-spark-table-for-training-dataset"></a>Criar uma tabela Spark para conjunto de dados de treino

Vai precisar de uma mesa spark para este tutorial. O seguinte caderno criará uma mesa Spark.

1. Descarregue o caderno [Create-Spark-Table-NYCTaxi-Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)

1. Importe o caderno para o Azure Synapse Studio.
![Caderno de Importação](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Selecione a piscina Spark que pretende utilizar e clique `Run all` em . Executar este caderno obterá dados de táxi de Nova Iorque a partir de conjunto de dados abertos e economizará na sua base de dados padrão Spark.
![Executar tudo](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Após a execução do portátil, uma nova tabela Spark será criada na base de dados padrão spark. Vá ao Data Hub e encontre a tabela com o qual `nyc_taxi` .
![Mesa de Faísca](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automl-wizard-to-train-a-model"></a>Lançar assistente AutoML para treinar um modelo

Clique à direita na tabela Spark criada no passo anterior. Selecione "Machine Learning-> Enrich with new model" para abrir o assistente.
![Lançar assistente autoML](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

Aparecerá um painel de configuração e ser-lhe-á pedido que forneça detalhes de configuração para criar uma experiência AutoML executada em Azure Machine Learning. Esta corrida irá formar vários modelos e o melhor modelo de uma corrida bem sucedida será registado no registo do modelo Azure ML:

![Configurar passo de corrida1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Espaço de trabalho Azure ML**: É necessário um espaço de trabalho Azure ML para a criação da experiência AutoML. Também precisa de ligar o seu espaço de trabalho Azure Synapse ao espaço de trabalho Azure ML utilizando um [serviço ligado](quickstart-integrate-azure-machine-learning.md). Uma vez que tenha todos os pré-requisitos, pode especificar o espaço de trabalho Azure ML que pretende utilizar para esta execução AutoML.

- **Nome da experiência**: Especificar o nome da experiência. Quando submete uma execução AutoML, fornece um nome de experiência. A informação para a execução é armazenada no âmbito dessa experiência no espaço de trabalho Azure ML. Esta experiência criará uma nova experiência por padrão e está a gerar um nome proposto, mas também pode fornecer o nome de uma experiência existente.

- **Melhor modelo**: Especifique o nome do melhor modelo da corrida AutoML. O melhor modelo será dado este nome e guardado automaticamente no registo do modelo Azure ML após esta execução. Uma corrida AutoML criará muitos modelos de aprendizagem automática. Com base na métrica primária que irá selecionar num passo posterior, estes modelos podem ser comparados e o melhor modelo pode ser selecionado.

- **Coluna-alvo**: Isto é o que o modelo é treinado para prever. Escolha a coluna que pretende prever.

- **Piscina de** faíscas : A piscina Spark que pretende utilizar para a experiência AutoML. Os cálculos serão executados na piscina que especifica.

- **Detalhes da configuração** da faísca : Além da piscina Spark, você também tem a opção de fornecer detalhes de configuração da sessão.

Neste tutorial, selecionamos a coluna numérica `fareAmount` como coluna alvo.

Clique em "Continuar".

## <a name="choose-task-type"></a>Escolha o tipo de tarefa

Selecione o tipo de modelo de aprendizagem automática para a experiência com base na pergunta que está a tentar responder. Uma vez que selecionamos `fareAmount` como coluna-alvo, e é um valor numérico, vamos selecionar *Regressão.*

Clique em "Continuar" para configurar definições adicionais.

![Seleção do tipo de tarefa](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Configurações adicionais

Se selecionar o tipo *de Classificação* ou *Regressão,* as configurações adicionais são:

- **Métrica primária**: A métrica usada para medir o quão bem o modelo está a correr. Esta é a métrica que será usada para comparar diferentes modelos criados na corrida AutoML, e determinar qual o modelo mais bem executado.

- Tempo de **formação (horas)**: O máximo de tempo, em horas, para uma experiência para correr e treinar modelos. Note que também pode fornecer valores inferiores a 1. Por exemplo, `0.5`.

- **Iterações máximas simultâneas**: Representa o número máximo de iterações que seriam executadas em paralelo.

- **Compatibilidade do modelo ONNX**: Se ativado, os modelos treinados por AutoML serão convertidos para o formato ONNX. Isto é particularmente relevante se quiser utilizar o modelo para pontuar nas piscinas Azure Synapse SQL.

Todas estas definições têm um valor predefinido que pode personalizar.
![configurações adicionais](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Note que selecione "Previsão de séries de tempo", há mais configurações necessárias. A previsão também não suporta a compatibilidade do modelo ONNX.

Uma vez feitas todas as configurações necessárias, pode iniciar a execução AutoML.

Existem duas formas de iniciar uma corrida autoML em Azure Azure Synapse. Para uma experiência sem código, pode optar por **criar executar** diretamente. Se preferir o código, pode selecionar **Open no portátil**, o que lhe permite ver o código que cria a execução e executar o caderno.

### <a name="create-run-directly"></a>Criar Executar diretamente

Clique em "Iniciar execução" para iniciar a execução autoML diretamente. Haverá uma notificação que indica que a execução autoML está a começar.

Depois de a execução autoML ter começado com sucesso, verá outra notificação bem sucedida. Também pode clicar no botão de notificação para verificar o estado da submissão.
Azure ML clicando no link na notificação bem sucedida.
![Notificação bem sucedida](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Criar correr com caderno

Selecione *Abrir no Caderno* para gerar um caderno. Clique *em Executar tudo* para executar o caderno.
Isto também lhe dá a oportunidade de adicionar definições adicionais à sua execução AutoML.

![Caderno Aberto](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Após a execução do caderno ter sido submetida com sucesso, haverá um link para a experiência realizada no espaço de trabalho Azure ML na saída do portátil. Pode clicar no link para monitorizar a sua execução AutoML em Azure ML.
![Notebook executar tudo ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Modelo de machine learning pontuação em Azure Synapse dedicada SQL Pools](tutorial-sql-pool-model-scoring-wizard.md).
- [Quickstart: Criar um novo serviço Azure Machine Learning ligado em Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Capacidades de aprendizagem automática em Azure Synapse Analytics](what-is-machine-learning.md)