---
title: Remodelar modelos utilizando o designer de machine learning Azure (pré-visualização)
titleSuffix: Azure Machine Learning
description: Saiba como retreinar modelos com oleodutos publicados no designer de Machine Learning Azure (pré-visualização).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 02/24/2020
ms.openlocfilehash: 8e7874ec2a0ea160d29f8755ca8680c4dfbeec1d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268491"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Remodelar modelos com designer de Machine Learning Azure (pré-visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste como-fazer, aprende-se a usar o designer de Machine Learning Azure para retreinar um modelo de aprendizagem automática. Descubra como utilizar os gasodutos publicados para automatizar fluxos de trabalho de aprendizagem automática para reciclagem.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Treine um modelo de aprendizagem automática.
> * Crie um parâmetro de gasoduto.
> * Publique o seu oleoduto de treino.
> * Retrete o seu modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://aka.ms/AMLFree)

* Um espaço de trabalho azure machine learning com a Enterprise SKU.

Este como-assumir você tem conhecimento básico de construir oleodutos no designer. Para uma introdução guiada ao designer, complete o [tutorial.](tutorial-designer-automobile-price-train-score.md) 

### <a name="sample-pipeline"></a>Gasoduto de amostra

O gasoduto utilizado neste artigo é uma versão alterada da encontrada na [Amostra 3 - Previsão](how-to-designer-sample-classification-predict-income.md)do Rendimento . Utiliza o módulo [Dados de Importação](algorithm-module-reference/import-data.md) em vez do conjunto de dados da amostra para lhe mostrar como treinar um modelo utilizando os seus próprios dados.

![Screenshot mostrando o pipeline de amostra modificado com uma caixa realçando o módulo de dados de importação](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Treine um modelo de aprendizagem automática

Para retreinar um modelo, precisa de um modelo inicial. Nesta secção, aprende-se a treinar um modelo e a aceder ao modelo guardado utilizando o designer.

1. Selecione o módulo **dados de importação.**
1. No painel de propriedades, especifique uma fonte de dados.

    ![Screenshot mostrando uma configuração de amostra do módulo de dados de importação](./media/how-to-retrain-designer/import-data-settings.png)

    Para este exemplo, os dados são armazenados numa loja de [dados Azure.](how-to-access-data.md) Se ainda não tiver uma datastore, pode criar uma agora selecionando **a New datastore**.

1. Especifique o caminho para os seus dados. Também pode selecionar o **caminho do Browse** para navegar visualmente na sua loja de dados. 

1. Selecione **Executar** na parte superior da tela.
    
    > [!NOTE]
    > Se já definiu uma computação predefinida para este projeto de gasoduto, o gasoduto funcionará automaticamente. Caso contrário, pode seguir as instruções no painel de definições que parece definir uma agora.

### <a name="locate-your-trained-model"></a>Localize o seu modelo treinado

O designer guarda todas as saídas de gasoduto, incluindo modelos treinados, para a conta de armazenamento padrão. Também pode aceder ao modelo treinado diretamente no designer:

1. Espere que o oleoduto termine de funcionar.

1. Selecione o módulo **Modelo de Comboio.**

1. No painel de definições, selecione **Outputs+logs**.

1. Clique no ícone **de saída do Ver** e siga as instruções na janela pop-up para localizar o modelo treinado.

![Screenshot mostrando como descarregar o modelo treinado](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Criar um parâmetro de gasoduto

Adicione parâmetros de gasoduto para definir variáveis dinâmicamente no tempo de execução. Para este pipeline, adicione um parâmetro para o caminho de dados de treino para que possa retreinar o seu modelo num novo conjunto de dados.

1. Selecione o módulo **dados de importação.**
1. No painel de definições, selecione as elipses acima do campo **Caminho.**
1. **Selecione Adicionar ao parâmetro do gasoduto**.
1. Forneça um nome de parâmetro e valor predefinido.

    > [!NOTE]
    > Pode inspecionar e editar os parâmetros do gasoduto selecionando o ícone de **engrenagem Definições** ao lado do título da sua corrente de gasoduto. 

![Screenshot mostrando como criar um parâmetro de gasoduto](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publicar um pipeline de formação

Quando se publica um oleoduto, cria-se um ponto final de gasoduto. Os pontos finais do gasoduto permitem-lhe reutilizar e gerir os seus oleodutos para a repetição e automação. Neste exemplo, instalou o seu oleoduto para reconversão.

1. **Selecione Publicar** acima da tela do designer.
1. Selecione, ou crie, um novo ponto final do oleoduto.

    > [!NOTE]
    > Pode publicar vários oleodutos num único ponto final. Cada gasoduto no ponto final recebe um número de versão, que pode especificar quando ligar para o ponto final do gasoduto.

1. Selecione **Publicar**.

## <a name="retrain-your-model"></a>Retrete o seu modelo

Agora que tem um pipeline de treino publicado, pode usá-lo para retreinar o seu modelo usando novos dados. Pode submeter execuções a partir de um ponto final de gasoduto a partir de um portal ou programaticamente.

### <a name="submit-runs-with-the-designer"></a>Submeter corridas com o designer

Utilize os seguintes passos para submeter um ponto final de gasoduto executado pelo designer:

1. Vá para a página **Endpoints.**

1. Selecione o separador de **pontos finais do Pipeline.**

1. Selecione o ponto final do seu pipeline.

1. Selecione o separador **de gasodutos Publicados.**

1. Selecione o oleoduto que pretende executar.

1. Selecione **Submeter**.

1. No diálogo de configuração, pode especificar um novo valor de percurso de dados de entrada, que aponta para o seu novo conjunto de dados.

![Screenshot mostrando como configurar um pipeline parametrizado executado no designer](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-with-code"></a>Submeter corre com código

Pode encontrar o ponto final REST de um oleoduto publicado no painel de visão geral. Ao ligar para o ponto final, pode retreinar o oleoduto publicado.

Para fazer uma chamada REST, você precisará de um cabeçalho de autenticação tipo OAuth 2.0. Consulte a seguinte [secção tutorial](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) para obter mais detalhes sobre a instalação da autenticação no seu espaço de trabalho e fazer uma chamada REST parametrizada.

## <a name="next-steps"></a>Passos seguintes

Siga o [tutorial](tutorial-designer-automobile-price-train-score.md) do designer para treinar e implementar um modelo de regressão.
