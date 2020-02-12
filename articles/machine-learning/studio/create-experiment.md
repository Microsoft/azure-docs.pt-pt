---
title: 'Quickstart: Criar uma experiência de ciência de dados'
titleSuffix: ML Studio (classic) - Azure
description: Este arranque de aprendizagem automática acompanha-o através de uma experiência fácil de ciência de dados. Vamos prever o preço de um carro através de um algoritmo de regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: 3897047bbe3f105c0b4adf3da38b037ea62dd646
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153643"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-azure-machine-learning-studio-classic"></a>Quickstart: Crie a sua primeira experiência em ciência de dados no Azure Machine Learning Studio (clássico)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Neste arranque rápido, cria-se uma experiência de machine learning no [Azure Machine Learning Studio (clássico)](what-is-ml-studio.md) que prevê o preço de um carro com base em diferentes variáveis, como fazer e especificações técnicas.

Se você é novo em machine learning, a série de vídeo [Data Science for Beginners](data-science-for-beginners-the-5-questions-data-science-answers.md) é uma ótima introdução ao machine learning usando linguagem e conceitos do dia-a-dia.

Este quickstart segue o fluxo de trabalho padrão para uma experiência:

1. **Criar um modelo**
    - [Obtenha os dados]
    - [Preparar os dados]
    - [Definir funcionalidades]
1. **Preparar o modelo**
    - [Escolha e aplique um algoritmo]
1. **Classificar e testar o modelo**
    - [Prever novos preços do automóvel]

[Obtenha os dados]: #get-the-data
[Preparar os dados]: #prepare-the-data
[Definir funcionalidades]: #define-features
[Escolha e aplique um algoritmo]: #choose-and-apply-an-algorithm
[Prever novos preços do automóvel]: #predict-new-automobile-prices

## <a name="get-the-data"></a>Obter os dados

A primeira coisa que precisa na aprendizagem automática são os dados.
Existem vários conjuntos de dados de amostras incluídos no Studio (clássico) que pode utilizar, ou pode importar dados de muitas fontes. Neste exemplo, vamos utilizar o conjunto de dados de exemplo, **Automobile price data (Raw) (Dados de preços de automóveis (não processados))** , que está incluído na sua área de trabalho.
Este conjunto de dados inclui entradas para vários automóveis individuais, incluindo informações como a marca, o modelo, as especificações técnicas e o preço.

