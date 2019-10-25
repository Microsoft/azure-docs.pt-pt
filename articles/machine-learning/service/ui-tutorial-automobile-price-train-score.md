---
title: 'Tutorial: prever o preço do automóvel com a interface visual'
titleSuffix: Azure Machine Learning
description: Saiba como treinar, pontuar e implantar um modelo de aprendizado de máquina usando uma interface visual de arrastar e soltar. Este tutorial é a parte um de uma série de duas partes sobre a previsão de preços de automóvel usando a regressão linear.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 3852531615418ffe5397295bc194de34139d6e81
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792631"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Tutorial: prever o preço do automóvel com a interface visual

Neste tutorial de duas partes, você aprende a usar a interface visual do Azure Machine Learning para desenvolver e implantar uma solução de análise preditiva que prevê o preço de qualquer carro. 

Na parte um, você configura o ambiente, os módulos do tipo "arrastar e soltar" em uma tela interativa e os conecta para criar um pipeline de Azure Machine Learning.

Na parte um dos tutoriais, você aprenderá a:

> [!div class="checklist"]
> * Criar um novo pipeline
> * Importar dados
> * Preparar dados
> * Treinar um modelo de aprendizado de máquina
> * Avaliar um modelo de aprendizado de máquina

Na [parte dois](ui-tutorial-automobile-price-deploy.md) do tutorial, você aprende a implantar seu modelo de previsão como um serviço Web do Azure para prever o preço de qualquer carro com base em especificações técnicas que você enviar. 

> [!Note]
>Uma versão completa deste tutorial está disponível como um pipeline de exemplo.
>
>Para encontrá-lo, vá para a **interface visual em seu espaço de trabalho**. Na seção **novo pipeline** , selecione **amostra 1-regressão: Previsão de preço de automóvel (básica)** .

## <a name="create-a-new-pipeline"></a>Criar um novo pipeline

Azure Machine Learning pipelines organizam várias etapas dependentes de aprendizado de máquina e processamento de dados em um único recurso. Os pipelines ajudam você a organizar, gerenciar e reutilizar fluxos de trabalho de aprendizado de máquina complexos entre projetos e usuários. Para criar um pipeline de Azure Machine Learning, você precisa de um espaço de trabalho de serviço do Azure Machine Learning. Nesta seção, você aprenderá a criar esses dois recursos.

### <a name="create-a-new-workspace"></a>Criar um novo espaço de trabalho

Se você tiver um espaço de trabalho de serviço Azure Machine Learning, pule para a próxima seção.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-the-pipeline"></a>Criar o pipeline

