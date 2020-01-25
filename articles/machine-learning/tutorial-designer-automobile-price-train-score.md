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
ms.openlocfilehash: 639a61cddde27b0d989e5a3dd4c599c353182a73
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720190"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Tutorial: prever o preço do automóvel com o designer (versão prévia)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste tutorial de duas partes, você aprende a usar o Azure Machine Learning designer para desenvolver e implantar uma solução de análise preditiva que prevê o preço de qualquer carro.

Na parte um do tutorial, você aprenderá a:

> [!div class="checklist"]
> * Crie um novo pipeline.
> * Importar dados.
> * Preparar dados.
> * Treinar um modelo de aprendizado de máquina.
> * Avalie um modelo de aprendizado de máquina.

Na [segunda parte](tutorial-designer-automobile-price-deploy.md) do tutorial, irá implementar o seu modelo como um ponto final de inferência em tempo real para prever o preço de qualquer carro com base em especificações técnicas que o envia. 

> [!NOTE]
>Uma versão completa deste tutorial está disponível como um pipeline de exemplo.
>
>Para encontrá-lo, vá para o designer em seu espaço de trabalho. Na seção **novo pipeline** , selecione **amostra 1-regressão: Previsão de preço de automóvel (básica)** .

## <a name="create-a-new-pipeline"></a>Criar um novo pipeline

Os oleodutos Azure Machine Learning organizam múltiplas etapas de aprendizagem automática e processamento de dados num único recurso. Os oleodutos permitem-lhe organizar, gerir e reutilizar fluxos complexos de machine learning em projetos e utilizadores.

Para criar um pipeline de Azure Machine Learning, você precisa de um espaço de trabalho do Azure Machine Learning. Nesta seção, você aprenderá a criar esses dois recursos.

### <a name="create-a-new-workspace"></a>Criar uma nova área de trabalho

