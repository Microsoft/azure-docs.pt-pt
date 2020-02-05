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
ms.date: 01/30/2020
ms.openlocfilehash: de9ed700363bd6578ac49f0add0c48dc33356692
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982624"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Tutorial: prever o preço do automóvel com o designer (versão prévia)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste tutorial em duas partes, aprende-se a usar o designer de Machine Learning Azure para treinar e implementar um modelo de machine learning que preveja o preço de qualquer carro. O designer é uma ferramenta de drag-and-drop que permite criar modelos de aprendizagem automática sem uma única linha de código.

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

Para utilizar o designer, precisa primeiro de um espaço de trabalho Azure Machine Learning. O espaço de trabalho é o recurso de alto nível para o Azure Machine Learning, que fornece um lugar centralizado para trabalhar com todos os artefactos que cria em Azure Machine Learning.

Se você tiver um espaço de trabalho Azure Machine Learning com uma Enterprise Edition, [pule para a próxima seção](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Criar o pipeline

1. Entre no <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>e selecione o espaço de trabalho com o qual você deseja trabalhar.

1. Selecione **Designer**.

    ![Captura de tela do espaço de trabalho Visual mostrando como acessar o designer](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Selecione **módulos pré-criados fáceis de usar**.

1. Na parte superior da tela, selecione o nome de pipeline padrão **Pipeline-Created-on**. Mude o nome para previsão de *preço do Automóvel.* O nome não tem de ser exclusivo.

## <a name="set-the-default-compute-target"></a>Definir o alvo de computação padrão

Um oleoduto funciona num alvo de cálculo, que é um recurso de computação que está ligado ao seu espaço de trabalho. Depois de criar um alvo de cálculo, pode reutilizá-lo para futuras corridas.

Pode definir um alvo de **computação Padrão** para todo o pipeline, que dirá a cada módulo para usar o mesmo alvo de computação por padrão. No entanto, pode especificar os alvos da computação numa base por módulo.

1. Ao lado do nome do pipeline, selecione o **ícone de Engrenagem** ![Screenshot do ícone de engrenagem](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) na parte superior da tela para abrir o painel **Definições.**

1. No painel **Definições** à direita da tela, **selecione Selecione o alvo computacional**.

    Se já tem um alvo de cálculo disponível, pode selecioná-lo para executar este pipeline.

    > [!NOTE]
    > O designer só pode fazer experiências com alvos da Computação de Machine Learning Azure. Outros alvos de computação não serão mostrados.

1. Introduza um nome para o recurso computacional.

1. Selecione **Guardar**.

    > [!NOTE]
    > Leva aproximadamente cinco minutos para criar um recurso de computação. Depois de criado o recurso, pode reutilizá-lo e saltar este tempo de espera para futuras corridas.
    >
    > O recurso computacional automaticamente escala para zero nódosos quando está inativo para economizar custos. Quando o voltar a usar após um atraso, poderá experimentar aproximadamente cinco minutos de tempo de espera enquanto volta a subir.

## <a name="import-data"></a>Importar dados

Há vários exemplos de conjuntos de exemplo incluídos no designer para você experimentar. Para este tutorial, use **dados de preço do automóvel (brutos)** . 

1. À esquerda da tela do pipeline há uma paleta de conjuntos de valores e módulos. Selecione **conjuntos de valores**e, em seguida, exiba a seção **amostras** para exibir os conjuntos de exemplos de conjunto de linhas disponíveis.

1. Selecione os dados de preço do automóvel do conjunto de **dados (bruto)** e arraste-os para a tela.

   ![Arrastar dados para tela](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Ver os dados

Você pode visualizar os dados para entender o DataSet que você usará.

1. Selecione o módulo **dados de preço do automóvel (brutos)** .

1. No módulo os detalhes espem à direita da tela, selecione **Saídas**.

1. Selecione o ícone de grafo para visualizar os dados.

    ![Ver os dados](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Selecione as colunas diferentes na janela de dados para exibir informações sobre cada uma delas.

    Cada linha representa um automóvel, e as variáveis associadas a cada automóvel aparecem como colunas. Há 205 linhas e 26 colunas neste conjunto de registros.

## <a name="prepare-data"></a>Preparar dados

Os conjuntos de linhas normalmente exigem algum pré-processamento antes da análise. Talvez você tenha notado alguns valores ausentes ao inspecionar o conjunto de um. Esses valores ausentes devem ser limpos para que o modelo possa analisar os dados corretamente.

### <a name="remove-a-column"></a>Remover uma coluna

Ao treinar um modelo, você precisa fazer algo sobre os dados ausentes. Neste conjunto de dados, a coluna **de perdas normalizadas** está a perder muitos valores, pelo que excluirá completamente essa coluna do modelo.

1. Na paleta de módulos à esquerda da tela, expanda a secção **de Transformação** de Dados e encontra as Colunas Select no módulo **Dataset.**

1. Arraste o módulo **selecionar colunas no conjunto de DataSet** para a tela. Descarte o módulo abaixo do módulo DataSet.

1. Conecte o conjunto de **dados de preço de automóvel (bruto)** ao módulo **selecionar colunas no conjunto de** dados. Arraste a partir da porta de saída do conjunto de dados, que é o pequeno círculo na parte inferior do conjunto de dados na tela, para a porta de entrada de **selecionar colunas no DataSet**, que é o pequeno círculo na parte superior do módulo.

    > [!TIP]
    > Você cria um fluxo de dados por meio de seu pipeline ao conectar a porta de saída de um módulo a uma porta de entrada de outro.
    >

    ![Módulos de conexão](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Selecione o módulo **selecionar colunas no conjunto** de módulos.

1. No módulo os detalhes painelem à direita da tela, selecione **coluna Editar**.

1. Expanda os **nomes da Coluna** ao lado de **Incluir**, e selecione Todas **as colunas**.

1. Selecione o **+** para adicionar uma nova regra.

1. A partir dos menus suspensos, selecione **excluir** e **colunas nomes**.
    
1. Insira as *perdas normalizadas* na caixa de texto.

1. No canto inferior direito, selecione **salvar** para fechar o seletor de coluna.

    ![Excluir uma coluna](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Selecione o módulo **selecionar colunas no conjunto** de módulos. 

1. No módulo os detalhes painelem à direita da tela, selecione a caixa de texto **'Comentar'** e *introduza excluir perdas normalizadas*.

    Os comentários aparecerão no gráfico para ajudá-lo a organizar o seu pipeline.

### <a name="clean-missing-data"></a>Apagar dados em falta

O conjunto de seus conjuntos de seus valores ainda faltam depois que você remover a coluna de **perdas normalizadas** . Você pode remover os dados ausentes restantes usando o módulo **limpar dados ausentes** .

> [!TIP]
> Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos no designer.

1. Na paleta de módulos à esquerda da tela, expanda a secção **Data Transformation,** e encontre o módulo **Clean Missing Data.**

1. Arraste o módulo **limpar dados ausentes** para a tela do pipeline. Conecte-o ao módulo **selecionar colunas no conjunto de DataSet** . 

1. Selecione o módulo **Clean Missing Data.**

1. No módulo, os detalhes são à direita da tela, **selecione Remova a linha inteira** sob o modo de **limpeza**.

1. No módulo os detalhes painelam à direita da tela, selecionem a caixa **de comentários** e introduzam Remover as linhas de valor *em falta*. 

    Seu pipeline agora deve ser semelhante a este:
    
    ![Selecionar coluna](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Treinar um modelo de aprendizado de máquina

Agora que tem os módulos no lugar para processar os dados, pode configurar os módulos de treino.

Como você deseja prever o preço, que é um número, você pode usar um algoritmo de regressão. Para este exemplo, você usa um modelo de regressão linear.

### <a name="split-the-data"></a>Divida os dados

Dividir dados é uma tarefa comum na aprendizagem automática. Dividirá os seus dados em dois conjuntos de dados separados. Um conjunto de dados irá treinar o modelo e o outro vai testar o quão bem o modelo foi executado.

1. Na paleta de módulos, expanda a secção **Data Transformation** e encontre o módulo **De dados divididos.**

1. Arraste o módulo **De dados divididos** para a tela do gasoduto.

1. Ligue a porta esquerda do módulo **Clean Missing Data** ao módulo De dados **divididos.**

    > [!IMPORTANT]
    > Certifique-se de que as portas de saída esquerda de **Dados Em Falta Limpos** ligam-se a Dados **Divididos**. A porta esquerda contém os dados limpos. A porta direita contém os dados desprotegidos.

1. Selecione o módulo **dividir dados** .

1. No módulo os detalhes painelam à direita da tela, coloque a **Fração de linhas no primeiro conjunto de dados de saída** para 0,7.

    Essa opção divide 70 por cento dos dados para treinar o modelo e 30 por cento para testá-lo. O conjunto de dados de 70% será acessível através da porta de saída esquerda. Os restantes dados estarão disponíveis através da porta de saída certa.

1. No módulo os detalhes painelam à direita da tela, selecionem a caixa **de comentários** e introduzam dividir o conjunto de dados em conjunto de *treino (0.7) e conjunto de teste (0.3)* .

### <a name="train-the-model"></a>Formar o modelo

Treine o modelo dando-lhe um conjunto de dados que inclui o preço. O algoritmo constrói um modelo que explica a relação entre as características e o preço apresentado pelos dados de formação.

1. Na paleta de módulos, expanda algoritmos de **aprendizagem automática.**
    
    Essa opção exibe várias categorias de módulos que você pode usar para inicializar algoritmos de aprendizado.

1. Selecione **regressão** > **regressão linear**e arraste-a para a tela do pipeline.

1. Localize e arraste o módulo **modelo de treinamento** até a tela do pipeline. 

1. Conecte a saída do módulo **regressão linear** à entrada à esquerda do módulo **modelo de treinamento** .

1. Conecte a saída de dados de treinamento (porta esquerda) do módulo **dividir dados** à entrada à direita do módulo **modelo de treinamento** .
    
    > [!IMPORTANT]
    > Certifique-se de que as portas de saída esquerda de **Dados Split** se ligam ao Modelo **de Comboio**. A porta esquerda contém o conjunto de treino. A porta direita contém o conjunto de teste.

    ![Captura de tela mostrando a configuração correta do módulo modelo de treinamento. O módulo regressão linear conecta-se à porta esquerda do módulo modelo de treinamento e o módulo dividir dados se conecta à porta direita do modelo de treinamento](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Na paleta de módulos, expanda o treino do **Módulo**de secção e arraste o módulo **Modelo de Comboio** para a tela.

1. Selecione o módulo **modelo de treinamento** .

1. No módulo os detalhes painelem à direita da tela, selecione Seletor de **colunas Editar.**

1. Na caixa de diálogo **coluna de rótulo** , expanda o menu suspenso e selecione **nomes de coluna**. 

1. Na caixa de texto, insira o *preço* para especificar o valor que o seu modelo vai prever.

    Seu pipeline deve ter a seguinte aparência:

    ![Captura de tela mostrando a configuração correta do pipeline depois de adicionar o módulo modelo de treinamento.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

### <a name="add-the-score-model-module"></a>Adicione o módulo 'Modelo de Pontuação'

Depois de treinar seu modelo usando 70% dos dados, você pode usá-lo para pontuar os outros 30% para ver como seu modelo funciona.

1. Insira o *modelo de Pontuação* na caixa de pesquisa para localizar o módulo modelo de **Pontuação** . Arraste o módulo para a tela de pipeline. 

1. Conecte a saída do módulo **modelo de treinamento** à porta de entrada à esquerda do **modelo de Pontuação**. Conecte a saída de dados de teste (porta direita) do módulo **dividir dados** à porta de entrada à direita do **modelo de Pontuação**.

### <a name="add-the-evaluate-model-module"></a>Adicionar o módulo modelo de avaliação

Utilize o módulo **'Avaliar modelo'** para avaliar a forma como o seu modelo obteve o conjunto de dados de teste.

1. Digite *Evaluate* na caixa de pesquisa para localizar o módulo **modelo de avaliação** . Arraste o módulo para a tela de pipeline. 

1. Conecte a saída do módulo **modelo de Pontuação** à entrada à esquerda do **modelo de avaliação**. 

    O pipeline final deve ser semelhante a este:

    ![Captura de tela mostrando a configuração correta do pipeline.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="run-the-pipeline"></a>Executar o pipeline

Agora que o seu oleoduto está pronto, pode submeter uma corrida de gasodutos.

1. Na parte superior da tela, selecione **Executar**.

1. Na caixa de diálogo de execução do **gasoduto Configurar, selecione** **+ Nova experiência** para a **Experiência**.

    > [!NOTE]
    > Experiências grupo gasoduto semelhante corre juntos. Se executar um pipeline várias vezes, pode selecionar a mesma experiência para execuções sucessivas.

    1. Introduza um nome descritivo para **Nome da Experiência**.

    1. Selecione **Executar**.
    
    Pode ver o estado de execução e os detalhes na parte superior direita da tela.

### <a name="view-scored-labels"></a>Ver etiquetas pontuadas

Depois que a execução for concluída, você poderá exibir os resultados da execução do pipeline. Primeiro, veja as previsões geradas pelo modelo de regressão.

1. Selecione o módulo **modelo de Pontuação** para exibir sua saída.

1. No módulo os detalhes painelam à direita da tela, selecione **Outputs** > ícone de gráfico ![visualizar ícone](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) para visualizar resultados.

    Aqui você pode ver os preços previstos e os preços reais dos dados de teste.

    ![Captura de tela da visualização de saída realçando a coluna de rótulo pontuado](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Avaliar modelos

Utilize o **Modelo de Avaliação** para ver se o modelo treinado foi executado no conjunto de dados do teste.

1. Selecione o módulo **avaliar modelo** para exibir sua saída.

1. No módulo os detalhes painelam à direita da tela, selecione **Output** > ícone de gráfico ![visualizar ícone](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) para visualizar resultados.

As seguintes estatísticas são mostradas para seu modelo:

* **Erro de média absoluta (Mae)** : a média de erros absolutos. Um erro é a diferença entre o valor previsto e o valor real.
* **Erro de quadrado médio de raiz (RMSE)** : a raiz quadrada da média de erros quadrados de previsões feitas no conjunto de testes.
* **Erro relativo absoluto**: A média dos erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado absoluto**: A média dos erros ao quadrado relativos à diferença ao quadrado entre os valores reais e a média de todos os valores reais.
* **Coeficiente de determinação**: também conhecido como o valor de R quadrado, essa métrica estatística indica o quão bem um modelo se ajusta aos dados.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. Um valor menor indica que as previsões estão mais próximas dos valores reais. Para o coeficiente de determinação, quanto mais próximo seu valor for para um (1,0), melhor será a previsão.

## <a name="clean-up-resources"></a>Limpar recursos

Ignore esta secção se quiser continuar com a parte 2 do tutorial, [implantando modelos](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Na parte dois, você aprenderá a implantar seu modelo como um ponto de extremidade em tempo real.

> [!div class="nextstepaction"]
> [Continuar a implantar modelos](tutorial-designer-automobile-price-deploy.md)
