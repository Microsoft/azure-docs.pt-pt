---
title: 'Tutorial: Prever o preço do automóvel com o designer'
titleSuffix: Azure Machine Learning
description: Aprenda a treinar, marcar e implementar um modelo de aprendizagem automática utilizando uma interface de arrasto e queda. Este tutorial é parte de uma série de duas partes sobre a previsão dos preços dos automóveis usando a regressão linear.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 8b471dd509dca02ab2a66d70dbc7fac9988f1afe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472242"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Tutorial: Prever o preço do automóvel com o designer (pré-visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste tutorial em duas partes, aprende-se a usar o designer de Machine Learning Azure para treinar e implementar um modelo de machine learning que preveja o preço de qualquer carro. O designer é uma ferramenta de drag-and-drop que permite criar modelos de aprendizagem automática sem uma única linha de código.

Na primeira parte do tutorial, aprenderá a:

> [!div class="checklist"]
> * Criar um novo oleoduto.
> * Dados de importação.
> * Prepare dados.
> * Treine um modelo de aprendizagem automática.
> * Avaliar um modelo de aprendizagem automática.

Na [segunda parte](tutorial-designer-automobile-price-deploy.md) do tutorial, irá implementar o seu modelo como um ponto final de inferência em tempo real para prever o preço de qualquer carro com base em especificações técnicas que o envia. 

> [!NOTE]
>Uma versão completa deste tutorial está disponível como um pipeline de amostra.
>
>Para encontrá-lo, vá ao designer no seu espaço de trabalho. Na secção **New pipeline,** selecione **Amostra 1 - Regressão: Previsão do Preço do Automóvel (Básico)**.

## <a name="create-a-new-pipeline"></a>Criar um novo oleoduto

Os oleodutos Azure Machine Learning organizam múltiplas etapas de aprendizagem automática e processamento de dados num único recurso. Os oleodutos permitem-lhe organizar, gerir e reutilizar fluxos complexos de machine learning em projetos e utilizadores.

Para criar um oleoduto azure machine learning, você precisa de um espaço de trabalho Azure Machine Learning. Nesta secção, aprende-se a criar estes dois recursos.

### <a name="create-a-new-workspace"></a>Crie uma nova área de trabalho

Para utilizar o designer, precisa primeiro de um espaço de trabalho Azure Machine Learning. O espaço de trabalho é o recurso de alto nível para o Azure Machine Learning, que fornece um lugar centralizado para trabalhar com todos os artefactos que cria em Azure Machine Learning.

