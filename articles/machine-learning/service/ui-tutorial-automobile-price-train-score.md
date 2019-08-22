---
title: 'Tutorial: Prever o preço do automóvel com a interface visual'
titleSuffix: Azure Machine Learning service
description: Saiba como treinar, pontuar e implantar um modelo de aprendizado de máquina usando uma interface visual de arrastar e soltar. Este tutorial é a parte um de uma série de duas partes sobre a previsão de preços de automóvel usando a regressão linear.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 08/16/2019
ms.openlocfilehash: a2134853c48ca09faa150f038be2d9327af75eee
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891681"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Tutorial: Prever o preço do automóvel com a interface visual

Neste tutorial de duas partes, você aprende a usar a interface visual do serviço de Azure Machine Learning para desenvolver e implantar uma solução analítica preditiva que prevê o preço de qualquer carro. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

Na parte um, você configurará seu ambiente, os conjuntos de valores de arrastar e soltar e os módulos de análise em uma tela interativa e os conectará para criar um experimento. 

Na parte um dos tutoriais, você aprenderá a:

> [!div class="checklist"]
> * Criar uma nova experimentação
> * Importar dados
> * Preparar dados
> * Treinar um modelo de aprendizado de máquina
> * Avaliar um modelo de aprendizado de máquina

Na [parte dois](ui-tutorial-automobile-price-deploy.md) do tutorial, você aprenderá a implantar seu modelo de previsão como um serviço Web do Azure para que possa usá-lo para prever o preço de qualquer carro com base nas especificações técnicas que você enviar. 

Uma versão completa deste tutorial está disponível como um experimento de exemplo.

Para encontrá-lo, na **página experimentos**, selecione **Adicionar novo**e, em **seguida, selecione o exemplo 1-regressão: Experimento de previsão de preço de automóvel** (básico).

## <a name="create-a-new-experiment"></a>Criar uma nova experimentação

Para criar um experimento de interface visual, primeiro você precisa de um espaço de trabalho de serviço de aprendizado de máquina do Azure. Nesta seção, você aprenderá a criar esses dois recursos.

### <a name="create-a-new-workspace"></a>Criar uma nova área de trabalho

Se você tiver um espaço de trabalho de serviço Azure Machine Learning, pule para a próxima seção.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-an-experiment"></a>Criar uma experimentação

