---
title: 'Tutorial: Treine um modelo usando ML automatizado'
description: Tutorial sobre como treinar um modelo de machine learning sem código em Azure Synapse usando Apache Spark e ML automatizado.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4967d5305b4b438f3baa6fca078d7b3169612590
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093405"
---
# <a name="tutorial-train-a-machine-learning-model-code-free-in-azure-synapse-with-apache-spark-and-automated-ml"></a>Tutorial: Treine um modelo de aprendizagem automática sem código em Azure Synapse com Apache Spark e ML automatizado

Saiba como enriquecer facilmente os seus dados em tabelas Spark com novos modelos de machine learning que treina utilizando [ML automatizado em Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml).  Um utilizador em Synapse pode simplesmente selecionar uma tabela Spark no espaço de trabalho Azure Synapse para usar como conjunto de dados de formação para construir modelos de aprendizagem automática numa experiência sem código.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> - Modelos de aprendizagem automática de comboios usando uma experiência sem código no estúdio Azure Synapse que usa ML automatizado em Azure Machine Learning. O tipo de modelo que treina depende do problema que está a tentar resolver.

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

## <a name="launch-automated-ml-wizard-to-train-a-model"></a>Lançar assistente de ML automatizado para treinar um modelo

Clique à direita na tabela Spark criada no passo anterior. Selecione "Machine Learning-> Enrich with new model" para abrir o assistente.
![Lançar assistente ML automatizado](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

Aparecerá um painel de configuração e ser-lhe-á pedido que forneça detalhes de configuração para criar uma experiência ML automatizada executada em Azure Machine Learning. Esta corrida irá formar vários modelos e o melhor modelo de uma corrida bem sucedida será registado no registo do modelo Azure Machine Learning:

![Configurar passo de corrida1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Espaço de trabalho Azure Machine Learning**: É necessário um espaço de trabalho para a criação da experiência automatizada do ML. Também precisa de ligar o seu espaço de trabalho Azure Synapse ao espaço de trabalho Azure Machine Learning utilizando um [serviço ligado.](quickstart-integrate-azure-machine-learning.md) Uma vez que tenha todos os pré-requisitos, pode especificar o espaço de trabalho Azure Machine Learning que pretende utilizar para esta execução automática de ML.

- **Nome da experiência**: Especificar o nome da experiência. Quando submete uma execução automática de ML, fornece um nome de experiência. A informação para a execução é armazenada no âmbito dessa experiência no espaço de trabalho Azure Machine Learning. Esta experiência criará uma nova experiência por padrão e está a gerar um nome proposto, mas também pode fornecer o nome de uma experiência existente.

- **Melhor modelo**: Especifique o nome do melhor modelo da execução automática de ML. O melhor modelo será dado este nome e guardado no registo do modelo Azure Machine Learning automaticamente após esta execução. Uma execução automática de ML criará muitos modelos de aprendizagem automática. Com base na métrica primária que irá selecionar num passo posterior, estes modelos podem ser comparados e o melhor modelo pode ser selecionado.

- **Coluna-alvo**: Isto é o que o modelo é treinado para prever. Escolha a coluna que pretende prever.

- **Piscina de** faíscas : A piscina Spark que você deseja usar para a corrida automatizada de experiências ML. Os cálculos serão executados na piscina que especifica.

- **Detalhes da configuração** da faísca : Além da piscina Spark, você também tem a opção de fornecer detalhes de configuração da sessão.

Neste tutorial, selecionamos a coluna numérica `fareAmount` como coluna alvo.

Clique em "Continuar".

## <a name="choose-task-type"></a>Escolha o tipo de tarefa

Selecione o tipo de modelo de aprendizagem automática para a experiência com base na pergunta que está a tentar responder. Uma vez que selecionamos `fareAmount` como coluna-alvo, e é um valor numérico, vamos selecionar *Regressão.*

Clique em "Continuar" para configurar definições adicionais.

![Seleção do tipo de tarefa](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Configurações adicionais

Se selecionar o tipo *de Classificação* ou *Regressão,* as configurações adicionais são:

- **Métrica primária**: A métrica usada para medir o quão bem o modelo está a correr. Esta é a métrica que será usada para comparar diferentes modelos criados na execução automática de ML, e determinar qual o modelo que melhor desempenhou.

- Tempo de **formação (horas)**: O máximo de tempo, em horas, para uma experiência para correr e treinar modelos. Note que também pode fornecer valores inferiores a 1. Por exemplo, `0.5`.

- **Iterações máximas simultâneas**: Representa o número máximo de iterações que seriam executadas em paralelo.

- **Compatibilidade do modelo ONNX**: Se ativado, os modelos treinados por ML automatizado serão convertidos para o formato ONNX. Isto é particularmente relevante se quiser utilizar o modelo para pontuar nas piscinas Azure Synapse SQL.

Todas estas definições têm um valor predefinido que pode personalizar.
![configurações adicionais](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Note que se selecionar "Previsão de séries de tempo", são necessárias mais configurações. A previsão também não suporta a compatibilidade do modelo ONNX.

Uma vez feitas todas as configurações necessárias, pode iniciar uma execução automática de ML.

Há duas maneiras de iniciar uma corrida automática de ML em Azure Azure Synapse. Para uma experiência sem código, pode optar por **criar executar** diretamente. Se preferir o código, pode selecionar **Open no portátil**, o que lhe permite ver o código que cria a execução e executar o caderno.

### <a name="create-run-directly"></a>Criar Executar diretamente

Clique em "Start Run" para iniciar a execução automática de ML diretamente. Haverá uma notificação que indica que a execução automatizada da ML está a começar.

Após o início da execução automatizada de ML com sucesso, verá outra notificação bem sucedida. Também pode clicar no botão de notificação para verificar o estado da submissão.
Azure Machine Learning clicando no link na notificação bem sucedida.
![Notificação bem sucedida](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Criar correr com caderno

Selecione *Abrir no Caderno* para gerar um caderno. Clique *em Executar tudo* para executar o caderno.
Isto também lhe dá a oportunidade de adicionar configurações adicionais à sua execução automatizada de ML.

![Caderno Aberto](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Após a execução do caderno ter sido submetida com sucesso, haverá um link para a experiência realizada no espaço de trabalho Azure Machine Learning na saída do caderno. Pode clicar no link para monitorizar a sua execução automática de ML em Azure Machine Learning.
![Notebook executar tudo ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Modelo de machine learning pontuação em Azure Synapse dedicada SQL Pools](tutorial-sql-pool-model-scoring-wizard.md).
- [Quickstart: Criar um novo serviço Azure Machine Learning ligado em Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Capacidades de aprendizagem automática em Azure Synapse Analytics](what-is-machine-learning.md)
