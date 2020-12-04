---
title: 'Tutorial: Prever preços dos carros com o designer'
titleSuffix: Azure Machine Learning
description: Modelo de aprendizagem de máquinas de comboio para prever os preços do automóvel usando regressão linear. Este tutorial é a primeira parte de uma série composta por duas partes.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 09/28/2020
ms.custom: designer
ms.openlocfilehash: ca812fc7548e3c70f1faa1e1ed6a34afda3872af
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575980"
---
# <a name="tutorial-predict-automobile-price-with-the-designer"></a>Tutorial: Prever o preço de um automóvel com o estruturador


Neste tutorial de duas partes, aprende-se a usar o designer Azure Machine Learning para treinar e implementar um modelo de machine learning que prevê o preço de qualquer carro. O designer é uma ferramenta de arrastar e largar que permite criar modelos de machine learning sem uma única linha de código.

Na primeira parte do tutorial, vais aprender a:

> [!div class="checklist"]
> * Criar um novo oleoduto.
> * Dados de importação.
> * Preparar dados.
> * Treine um modelo de aprendizagem automática.
> * Avaliar um modelo de aprendizagem automática.

Na [segunda parte](tutorial-designer-automobile-price-deploy.md) do tutorial, irá implementar o seu modelo como um ponto final inferencing em tempo real para prever o preço de qualquer carro com base nas especificações técnicas que o envia. 

> [!NOTE]
>Uma versão completa deste tutorial está disponível como um pipeline de amostra.
>
>Para encontrá-lo, vá ao designer no seu espaço de trabalho. Na secção **Novo pipeline,** selecione **Sample 1 - Regression: Automobile Price Prediction (Basic)**.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-new-pipeline"></a>Criar um novo oleoduto

Os oleodutos Azure Machine Learning organizam múltiplas etapas de aprendizagem automática e processamento de dados num único recurso. Os oleodutos permitem organizar, gerir e reutilizar fluxos complexos de aprendizagem automática em projetos e utilizadores.

Para criar um oleoduto Azure Machine Learning, precisa de um espaço de trabalho para aprendizagem automática Azure. Nesta secção, aprende-se a criar estes dois recursos.

### <a name="create-a-new-workspace"></a>Criar uma nova área de trabalho

Precisa de um espaço de trabalho para aprender máquinas Azure para usar o designer. O espaço de trabalho é o recurso de alto nível para a Azure Machine Learning, fornece um lugar centralizado para trabalhar com todos os artefactos que você cria em Azure Machine Learning. Para obter instruções sobre a criação de um espaço de trabalho, consulte [Criar e gerir espaços de trabalho de Aprendizagem automática Azure.](how-to-manage-workspace.md)

> [!NOTE]
> Se o seu espaço de trabalho utilizar uma rede Virtual, existem passos de configuração adicionais que deve utilizar para utilizar o designer. Para mais informações, consulte [o estúdio Use Azure Machine Learning numa rede virtual Azure](how-to-enable-studio-virtual-network.md)

### <a name="create-the-pipeline"></a>Criar o pipeline

1. Inscreva-se para <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>e selecione o espaço de trabalho com o quais pretende trabalhar.