Se tiver um espaço de trabalho Azure Machine Learning com uma edição enterprise, [salte para a secção seguinte.](#create-the-pipeline)

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Criar o pipeline

1. Inscreva-se na <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>e selecione o espaço de trabalho com que pretende trabalhar.

1. Selecione **Designer**.

    ![Screenshot do espaço de trabalho visual mostrando como aceder ao designer](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Selecione **módulos pré-construídos easy-to-use**.

1. Na parte superior da tela, selecione o nome de pipeline padrão **Pipeline-Created-on**. Mude o nome para previsão de *preço do Automóvel.* O nome não tem de ser exclusivo.

## <a name="set-the-default-compute-target"></a>Definir o alvo de computação padrão

Um oleoduto funciona num alvo de cálculo, que é um recurso de computação que está ligado ao seu espaço de trabalho. Depois de criar um alvo de cálculo, pode reutilizá-lo para futuras corridas.

Pode definir um alvo de **computação Padrão** para todo o pipeline, que dirá a cada módulo para usar o mesmo alvo de computação por padrão. No entanto, pode especificar os alvos da computação numa base por módulo.

1. Ao lado do nome do pipeline, selecione o **ícone** ![de engrenagem Screenshot do ícone](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) de engrenagem na parte superior da tela para abrir o painel **Definições.**

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

Existem vários conjuntos de dados de amostras incluídos no designer para que possa experimentar. Para este tutorial, utilize **os dados do preço do Automóvel (Raw)**. 

1. À esquerda da tela do oleoduto está uma paleta de conjuntos de dados e módulos. Selecione **Conjuntos**de Dados e, em seguida, veja a secção **Amostras** para visualizar os conjuntos de dados disponíveis.

1. Selecione os dados do **conjunto de dados Dados de preço do automóvel (Cru)** e arraste-os para a tela.

   ![Arrastar dados para tela](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Ver os dados

Pode visualizar os dados para compreender o conjunto de dados que irá utilizar.

1. Selecione o módulo de dados de **preços do Automóvel (Raw).**

1. No módulo os detalhes espem à direita da tela, selecione **Saídas**.

1. Selecione o ícone do gráfico para visualizar os dados.

    ![Ver os dados](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Selecione as diferentes colunas na janela de dados para visualizar informações sobre cada uma delas.

    Cada linha representa um automóvel, e as variáveis associadas a cada automóvel aparecem como colunas. Existem 205 linhas e 26 colunas neste conjunto de dados.

## <a name="prepare-data"></a>Preparar dados

Os conjuntos de dados normalmente requerem algum pré-processamento antes da análise. Pode ter notado alguns valores em falta quando inspecionou o conjunto de dados. Estes valores em falta devem ser limpos para que o modelo possa analisar corretamente os dados.

### <a name="remove-a-column"></a>Remover uma coluna

Quando se treina um modelo, tem que fazer algo sobre os dados que faltam. Neste conjunto de dados, a coluna **de perdas normalizadas** está a perder muitos valores, pelo que excluirá completamente essa coluna do modelo.

1. Na paleta de módulos à esquerda da tela, expanda a secção **de Transformação** de Dados e encontra as Colunas Select no módulo **Dataset.**

1. Arraste as **Colunas Select no** módulo Dataset para a tela. Largue o módulo abaixo do módulo dataset.

1. Ligue o conjunto de dados de **preços do Automóvel (Raw)** às Colunas Select no módulo **Dataset.** Arraste da porta de saída do conjunto de dados, que é o pequeno círculo na parte inferior do conjunto de dados da tela, para a porta de entrada de **Colunas Select em Dataset**, que é o pequeno círculo na parte superior do módulo.

    > [!TIP]
    > Cria um fluxo de dados através do seu oleoduto quando liga a porta de saída de um módulo a uma porta de entrada de outro.
    >

    ![Conecte módulos](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Selecione as Colunas Select no módulo **Dataset.**

1. No módulo os detalhes painelem à direita da tela, selecione **coluna Editar**.

1. Expanda os **nomes da Coluna** ao lado de **Incluir**, e selecione Todas **as colunas**.

1. Selecione o **+** para adicionar uma nova regra.

1. A partir dos menus suspensos, selecione **excluir** e **colunas nomes**.
    
1. Introduza *perdas normalizadas* na caixa de texto.

1. Na direita inferior, selecione **Guardar** para fechar o seletor de colunas.

    ![Excluir uma coluna](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Selecione as Colunas Select no módulo **Dataset.** 

1. No módulo os detalhes painelem à direita da tela, selecione a caixa de texto **'Comentar'** e *introduza excluir perdas normalizadas*.

    Os comentários aparecerão no gráfico para ajudá-lo a organizar o seu pipeline.

### <a name="clean-missing-data"></a>Limpar dados em falta

O seu conjunto de dados ainda tem valores em falta depois de remover a coluna **de perdas normalizadas.** Pode remover os dados restantes em falta utilizando o módulo **Clean Missing Data.**

> [!TIP]
> A limpeza dos valores em falta a partir de dados de entrada é um pré-requisito para a utilização da maioria dos módulos do designer.

1. Na paleta de módulos à esquerda da tela, expanda a secção **Data Transformation,** e encontre o módulo **Clean Missing Data.**

1. Arraste o módulo **Clean Missing Data** para a tela do gasoduto. Conecte-o às Colunas Select no módulo **Dataset.** 

1. Selecione o módulo **Clean Missing Data.**

1. No módulo os detalhes painelem à direita da tela, selecione **Editar Coluna**.

1. Nas **Colunas para serem limpas** janelas que aparecem, expanda o menu suspenso ao lado de **Incluir**. Selecione, **Todas as colunas**

1. Selecione **Guardar**

1. No módulo, os detalhes são à direita da tela, **selecione Remova a linha inteira** sob o modo de **limpeza**.

1. No módulo os detalhes painelam à direita da tela, selecionem a caixa **de comentários** e introduzam Remover as linhas de valor *em falta*. 

    O seu oleoduto deve agora ser parecido com isto:
    
    ![Select-column](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Treine um modelo de aprendizagem automática

Agora que tem os módulos no lugar para processar os dados, pode configurar os módulos de treino.

Porque quer prever o preço, que é um número, pode usar um algoritmo de regressão. Para este exemplo, usa-se um modelo linear de regressão.

### <a name="split-the-data"></a>Dividir os dados

Dividir dados é uma tarefa comum na aprendizagem automática. Dividirá os seus dados em dois conjuntos de dados separados. Um conjunto de dados irá treinar o modelo e o outro vai testar o quão bem o modelo foi executado.

1. Na paleta de módulos, expanda a secção **Data Transformation** e encontre o módulo **De dados divididos.**

1. Arraste o módulo **De dados divididos** para a tela do gasoduto.

1. Ligue a porta esquerda do módulo **Clean Missing Data** ao módulo De dados **divididos.**

    > [!IMPORTANT]
    > Certifique-se de que as portas de saída esquerda de **Dados Em Falta Limpos** ligam-se a Dados **Divididos**. A porta esquerda contém os dados limpos. A porta direita contém os dados desprotegidos.

1. Selecione o módulo **De dados split.**

1. No módulo os detalhes painelam à direita da tela, coloque a **Fração de linhas no primeiro conjunto de dados de saída** para 0,7.

    Esta opção divide 70% dos dados para treinar o modelo e 30% para testá-lo. O conjunto de dados de 70% será acessível através da porta de saída esquerda. Os restantes dados estarão disponíveis através da porta de saída certa.

1. No módulo os detalhes painelam à direita da tela, selecionem a caixa **de comentários** e introduzam dividir o conjunto de dados em conjunto de *treino (0.7) e conjunto de teste (0.3)*.

### <a name="train-the-model"></a>Dar formação sobre o modelo

Treine o modelo dando-lhe um conjunto de dados que inclui o preço. O algoritmo constrói um modelo que explica a relação entre as características e o preço apresentado pelos dados de formação.

1. Na paleta de módulos, expanda algoritmos de **aprendizagem automática.**
    
    Esta opção exibe várias categorias de módulos que pode utilizar para inicializar algoritmos de aprendizagem.

1. **Selecione Regressão** > **Linear regressão**e arraste-a para a tela do oleoduto.

1. Ligue a saída do módulo **De regressão linear** à entrada esquerda do módulo **Modelo de Comboio.**

1. Na paleta de módulos, expanda o treino do **Módulo**de secção e arraste o módulo **Modelo de Comboio** para a tela.

1. Selecione o módulo **Modelo de Comboio** e arraste-o para a tela do gasoduto.

1. Ligue a saída de dados de treino (porta esquerda) do módulo **Split Data** à entrada direita do módulo Modelo **de Comboio.**
    
    > [!IMPORTANT]
    > Certifique-se de que as portas de saída esquerda de **Dados Split** se ligam ao Modelo **de Comboio**. A porta esquerda contém o conjunto de treino. A porta direita contém o conjunto de teste.

    ![Screenshot mostrando a configuração correta do módulo Modelo de Comboio. O módulo de regressão linear liga-se à porta esquerda do módulo Modelo de Comboio e o módulo Split Data liga-se à porta direita do Modelo de Comboio](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Selecione o módulo **Modelo de Comboio.**

1. No módulo os detalhes painelem à direita da tela, selecione Seletor de **colunas Editar.**

1. Na caixa de diálogo da **coluna Label,** expanda o menu suspenso e selecione **nomes de Coluna**. 

1. Na caixa de texto, insira o *preço* para especificar o valor que o seu modelo vai prever.

    >[!IMPORTANT]
    > Certifique-se de introduzir exatamente o nome da coluna. Não capitalize **o preço.** 

    O seu oleoduto deve ser assim:

    ![Screenshot mostrando a configuração correta do gasoduto depois de adicionar o módulo Modelo de Comboio.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

### <a name="add-the-score-model-module"></a>Adicione o módulo 'Modelo de Pontuação'

Depois de treinar o seu modelo utilizando 70% dos dados, pode usá-lo para marcar os outros 30% para ver se o seu modelo funciona bem.

1. Introduza o modelo de *pontuação* na caixa de pesquisa para encontrar o módulo **'Modelo de Pontuação'.** Arraste o módulo para a tela do oleoduto. 

1. Ligue a saída do módulo **Train Model** à porta de entrada da esquerda de **Score Model**. Ligue a saída de dados de teste (porta da direita) do módulo **Split Data** à porta de entrada da direita de **Score Model**.

### <a name="add-the-evaluate-model-module"></a>Adicione o módulo Modelo de Avaliação

Utilize o módulo **'Avaliar modelo'** para avaliar a forma como o seu modelo obteve o conjunto de dados de teste.

1. Introduza *a avaliação* na caixa de pesquisa para encontrar o módulo **'Avaliar Modelo'.** Arraste o módulo para a tela do oleoduto. 

1. Ligue a saída do módulo **'Modelo de Pontuação'** à entrada esquerda do Modelo de **Avaliação**. 

    O oleoduto final deve ser parecido com isto:

    ![Screenshot mostrando a configuração correta do gasoduto.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="submit-the-pipeline"></a>Submeter o gasoduto

Agora que o seu oleoduto está todo configurado, pode submeter uma corrida de gasodutos para treinar o seu modelo de aprendizagem automática. Você pode submeter um pipeline executado em qualquer ponto enquanto constrói oleodutos no designer. Pode fazer isto para verificar o seu trabalho à medida que vai verificar se o seu oleoduto funciona como esperado.

1. Na parte superior da tela, selecione **Submeter**.

1. Na caixa de diálogo de execução do **gasoduto Configurar,** selecione **Criar nova**.

    > [!NOTE]
    > Experiências grupo gasoduto semelhante corre juntos. Se executar um pipeline várias vezes, pode selecionar a mesma experiência para execuções sucessivas.

    1. Introduza um nome descritivo para **novo nome da experiência**.

    1. Selecione **Submeter**.
    
    Pode ver o estado de execução e os detalhes na parte superior direita da tela.

### <a name="view-scored-labels"></a>Ver etiquetas pontuadas

Após a execução, pode ver os resultados da execução do gasoduto. Primeiro, veja as previsões geradas pelo modelo de regressão.

1. Selecione o módulo **'Modelo de Pontuação'** para visualizar a sua saída.

1. No módulo os detalhes painelam à direita da tela, ![selecione **Saídas** > ícone de gráfico visualizar ícone](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) para visualizar resultados.

    Aqui pode ver os preços previstos e os preços reais a partir dos dados de teste.

    ![Screenshot da visualização de saída destacando a coluna Etiqueta scored](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Avaliar modelos

Utilize o **Modelo de Avaliação** para ver se o modelo treinado foi executado no conjunto de dados do teste.

1. Selecione o módulo **'Avaliar Modelo'** para visualizar a sua saída.

1. No módulo os detalhes painelem à direita da tela, selecione **Output** > ícone ![de gráfico visualizar ícone](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) para visualizar resultados.

As seguintes estatísticas são mostradas para o seu modelo:

* **Erro Absoluto Médio (MAE)**: A média de erros absolutos. Um erro é a diferença entre o valor previsto e o valor real.
* **Erro quadrado da raiz média (RMSE)**: A raiz quadrada da média de erros quadrados das previsões efetuadas no conjunto de dados do teste.
* **Erro relativo absoluto**: A média dos erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado absoluto**: A média dos erros ao quadrado relativos à diferença ao quadrado entre os valores reais e a média de todos os valores reais.
* **Coeficiente de Determinação**: Também conhecido como o valor quadrado R, esta métrica estatística indica quão bem um modelo se encaixa nos dados.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. Um valor menor indica que as previsões estão mais próximas dos valores reais. Para o coeficiente de determinação, quanto mais próximo o seu valor for para um (1.0), melhor serão as previsões.

## <a name="clean-up-resources"></a>Limpar recursos

Ignore esta secção se quiser continuar com a parte 2 do tutorial, [implantando modelos](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Na segunda parte, aprenderás a implementar o teu modelo como um ponto final em tempo real.

> [!div class="nextstepaction"]
> [Continuar a implementar modelos](tutorial-designer-automobile-price-deploy.md)