1. Entre no [ml.Azure.com](https://ml.azure.com) e selecione o espaço de trabalho com o qual você deseja trabalhar.

1. Selecione **interface visual**.

    ![Captura de tela do espaço de trabalho Visual mostrando como acessar a interface visual](./media/ui-tutorial-automobile-price-train-score/launch-visual-interface.png)

1. Selecione **módulos pré-criados fáceis de usar**.

1. Selecione o nome de pipeline padrão **"pipeline-created-on..."** na parte superior da tela e renomeie-o como algo significativo. Por exemplo, **"Previsão de preço de automóvel"** . O nome não tem de ser exclusivo.

## <a name="import-data"></a>Importar dados

Há vários exemplos de conjuntos de exemplo incluídos na interface visual para você experimentar. Para este tutorial, use o exemplo de **dados de preço de automóvel (RAW)** . 

1. À esquerda da tela do pipeline há uma paleta de conjuntos de valores e módulos. Selecione **conjuntos** de itens e, em seguida, exiba a seção de **exemplos** para exibir os conjuntos de valores de exemplo disponíveis.

1. Selecione o conjunto de **dados, o preço do automóvel (bruto)** e arraste-o para a tela.

   ![Arrastar dados para tela](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Ver os dados

Você pode visualizar os dados para entender o DataSet que será usado.

1. Selecione o módulo **dados de preço do automóvel (brutos)** .

1. No painel **Propriedades** à direita da tela, selecione **saídas**.

1. Selecione o ícone de grafo para visualizar os dados.

    ![Ver os dados](./media/ui-tutorial-automobile-price-train-score/visualize-data.png)

1. Selecione as colunas diferentes na janela de dados para exibir informações sobre cada uma delas.

    Cada linha representa um automóvel, e as variáveis associadas a cada automóvel aparecem como colunas. Há 205 linhas e 26 colunas neste conjunto de registros.

## <a name="prepare-data"></a>Preparar dados

Os conjuntos de linhas normalmente exigem algum pré-processamento antes da análise. Talvez você tenha notado alguns valores ausentes ao visualizar o conjunto de um. Estes valores em falta têm de ser apagados para que o modelo possa analisar os dados corretamente. Você removerá colunas com muitos valores ausentes e removerá quaisquer linhas individuais que tenham valores ausentes.

### <a name="remove-a-column"></a>Remover uma coluna

Ao treinar um modelo, você precisa fazer algo sobre os dados ausentes. Nesse conjunto de DataSet, a coluna **normalized-** losss não tem muitos valores, portanto, você excluirá a coluna do modelo completamente.

1. Selecione as colunas de dados com as quais trabalhar. Digite **Select** na caixa de pesquisa na parte superior da paleta para localizar o módulo **selecionar colunas no conjunto de DataSet** .

1. Clique e arraste o módulo **selecionar colunas no conjunto de DataSet** para a tela. Descarte o módulo abaixo do módulo DataSet.

1. Conecte o conjunto de os que você adicionou anteriormente ao módulo **selecionar colunas no conjunto de banco** de um clique e arrastando. Arraste a partir da porta de saída do conjunto de dados, que é o pequeno círculo na parte inferior do conjunto de dados na tela, para a porta de entrada de **selecionar colunas no DataSet**, que é o pequeno círculo na parte superior do módulo.

    > [!TIP]
    > Você cria um fluxo de dados por meio de seu pipeline ao conectar a porta de saída de um módulo a uma porta de entrada de outro.
    >

    ![Módulos de conexão](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Selecione o módulo **selecionar colunas no conjunto** de módulos.

1. No painel **Propriedades** à direita da tela, selecione **parâmetros**  > **Editar coluna**.

1. Selecione o **+** para adicionar uma nova regra.

1. No menu suspenso, selecione **excluir** e **nomes de coluna**.
    
1. Insira as **perdas normalizadas** na caixa de texto.

1. No canto inferior direito, selecione **salvar** para fechar o seletor de coluna.

    ![Excluir uma coluna](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    O painel Propriedades mostra que a coluna de **perdas normalizadas** é excluída.

1. Selecione o módulo **selecionar colunas no conjunto** de módulos. 

1. Nas **Propriedades**, selecione **parâmetros**  > **Comentário** e insira "excluir perdas normalizadas.".

### <a name="clean-missing-data"></a>Limpar dados ausentes

Seu conjunto de seus ainda tem valores ausentes após a remoção da coluna de **perdas normalizadas** . Você pode remover os dados ausentes restantes usando o módulo **limpar dados ausentes** .

> [!TIP]
> Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos na interface visual.

1. Digite **limpar** na caixa de pesquisa para localizar o módulo **limpar dados ausentes** .

1. Arraste o módulo **limpar dados ausentes** para a tela do pipeline e conecte-o ao módulo **selecionar colunas no conjunto de** dados. 

1. No painel Propriedades, selecione **remover linha inteira** no **modo de limpeza**.

1. No painel Propriedades, digite "remover linhas de valor ausentes". na caixa **Comentário** .  

    Seu pipeline agora deve ser semelhante a este:
    
    ![Selecionar coluna](./media/ui-tutorial-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Treinar um modelo de aprendizado de máquina

Agora que os dados são pré-processados, você pode construir um modelo de previsão. Você usará seus dados para treinar o modelo. Em seguida, você testará o modelo para ver com que precisão ele é capaz de prever os preços.

### <a name="select-an-algorithm"></a>Selecionar um algoritmo

**Classficação** e **regrssão** são dois tipos de algoritmos de machine learning supervisionados. A **classificação** prevê uma resposta de um conjunto definido de categorias, como uma cor (vermelho, azul ou verde). A **regressão** é usada para prever um número.

Como você deseja prever o preço, que é um número, você pode usar um algoritmo de regressão. Para este exemplo, você usará um modelo de regressão linear.

### <a name="split-the-data"></a>Dividir os dados

Use seus dados para treinar o modelo e testá-los dividindo-os em dois conjuntos de dados separados.

1. Digite **dividir dados** na caixa de pesquisa para localizar o módulo **dividir dados** e conectá-lo à porta à esquerda do módulo **limpar dados ausentes** .

1. Selecione o módulo **dividir dados** .

1. No painel Propriedades, defina a **fração de linhas no primeiro conjunto de registros de saída** como 0,7.

    Isso divide a 70 por cento dos dados para treinar o modelo e mantém 30% de volta para teste.

1. No painel Propriedades, digite "dividir o conjunto de os em conjunto de treinamento (0,7) e conjunto de testes (0.3)". na caixa **Comentário** .

### <a name="train-the-model"></a>Formar o modelo

Treine o modelo fornecendo a ele um conjunto de dados que inclui o preço. O modelo examina os dados e procura correlações entre os recursos de um carro e seu preço.

1. Para selecionar o algoritmo de aprendizado, desmarque a caixa de pesquisa paleta de módulos.

1. Expanda **algoritmos Machine Learning**.
    
    Isto apresenta várias categorias de módulos que podem ser utilizadas para inicializar algoritmos do Machine Learning.

1. Para esse pipeline, selecione **regressão**  > **regressão linear** e arraste-a para a tela do pipeline.

1. Localize e arraste o módulo **modelo de treinamento** até a tela do pipeline. 

1. Conecte a saída do módulo regressão linear à entrada à esquerda do módulo modelo de treinamento.

1. Conecte a saída de dados de treinamento (porta esquerda) do módulo **dividir dados** à entrada à direita do módulo **modelo de treinamento** .

    ![Captura de tela mostrando a configuração correta do módulo modelo de treinamento. O módulo regressão linear conecta-se à porta esquerda do módulo modelo de treinamento e o módulo dividir dados se conecta à porta direita do modelo de treinamento](./media/ui-tutorial-automobile-price-train-score/pipeline-train-model.png)

1. Selecione o módulo **modelo de treinamento** .

1. No painel Propriedades, selecione Editar seletor de **coluna** .

1. Na caixa de diálogo **coluna de rótulo** , expanda o menu suspenso e selecione **nomes de coluna**. Na caixa de texto, insira **preço**. Price é o valor que seu modelo pretende prever.

    Seu pipeline deve ter a seguinte aparência:

    ![Captura de tela mostrando a configuração correta do pipeline depois de adicionar o módulo modelo de treinamento.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Avaliar um modelo de aprendizado de máquina

Agora que você treinou o modelo usando 70% dos seus dados, você pode usá-lo para pontuar os outros 30 por cento dos dados para ver como seu modelo funciona.

1. Digite **modelo de Pontuação** na caixa de pesquisa para localizar o módulo **modelo de Pontuação** e arraste o módulo para a tela de pipeline. 

1. Conecte a saída do módulo **modelo de treinamento** à porta de entrada à esquerda do **modelo de Pontuação**. Conecte a saída de dados de teste (porta direita) do módulo **dividir dados** à porta de entrada à direita do **modelo de Pontuação**.

1. Digite **Evaluate** na caixa de pesquisa para localizar o **modelo de avaliação** e arraste o módulo para a tela de pipeline. 

1. Conecte a saída do módulo **modelo de Pontuação** à entrada à esquerda do **modelo de avaliação**. 

    O pipeline final deve ser semelhante a este:

    ![Captura de tela mostrando a configuração correta do pipeline.](./media/ui-tutorial-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>Executar o pipeline

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Ver resultados

Depois que a execução for concluída, você poderá exibir os resultados da execução do pipeline. 

1. Exiba a saída do módulo **modelo de Pontuação** selecionando o módulo **modelo de Pontuação** .

1. No painel **Propriedades** , selecione **saídas**  > **Visualizar**. O resultado mostra os valores previstos para os preços e os valores conhecidos dos dados do teste.

    ![Captura de tela da visualização de saída realçando a coluna "rótulo pontuado"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Exiba a saída do módulo **modelo de avaliação** selecionando o módulo **modelo de Pontuação** .

1. No painel **Propriedades** , selecione **saída**  > **Visualizar**e, em seguida, selecione **Visualizar**.

As seguintes estatísticas são mostradas para seu modelo:

* **Erro de média absoluta (Mae)** : a média de erros absolutos (um erro é a diferença entre o valor previsto e o valor real).
* **Erro de quadrado médio de raiz (RMSE)** : a raiz quadrada da média de erros quadrados de previsões feitas no conjunto de testes.
* **Erro relativo absoluto**: A média dos erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado absoluto**: A média dos erros ao quadrado relativos à diferença ao quadrado entre os valores reais e a média de todos os valores reais.
* **Coeficiente de determinação**: também conhecido como o valor de R quadrado, essa é uma métrica estatística que indica o quão bem um modelo se ajusta aos dados.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. Um valor mais pequeno indica que as predições mais detalhadas correspondem aos valores reais. Para o coeficiente de determinação, quanto mais próximo seu valor for para um (1,0), melhor será a previsão.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Na parte um deste tutorial, você concluiu estas etapas:

* Criou um pipeline
* Preparar os dados
* Treinado o modelo
* Pontuado e avaliado o modelo

Na parte dois, você aprenderá a implantar seu modelo como um ponto de extremidade em tempo real.

> [!div class="nextstepaction"]
> [Continuar a implantar modelos](ui-tutorial-automobile-price-deploy.md)