> [!TIP]
> Pode encontrar uma cópia de trabalho da experimentação seguinte na [Galeria de IA do Azure](https://gallery.azure.ai). Vá à **[sua primeira experiência](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** em ciência de dados - Previsão de preços do automóvel e clique em Open in **Studio** para descarregar uma cópia da experiência no seu espaço de trabalho machine learning studio (clássico).

Eis como obter o conjunto de dados na sua experimentação.

1. Crie uma nova experiência clicando **+NEW** na parte inferior da janela machine learning studio (clássico). Selecione **EXPERIMENT** >  **Experiência Em Branco**.

1. É dado um nome predefinido à experimentação, que pode ver na parte superior da tela. Selecione este texto e mude o nome para algo significativo, como, por exemplo, **Previsão de preços de automóveis**. O nome não tem de ser exclusivo.

    ![Mude o nome da experimentação](./media/create-experiment/rename-experiment.png)

1. À esquerda da tela da experimentação existe uma paleta de conjuntos de dados e módulos. Digite **automóvel** na caixa de pesquisa na parte superior desta paleta para localizar o conjunto de dados denominado **Dados do preço automóvel (bruto)** . Arraste este conjunto de dados para a tela da experimentação.

    ![Encontre o conjunto de dados do automóvel e arraste-o para a tela de experiência](./media/create-experiment/type-automobile.png)

Para ver como são estes dados, clique na porta de saída na parte inferior do conjunto de dados do automóvel e, em seguida, **selecione Visualize**.

![Clique na porta de saída e selecione "Visualize"](./media/create-experiment/select-visualize.png)

> [!TIP]
> Os conjuntos de dados e os módulos têm portas de entrada e de saída, representadas por pequenos círculos. As portas de entrada estão em cima e as de saída estão em baixo.
Para criar um fluxo de dados através da experimentação, ligue uma porta de saída de um módulo a uma porta de entrada de outro.
Pode, em qualquer altura, clicar na porta de saída de um conjunto de dados ou de um módulo para ver o aspeto dos dados nessa fase do fluxo de dados.

Neste conjunto de dados, cada linha representa um automóvel, e as variáveis associadas a cada automóvel aparecem como colunas. Vamos prever o preço na coluna de extrema-direita (coluna 26, intitulada "preço") usando as variáveis para um automóvel específico.

![Veja os dados do automóvel na janela de visualização de dados](./media/create-experiment/visualize-auto-data.png)

Feche a janela de visualização, clicando no "**x**" no canto superior direito.

## <a name="prepare-the-data"></a>Preparar os dados

Normalmente, um conjunto de dados requer alguns pré-processamentos antes de poder ser analisado. Poderá ter reparado nos valores em falta presentes nas colunas de várias linhas. Estes valores em falta têm de ser apagados para que o modelo possa analisar os dados corretamente. Removeremos todas as linhas que tenham valores em falta. Além disso, a coluna das **perdas normalizadas** tem uma grande proporção de valores em falta, pelo que recomendamos que exclua totalmente essa coluna do modelo.

> [!TIP]
> Apagar os valores em falta a partir dos dados de entrada é um pré-requisito para utilizar a maioria dos módulos.

Primeiro, adicionamos um módulo que remove completamente a coluna **de perdas normalizadas.** Em seguida, adicionamos outro módulo que remove qualquer linha que tenha dados em falta.

1. Digite **colunas na** caixa de pesquisa na parte superior da paleta de módulos para encontrar as Colunas Select no módulo [Dataset.][select-columns] Em seguida, arrastá-lo para a tela de experiência. Este módulo permite-nos selecionar quais as colunas de dados que pretendemos incluir ou excluir no modelo.

1. Ligue a porta de saída dos dados de preços do **Automóvel (Raw)** à porta de entrada das Colunas Select em Dataset.

    ![Adicione o módulo "Selecionar Colunas no Conjunto de Dados" à tela de experiência e conectá-lo](./media/create-experiment/type-select-columns.png)

1. Clique [nas Colunas Select no][select-columns] módulo Dataset e clique no seletor de **colunas de lançamento** no painel **Propriedades.**

   - À esquerda, clique em **Com regras**
   - Em **Começar Com**, clique em **Todas as colunas**. Estas regras direcionam [colunas select as colunas em Dataset][select-columns] para passar em todas as colunas (exceto aquelas colunas que estamos prestes a excluir).
   - Nas listas pendentes, selecione **Excluir** e os **nomes das colunas** e, em seguida, clique dentro da caixa de texto. É apresentada uma lista de colunas. Selecione **normalized-losses**, que é adicionada à caixa de texto.
   - Clique no botão marca de verificação (OK) para fechar o seletor de colunas (na parte inferior direita).

     ![Lançar o seletor de colunas e excluir a coluna "perdas normalizadas"](./media/create-experiment/launch-column-selector.png)

     Agora, o painel de propriedades de **Select Columns in Datase** indica que o módulo vai percorrer todas as colunas do conjunto de dados, exceto **normalized-losses**.

     ![O painel de propriedades mostra que a coluna "perdas normalizadas" está excluída](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > Pode adicionar um comentário a um módulo, fazendo duplo clique no módulo e introduzindo o texto. Isto pode ajudá-lo a ver rapidamente o que o módulo está a fazer na sua experimentação. Neste caso, clique duas [vezes nas Colunas Select no][select-columns] módulo Dataset e digite o comentário "Exclua perdas normalizadas".

     ![Clique duas vezes num módulo para adicionar um comentário](./media/create-experiment/add-comment.png)

1. Arraste o módulo [Clean Missing Data][clean-missing-data] para a tela de experiência e conecte-o às Colunas Select no módulo [Dataset.][select-columns] No painel **Properties**, selecione **Remove entire row (Remover linha inteira)** , em **Cleaning mode (Modo de limpeza)** . Estas opções direcionam dados [em falta][clean-missing-data] para limpar os dados removendo linhas que têm valores em falta. Clique duas vezes no módulo e escreva o comentário "Remover linhas de valor em falta".

    ![Desajuste o modo de limpeza para "Remover a linha inteira" para o módulo "Limpar dados em falta"](./media/create-experiment/set-remove-entire-row.png)

1. Execute a experimentação clicando em **EXECUTAR** na parte inferior da página.

    Quando a execução da experimentação estiver concluída, todos os módulos têm uma marca de verificação a verde para indicar que foram concluídos com êxito. Repare também no estado **Execução terminada** no canto superior direito.

    ![Depois de executá-lo, a experiência deve ser algo como este](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Porque é que executámos a experimentação agora? Ao executar a experiência, as definições de coluna para os nossos dados passam a partir do conjunto de dados, através das [Colunas Select no][select-columns] módulo Dataset e através do módulo [Clean Missing Data.][clean-missing-data] Isto significa que quaisquer módulos que nos conectamos a [Dados Desaparecidos Limpos][clean-missing-data] também terão essa mesma informação.

Agora temos dados limpos. Se quiser visualizar o conjunto de dados limpo, clique na porta de saída esquerda do módulo [Clean Missing Data][clean-missing-data] e selecione **Visualize**. Repare que a coluna **perdas normalizadas** já não está mais incluída e não existem valores em falta.

Agora que os dados foram apagados, estamos prontos para especificar quais as funcionalidades que vai utilizar no modelo preditivo.

## <a name="define-features"></a>Definir funcionalidades

No Machine Learning, as *funcionalidades* são propriedades mensuráveis individuais de algo que lhe interessa. No nosso conjunto de dados, cada linha representa um automóvel e cada coluna é uma funcionalidade desse automóvel.

Encontrar um bom conjunto de funcionalidades para criar um modelo preditivo requer experimentação e conhecimentos sobre o problema que pretende resolver. Algumas funcionalidades são melhores para prever num destino do que outras. Algumas características têm uma forte correlação com outras características e podem ser removidas. Por exemplo, city-mpg e highway-mpg estão intimamente relacionados, pelo que podemos manter um e remover o outro sem afetar a predição significativamente.

Vamos criar um modelo que utiliza um subconjunto das funcionalidades no nosso conjunto de dados. Pode regressar mais tarde e selecionar funcionalidades diferentes, executar novamente a experimentação e ver se consegue obter melhores resultados. Contudo, para começar, vamos experimentar as seguintes funcionalidades:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Arraste [outraS Colunas Selecionadas no][select-columns] módulo Dataset para a tela de experimentação. Ligue a porta de saída esquerda do módulo [Clean Missing Data][clean-missing-data] à entrada das Colunas Select no módulo [Dataset.][select-columns]

    ![Ligue o módulo "Selecionar colunas no Conjunto de Dados" ao módulo "Limpar dados em falta"](./media/create-experiment/connect-clean-to-select.png)

1. Clique duas vezes no módulo e escreva "Selecionar as funcionalidades para predição".

1. Clique no painel **Iniciar seletor de colunas** no painel **Propriedades**.

1. Clique em **Com regras**.

1. Em **Begin With (Começar Em)** , clique em **No columns (Nenhuma coluna)** . Na linha do filtro, selecione **Include (Incluir)** e **column names (nomes das colunas)** e selecione a lista de nomes de colunas na caixa de texto. Este filtro direciona o módulo para não passar por quaisquer colunas (funcionalidades), exceto as que especificamos.

1. Clique no botão de marca de verificação (OK).

    ![Selecione as colunas (características) para incluir na previsão](./media/create-experiment/select-columns-to-include.png)

Este módulo produz um conjunto de dados filtrado que contém apenas as funcionalidades que queremos passar para o algoritmo de aprendizagem que usaremos no próximo passo. Mais tarde, pode voltar e tentar novamente com uma seleção de funcionalidades diferente.

## <a name="choose-and-apply-an-algorithm"></a>Escolha e aplique um algoritmo

Agora que os dados estão prontos, construir um modelo preditivo consiste em formar e testar. Utilizaremos os nossos dados para preparar o modelo e, em seguida, vamos testá-lo para ver com que exatidão consegue prever os preços.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Classficação* e *regrssão* são dois tipos de algoritmos de machine learning supervisionados. A classificação prevê uma resposta a partir de um conjunto definido de categorias, tais como uma cor (vermelho, azul ou verde). A regressão é utilizada para prever um número.

Uma vez que queremos prever o preço, que é um número, vamos utilizar um algoritmo de regressão. Para este exemplo, usaremos um modelo linear de *regressão.*


Para preparar o modelo, damos-lhe um conjunto de dados que incluem o preço. O modelo analisa os dados e procura correlações entre as características e o preço de um automóvel. Depois, testamos o modelo. Damos-lhe um conjunto de características de automóveis com que estamos familiarizados e vemos a exatidão com que o modelo consegue prever o preço sabido.

Vamos utilizar os dados quer para preparar o modelo, quer para testá-lo, dividindo-os em conjuntos de dados de preparação e teste separados.

1. Selecione e arraste o módulo [De dados divididos][split] para a tela de experiência e conecte-o às [últimas Colunas Select no módulo Dataset.][select-columns]

1. Clique no módulo [De dados divididos][split] para selecioná-lo. Localize **Fraction of rows in the first output dataset (Fração de linhas no primeiro conjunto de dados de saída)** (no painel **Properties (Propriedades)** , no lado direito da tela) e defina-a como 0,75. Desta forma, vamos utilizar 75% dos dados para preparar o modelo e retenha 25% para fins de teste.

    ![Desloque a fração dividida do módulo "Dados Divididos" para 0,75](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > Ao alterar o parâmetro **Seed aleatório**, pode produzir diferentes amostras aleatórias para formação e testar. Este parâmetro controla a propagação do gerador de número pseudo-aleatório.

1. Execute a experimentação. Quando a experiência for executada, as [Colunas Select em Dataset][select-columns] e [Split Data][split] passam definições de colunas para os módulos que vamos adicionar a seguir.  

1. Para selecionar o algoritmo do Learning, expanda a categoria **Machine Learning** na paleta do módulo para a esquerda da tela e, em seguida, expanda **Inicializar modelo**. Isto apresenta várias categorias de módulos que podem ser utilizadas para inicializar algoritmos do Machine Learning. Para esta experiência, selecione o módulo [de regressão linear][linear-regression] na categoria **Regression** e arraste-o para a tela de experiência. (Para encontrar o módulo, também pode escrever “linear regression” na caixa Pesquisa da paleta.)

1. Encontre e arraste o módulo [Modelo de Comboio][train-model] para a tela de experiência. Ligue a saída do módulo [De Regressão Linear][linear-regression] à entrada esquerda do módulo [Modelo de Comboio][train-model] e ligue a saída de dados de treino (porta esquerda) do módulo Split [Data][split] à entrada direita do módulo Modelo [de Comboio.][train-model]

    ![Ligue o módulo "Modelo de Comboio" aos módulos "Regressão Linear" e "Dados Divididos"](./media/create-experiment/connect-train-model.png)

1. Clique no módulo [Modelo de Comboio,][train-model] clique no seletor de **colunas de lançamento** no painel **Properties** e, em seguida, selecione a coluna de **preços.** **O preço** é o valor que o nosso modelo vai prever.

    Para selecionar a coluna **price** no seletor de colunas, mova-a da lista **Available columns (Colunas disponíveis)** para a lista **Selected columns (Colunas selecionadas)** .

    ![Selecione a coluna de preços para o módulo "Modelo de Comboio"](./media/create-experiment/select-price-column.png)

1. Execute a experimentação.

Temos agora um modelo de regressão preparado que pode ser utilizado para classificar dados de automóveis novos e fazer predições de preços.

![Depois de correr, a experiência deve agora ser algo como isto](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Prever novos preços do automóvel

Agora que experimentámos o modelo, utilizando 75% dos nossos dados, podemos utilizá-lo para pontuar os outros 25% por cento dos dados para ver quão bem funciona o nosso modelo.

1. Encontre e arraste o módulo [Score Model][score-model] para a tela de experiência. Ligue a saída do módulo [Modelo de Comboio][train-model] à porta de entrada esquerda do Modelo de [Pontuação][score-model]. Ligue a saída de dados de teste (porta direita) do módulo [De dados divididos][split] à porta de entrada direita do [Modelo de Pontuação][score-model].

    ![Ligue o módulo "Score Model" aos módulos "Modelo de Comboio" e "Dados Divididos"](./media/create-experiment/connect-score-model.png)

1. Execute a experiência e veja a saída a partir do módulo ['Modelo de Pontuação',][score-model] clicando na porta de saída do [Score Model][score-model] e selecione **Visualize**. O resultado mostra os valores previstos para os preços e os valores conhecidos dos dados do teste.  

    ![Saída do módulo "Score Model"](./media/create-experiment/score-model-output.png)

1. Por fim, vamos testar a qualidade dos resultados. Selecione e arraste o módulo ['Avaliar Modelo'][evaluate-model] para a tela de experimentação e ligue a saída do módulo ['Modelo de Pontuação'][score-model] à entrada esquerda do Modelo de [Avaliação][evaluate-model]. A experimentação final deve ter este aspeto:

    ![A experiência final](./media/create-experiment/complete-linear-regression-experiment.png)

1. Execute a experimentação.

Para visualizar a saída do módulo ['Avaliar Modelo',][evaluate-model] clique na porta de saída e, em seguida, selecione **Visualize**.

![Resultados da avaliação da experiência](./media/create-experiment/evaluation-results.png)

As estatísticas seguintes são apresentadas para o nosso modelo:

- **Média dos erros absolutos** (MAE): A média dos erros absolutos (um *erro* é a diferença entre o valor previsto e o valor real).
- **Raiz quadrada da média dos erros** (RMSE): A raiz quadrada da média dos erros ao quadrado das predições efetuadas no conjunto de dados de teste.
- **Erro relativo absoluto**: A média dos erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
- **Erro ao quadrado absoluto**: A média dos erros ao quadrado relativos à diferença ao quadrado entre os valores reais e a média de todos os valores reais.
- **Coeficiente de determinação**: Também conhecido como o **valor de R ao quadrado**, esta é uma métrica de estatística que indica o quão bem um modelo é adequado para os dados.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. Um valor mais pequeno indica que as predições mais detalhadas correspondem aos valores reais. No **Coeficiente de determinação**, quanto mais próximo for um valor de outro (1.0), melhores serão as predições.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma experiência simples utilizando um conjunto de dados de amostras. Para explorar o processo de criação e implantação de um modelo com mais profundidade, continue a ser tutorial de solução preditiva.

> [!div class="nextstepaction"]
> [Tutorial: Desenvolver uma solução preditiva no Studio (clássico)](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