Se você tiver um espaço de trabalho Azure Machine Learning com uma Enterprise Edition, [pule para a próxima seção](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Criar o pipeline

1. Entre no [ml.Azure.com](https://ml.azure.com)e selecione o espaço de trabalho com o qual você deseja trabalhar.

1. Selecione **Designer**.

    ![Captura de tela do espaço de trabalho Visual mostrando como acessar o designer](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Selecione **módulos pré-criados fáceis de usar**.

1. Na parte superior da tela, selecione o nome de pipeline padrão **Pipeline-Created-on**. Mude o nome para previsão de *preço do Automóvel.* O nome não tem de ser exclusivo.

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

1. Nas propriedades espumrem à direita da tela, selecione **Todas as colunas**.

1. Selecione o **+** para adicionar uma nova regra.

1. No menu suspenso, selecione **excluir** e **nomes de coluna**.
    
1. Insira as *perdas normalizadas* na caixa de texto.

1. No canto inferior direito, selecione **salvar** para fechar o seletor de coluna.

    ![Excluir uma coluna](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Selecione o módulo **selecionar colunas no conjunto** de módulos. 

1. No painel de propriedades, selecione a caixa de texto **Comentar** e *introduza Excluir perdas normalizadas*.

    Os comentários aparecerão no gráfico para ajudá-lo a organizar o seu pipeline.

### <a name="clean-missing-data"></a>Apagar dados em falta

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

Agora que tem os módulos no lugar para processar os dados, pode configurar os módulos de treino.

Como você deseja prever o preço, que é um número, você pode usar um algoritmo de regressão. Para este exemplo, você usa um modelo de regressão linear.

### <a name="split-the-data"></a>Divida os dados

Dividir dados é uma tarefa comum na aprendizagem automática. Dividirá os seus dados em dois conjuntos de dados separados. Um conjunto de dados irá treinar o modelo e o outro vai testar o quão bem o modelo foi executado.

1. Insira **dividir dados** na caixa de pesquisa para localizar o módulo **dividir dados** . Ligue a porta esquerda do módulo **Clean Missing Data** ao módulo De dados **divididos.**

    > [!IMPORTANT]
    > Certifique-se de que as portas de saída esquerda de **Dados Em Falta Limpos** ligam-se a Dados **Divididos**. A porta esquerda contém os dados limpos. A porta direita contém os dados desprotegidos.

1. Selecione o módulo **dividir dados** .

1. No painel Propriedades, defina a **fração de linhas no primeiro conjunto de registros de saída** como 0,7.

    Essa opção divide 70 por cento dos dados para treinar o modelo e 30 por cento para testá-lo. O conjunto de dados de 70% será acessível através da porta de saída esquerda. Os restantes dados estarão disponíveis através da porta de saída certa.

1. Na caixa de **Comentário** do painel Propriedades, digite *dividir o conjunto de os em conjunto de treinamento (0,7) e conjunto de teste (0,3)* .

### <a name="train-the-model"></a>Formar o modelo

Treine o modelo dando-lhe um conjunto de dados que inclui o preço. O algoritmo constrói um modelo que explica a relação entre as características e o preço apresentado pelos dados de formação.

1. Para selecionar o algoritmo de aprendizado, desmarque a caixa de pesquisa paleta de módulos.

1. Expanda **algoritmos Machine Learning**.
    
    Essa opção exibe várias categorias de módulos que você pode usar para inicializar algoritmos de aprendizado.

1. Selecione **regressão** > **regressão linear**e arraste-a para a tela do pipeline.

1. Localize e arraste o módulo **modelo de treinamento** até a tela do pipeline. 

1. Conecte a saída do módulo **regressão linear** à entrada à esquerda do módulo **modelo de treinamento** .

1. Conecte a saída de dados de treinamento (porta esquerda) do módulo **dividir dados** à entrada à direita do módulo **modelo de treinamento** .
    
    > [!IMPORTANT]
    > Certifique-se de que as portas de saída esquerda de **Dados Split** se ligam ao Modelo **de Comboio**. A porta esquerda contém o conjunto de treino. A porta direita contém o conjunto de teste.

    ![Captura de tela mostrando a configuração correta do módulo modelo de treinamento. O módulo regressão linear conecta-se à porta esquerda do módulo modelo de treinamento e o módulo dividir dados se conecta à porta direita do modelo de treinamento](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Selecione o módulo **modelo de treinamento** .

1. No painel Propriedades, selecione Editar seletor de **coluna** .

1. Na caixa de diálogo **coluna de rótulo** , expanda o menu suspenso e selecione **nomes de coluna**. 

1. Na caixa de texto, insira o *preço* para especificar o valor que o seu modelo vai prever.

    Seu pipeline deve ter a seguinte aparência:

    ![Captura de tela mostrando a configuração correta do pipeline depois de adicionar o módulo modelo de treinamento.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

## <a name="score-a-machine-learning-model"></a>Marque um modelo de aprendizagem automática

Depois de treinar seu modelo usando 70% dos dados, você pode usá-lo para pontuar os outros 30% para ver como seu modelo funciona.

1. Insira o *modelo de Pontuação* na caixa de pesquisa para localizar o módulo modelo de **Pontuação** . Arraste o módulo para a tela de pipeline. 

1. Conecte a saída do módulo **modelo de treinamento** à porta de entrada à esquerda do **modelo de Pontuação**. Conecte a saída de dados de teste (porta direita) do módulo **dividir dados** à porta de entrada à direita do **modelo de Pontuação**.

## <a name="evaluate-a-machine-learning-model"></a>Avaliar um modelo de aprendizado de máquina

Utilize o módulo **'Avaliar modelo'** para avaliar a forma como o seu modelo obteve o conjunto de dados de teste.

1. Digite *Evaluate* na caixa de pesquisa para localizar o módulo **modelo de avaliação** . Arraste o módulo para a tela de pipeline. 

1. Conecte a saída do módulo **modelo de Pontuação** à entrada à esquerda do **modelo de avaliação**. 

    O pipeline final deve ser semelhante a este:

    ![Captura de tela mostrando a configuração correta do pipeline.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="run-the-pipeline"></a>Executar o pipeline

[!INCLUDE [aml-ui-create-training-compute](../../includes/aml-ui-create-training-compute.md)]

### <a name="view-scored-labels"></a>Ver etiquetas pontuadas

Depois que a execução for concluída, você poderá exibir os resultados da execução do pipeline. Primeiro, veja as previsões geradas pelo modelo de regressão.

1. Selecione o módulo **modelo de Pontuação** para exibir sua saída.

1. No painel de propriedades, selecione **Outputs** > ícone de gráfico ![visualizar o ícone](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) para visualizar resultados.

    Aqui você pode ver os preços previstos e os preços reais dos dados de teste.

    ![Captura de tela da visualização de saída realçando a coluna de rótulo pontuado](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Avaliar modelos

Utilize o **Modelo de Avaliação** para ver se o modelo treinado foi executado no conjunto de dados do teste.

1. Selecione o módulo **avaliar modelo** para exibir sua saída.

1. No painel de propriedades, selecione **Output** > ícone de gráfico ![visualizar o ícone](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) para visualizar resultados.

As seguintes estatísticas são mostradas para seu modelo:

* **Erro de média absoluta (Mae)** : a média de erros absolutos. Um erro é a diferença entre o valor previsto e o valor real.
* **Erro de quadrado médio de raiz (RMSE)** : a raiz quadrada da média de erros quadrados de previsões feitas no conjunto de testes.
* **Erro relativo absoluto**: A média dos erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado absoluto**: A média dos erros ao quadrado relativos à diferença ao quadrado entre os valores reais e a média de todos os valores reais.
* **Coeficiente de determinação**: também conhecido como o valor de R quadrado, essa métrica estatística indica o quão bem um modelo se ajusta aos dados.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. Um valor menor indica que as previsões estão mais próximas dos valores reais. Para o coeficiente de determinação, quanto mais próximo seu valor for para um (1,0), melhor será a previsão.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Na parte um deste tutorial, você concluiu as seguintes tarefas:

* Criar um pipeline
* Preparar os dados
* Formar o modelo
* Pontuar e avaliar o modelo

Na parte dois, você aprenderá a implantar seu modelo como um ponto de extremidade em tempo real.

> [!div class="nextstepaction"]
> [Continuar a implantar modelos](tutorial-designer-automobile-price-deploy.md)