1. Abra seu espaço de trabalho no [portal do Azure](https://portal.azure.com/).

1. Em seu espaço de trabalho, selecione **interface visual**. Em seguida, selecione **Iniciar interface visual**. 

    ![Captura de tela do portal do Azure mostrando como acessar a interface visual de um espaço de trabalho do serviço Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

1. Crie um novo experimento selecionando **+ novo** na parte inferior da janela da interface visual.

1. Selecione **experimento em branco**.

1. Selecione o nome de teste padrão **"experimento criado em...** " na parte superior da tela e renomeie-o para algo significativo. Por exemplo, **"Previsão de preço de automóvel"** . O nome não tem de ser exclusivo.

## <a name="import-data"></a>Importar dados

O Machine Learning depende dos dados. Felizmente, há vários exemplos de conjuntos de exemplo incluídos nessa interface disponíveis para você experimentar. Para este tutorial, use o exemplo de **dados de preço de automóvel (RAW)** . 

1. À esquerda da tela da experimentação existe uma paleta de conjuntos de dados e módulos. Selecione **DataSets salvos** e, em seguida, selecione **exemplos** para exibir os conjuntos de exemplos de conjunto de linhas disponíveis.

1. Selecione o conjunto de **dados, o preço do automóvel (bruto)** e arraste-o para a tela.

   ![Arrastar dados para tela](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

1. Selecione as colunas de dados com as quais trabalhar. Digite **Select** na caixa de pesquisa na parte superior da paleta para localizar o módulo **selecionar colunas no conjunto de DataSet** .

1. Clique e arraste o módulo **selecionar colunas no conjunto de DataSet** para a tela. Descarte o módulo abaixo do módulo DataSet.

1. Conecte o conjunto de os que você adicionou anteriormente ao módulo **selecionar colunas no conjunto de banco** de um clique e arrastando. Arraste da porta de saída do conjunto de dados, que é o pequeno círculo na parte inferior do conjunto de dados na tela, até a porta de entrada de **selecionar colunas no conjunto**de dados, que é o pequeno círculo na parte superior do módulo.

    > [!TIP]
    > Você cria um fluxo de dados por meio de seu experimento ao conectar a porta de saída de um módulo a uma porta de entrada de outro.
    >

    ![Módulos de conexão](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    O ponto de exclamação vermelho indica que você ainda não definiu as propriedades para o módulo.

1. Selecione o módulo **selecionar colunas no conjunto** de módulos.

1. No painel **Propriedades** à direita da tela, selecione **Editar colunas**.

    Na caixa de diálogo **selecionar colunas** , selecione **todas as colunas** e inclua **todos os recursos**. A caixa de diálogo deve ter a seguinte aparência:

     ![seletor de coluna](./media/ui-tutorial-automobile-price-train-score/select-all.gif)

1. No canto inferior direito, selecione **OK** para fechar o seletor de coluna.

### <a name="run-the-experiment"></a>Executar a experimentação

A qualquer momento, clique na porta de saída de um conjunto de dados ou módulo para ver a aparência dos dados nesse ponto no fluxo de dados. Se a opção **Visualizar** estiver desabilitada, primeiro você precisará executar o experimento.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Depois que o destino de computação estiver disponível, o experimento será executado. Quando a execução for concluída, uma marca de seleção verde aparecerá em cada módulo.


### <a name="visualize-the-data"></a>Ver os dados

Agora que você executou o experimento inicial, é possível visualizar os dados para entender mais sobre o conjunto que você tem.

1. Selecione a porta de saída na parte inferior das **colunas selecionar no conjunto de** um e, em seguida, selecione **Visualizar**.

1. Clique em colunas diferentes na janela dados para exibir informações sobre essa coluna.

    Nesse conjunto de linhas, cada linha representa um automóvel, e as variáveis associadas a cada automóvel aparecem como colunas. Há 205 linhas e 26 colunas neste conjunto de registros.

    Cada vez que você clica em uma coluna de dados, as informações de **estatísticas** e a imagem de **Visualização** dessa coluna aparecem à esquerda.

    [![Visualizar os dados](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)](./media/ui-tutorial-automobile-price-train-score/preview-data.gif#lightbox)

1. Clique em cada coluna para entender mais sobre seu conjunto de informações e pense se essas colunas serão úteis para prever o preço de um automóvel.

## <a name="prepare-data"></a>Preparar dados

Normalmente, um conjunto de um DataSet requer algum pré-processamento antes que possa ser analisado. Talvez você tenha notado alguns valores ausentes ao visualizar o conjunto de um. Estes valores em falta têm de ser apagados para que o modelo possa analisar os dados corretamente. Você removerá todas as linhas que têm valores ausentes. Além disso, a coluna **normalized-** losss tem uma grande proporção de valores ausentes, portanto, você excluirá completamente essa coluna do modelo.

> [!TIP]
> Apagar os valores em falta a partir dos dados de entrada é um pré-requisito para utilizar a maioria dos módulos.

### <a name="remove-column"></a>Remover coluna

Primeiro, remova completamente a coluna **normalizada-perdas** .

1. Selecione o módulo **selecionar colunas no conjunto** de módulos.

1. No painel **Propriedades** à direita da tela, selecione **Editar colunas**.

    * Deixe **com regras** e **todas as colunas** selecionadas.

    * Nas listas pendentes, selecione **Excluir** e os **nomes das colunas** e, em seguida, clique dentro da caixa de texto. Tipo **normalizado-perdas**.

    * No canto inferior direito, selecione **OK** para fechar o seletor de coluna.

    ![Excluir uma coluna](./media/ui-tutorial-automobile-price-train-score/exclude-column.gif)
        
    Agora, o painel de propriedades para selecionar colunas no conjunto de um indica que ele passará por todas as colunas do conjunto de linhas, exceto as **normalizadas-perdas**.
        
    O painel Propriedades mostra que a coluna de **perdas normalizadas** é excluída.

1. Clique duas vezes no módulo **selecionar colunas no conjunto** de módulos e digite o comentário "excluir perdas normalizadas". 
    
    Depois de digitar o comentário, clique fora do módulo. Uma seta para baixo é exibida para mostrar que o módulo contém um comentário.

1. Clique na seta para baixo para exibir o comentário.

    Agora, o módulo mostra uma seta para cima para ocultar o comentário.
        
    ![Comentários](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Apagar dados em falta

Ao treinar um modelo, você precisa fazer algo sobre os dados ausentes. Nesse caso, você adicionará um módulo para remover qualquer linha restante que tenha dados ausentes.

1. Digite **limpar** na caixa de pesquisa para localizar o módulo **limpar dados ausentes** .

1. Arraste o módulo **limpar dados ausentes** para a tela do experimento e conecte-o ao módulo **selecionar colunas no conjunto de** dados. 

1. No painel Propriedades, selecione **remover linha inteira** no **modo de limpeza**.

1. Clique duas vezes no módulo e escreva o comentário "Remover linhas de valor em falta".

    Seu experimento agora deve ser semelhante a este:
    
    ![Selecionar coluna](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="train-a-machine-learning-model"></a>Treinar um modelo de aprendizado de máquina

Agora que os dados estão prontos, você pode construir um modelo de previsão. Você usará seus dados para treinar o modelo. Em seguida, você testará o modelo para ver com que precisão ele é capaz de prever os preços.

### <a name="select-an-algorithm"></a>Selecionar um algoritmo

**Classficação** e **regrssão** são dois tipos de algoritmos de machine learning supervisionados. A **classificação** prevê uma resposta de um conjunto definido de categorias, como uma cor (vermelho, azul ou verde). A regressão é usada para prever um número.

Como você deseja prever o preço, que é um número, você pode usar um algoritmo de regressão. Para este exemplo, você usará um modelo de regressão linear.

### <a name="split-the-data"></a>Dividir os dados

Use seus dados para treinar o modelo e testá-los dividindo-os em conjuntos de dados de treinamento e de teste separados.

1. Digite **dividir dados** na caixa de pesquisa para localizar o módulo **dividir dados** e conectá-lo à porta à esquerda do módulo **limpar dados ausentes** .

1. Selecione o módulo **dividir dados** . No painel Propriedades, defina a fração de linhas no primeiro conjunto de registros de saída como 0,7. Dessa forma, usaremos 70% dos dados para treinar o modelo e manter novamente 30% para teste.

1. Clique duas vezes nos **dados divididos** e digite o comentário "dividir o conjunto em conjunto de treinamento (0,7) e o conjunto de testes (0.3)"

### <a name="train-the-model"></a>Dar formação sobre o modelo

Treine o modelo fornecendo a ele um conjunto de dados que inclui o preço. O modelo examina os dados e procura correlações entre os recursos de um carro e seu preço.

1. Para selecionar o algoritmo de aprendizado, desmarque a caixa de pesquisa paleta de módulos.

1. Expanda a **Machine Learning** em seguida, expanda **inicializar modelo**. Isto apresenta várias categorias de módulos que podem ser utilizadas para inicializar algoritmos do Machine Learning.

1. Para este experimento, selecione > regressão**linear** de regressão e arraste-a para a tela do experimento.

1. Localize e arraste o módulo **modelo de treinamento** até a tela do experimento. Conecte a saída do módulo regressão linear à entrada à esquerda do módulo modelo de treinamento e conecte a saída de dados de treinamento (porta esquerda) do módulo **dividir dados** à entrada à direita do módulo **modelo** de treinamento.

    ![Captura de tela mostrando a configuração correta do módulo modelo de treinamento. O módulo regressão linear conecta-se à porta esquerda do módulo modelo de treinamento e o módulo dividir dados se conecta à porta direita do modelo de treinamento](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Selecione o módulo **modelo de treinamento** . No painel Propriedades, selecione Iniciar seletor de coluna e digite **Price** ao lado de **incluir nomes de coluna**. Price é o valor que seu modelo pretende prever

    ![Captura de tela mostrando a configuração correta para o módulo seletor de coluna. Com regras > incluem nomes de coluna > "Price"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Seu experimento deve ter a seguinte aparência:

    ![Captura de tela mostrando a configuração correta do experimento depois de adicionar o módulo modelo de treinamento.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Avaliar um modelo de aprendizado de máquina

Agora que você treinou o modelo usando 70% dos seus dados, você pode usá-lo para pontuar os outros 30 por cento dos dados para ver como seu modelo funciona.

1. Digite **modelo de Pontuação** na caixa de pesquisa para localizar o módulo **modelo de Pontuação** e arraste o módulo para a tela do experimento. Conecte a saída do módulo **modelo de treinamento** à porta de entrada à esquerda do **modelo de Pontuação**. Conecte a saída de dados de teste (porta direita) do módulo **dividir dados** à porta de entrada à direita do **modelo de Pontuação**.

1. Digite **Evaluate** na caixa de pesquisa para localizar o **modelo de avaliação** e arraste o módulo para a tela do experimento. Conecte a saída do módulo **modelo de Pontuação** à entrada à esquerda do **modelo de avaliação**. A experimentação final deve ter este aspeto:

    ![Captura de tela mostrando a configuração final correta do experimento.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Execute o experimento usando o recurso de computação que você criou anteriormente.

1. Exiba a saída do módulo **modelo de Pontuação** selecionando a porta de saída do **modelo de Pontuação** e selecione **Visualizar**. O resultado mostra os valores previstos para os preços e os valores conhecidos dos dados do teste.

    ![Captura de tela da visualização de saída realçando a coluna "rótulo pontuado"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Para exibir a saída do módulo **avaliar modelo** , selecione a porta de saída e, em seguida, selecione **Visualizar**.

    ![Captura de tela mostrando os resultados da avaliação para o experimento final.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

As seguintes estatísticas são mostradas para seu modelo:

* **Erro de média absoluta (Mae)** : A média de erros absolutos (um erro é a diferença entre o valor previsto e o valor real).
* **Erro de quadrado médio de raiz (RMSE)** : A raiz quadrada da média de erros quadrados de previsões feitas no conjunto de testes.
* **Erro absoluto relativo**: A média de erros absolutos em relação à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro de quadrado relativo**: A média de erros quadrados em relação à diferença quadrada entre os valores reais e a média de todos os valores reais.
* **Coeficiente de determinação**: Também conhecido como o valor de R quadrado, essa é uma métrica estatística que indica o quão bem um modelo se ajusta aos dados.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. Um valor mais pequeno indica que as predições mais detalhadas correspondem aos valores reais. Para o coeficiente de determinação, quanto mais próximo seu valor for para um (1,0), melhor será a previsão.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Na parte um deste tutorial, você concluiu estas etapas:

* Criou um experimento
* Preparar os dados
* Dar formação sobre o modelo
* Pontuar e avaliar o modelo

Na parte dois, você aprenderá a implantar seu modelo como um serviço Web do Azure.

> [!div class="nextstepaction"]
> [Continuar a implantar modelos](ui-tutorial-automobile-price-deploy.md)
