---
title: 'Início rápido: criar um experimento de ciência de dados'
titleSuffix: Azure Machine Learning Studio (classic)
description: Este guia de início rápido do Machine Learning orienta você por um experimento de ciência de dados fácil. Vamos prever o preço de um carro através de um algoritmo de regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: c95954bbe96b947312a952ebaab4e6bfc2b2f5a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493285"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-azure-machine-learning-studio-classic"></a>Início rápido: criar seu primeiro experimento de ciência de dados no Azure Machine Learning Studio (clássico)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Neste guia de início rápido, você cria um experimento de aprendizado de máquina no [Azure Machine Learning Studio (clássico)](what-is-ml-studio.md) que prevê o preço de um carro com base em variáveis diferentes, como make e especificações técnicas.

Se você é novo no Machine Learning, a série de vídeos [ciência de dados para iniciantes](data-science-for-beginners-the-5-questions-data-science-answers.md) é uma excelente introdução ao aprendizado de máquina usando linguagem e conceitos cotidianos.

Este guia de início rápido segue o fluxo de trabalho padrão para um experimento:

1. **Criar um modelo**
    - [Obter os dados]
    - [Preparar os dados]
    - [Definir recursos]
1. **Preparar o modelo**
    - [Escolher e aplicar um algoritmo]
1. **Classificar e testar o modelo**
    - [Prever novos preços de automóvel]

[Obter os dados]: #get-the-data
[Preparar os dados]: #prepare-the-data
[Definir recursos]: #define-features
[Escolher e aplicar um algoritmo]: #choose-and-apply-an-algorithm
[Prever novos preços de automóvel]: #predict-new-automobile-prices

## <a name="get-the-data"></a>Obter os dados

A primeira coisa que você precisa no aprendizado de máquina são os dados.
Há vários conjuntos de dados de exemplo incluídos na versão clássica do estúdio que você pode usar, ou você pode importar dados de várias fontes. Neste exemplo, vamos utilizar o conjunto de dados de exemplo, **Automobile price data (Raw) (Dados de preços de automóveis (não processados))** , que está incluído na sua área de trabalho.
Este conjunto de dados inclui entradas para vários automóveis individuais, incluindo informações como a marca, o modelo, as especificações técnicas e o preço.

