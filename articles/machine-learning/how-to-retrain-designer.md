---
title: Use parâmetros de gasoduto para retreinar modelos no designer
titleSuffix: Azure Machine Learning
description: Retrain modelos com oleodutos publicados e parâmetros de pipeline no Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 03/06/2021
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: ed5f59fc1d751f92a7c016aa3586497659f778fd
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962956"
---
# <a name="use-pipeline-parameters-to-retrain-models-in-the-designer"></a>Use parâmetros de gasoduto para retreinar modelos no designer


Neste artigo de como fazer, aprende-se a usar o designer de Aprendizagem automática Azure para treinar um modelo de aprendizagem automática utilizando parâmetros de pipeline. Utilizará os oleodutos publicados para automatizar o seu fluxo de trabalho e definirá parâmetros para treinar o seu modelo em novos dados. Os parâmetros do gasoduto permitem-lhe reutilizar os gasodutos existentes para diferentes trabalhos.  

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Treine um modelo de aprendizagem automática.
> * Crie um parâmetro de gasoduto.
> * Publique o seu oleoduto de treino.
> * Retreine o seu modelo com novos parâmetros.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning
* Complete a primeira parte desta série como fazer, [transforme os dados no designer](how-to-designer-transform-data.md)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

Este artigo também assume que você tem algum conhecimento de construção de oleodutos no designer. Para uma introdução guiada, complete o [tutorial.](tutorial-designer-automobile-price-train-score.md) 

### <a name="sample-pipeline"></a>Gasoduto de amostra

O oleoduto utilizado neste artigo é uma versão alterada de um pipeline de amostra [Previsão de rendimento](samples-designer.md#classification) na página inicial do designer. O pipeline utiliza o módulo [De Dados de Importação](algorithm-module-reference/import-data.md) em vez do conjunto de dados da amostra para mostrar como treinar modelos usando os seus próprios dados.

![Screenshot que mostra o pipeline de amostra modificado com uma caixa realçando o módulo de dados de importação](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Criar um parâmetro de pipeline

Os parâmetros do gasoduto são utilizados para construir oleodutos versáteis que podem ser reenviados mais tarde com valores de parâmetros variados. Alguns cenários comuns são a atualização de conjuntos de dados ou alguns hiper-parâmetros para a reconversão. Crie parâmetros de gasoduto para definir dinâmicamente variáveis em tempo de execução. 

Os parâmetros do gasoduto podem ser adicionados à fonte de dados ou parâmetros do módulo num oleoduto. Quando o gasoduto é reenviado, os valores destes parâmetros podem ser especificados.

Para este exemplo, irá alterar o caminho dos dados de treino de um valor fixo para um parâmetro, para que possa reconverter o seu modelo em diferentes dados. Também pode adicionar outros parâmetros do módulo como parâmetros de pipeline de acordo com a sua caixa de utilização.

1. Selecione o módulo **de Dados de Importação.**

    > [!NOTE]
    > Este exemplo utiliza o módulo De Dados de Importação para aceder a dados numa datas registada. No entanto, pode seguir passos semelhantes se utilizar padrões alternativos de acesso a dados.

1. No painel de detalhes do módulo, à direita da tela, selecione a sua fonte de dados.

1. Insira o caminho para os seus dados. Também pode selecionar **procurar o caminho** para navegar na sua árvore de arquivo. 

1. Mouseover the **Path** field, e selecione as elipses acima do campo **Caminho** que aparecem.

1. **Selecione Adicionar ao parâmetro do gasoduto**.

1. Forneça um nome de parâmetro e um valor padrão.

   ![Screenshot que mostra como criar um parâmetro de pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Selecione **Guardar**.

   > [!NOTE]
   > Também pode separar um parâmetro do módulo do parâmetro do pipeline no painel de detalhes do módulo, semelhante à adição de parâmetros de pipeline.
   >
   > Pode inspecionar e editar os parâmetros do pipeline selecionando o ícone de engrenagem **De Definições** ao lado do título do projeto do seu pipeline. 
   >    - Depois de se separar, pode eliminar o parâmetro do gasoduto na **vidraça de definição.**
   >    - Também pode adicionar um parâmetro de pipeline no painel **de Definições** e, em seguida, aplicá-lo em algum parâmetro do módulo.

1. Submeta o curso do gasoduto.

## <a name="publish-a-training-pipeline"></a>Publicar um pipeline de formação

Publique um oleoduto num ponto final de gasoduto para reutilizar facilmente os seus oleodutos no futuro. Um ponto final do gasoduto cria um ponto final REST para invocar o gasoduto no futuro. Neste exemplo, o seu ponto final do pipeline permite-lhe reutilizar o seu oleoduto para reativar um modelo com dados diferentes.

1. Selecione **Publicar** acima da tela do designer.
1. Selecione ou crie um ponto final de gasoduto.

   > [!NOTE]
   > Pode publicar vários oleodutos num único ponto final. Cada gasoduto num dado ponto final recebe um número de versão, que pode especificar quando ligar para o ponto final do gasoduto.

1. Selecione **Publicar**.

## <a name="retrain-your-model"></a>Retreine o seu modelo

Agora que tem um pipeline de treino publicado, pode usá-lo para treinar o seu modelo em novos dados. Pode submeter corridas a partir de um ponto final do pipeline a partir do espaço de trabalho do estúdio ou programáticamente.

### <a name="submit-runs-by-using-the-studio-portal"></a>Submeter runs usando o portal do estúdio

Utilize os seguintes passos para submeter um ponto final de gasoduto parametrizado a partir do portal do estúdio:

1. Vá à página **Endpoints** no seu espaço de trabalho do estúdio.
1. Selecione o **separador pontos finais pipeline.** Em seguida, selecione o seu ponto final do gasoduto.
1. Selecione o **separador pipelines publicado.** Em seguida, selecione a versão do pipeline que pretende executar.
1. Selecione **Submeter**.
1. Na caixa de diálogo de configuração, pode especificar os valores dos parâmetros para a execução. Para este exemplo, atualize a trajetória de dados para treinar o seu modelo utilizando um conjunto de dados não americano.

![Screenshot que mostra como configurar um pipeline parametrizado executado no designer](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Enviar corre usando código

Pode encontrar o ponto final REST de um pipeline publicado no painel de visão geral. Ao ligar para o ponto final, pode voltar a treinar o oleoduto publicado.

Para fazer uma chamada REST, precisa de um cabeçalho de autenticação do tipo portador OAuth 2.0. Para obter informações sobre a configuração da autenticação no seu espaço de trabalho e fazer uma chamada de REST parametrizada, consulte [Construir um pipeline de Aprendizagem automática Azure para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar um ponto final de pipeline de treino parametrizado usando o designer.

Para uma caminhada completa de como pode implementar um modelo para fazer previsões, consulte o tutorial do [designer](tutorial-designer-automobile-price-train-score.md) para treinar e implementar um modelo de regressão.

Para saber como publicar e submeter uma corrida ao ponto final do gasoduto utilizando o SDK, consulte [este artigo](how-to-deploy-pipelines.md).
