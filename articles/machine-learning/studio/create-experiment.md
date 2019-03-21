---
title: 'Início rápido: Criar uma experimentação de ciência de dados'
titleSuffix: Azure Machine Learning Studio
description: Este guia de introdução do machine learning explica-lhe uma experimentação de ciência de dados fácil. Vamos prever o preço de um carro através de um algoritmo de regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: 0819c232412e1619f82a25476a8318d26c8087da
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105731"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Início rápido: Criar a sua primeira experimentação de ciência de dados no Azure Machine Learning Studio

Neste início rápido, vai criar uma experimentação do machine learning na [Azure Machine Learning Studio](what-is-ml-studio.md) que prevê o preço de um automóvel com base em diferentes variáveis, como a marca e as especificações técnicas.

Se é novato Machine learning, a série de vídeos [ciência de dados para principiantes](data-science-for-beginners-the-5-questions-data-science-answers.md) é uma excelente introdução ao machine learning utilizando uma linguagem e conceitos.

Este guia de introdução segue o fluxo de trabalho predefinida para uma experimentação:

1. **Criar um modelo**
    - [Obter os dados]
    - [Preparar os dados]
    - [Definir funcionalidades]
1. **Preparar o modelo**
    - [Escolher e aplicar um algoritmo]
1. **Classificar e testar o modelo**
    - [Prever novos preços para automóveis]

[Obter os dados]: #get-the-data
[Preparar os dados]: #prepare-the-data
[Definir funcionalidades]: #define-features
[Escolher e aplicar um algoritmo]: #choose-and-apply-an-algorithm
[Prever novos preços para automóveis]: #predict-new-automobile-prices

