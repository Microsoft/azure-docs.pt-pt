---
title: 'Tutorial: prever o preço do automóvel com o designer'
titleSuffix: Azure Machine Learning
description: Saiba como treinar, pontuar e implantar um modelo de aprendizado de máquina usando uma interface do tipo "arrastar e soltar". Este tutorial é a parte um de uma série de duas partes sobre a previsão de preços de automóvel usando a regressão linear.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 3d0efbaec6905d0d24d9dc1c310d472958011960
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702087"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Tutorial: prever o preço do automóvel com o designer (versão prévia)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Neste tutorial de duas partes, você aprende a usar o Azure Machine Learning designer para desenvolver e implantar uma solução de análise preditiva que prevê o preço de qualquer carro. 

Na parte um, você configura seu ambiente, arrasta módulos para uma tela interativa e os conecta para criar um pipeline de Azure Machine Learning.

Na parte um do tutorial, você aprenderá a:

> [!div class="checklist"]
> * Crie um novo pipeline.
> * Importar dados.
> * Preparar dados.
> * Treinar um modelo de aprendizado de máquina.
> * Avalie um modelo de aprendizado de máquina.

Na [parte dois](tutorial-designer-automobile-price-deploy.md) do tutorial, você aprenderá a implantar seu modelo de previsão como um ponto de extremidade inferência em tempo real para prever o preço de qualquer carro com base nas especificações técnicas que você enviar. 

> [!NOTE]
>Uma versão completa deste tutorial está disponível como um pipeline de exemplo.
>
>Para encontrá-lo, vá para o designer em seu espaço de trabalho. Na seção **novo pipeline** , selecione **amostra 1-regressão: Previsão de preço de automóvel (básica)** .

## <a name="create-a-new-pipeline"></a>Criar um novo pipeline

Azure Machine Learning pipelines organizam várias etapas dependentes de aprendizado de máquina e processamento de dados em um único recurso. Os pipelines ajudam você a organizar, gerenciar e reutilizar fluxos de trabalho de aprendizado de máquina complexos entre projetos e usuários. Para criar um pipeline de Azure Machine Learning, você precisa de um espaço de trabalho do Azure Machine Learning. Nesta seção, você aprenderá a criar esses dois recursos.

### <a name="create-a-new-workspace"></a>Criar um novo espaço de trabalho