1. Selecione **Designer**.

    ![Screenshot do espaço de trabalho visual mostrando como aceder ao designer](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Selecione **módulos pré-construídos fáceis de usar.**

1. Na parte superior da tela, selecione o nome de gasoduto padrão **Pipeline-Created-on**. Mude-o para *previsão de preços do automóvel.* O nome não tem de ser exclusivo.

## <a name="set-the-default-compute-target"></a>Definir o alvo de computação padrão

Um oleoduto funciona num alvo de computação, que é um recurso computacional que está ligado ao seu espaço de trabalho. Depois de criar um alvo de computação, pode reutilizá-lo para futuras execuções.

Pode definir um **alvo de computação padrão** para todo o pipeline, que irá dizer a cada módulo para usar o mesmo alvo de computação por padrão. No entanto, pode especificar alvos de cálculo numa base por módulo.

1. Ao lado do nome do pipeline, selecione o **ícone de Gear** Screenshot do ícone de ![ engrenagem na parte superior da tela para ](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) abrir o painel de **Definições.**

1. No painel **de Definições** à direita da tela, **selecione Selecione o alvo de cálculo**.

    Se já tiver um alvo de computação disponível, pode selecioná-lo para executar este pipeline.

    > [!NOTE]
    > O designer só pode realizar experiências de treino no Azure Machine Learning Compute, mas outros alvos de computação não serão mostrados.

1. Insira um nome para o recurso compute.

1. Selecione **Guardar**.

    > [!NOTE]
    > Leva aproximadamente cinco minutos para criar um recurso computacional. Depois de criado o recurso, pode reutilizá-lo e saltar este tempo de espera para futuras execuções.
    >
    > O recurso computacional escala automaticamente para zero nós quando é inativo para economizar custos. Quando voltar a usá-lo depois de um atraso, poderá experimentar aproximadamente cinco minutos de tempo de espera enquanto volta a aumentar.

## <a name="import-data"></a>Importar dados

Existem vários conjuntos de dados de amostras incluídos no designer para você experimentar. Para este tutorial, utilize **os dados sobre os preços do automóvel (Raw)**. 

1. À esquerda da tela do gasoduto está uma paleta de conjuntos de dados e módulos. Selecione **conjuntos de dados de amostra** para ver os conjuntos de dados da amostra disponíveis.

1. Selecione os **dados dados de preços do automóvel (Raw)** e arraste-os para a tela.

   ![Arraste dados para tela](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Ver os dados

Pode visualizar os dados para entender o conjunto de dados que utilizará.

1. Clique com o botão direito nos dados de preços do **automóvel (Raw)** e selecione **Visualize**.

1. Selecione as diferentes colunas na janela de dados para visualizar informações sobre cada uma delas.

    Cada linha representa um automóvel, e as variáveis associadas a cada automóvel aparecem como colunas. Há 205 linhas e 26 colunas neste conjunto de dados.

## <a name="prepare-data"></a>Preparar dados

Os conjuntos de dados normalmente requerem algum pré-processamento antes da análise. Deve ter reparado em alguns valores em falta quando inspecionou o conjunto de dados. Estes valores em falta devem ser limpos para que o modelo possa analisar os dados corretamente.

### <a name="remove-a-column"></a>Remover uma coluna

Quando treinas um modelo, tens de fazer alguma coisa em relação aos dados que faltam. Neste conjunto de dados, a coluna **de perdas normalizada** está a perder muitos valores, pelo que excluirá completamente essa coluna do modelo.

1. Na paleta de módulos à esquerda da tela, expanda a secção **de Transformação de Dados** e encontre as **Colunas Selecionadas no módulo Dataset.**

1. Arraste as **colunas selecionais no** módulo Dataset para a tela. Deixe cair o módulo abaixo do módulo de conjunto de dados.

1. Ligue o conjunto **de dados de preços do automóvel (Raw)** ao **conjunto de colunas selecionadas no módulo Dataset.** Arraste da porta de saída do conjunto de dados, que é o pequeno círculo na parte inferior do conjunto de dados na tela, para a porta de entrada de **Colunas Selecionadas no Conjunto de Dados,** que é o pequeno círculo na parte superior do módulo.

    > [!TIP]
    > Cria-se um fluxo de dados através do seu oleoduto quando liga a porta de saída de um módulo a uma porta de entrada de outro.
    >

    ![Ligar módulos](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Selecione **as Colunas Selecionadas no módulo Dataset.**

1. No painel de detalhes do módulo à direita da tela, **selecione Editar coluna**.

1. Expandir os **nomes da Coluna** cair ao lado de **Incluir**, e selecione Todas  **as colunas**.

1. Selecione **+** o para adicionar uma nova regra.

1. A partir dos menus suspensos, selecione Os nomes **De Excluir** e **Colunas**.
    
1. Introduza *as perdas normalizadas* na caixa de texto.

1. No inferior direito, **selecione Guardar** para fechar o seletor de colunas.

    ![Excluir uma coluna](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Selecione **as Colunas Selecionadas no módulo Dataset.** 

1. No painel de detalhes do módulo à direita da tela, selecione a caixa de texto **'Comentar'** e *introduza excluir perdas normalizadas.*

    Os comentários aparecerão no gráfico para ajudá-lo a organizar o seu oleoduto.

### <a name="clean-missing-data"></a>Limpar dados em falta

O seu conjunto de dados ainda tem valores em falta depois de remover a coluna **de perdas normalizada.** Pode remover os dados restantes em falta utilizando o módulo **De Dados Desaparecidos Limpos.**

> [!TIP]
> A limpeza dos valores em falta dos dados de entrada é um pré-requisito para a utilização da maioria dos módulos do designer.

1. Na paleta de módulos à esquerda da tela, expanda a secção **Transformação de Dados** e encontre o módulo **de Dados Desaparecidos Limpos.**

1. Arraste o módulo **de dados em falta** para a tela do gasoduto. Conecte-o ao **módulo Select Columns no dataset.** 

1. Selecione o módulo **de dados em falta.**

1. No painel de detalhes do módulo à direita da tela, **selecione Editar Coluna**.

1. Nas **Colunas a limpar** a janela que aparece, expanda o menu suspenso ao lado **de Incluir**. Selecione, **Todas as colunas**

1. Selecione **Guardar**

1. No painel de detalhes do módulo à direita da tela, selecione **Remover toda** a linha no modo **de limpeza**.

1. No painel de detalhes do módulo à direita da tela, selecione a caixa **de comentários** e *introduza Remover as linhas de valor em falta*. 

    O seu oleoduto deve agora ser parecido com isto:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png"alt-text="Selecione coluna":::

## <a name="train-a-machine-learning-model"></a>Preparar um modelo de machine learning

Agora que tem os módulos no local para processar os dados, pode configurar os módulos de treino.

Porque queres prever o preço, que é um número, podes usar um algoritmo de regressão. Para este exemplo, utiliza-se um modelo linear de regressão.

### <a name="split-the-data"></a>Dividir os dados

Dividir dados é uma tarefa comum na aprendizagem automática. Dividirá os seus dados em dois conjuntos de dados separados. Um conjunto de dados irá treinar o modelo e o outro irá testar o quão bem o modelo foi executado.

1. Na paleta de módulos, expanda a secção **Transformação de Dados** e encontre o módulo **de Dados Divididos.**

1. Arraste o módulo **de dados divididos** para a tela do gasoduto.

1. Ligue a porta esquerda do módulo **de dados em falta** ao módulo **Dados Divididos.**

    > [!IMPORTANT]
    > Certifique-se de que as portas de saída esquerda de **Dados Desaparecidos Limpos** se ligam a **Dados Divididos**. A porta esquerda contém os dados limpos. A porta direita contém os dados discartados.

1. Selecione o módulo **de dados divididos.**

1. No painel de detalhes do módulo à direita da tela, defina a **Fração de linhas no primeiro conjunto de dados de saída** para 0,7.

    Esta opção divide 70% dos dados para treinar o modelo e 30% para testá-lo. O conjunto de dados de 70% será acessível através da porta de saída esquerda. Os restantes dados estarão disponíveis através da porta de saída certa.

1. No painel de detalhes do módulo à direita da tela, selecione a caixa **de comentários** e *introduza dividir o conjunto de dados no conjunto de treino (0.7) e no conjunto de teste (0.3)*.

### <a name="train-the-model"></a>Preparar o modelo

Treine o modelo dando-lhe um conjunto de dados que inclui o preço. O algoritmo constrói um modelo que explica a relação entre as características e o preço apresentado pelos dados de formação.

1. Na paleta de módulos, expanda os **algoritmos de aprendizagem automática.**
    
    Esta opção exibe várias categorias de módulos que pode utilizar para inicializar algoritmos de aprendizagem.

1. Selecione **Regression**  >  **Linear Regression**, e arraste-a para a tela do gasoduto.

1. Na paleta de módulos, expanda a secção **De formação do Módulo,** e arraste o módulo **Train Model** para a tela.

1. Ligue a saída do módulo **linear de regressão** à entrada esquerda do módulo **Modelo de Comboio.**

1. Ligue a saída de dados de treino (porta esquerda) do módulo **Dados Divididos** à entrada direita do módulo **Modelo de Comboio.**
    
    > [!IMPORTANT]
    > Certifique-se de que as portas de saída esquerda de **Dados Divididos** se ligam ao **Modelo de Comboio.** A porta esquerda contém o conjunto de treino. A porta direita contém o conjunto de teste.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png"alt-text="Screenshot mostrando a configuração correta do módulo Modelo de Comboio. O módulo linear de regressão liga-se à porta esquerda do módulo Modelo de Comboio e o módulo de Dados Divididos liga-se à porta direita do Modelo de Comboio.":::

1. Selecione o módulo **Modelo de Comboio.**

1. No painel de detalhes do módulo à direita da tela, **selecione Editar** o seletor de colunas.

1. Na caixa de diálogo da **coluna Label,** expanda o menu suspenso e selecione **os nomes da Coluna**. 

1. Na caixa de texto, insira o *preço* para especificar o valor que o seu modelo vai prever.

    >[!IMPORTANT]
    > Certifique-se de inserir o nome da coluna exatamente. Não capitalize o **preço.** 

    O seu oleoduto deve ser assim:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png"alt-text="Screenshot mostrando a configuração correta do pipeline depois de adicionar o módulo Modelo de Comboio.":::

### <a name="add-the-score-model-module"></a>Adicione o módulo Modelo de Pontuação

Depois de treinar o seu modelo utilizando 70% dos dados, pode usá-lo para marcar os outros 30% para ver como funciona o seu modelo.

1. Introduza o *modelo de pontuação* na caixa de pesquisa para encontrar o módulo **'Modelo de Pontuação'.** Arraste o módulo para a tela do gasoduto. 

1. Ligue a saída do módulo **Train Model** à porta de entrada da esquerda de **Score Model**. Ligue a saída de dados de teste (porta da direita) do módulo **Split Data** à porta de entrada da direita de **Score Model**.

### <a name="add-the-evaluate-model-module"></a>Adicione o módulo de modelo de avaliação

Utilize o módulo **Modelo avaliar** para avaliar o quão bem o seu modelo obteve o conjunto de dados de teste.

1. Introduza *a avaliação* na caixa de pesquisa para encontrar o módulo **Modelo avaliar.** Arraste o módulo para a tela do gasoduto. 

1. Ligue a saída do módulo **'Modelo de Pontuação'** à entrada esquerda do **Modelo de Avaliação**. 

    O oleoduto final deve ser mais ou menos assim:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png"alt-text="Screenshot mostrando a configuração correta do pipeline.":::

## <a name="submit-the-pipeline"></a>Enviar o oleoduto

Agora que o seu oleoduto está todo configurado, pode submeter uma corrida de gasoduto para treinar o seu modelo de aprendizagem automática. Pode submeter um gasoduto válido a qualquer ponto, que pode ser usado para rever alterações no seu pipeline durante o desenvolvimento.

1. Na parte superior da tela, **selecione Enviar por favor.**

1. Na caixa de diálogo **de execução do gasoduto set up,** selecione **Criar novo**.

    > [!NOTE]
    > Experiências grupo semelhantes oleodutos funciona juntos. Se executar um oleoduto várias vezes, pode selecionar a mesma experiência para execuções sucessivas.

    1. Introduza um nome descritivo para **Novo Nome da Experiência**.

    1. Selecione **Submeter**.
    
    Pode ver o estado de execução e os detalhes no canto superior direito da tela.
    
    Se esta for a primeira corrida, pode levar até 20 minutos para o seu oleoduto terminar de funcionar. As definições de computação padrão têm um tamanho mínimo de nó de 0, o que significa que o designer deve alocar recursos depois de estar inativo. As repetidas operações de gasoduto levarão menos tempo, uma vez que os recursos de computação já estão atribuídos. Além disso, o designer utiliza resultados em cache para cada módulo para melhorar ainda mais a eficiência.

### <a name="view-scored-labels"></a>Ver rótulos pontuados

Após a execução concluída, pode ver os resultados do percurso do gasoduto. Primeiro, veja as previsões geradas pelo modelo de regressão.

1. Clique com o botão direito no módulo **'Modelo de pontuação'** e selecione **Visualize** para ver a sua saída.

    Aqui pode ver os preços previstos e os preços reais a partir dos dados de teste.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/score-result.png"alt-text="Screenshot da visualização de saída realçando a coluna Marcador":::

### <a name="evaluate-models"></a>Avaliar modelos

Utilize o **Modelo de Avaliação** para ver como o modelo treinado foi bem executado no conjunto de dados de teste.

1. Clique com o botão direito no módulo **Modelo de Avaliação** e selecione **Visualize** para ver a sua saída.

As seguintes estatísticas são mostradas para o seu modelo:

* **Erro Absoluto Médio (MAE)**: A média de erros absolutos. Um erro é a diferença entre o valor previsto e o valor real.
* **Raiz Média Erro Quadrado (RMSE)**: A raiz quadrada da média de erros quadrados de previsões feitas no conjunto de dados do teste.
* **Erro relativo absoluto**: A média dos erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado absoluto**: A média dos erros ao quadrado relativos à diferença ao quadrado entre os valores reais e a média de todos os valores reais.
* **Coeficiente de Determinação**: Também conhecido como valor quadrado R, esta métrica estatística indica o quão bem um modelo se encaixa nos dados.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. Um valor menor indica que as previsões estão mais próximas dos valores reais. Para o coeficiente de determinação, quanto mais próximo o seu valor for de um (1.0), melhor serão as previsões.

## <a name="clean-up-resources"></a>Limpar recursos

Salte esta secção se quiser continuar com a parte 2 do tutorial, [implantando modelos](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Na segunda parte, aprenderás a implantar o teu modelo como ponto final em tempo real.

> [!div class="nextstepaction"]
> [Continuar a implementar modelos](tutorial-designer-automobile-price-deploy.md)