Se não tiver uma conta do Studio, vá para o [home page do Studio](https://studio.azureml.net) e selecione **inscrever-se aqui** para criar uma conta gratuita. A área de trabalho gratuita terá todos os recursos que necessários para este início rápido.

## <a name="get-the-data"></a>Obter os dados

A primeira coisa que precisa no machine learning é os dados.
Existem vários conjuntos de dados exemplo incluídos no Studio que pode utilizar, ou pode importar dados a partir de várias origens. Neste exemplo, vamos utilizar o conjunto de dados de exemplo, **Automobile price data (Raw) (Dados de preços de automóveis (não processados))**, que está incluído na sua área de trabalho.
Este conjunto de dados inclui entradas para vários automóveis individuais, incluindo informações como a marca, o modelo, as especificações técnicas e o preço.

> [!TIP]
> Pode encontrar uma cópia de trabalho da experimentação seguinte na [Galeria de IA do Azure](https://gallery.azure.ai). Aceda a **[Your first data science experiment - Automobile price prediction (A sua primeira experimentação de ciência de dados - Previsão do preço de automóveis)](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** e clique em **Open in Studio (Abrir no Studio)** para transferir uma cópia da experimentação para a sua área de trabalho do Machine Learning Studio.

Eis como obter o conjunto de dados na sua experimentação.

1. Criar uma nova experimentação ao clicar em **+ novo** na parte inferior da janela Machine Learning Studio. Selecione **experimentação** >  **em branco experimentação**.

1. É dado um nome predefinido à experimentação, que pode ver na parte superior da tela. Selecione este texto e mude o nome para algo significativo, como, por exemplo, **Previsão de preços de automóveis**. O nome não tem de ser exclusivo.

    ![Mude o nome da experimentação](./media/create-experiment/rename-experiment.png)

1. À esquerda da tela da experimentação existe uma paleta de conjuntos de dados e módulos. Digite **automóvel** na caixa de pesquisa na parte superior desta paleta para localizar o conjunto de dados denominado **Dados de preços de automóveis (não processados)**. Arraste este conjunto de dados para a tela da experimentação.

    ![Localize o conjunto de dados de automóvel e arraste-o para a tela de experimentação](./media/create-experiment/type-automobile.png)

Veja o que esses dados parecem, clique na porta de saída na parte inferior do conjunto de dados de automóvel, em seguida, selecione **Visualize**.

![Clique na porta de saída e selecione "Visualizar"](./media/create-experiment/select-visualize.png)

> [!TIP]
> Os conjuntos de dados e os módulos têm portas de entrada e de saída, representadas por pequenos círculos. As portas de entrada estão em cima e as de saída estão em baixo.
Para criar um fluxo de dados através da experimentação, ligue uma porta de saída de um módulo a uma porta de entrada de outro.
Pode, em qualquer altura, clicar na porta de saída de um conjunto de dados ou de um módulo para ver o aspeto dos dados nessa fase do fluxo de dados.

Este conjunto de dados, cada linha representa um automóvel e as variáveis associadas a cada um destes aparecem como colunas. Vamos prever o preço na coluna da direita (coluna 26, com o nome "preço") com as variáveis de um automóvel específico.

![Veja os dados dos automóveis na janela de visualização de dados](./media/create-experiment/visualize-auto-data.png)

Feche a janela de visualização, clicando no "**x**" no canto superior direito.

## <a name="prepare-the-data"></a>Preparar os dados

Normalmente, um conjunto de dados requer alguns pré-processamentos antes de poder ser analisado. Poderá ter reparado nos valores em falta presentes nas colunas de várias linhas. Estes valores em falta têm de ser apagados para que o modelo possa analisar os dados corretamente. Iremos remover quaisquer linhas que tenham valores em falta. Além disso, a coluna das **perdas normalizadas** tem uma grande proporção de valores em falta, pelo que recomendamos que exclua totalmente essa coluna do modelo.

> [!TIP]
> Apagar os valores em falta a partir dos dados de entrada é um pré-requisito para utilizar a maioria dos módulos.

Em primeiro lugar, vamos adicionar um módulo que remove o **normalized-losses** coluna completamente. Em seguida, adicionamos outro módulo que remove todas as linhas que tenham dados em falta.

1. Tipo **selecionar colunas** na caixa de pesquisa na parte superior da paleta do módulo para localizar o [Select Columns in Dataset] [ select-columns] módulo. Em seguida, arraste-o para a tela da experimentação. Este módulo permite-nos selecionar quais as colunas de dados que pretendemos incluir ou excluir no modelo.

1. Ligue a porta de saída a **dados de preço do automóvel (bruto)** conjunto de dados para a porta de entrada de selecionar colunas no conjunto de dados.

    ![Adicione o módulo "Select Columns in Dataset" à tela da experimentação e ligue-](./media/create-experiment/type-select-columns.png)

1. Clique no módulo [Select Columns in Dataset][select-columns] e clique em **Launch column selector (Iniciar seletor de colunas)**, no painel **Properties (Propriedades)**.

   - À esquerda, clique em **Com regras**
   - Em **Começar Com**, clique em **Todas as colunas**. Estas regras em direto [Select Columns in Dataset] [ select-columns] a passar por todas as colunas (exceto as que estamos prestes a excluir).
   - Nas listas pendentes, selecione **Excluir** e os **nomes das colunas** e, em seguida, clique dentro da caixa de texto. É apresentada uma lista de colunas. Selecione **normalized-losses**, que é adicionada à caixa de texto.
   - Clique no botão de marca de verificação (OK) para fechar o Seletor de colunas (no canto inferior direito).

     ![Inicie o Seletor de colunas e exclua a coluna "normalized-losses"](./media/create-experiment/launch-column-selector.png)

     Agora, o painel de propriedades de **Select Columns in Datase** indica que o módulo vai percorrer todas as colunas do conjunto de dados, exceto **normalized-losses**.

     ![O painel de propriedades mostra que a coluna "normalized-losses" está excluída](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > Pode adicionar um comentário a um módulo, fazendo duplo clique no módulo e introduzindo o texto. Isto pode ajudá-lo a ver rapidamente o que o módulo está a fazer na sua experimentação. Neste caso, faça duplo clique no módulo [Select Columns in Dataset (Selecionar Colunas no Conjunto de Dados)][select-columns] e escreva o comentário "Exclude normalized losses” (“Excluir perdas normalizadas").

     ![Faça duplo clique num módulo para adicionar um comentário](./media/create-experiment/add-comment.png)

1. Arraste o módulo [Apagar dados em falta][clean-missing-data] para a tela de experimentação e conecte-o ao módulo [Selecionar colunas no conjunto de dados][select-columns]. No painel **Properties**, selecione **Remove entire row (Remover linha inteira)**, em **Cleaning mode (Modo de limpeza)**. Estas opções direct [apagar dados em falta] [ clean-missing-data] para limpar os dados ao remover as linhas que tenham valores em falta. Clique duas vezes no módulo e escreva o comentário "Remover linhas de valor em falta".

    ![Defina o modo de limpeza como "Remove entire row" para o módulo "Clean Missing Data"](./media/create-experiment/set-remove-entire-row.png)

1. Execute a experimentação clicando em **EXECUTAR** na parte inferior da página.

    Quando a execução da experimentação estiver concluída, todos os módulos têm uma marca de verificação a verde para indicar que foram concluídos com êxito. Repare também no estado **Execução terminada** no canto superior direito.

    ![Depois de a executar, a experimentação deve ter um aspeto semelhante ao seguinte](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Porque é que executámos a experimentação agora? Ao executar a experimentação, as definições das colunas dos nossos dados passam do conjunto de dados para o módulo [Select Columns in Dataset][select-columns] e para o módulo [Clean Missing Data][clean-missing-data]. Isto significa que qualquer módulo que liguemos a [Clean Missing Data][clean-missing-data] também terá estas informações.

Agora temos dados limpos. Se pretender ver o conjunto de dados limpo, clique na porta de saída à esquerda do módulo [Clean Missing Data][clean-missing-data] e selecione **Visualize**. Repare que a coluna **perdas normalizadas** já não está mais incluída e não existem valores em falta.

Agora que os dados foram apagados, estamos prontos para especificar quais as funcionalidades que vai utilizar no modelo preditivo.

## <a name="define-features"></a>Definir funcionalidades

No Machine Learning, as *funcionalidades* são propriedades mensuráveis individuais de algo que lhe interessa. No nosso conjunto de dados, cada linha representa um automóvel e cada coluna é uma funcionalidade desse automóvel.

Encontrar um bom conjunto de funcionalidades para criar um modelo preditivo requer experimentação e conhecimentos sobre o problema que pretende resolver. Algumas funcionalidades são melhores para prever num destino do que outras. Algumas funcionalidades têm uma correlação forte com outras funcionalidades e podem ser removidas. Por exemplo, city-mpg e highway-mpg estão intimamente relacionados, pelo que podemos manter um e remover o outro sem afetar a predição significativamente.

Vamos criar um modelo que utiliza um subconjunto das funcionalidades no nosso conjunto de dados. Pode regressar mais tarde e selecionar funcionalidades diferentes, executar novamente a experimentação e ver se consegue obter melhores resultados. Contudo, para começar, vamos experimentar as seguintes funcionalidades:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Arraste outro módulo [Select Columns in Dataset][select-columns] para a tela da experimentação. Ligue a porta de saída à esquerda do módulo [Clean Missing Data][clean-missing-data] à porta de entrada do módulo [Select Columns in Dataset][select-columns].

    ![Ligar o módulo "Select Columns in Dataset" ao módulo "Clean Missing Data"](./media/create-experiment/connect-clean-to-select.png)

1. Clique duas vezes no módulo e escreva "Selecionar as funcionalidades para predição".

1. Clique no painel **Iniciar seletor de colunas** no painel **Propriedades**.

1. Clique em **Com regras**.

1. Em **Begin With (Começar Em)**, clique em **No columns (Nenhuma coluna)**. Na linha do filtro, selecione **Include (Incluir)** e **column names (nomes das colunas)** e selecione a lista de nomes de colunas na caixa de texto. Este filtro instrui o módulo a não passar por quaisquer colunas (características), exceto aqueles que especificamos.

1. Clique no botão de marca de verificação (OK).

    ![Selecione as colunas (características) a incluir na predição](./media/create-experiment/select-columns-to-include.png)

Este módulo produz um conjunto de dados filtrado que contêm apenas as funcionalidades que queremos passar para o algoritmo de aprendizagem que vamos utilizar no próximo passo. Mais tarde, pode voltar e tentar novamente com uma seleção de funcionalidades diferente.

## <a name="choose-and-apply-an-algorithm"></a>Escolher e aplicar um algoritmo

Agora que os dados estão prontos, construir um modelo preditivo consiste em formar e testar. Utilizaremos os nossos dados para preparar o modelo e, em seguida, vamos testá-lo para ver com que exatidão consegue prever os preços.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Classficação* e *regrssão* são dois tipos de algoritmos de machine learning supervisionados. A classificação prevê uma resposta a partir de um conjunto definido de categorias, tais como uma cor (vermelho, azul ou verde). A regressão é utilizada para prever um número.

Uma vez que queremos prever o preço, que é um número, vamos utilizar um algoritmo de regressão. Neste exemplo, vamos utilizar um *regressão linear* modelo.


Para preparar o modelo, damos-lhe um conjunto de dados que incluem o preço. O modelo analisa os dados e procura correlações entre as características e o preço de um automóvel. Depois, testamos o modelo. Damos-lhe um conjunto de características de automóveis com que estamos familiarizados e vemos a exatidão com que o modelo consegue prever o preço sabido.

Vamos utilizar os dados quer para preparar o modelo, quer para testá-lo, dividindo-os em conjuntos de dados de preparação e teste separados.

1. Selecione e arraste o módulo [Split Data (Dividir Dados)][split] para a tela da experimentação e ligue-o ao último módulo [Select Columns in Dataset][select-columns].

1. Clique no módulo [Split Data][split] para selecioná-lo. Localize **Fraction of rows in the first output dataset (Fração de linhas no primeiro conjunto de dados de saída)** (no painel **Properties (Propriedades)**, no lado direito da tela) e defina-a como 0,75. Desta forma, vamos utilizar 75% dos dados para preparar o modelo e retenha 25% para fins de teste.

    ![Defina a fração de divisão do módulo "Split Data" como 0,75](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > Ao alterar o parâmetro **Seed aleatório**, pode produzir diferentes amostras aleatórias para formação e testar. Este parâmetro controla a propagação do gerador de número pseudo-aleatório.

1. Execute a experimentação. Quando a experimentação é executada, os módulos [Select Columns in Dataset][select-columns] e [Split Data][split] passam as definições das colunas para os módulos que vamos adicionar em seguida.  

1. Para selecionar o algoritmo do Learning, expanda a categoria **Machine Learning** na paleta do módulo para a esquerda da tela e, em seguida, expanda **Inicializar modelo**. Isto apresenta várias categorias de módulos que podem ser utilizadas para inicializar algoritmos do Machine Learning. Nesta experimentação, selecione o módulo [Linear Regression (Regressão Linear)][linear-regression], na categoria **Regression (Regressão)** e arraste-o para a tela da experimentação. (Para encontrar o módulo, também pode escrever “linear regression” na caixa Pesquisa da paleta.)

1. Localize e arraste o módulo [Modelo de formação][train-model] para a tela de experimentação. Ligue a saída do módulo [Linear Regression][linear-regression] à entrada da esquerda do módulo [Train Model][train-model] e ligue a saída de dados de preparação (porta da esquerda) do módulo [Split Data][split] à entrada da direita do módulo [Train Model][train-model].

    ![Ligar o módulo "Train Model" aos módulos "Linear Regression" e no "Split Data"](./media/create-experiment/connect-train-model.png)

1. Clique no módulo [Train Model][train-model], clique em **Launch column selector**no painel **Properties** e selecione a coluna **price (preço)**. **Preço** é o valor que o nosso modelo irá prever.

    Para selecionar a coluna **price** no seletor de colunas, mova-a da lista **Available columns (Colunas disponíveis)** para a lista **Selected columns (Colunas selecionadas)**.

    ![Selecione a coluna de preços para o módulo "Train Model"](./media/create-experiment/select-price-column.png)

1. Execute a experimentação.

Temos agora um modelo de regressão preparado que pode ser utilizado para classificar dados de automóveis novos e fazer predições de preços.

![Depois de ser executada, a experimentação deverá agora ser semelhante à seguinte](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Prever novos preços para automóveis

Agora que experimentámos o modelo, utilizando 75% dos nossos dados, podemos utilizá-lo para pontuar os outros 25% por cento dos dados para ver quão bem funciona o nosso modelo.

1. Localize e arraste o módulo [Score Model (Classificar Modelo)][score-model] para a tela da experimentação. Ligue a saída do módulo [Train Model][train-model] à porta de entrada da esquerda de [Score Model][score-model]. Ligue a saída de dados de teste (porta da direita) do módulo [Split Data][split] à porta de entrada da direita de [Score Model][score-model].

    ![Ligar o módulo "Score Model" aos módulos "Train Model" e no "Split Data"](./media/create-experiment/connect-score-model.png)

1. Execute a experimentação e ver o resultado dos [modelo de pontuação] [ score-model] módulo clicando na porta de saída de [Score Model] [ score-model] e selecione  **Visualizar**. O resultado mostra os valores previstos para os preços e os valores conhecidos dos dados do teste.  

    ![Saída do módulo "Score Model"](./media/create-experiment/score-model-output.png)

1. Por fim, vamos testar a qualidade dos resultados. Selecione e arraste o módulo [Evaluate Model (Avaliar Modelo)][evaluate-model] para a tela da experimentação e ligue a saída do módulo [Score Model][score-model] à entrada da esquerda de [Evaluate Model][evaluate-model]. A experimentação final deve ter este aspeto:

    ![A experimentação final](./media/create-experiment/complete-linear-regression-experiment.png)

1. Execute a experimentação.

Para ver o resultado do módulo [Evaluate Model][evaluate-model], clique na porta de saída e, em seguida, selecione **Visualize**.

![Resultados da avaliação da experimentação](./media/create-experiment/evaluation-results.png)

As estatísticas seguintes são apresentadas para o nosso modelo:

- **Significa que o erro absoluto** (MAE): A média dos erros absolutos (um *erro* é a diferença entre o valor previsto e o valor real).
- **Média de raiz ao quadrado erro** (RMSE): A raiz quadrada da média dos erros ao quadrado das predições efetuadas no conjunto de dados de teste.
- **Erro relativo absoluto**: A média dos erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
- **Relativo ao quadrado erro**: A média dos erros ao quadrado relativos à diferença ao quadrado entre os valores reais e a média de todos os valores reais.
- **Coeficiente de determinação**: Também conhecido como o **valor de R ao quadrado**, esta é uma métrica de estatística que indica o quão bem um modelo é adequado para os dados.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. Um valor mais pequeno indica que as predições mais detalhadas correspondem aos valores reais. No **Coeficiente de determinação**, quanto mais próximo for um valor de outro (1.0), melhores serão as predições.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma experiência simples usando um conjunto de dados de exemplo. Para explorar o processo de criação e implantação de um modelo mais detalhadamente, avance para o tutorial de solução preditiva.

> [!div class="nextstepaction"]
> [Tutorial: Desenvolver uma solução preditiva no Studio](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