Se você tiver um espaço de trabalho Azure Machine Learning com uma Enterprise Edition, [pule para a próxima seção](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Criar o pipeline

1. Entre no [ml.Azure.com](https://ml.azure.com)e selecione o espaço de trabalho com o qual você deseja trabalhar.

1. Selecione **Designer**.

    ![Captura de tela do espaço de trabalho Visual mostrando como acessar o designer](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Selecione **módulos pré-criados fáceis de usar**.

1. Selecione o nome de pipeline padrão **pipeline-created-on** na parte superior da tela. Renomeie-o para algo significativo. Um exemplo é a *previsão de preço de automóvel*. O nome não tem de ser exclusivo.

## <a name="import-data"></a>Importar dados

Há vários exemplos de conjuntos de exemplo incluídos no designer para você experimentar. Para este tutorial, use **dados de preço do automóvel (brutos)** . 

1. À esquerda da tela do pipeline há uma paleta de conjuntos de valores e módulos. Selecione **conjuntos de valores**e, em seguida, exiba a seção **amostras** para exibir os conjuntos de exemplos de conjunto de linhas disponíveis.

1. Selecione os dados de preço do automóvel do conjunto de **dados (bruto)** e arraste-os para a tela.

   ![Arrastar dados para tela](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Ver os dados

Você pode visualizar os dados para entender o DataSet que você usará.

1. Selecione o módulo **dados de preço do automóvel (brutos)** .

1. No painel Propriedades à direita da tela, selecione **saídas**.

1. Selecione o ícone de grafo para visualizar os dados.

    ![Ver os dados](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Selecione as colunas diferentes na janela de dados para exibir informações sobre cada uma delas.

    Cada linha representa um automóvel, e as variáveis associadas a cada automóvel aparecem como colunas. Há 205 linhas e 26 colunas neste conjunto de registros.

## <a name="prepare-data"></a>Preparar dados

Os conjuntos de linhas normalmente exigem algum pré-processamento antes da análise. Talvez você tenha notado alguns valores ausentes ao inspecionar o conjunto de um. Esses valores ausentes devem ser limpos para que o modelo possa analisar os dados corretamente.

### <a name="remove-a-column"></a>Remover uma coluna

Ao treinar um modelo, você precisa fazer algo sobre os dados ausentes. Nesse conjunto de DataSet, a coluna **normalized-derrotas** não tem muitos valores, portanto, você exclui a coluna do modelo completamente.

1. Digite **Select** na caixa de pesquisa na parte superior da paleta para localizar o módulo **selecionar colunas no conjunto de DataSet** .

1. Arraste o módulo **selecionar colunas no conjunto de DataSet** para a tela. Descarte o módulo abaixo do módulo DataSet.

1. Conecte o conjunto de **dados de preço de automóvel (bruto)** ao módulo **selecionar colunas no conjunto de** dados. Arraste a partir da porta de saída do conjunto de dados, que é o pequeno círculo na parte inferior do conjunto de dados na tela, para a porta de entrada de **selecionar colunas no DataSet**, que é o pequeno círculo na parte superior do módulo.

    > [!TIP]
    > Você cria um fluxo de dados por meio de seu pipeline ao conectar a porta de saída de um módulo a uma porta de entrada de outro.
    >

    ![Módulos de conexão](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Selecione o módulo **selecionar colunas no conjunto** de módulos.

1. No painel Propriedades à direita da tela, selecione **parâmetros** > **Editar coluna**.

1. Selecione o **+** para adicionar uma nova regra.

1. No menu suspenso, selecione **excluir** e **nomes de coluna**.
    
1. Insira as *perdas normalizadas* na caixa de texto.

1. No canto inferior direito, selecione **salvar** para fechar o seletor de coluna.

    ![Excluir uma coluna](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)
        
    O painel Propriedades mostra que a coluna de **perdas normalizadas** é excluída.

1. Selecione o módulo **selecionar colunas no conjunto** de módulos. 

1. No painel Propriedades, selecione **parâmetros** > **Comentário** e insira *excluir perdas normalizadas*.

### <a name="clean-missing-data"></a>Limpar dados ausentes

O conjunto de seus conjuntos de seus valores ainda faltam depois que você remover a coluna de **perdas normalizadas** . Você pode remover os dados ausentes restantes usando o módulo **limpar dados ausentes** .

> [!TIP]
> Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos no designer.

1. Digite **limpar** na caixa de pesquisa para localizar o módulo **limpar dados ausentes** .

1. Arraste o módulo **limpar dados ausentes** para a tela do pipeline. Conecte-o ao módulo **selecionar colunas no conjunto de DataSet** . 

1. No painel Propriedades, selecione **remover linha inteira** no **modo de limpeza**.

1. Na caixa de **Comentário** do painel Propriedades, digite *remover linhas de valor ausentes*. 

    Seu pipeline agora deve ser semelhante a este:
    
    ![Selecionar coluna](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Treinar um modelo de aprendizado de máquina

Agora que os dados são processados, você pode treinar um modelo de previsão.

### <a name="select-an-algorithm"></a>Selecionar um algoritmo

*Classficação* e *regrssão* são dois tipos de algoritmos de machine learning supervisionados. A classificação prevê uma resposta de um conjunto definido de categorias, como uma cor como vermelho, azul ou verde. A regressão é utilizada para prever um número.

Como você deseja prever o preço, que é um número, você pode usar um algoritmo de regressão. Para este exemplo, você usa um modelo de regressão linear.

### <a name="split-the-data"></a>Dividir os dados

Divida seus dados em dois conjuntos separados para treinar o modelo e testá-los.

1. Insira **dividir dados** na caixa de pesquisa para localizar o módulo **dividir dados** . Conecte-o à porta à esquerda do módulo **limpar dados ausentes** .

1. Selecione o módulo **dividir dados** .

1. No painel Propriedades, defina a **fração de linhas no primeiro conjunto de registros de saída** como 0,7.

    Essa opção divide 70 por cento dos dados para treinar o modelo e 30 por cento para testá-lo.

1. Na caixa de **Comentário** do painel Propriedades, digite *dividir o conjunto de os em conjunto de treinamento (0,7) e conjunto de teste (0,3)* .

### <a name="train-the-model"></a>Formar o modelo

Treine o modelo fornecendo a ele um conjunto de dados que inclui o preço. O modelo examina os dados e procura correlações entre os recursos de um carro e seu preço para construir um modelo.

1. Para selecionar o algoritmo de aprendizado, desmarque a caixa de pesquisa paleta de módulos.

1. Expanda **algoritmos Machine Learning**.
    
    Essa opção exibe várias categorias de módulos que você pode usar para inicializar algoritmos de aprendizado.

1. Selecione **regressão** > **regressão linear**e arraste-a para a tela do pipeline.

1. Localize e arraste o módulo **modelo de treinamento** até a tela do pipeline. 

1. Conecte a saída do módulo **regressão linear** à entrada à esquerda do módulo **modelo de treinamento** .

1. Conecte a saída de dados de treinamento (porta esquerda) do módulo **dividir dados** à entrada à direita do módulo **modelo de treinamento** .

    ![Captura de tela mostrando a configuração correta do módulo modelo de treinamento. O módulo regressão linear conecta-se à porta esquerda do módulo modelo de treinamento e o módulo dividir dados se conecta à porta direita do modelo de treinamento](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Selecione o módulo **modelo de treinamento** .

1. No painel Propriedades, selecione Editar seletor de **coluna** .

1. Na caixa de diálogo **coluna de rótulo** , expanda o menu suspenso e selecione **nomes de coluna**. 

1. Na caixa de texto, insira *preço*. Price é o valor que seu modelo pretende prever.

    Seu pipeline deve ter a seguinte aparência:

    ![Captura de tela mostrando a configuração correta do pipeline depois de adicionar o módulo modelo de treinamento.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Avaliar um modelo de aprendizado de máquina

Depois de treinar seu modelo usando 70% dos dados, você pode usá-lo para pontuar os outros 30% para ver como seu modelo funciona.

1. Insira o *modelo de Pontuação* na caixa de pesquisa para localizar o módulo modelo de **Pontuação** . Arraste o módulo para a tela de pipeline. 

1. Conecte a saída do módulo **modelo de treinamento** à porta de entrada à esquerda do **modelo de Pontuação**. Conecte a saída de dados de teste (porta direita) do módulo **dividir dados** à porta de entrada à direita do **modelo de Pontuação**.

1. Digite *Evaluate* na caixa de pesquisa para localizar o módulo **modelo de avaliação** . Arraste o módulo para a tela de pipeline. 

1. Conecte a saída do módulo **modelo de Pontuação** à entrada à esquerda do **modelo de avaliação**. 

    O pipeline final deve ser semelhante a este:

    ![Captura de tela mostrando a configuração correta do pipeline.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>Executar o pipeline

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Ver resultados

Depois que a execução for concluída, você poderá exibir os resultados da execução do pipeline. 

1. Selecione o módulo **modelo de Pontuação** para exibir sua saída.

1. No painel Propriedades, selecione **saídas** > **Visualizar**.

    Aqui você pode ver os preços previstos e os preços reais dos dados de teste.

    ![Captura de tela da visualização de saída realçando a coluna de rótulo pontuado](./media/tutorial-designer-automobile-price-train-score/score-result.png)

1. Selecione o módulo **avaliar modelo** para exibir sua saída.

1. No painel Propriedades, selecione **saída** > **Visualizar**.

As seguintes estatísticas são mostradas para seu modelo:

* **Erro de média absoluta (Mae)** : a média de erros absolutos. Um erro é a diferença entre o valor previsto e o valor real.
* **Erro de quadrado médio de raiz (RMSE)** : a raiz quadrada da média de erros quadrados de previsões feitas no conjunto de testes.
* **Erro relativo absoluto**: A média dos erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado absoluto**: A média dos erros ao quadrado relativos à diferença ao quadrado entre os valores reais e a média de todos os valores reais.
* **Coeficiente de determinação**: também conhecido como o valor de R quadrado, essa métrica estatística indica o quão bem um modelo se ajusta aos dados.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. Um valor menor indica que as previsões estão mais próximas dos valores reais. Para o coeficiente de determinação, quanto mais próximo seu valor for para um (1,0), melhor será a previsão.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Na parte um deste tutorial, você concluiu as seguintes tarefas:

* Criar um pipeline
* Preparar os dados
* Formar o modelo
* Pontuar e avaliar o modelo

Na parte dois, você aprenderá a implantar seu modelo como um ponto de extremidade em tempo real.

> [!div class="nextstepaction"]
> [Continuar a implantar modelos](tutorial-designer-automobile-price-deploy.md)