> [!TIP]
> Pode encontrar uma cópia de trabalho da experimentação seguinte na [Galeria de IA do Azure](https://gallery.azure.ai). Vá para **[seu primeiro experimento de ciência de dados – previsão de preço de automóvel](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** e clique em **abrir no estúdio** para baixar uma cópia do experimento em seu espaço de trabalho Machine Learning Studio (clássico).

Eis como obter o conjunto de dados na sua experimentação.

1. Crie um novo experimento clicando em **+ novo** na parte inferior da janela Machine Learning Studio (clássica). Selecione **experimento** >  **experimento em branco**.

1. É dado um nome predefinido à experimentação, que pode ver na parte superior da tela. Selecione este texto e mude o nome para algo significativo, como, por exemplo, **Previsão de preços de automóveis**. O nome não tem de ser exclusivo.

    ![Mude o nome da experimentação](./media/create-experiment/rename-experiment.png)

1. À esquerda da tela da experimentação existe uma paleta de conjuntos de dados e módulos. Digite **automóvel** na caixa de pesquisa na parte superior desta paleta para localizar o conjunto de dados denominado **Dados do preço automóvel (bruto)** . Arraste este conjunto de dados para a tela da experimentação.

    ![Localize o conjunto de um automóvel e arraste-o para a tela do experimento](./media/create-experiment/type-automobile.png)

Para ver a aparência deste dado, clique na porta de saída na parte inferior do conjunto de dados do automóvel e selecione **Visualizar**.

![Clique na porta de saída e selecione "Visualizar"](./media/create-experiment/select-visualize.png)

> [!TIP]
> Os conjuntos de dados e os módulos têm portas de entrada e de saída, representadas por pequenos círculos. As portas de entrada estão em cima e as de saída estão em baixo.
Para criar um fluxo de dados através da experimentação, ligue uma porta de saída de um módulo a uma porta de entrada de outro.
Pode, em qualquer altura, clicar na porta de saída de um conjunto de dados ou de um módulo para ver o aspeto dos dados nessa fase do fluxo de dados.

Nesse conjunto de linhas, cada linha representa um automóvel, e as variáveis associadas a cada automóvel aparecem como colunas. Vamos prever o preço na coluna da extrema direita (coluna 26, intitulado "Price") usando as variáveis para um automóvel específico.

![Exibir os dados do automóvel na janela de visualização de dados](./media/create-experiment/visualize-auto-data.png)

Feche a janela de visualização, clicando no "**x**" no canto superior direito.

## <a name="prepare-the-data"></a>Preparar os dados

Normalmente, um conjunto de dados requer alguns pré-processamentos antes de poder ser analisado. Poderá ter reparado nos valores em falta presentes nas colunas de várias linhas. Estes valores em falta têm de ser apagados para que o modelo possa analisar os dados corretamente. Removeremos as linhas que têm valores ausentes. Além disso, a coluna das **perdas normalizadas** tem uma grande proporção de valores em falta, pelo que recomendamos que exclua totalmente essa coluna do modelo.

> [!TIP]
> Apagar os valores em falta a partir dos dados de entrada é um pré-requisito para utilizar a maioria dos módulos.

Primeiro, adicionamos um módulo que remove completamente a coluna **normalized-perdas** . Em seguida, adicionamos outro módulo que remove qualquer linha que tenha dados ausentes.

1. Digite **selecionar colunas** na caixa de pesquisa na parte superior da paleta de módulo para localizar o módulo [selecionar colunas no conjunto][select-columns] de módulos. Em seguida, arraste-o para a tela do experimento. Este módulo permite-nos selecionar quais as colunas de dados que pretendemos incluir ou excluir no modelo.

1. Conecte a porta de saída do conjunto de **dados do preço de automóvel (bruto)** para a porta de entrada do selecionar colunas no conjunto.

    ![Adicionar o módulo "selecionar colunas no conjunto de conjuntos" à tela do experimento e conectá-lo](./media/create-experiment/type-select-columns.png)

1. Clique no módulo [selecionar colunas no conjunto][select-columns] de módulos e clique em **Iniciar seletor de coluna** no painel **Propriedades** .

   - À esquerda, clique em **Com regras**
   - Em **Começar Com**, clique em **Todas as colunas**. Essas regras direcionam as [colunas selecionadas no conjunto][select-columns] de informações para passar por todas as colunas (exceto as colunas que estamos prestes a excluir).
   - Nas listas pendentes, selecione **Excluir** e os **nomes das colunas** e, em seguida, clique dentro da caixa de texto. É apresentada uma lista de colunas. Selecione **normalized-losses**, que é adicionada à caixa de texto.
   - Clique no botão de marca de seleção (OK) para fechar o seletor de coluna (no canto inferior direito).

     ![Inicie o seletor de coluna e exclua a coluna "normalizado-perdas"](./media/create-experiment/launch-column-selector.png)

     Agora, o painel de propriedades de **Select Columns in Datase** indica que o módulo vai percorrer todas as colunas do conjunto de dados, exceto **normalized-losses**.

     ![O painel Propriedades mostra que a coluna "normalizado-perdas" é excluída](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > Pode adicionar um comentário a um módulo, fazendo duplo clique no módulo e introduzindo o texto. Isto pode ajudá-lo a ver rapidamente o que o módulo está a fazer na sua experimentação. Nesse caso, clique duas vezes no módulo [selecionar colunas no conjunto][select-columns] de módulos e digite o comentário "excluir perdas normalizadas".

     ![Clique duas vezes em um módulo para adicionar um comentário](./media/create-experiment/add-comment.png)

1. Arraste o módulo [limpar dados ausentes][clean-missing-data] para a tela do experimento e conecte-o ao módulo [selecionar colunas no conjunto de][select-columns] dados. No painel **Properties**, selecione **Remove entire row (Remover linha inteira)** , em **Cleaning mode (Modo de limpeza)** . Essas opções direcionam [dados ausentes limpos][clean-missing-data] para limpar os dados removendo as linhas que têm valores ausentes. Clique duas vezes no módulo e escreva o comentário "Remover linhas de valor em falta".

    ![Defina o modo de limpeza como "remover linha inteira" para o módulo "limpar dados ausentes"](./media/create-experiment/set-remove-entire-row.png)

1. Execute a experimentação clicando em **EXECUTAR** na parte inferior da página.

    Quando a execução da experimentação estiver concluída, todos os módulos têm uma marca de verificação a verde para indicar que foram concluídos com êxito. Repare também no estado **Execução terminada** no canto superior direito.

    ![Depois de executá-lo, o experimento deve ser semelhante a este](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Porque é que executámos a experimentação agora? Ao executar o experimento, as definições de coluna para nossos dados são transmitidas do DataSet, por meio do módulo [selecionar colunas no conjunto][select-columns] de dados e através do módulo [limpar data ausente][clean-missing-data] . Isso significa que todos os módulos que conectamos para [limpar dados ausentes][clean-missing-data] também terão essas mesmas informações.

Agora temos dados limpos. Se você quiser exibir o conjunto de dados limpo, clique na porta de saída à esquerda do módulo [limpar data ausente][clean-missing-data] e selecione **Visualizar**. Repare que a coluna **perdas normalizadas** já não está mais incluída e não existem valores em falta.

Agora que os dados foram apagados, estamos prontos para especificar quais as funcionalidades que vai utilizar no modelo preditivo.

## <a name="define-features"></a>Definir recursos

No Machine Learning, as *funcionalidades* são propriedades mensuráveis individuais de algo que lhe interessa. No nosso conjunto de dados, cada linha representa um automóvel e cada coluna é uma funcionalidade desse automóvel.

Encontrar um bom conjunto de funcionalidades para criar um modelo preditivo requer experimentação e conhecimentos sobre o problema que pretende resolver. Algumas funcionalidades são melhores para prever num destino do que outras. Alguns recursos têm uma correlação forte com outros recursos e podem ser removidos. Por exemplo, city-mpg e highway-mpg estão intimamente relacionados, pelo que podemos manter um e remover o outro sem afetar a predição significativamente.

Vamos criar um modelo que utiliza um subconjunto das funcionalidades no nosso conjunto de dados. Pode regressar mais tarde e selecionar funcionalidades diferentes, executar novamente a experimentação e ver se consegue obter melhores resultados. Contudo, para começar, vamos experimentar as seguintes funcionalidades:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Arraste outro módulo [selecionar colunas no conjunto de DataSet][select-columns] para a tela do experimento. Conecte a porta de saída à esquerda do módulo [limpar dados ausentes][clean-missing-data] para a entrada do módulo [selecionar colunas no DataSet][select-columns] .

    ![Conectar o módulo "selecionar colunas no conjunto de dados" para o módulo "limpar" os dados ausentes](./media/create-experiment/connect-clean-to-select.png)

1. Clique duas vezes no módulo e escreva "Selecionar as funcionalidades para predição".

1. Clique no painel **Iniciar seletor de colunas** no painel **Propriedades**.

1. Clique em **Com regras**.

1. Em **Begin With (Começar Em)** , clique em **No columns (Nenhuma coluna)** . Na linha do filtro, selecione **Include (Incluir)** e **column names (nomes das colunas)** e selecione a lista de nomes de colunas na caixa de texto. Esse filtro direciona o módulo para não passar por nenhuma coluna (recursos), exceto aqueles que especificamos.

1. Clique no botão de marca de verificação (OK).

    ![Selecione as colunas (recursos) a serem incluídas na previsão](./media/create-experiment/select-columns-to-include.png)

Esse módulo produz um conjunto de informações filtrado contendo apenas os recursos que desejamos passar para o algoritmo de aprendizado que usaremos na próxima etapa. Mais tarde, pode voltar e tentar novamente com uma seleção de funcionalidades diferente.

## <a name="choose-and-apply-an-algorithm"></a>Escolher e aplicar um algoritmo

Agora que os dados estão prontos, construir um modelo preditivo consiste em formar e testar. Utilizaremos os nossos dados para preparar o modelo e, em seguida, vamos testá-lo para ver com que exatidão consegue prever os preços.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Classficação* e *regrssão* são dois tipos de algoritmos de machine learning supervisionados. A classificação prevê uma resposta a partir de um conjunto definido de categorias, tais como uma cor (vermelho, azul ou verde). A regressão é utilizada para prever um número.

Uma vez que queremos prever o preço, que é um número, vamos utilizar um algoritmo de regressão. Para este exemplo, usaremos um modelo de *regressão linear* .


Para preparar o modelo, damos-lhe um conjunto de dados que incluem o preço. O modelo analisa os dados e procura correlações entre as características e o preço de um automóvel. Depois, testamos o modelo. Damos-lhe um conjunto de características de automóveis com que estamos familiarizados e vemos a exatidão com que o modelo consegue prever o preço sabido.

Vamos utilizar os dados quer para preparar o modelo, quer para testá-lo, dividindo-os em conjuntos de dados de preparação e teste separados.

1. Selecione e arraste o módulo [dividir dados][split] para a tela do experimento e conecte-o ao último módulo [selecionar colunas no conjunto de][select-columns] dados.

1. Clique no módulo [dividir dados][split] para selecioná-lo. Localize **Fraction of rows in the first output dataset (Fração de linhas no primeiro conjunto de dados de saída)** (no painel **Properties (Propriedades)** , no lado direito da tela) e defina-a como 0,75. Desta forma, vamos utilizar 75% dos dados para preparar o modelo e retenha 25% para fins de teste.

    ![Defina a fração de divisão do módulo "dividir dados" como 0,75](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > Ao alterar o parâmetro **Seed aleatório**, pode produzir diferentes amostras aleatórias para formação e testar. Este parâmetro controla a propagação do gerador de número pseudo-aleatório.

1. Execute a experimentação. Quando o experimento é executado, o módulo [selecionar colunas no conjunto de][select-columns] [dados e dividir os][split] módulos passa definições de coluna para os módulos que adicionaremos a seguir.  

1. Para selecionar o algoritmo do Learning, expanda a categoria **Machine Learning** na paleta do módulo para a esquerda da tela e, em seguida, expanda **Inicializar modelo**. Isto apresenta várias categorias de módulos que podem ser utilizadas para inicializar algoritmos do Machine Learning. Para este experimento, selecione o módulo [regressão linear][linear-regression] na categoria **regressão** e arraste-o para a tela do experimento. (Para encontrar o módulo, também pode escrever “linear regression” na caixa Pesquisa da paleta.)

1. Localize e arraste o módulo [modelo de treinamento][train-model] até a tela do experimento. Conecte a saída do módulo [regressão linear][linear-regression] à entrada à esquerda do módulo [modelo de treinamento][train-model] e conecte a saída de dados de treinamento (porta esquerda) do módulo [dividir dados][split] à entrada à direita do módulo [modelo][train-model] de treinamento.

    ![Conectar o módulo "modelo de treinamento" aos módulos "regressão linear" e "dividir dados"](./media/create-experiment/connect-train-model.png)

1. Clique no módulo [modelo de treinamento][train-model] , clique em **Iniciar seletor de coluna** no painel **Propriedades** e selecione a coluna **preço** . **Price** é o valor que nosso modelo pretende prever.

    Para selecionar a coluna **price** no seletor de colunas, mova-a da lista **Available columns (Colunas disponíveis)** para a lista **Selected columns (Colunas selecionadas)** .

    ![Selecione a coluna preço para o módulo "modelo de treinamento"](./media/create-experiment/select-price-column.png)

1. Execute a experimentação.

Temos agora um modelo de regressão preparado que pode ser utilizado para classificar dados de automóveis novos e fazer predições de preços.

![Após a execução, o experimento agora deve ser semelhante a este](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Prever novos preços de automóvel

Agora que experimentámos o modelo, utilizando 75% dos nossos dados, podemos utilizá-lo para pontuar os outros 25% por cento dos dados para ver quão bem funciona o nosso modelo.

1. Localize e arraste o módulo [modelo de Pontuação][score-model] para a tela do experimento. Conecte a saída do módulo [modelo de treinamento][train-model] à porta de entrada à esquerda do [modelo de Pontuação][score-model]. Conecte a saída de dados de teste (porta direita) do módulo [dividir dados][split] à porta de entrada à direita do [modelo de Pontuação][score-model].

    ![Conectar o módulo "modelo de Pontuação" aos módulos "modelo de treinamento" e "dividir dados"](./media/create-experiment/connect-score-model.png)

1. Execute o experimento e exiba a saída do módulo [modelo de Pontuação][score-model] clicando na porta de saída do [modelo de Pontuação][score-model] e selecione **Visualizar**. O resultado mostra os valores previstos para os preços e os valores conhecidos dos dados do teste.  

    ![Saída do módulo "modelo de Pontuação"](./media/create-experiment/score-model-output.png)

1. Por fim, vamos testar a qualidade dos resultados. Selecione e arraste o módulo [modelo de avaliação][evaluate-model] para a tela do experimento e conecte a saída do módulo modelo de [Pontuação][score-model] à entrada à esquerda do modelo de [avaliação][evaluate-model]. A experimentação final deve ter este aspeto:

    ![O experimento final](./media/create-experiment/complete-linear-regression-experiment.png)

1. Execute a experimentação.

Para exibir a saída do módulo [modelo de avaliação][evaluate-model] , clique na porta de saída e selecione **Visualizar**.

![Resultados da avaliação para o experimento](./media/create-experiment/evaluation-results.png)

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

Neste guia de início rápido, você criou um experimento simples usando um conjunto de exemplo. Para explorar o processo de criação e implantação de um modelo com mais detalhes, prossiga para o tutorial de solução preditiva.

> [!div class="nextstepaction"]
> [Tutorial: desenvolver uma solução preditiva no estúdio (clássico)](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
