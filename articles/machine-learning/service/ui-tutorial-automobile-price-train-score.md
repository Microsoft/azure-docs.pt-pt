---
title: 'Tutorial: Prever o preço de automóvel com a interface visual'
titleSuffix: Azure Machine Learning service
description: Saiba como dar formação e classificação e implementar um modelo de aprendizagem automática através de uma interface visual de arrastar e soltar. Este tutorial é a parte de uma série de duas partes sobre prever preços de automóveis com regressão linear.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 21f5a2d93b708e93f124bd44177bb7852dfbd86a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720496"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Tutorial: Prever o preço de automóvel com a interface visual

Neste tutorial, fazer uma observação expandida ao desenvolver uma solução preditiva na interface visual de serviço do Azure Machine Learning. No final deste tutorial, terá uma solução que pode prever o preço de qualquer carro com base nas especificações técnicas que enviá-lo.

Na primeira parte do tutorial ficará a saber como:

> [!div class="checklist"]
> * Importar e limpar dados
> * Preparar um modelo de machine learning
> * Pontuar e avaliar um modelo

Na [parte dois](ui-tutorial-automobile-price-deploy.md) do tutorial, irá aprender a implementar o seu modelo preditivo como um serviço web do Azure.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

Uma versão concluída deste tutorial está disponível como uma experimentação de exemplo.

Encontrá-la, a partir do **página de experimentações**, selecione **adicionar novo**, em seguida, selecione o **exemplo 1 - regressão: Prediction(Basic) de preço de automóveis** experimentar.

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Se tiver uma área de trabalho do serviço do Azure Machine Learning, avance para o [próxima seção](#open-the-visual-interface-webpage).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Abra a página Web de visual interface

1. Abra a área de trabalho do [portal do Azure](https://portal.azure.com/).

1. Na área de trabalho, selecione **Visual interface**. Em seguida, selecione **interface visual do lançamento**. 

    ![Captura de ecrã do portal do Azure mostra como acessar a interface Visual a partir de uma área de trabalho do serviço de Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

## <a name="create-your-first-experiment"></a>Criar a sua primeira experimentação

A ferramenta de visual interface fornece um ponto de partida interativo e visual para criar modelos de Análise Preditiva. Módulos de conjuntos de dados e análise de arrastar e largar interativo na tela e ligá-los em conjunto para criar uma *experimentar*.

1. Criar uma nova experimentação selecionando **+ novo** na parte inferior da janela de visual interface.

    ![Adicionar nova experimentação](./media/ui-tutorial-automobile-price-train-score/add-new.png)

1. Selecione **em branco experimentação**.

1. Selecione o nome predefinido da experimentação **"as minhas experiências criado em...** "na parte superior da tela e renomeá-lo para algo significativo. Por exemplo, **predição do preço automóvel**. O nome não tem de ser exclusivo.

## <a name="add-data"></a>Adicionar dados

A primeira coisa que precisa para machine learning é os dados. Existem vários conjuntos de dados exemplo incluídos nesta interface que pode utilizar. Também pode importar dados a partir de origens existentes. Neste tutorial, utilize o conjunto de dados de exemplo **dados de preço do automóvel (bruto)** . 

1. À esquerda da tela da experimentação existe uma paleta de conjuntos de dados e módulos. Selecione **conjuntos de dados guardado** , em seguida, selecione **exemplos** para ver os conjuntos de dados de exemplo disponíveis.

1. Selecione o conjunto de dados **dados de preço do automóvel (brutos)** e arraste-o para a tela.

   ![Arraste os dados à tela](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

## <a name="select-columns"></a>Selecionar colunas

Selecione as colunas de dados para trabalhar com. Para começar, configure o módulo para mostrar todas as colunas disponíveis.

> [!TIP]
> Se souber o nome dos dados ou módulo que pretende, use a barra de pesquisa na parte superior da paleta do, para o encontrar rapidamente. O resto do tutorial, irá utilizar este atalho.


1. Tipo **selecionar** na caixa de pesquisa para localizar o **Select Columns in Dataset** módulo.

1. Clique e arraste a **Select Columns in Dataset** para a tela. Remova o módulo abaixo o conjunto de dados que adicionou anteriormente.

1. Ligar o conjunto de dados para o **Select Columns in Dataset**: clique na porta de saída do conjunto de dados, arraste para a porta de entrada de **selecionar colunas no conjunto de dados**, em seguida, largue o botão do mouse. O módulo e o conjunto de dados permanecem conectados, mesmo se move qualquer um na tela.

    > [!TIP]
    > Os conjuntos de dados e os módulos têm portas de entrada e de saída, representadas por pequenos círculos. As portas de entrada estão em cima e as de saída estão em baixo. Criar um fluxo de dados através da experimentação quando se liga a porta de saída de um módulo a uma porta de entrada de outro.
    >

    ![Ligue-se módulos](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    O ponto de exclamação vermelho indica que ainda não o defina as propriedades para o módulo ainda.

1. Selecione o **Select Columns in Dataset** módulo.

1. Na **propriedades** painel à direita da tela, selecione **editar colunas**.

    Na **selecionar colunas** caixa de diálogo, selecione **todas as colunas** e incluir **todas as funcionalidades**. A caixa de diálogo deve ter este aspeto:

     ![Seletor de colunas](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. No canto inferior direito, selecione **OK** para fechar o Seletor de colunas.

## <a name="run-the-experiment"></a>Executar a experimentação

Em qualquer altura, clique na porta de saída de um conjunto de dados ou o módulo para ver o que o aspeto dos dados nesse ponto no fluxo de dados. Se o **Visualize** opção está desativada, tem primeiro de executar a experimentação.

Uma experimentação é executada num destino de computação, o que é um recurso de computação que está ligado à área de trabalho. Depois de criar um destino de computação, pode ser reutilizado em execuções futuras.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Depois do destino de computação estiver disponível, a experimentação é executada. Quando a execução estiver concluída, aparece uma marca de verificação verde em cada módulo.


## <a name="preview-the-data"></a>Os dados de pré-visualização

Agora que tem de executar a experimentação inicial, pode visualizar os dados para saber mais sobre o conjunto de dados que tem que trabalhar com.

1. Selecione a porta de saída na parte inferior a **Select Columns in Dataset** , em seguida, selecione **Visualize**.

1. Clique em colunas diferentes na janela de dados para ver informações sobre essa coluna.

    Este conjunto de dados, cada linha representa um automóvel e as variáveis associadas a cada um destes aparecem como colunas. Existem 205 linhas e 26 colunas este conjunto de dados.

     Sempre que clicar numa coluna de dados, o **estatísticas** informações e **visualização** imagem dessa coluna é apresentada à esquerda. Por exemplo, quando clica em **NÚM. de portas** vir tem 2 valores exclusivos e 2 valores em falta. Desloque para baixo para ver os valores: duas e quatro portas.

     ![Os dados de pré-visualização](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. Clique em cada coluna para saber mais sobre o conjunto de dados e pensar se estas colunas serão úteis para prever o preço de um automóvel.

## <a name="prepare-data"></a>Preparar dados

Normalmente, um conjunto de dados requer alguns pré-processamentos antes de poder ser analisado. Poderá ter reparado alguns valores em falta ao visualizar o conjunto de dados. Estes valores em falta têm de ser apagados para que o modelo possa analisar os dados corretamente. Irá remover quaisquer linhas que tenham valores em falta. Além disso, o **normalized-losses** coluna tem uma grande proporção de valores em falta, portanto, vou excluir essa coluna do modelo completamente.

> [!TIP]
> Apagar os valores em falta a partir dos dados de entrada é um pré-requisito para utilizar a maioria dos módulos.

### <a name="remove-column"></a>Remover coluna

Primeiro, remova os **normalized-losses** coluna completamente.

1. Selecione o **Select Columns in Dataset** módulo.

1. Na **propriedades** painel à direita da tela, selecione **editar colunas**.

    * Deixe **com as regras** e **todas as colunas** selecionado.

    * Nas listas pendentes, selecione **Excluir** e os **nomes das colunas** e, em seguida, clique dentro da caixa de texto. Tipo **normalized-losses**.

    * No canto inferior direito, selecione **OK** para fechar o Seletor de colunas.

    ![Excluir uma coluna](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Agora o painel de propriedades para selecionar colunas no conjunto de dados indica que este irá percorrer todas as colunas do conjunto de dados, exceto **normalized-losses**.
        
    O painel de propriedades mostra que o **normalized-losses** excluir a coluna.
        
    ![Painel de propriedades](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    Pode adicionar um comentário a um módulo, fazendo duplo clique no módulo e introduzindo o texto. Isto pode ajudá-lo a ver rapidamente o que o módulo está a fazer na sua experimentação. 

1. Clique duas vezes o **Select Columns in Dataset** módulo e escreva o comentário "excluem perdas normalizadas." 
    
    Depois de escrever o comentário, clique em fora do módulo. É apresentada uma seta para baixo mostrar que o módulo contenha um comentário.

1. Clique na seta para baixo para apresentar o comentário.

    O módulo mostra agora uma seta para cima para ocultar o comentário.
        
    ![Comentários](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Apagar dados em falta

Quando preparar um modelo, terá de fazer algo sobre os dados que estão em falta. Neste caso, adicionará um módulo para remover todas as linhas restantes que tenham dados em falta.

1. Tipo **Clean** na caixa de pesquisa para localizar o **Clean Missing Data** módulo.

1. Arrastar o **apagar dados em falta** módulo para a experimentação tela e ligá-la para o **Select Columns in Dataset** módulo. 

1. No painel de propriedades, selecione **remover toda a linha** sob **modo de limpeza**.

    Estas opções direct **Clean Missing Data** para limpar os dados ao remover as linhas que tenham valores em falta.

1. Clique duas vezes no módulo e escreva o comentário "Remover linhas de valor em falta".
 
    ![Remover linhas](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    A experimentação deverá agora ser semelhante ao seguinte:
    
    ![Selecionar coluna](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="visualize-the-results"></a>Visualizar os resultados

Uma vez que as alterações que efetuou para os módulos na sua experimentação, o estado foi alterado para "No rascunho".  Para visualizar os novos dados limpos, primeiro tem de executar novamente a experimentação.

1. Selecione **executar** na parte inferior para executar a experimentação.

    Desta vez, pode reutilizar o destino de computação que criou anteriormente.

1. Selecione **executar** na caixa de diálogo.

   ![Execute experimentação](./media/ui-tutorial-automobile-price-train-score/select-compute.png)

1. Quando a execução for concluída, clique com botão direito sobre a **Clean Missing Data** módulo para visualizar os novos dados limpos.

    ![Visualizar dados limpos](./media/ui-tutorial-automobile-price-train-score/visualize-cleaned.png)

1. Clique em colunas diferentes na janela de dados limpo para ver como os dados foram alterados.

    ![Visualizar dados limpos](media/ui-tutorial-automobile-price-train-score/visualize-result.png)

    Existem agora 193 linhas e colunas de 25.

    Quando clica em **NÚM. de portas** verá que ele ainda tem 2 valores exclusivos, mas agora tem 0 valor em falta. Clique no resto das colunas para ver que existem não existem valores em falta no conjunto de dados. 

## <a name="train-the-model"></a>Dar formação sobre o modelo

Agora que os dados estão prontos, pode construir um modelo preditivo. Usará seus dados para preparar o modelo. Em seguida, irá testar para ver com exatidão é capaz de prever os preços.

**Classficação** e **regrssão** são dois tipos de algoritmos de machine learning supervisionados. **Classificação** prevê uma resposta a partir de um conjunto definido de categorias, tais como uma cor (vermelho, azul ou verde). **Regressão** é utilizado para prever um número.

Uma vez que pretende prever o preço, que é um número, pode usar um algoritmo de regressão. Neste exemplo, vai utilizar um modelo de regressão linear.

Prepare o modelo ao dar a ele um conjunto de dados que incluem o preço. O modelo analisa os dados e procura correlações entre os recursos de um automóvel e seu preço. Em seguida, testar o modelo ao dar a ele um conjunto de recursos para automóveis está familiarizado com e ver o quão próximo vem o modelo para prever o preço sabido.

Utilize os seus dados para o modelo de formação e testá-lo, dividindo os dados em preparação e teste de conjuntos de dados separados.

1. Tipo **dividir dados** na caixa de pesquisa para localizar o **Split Data** módulo e ligá-la para a porta à esquerda do **Clean Missing Data** módulo.

1. Selecione o **Split Data** módulo quais se ligou para selecioná-lo. No painel de propriedades, defina a fração de linhas no primeiro conjunto de dados de saída 0,7. Dessa forma, vamos utilizar 70 por cento dos dados para preparar o modelo e retenha 30 por cento para fins de teste.

    ![Captura de ecrã que mostra a configuração correta do painel de propriedades. Os valores de "Split Data" deve ser "Dividir linhas", 0,7, aleatória dividida, 0, False.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Clique duas vezes o **Split Data** e escreva o comentário "Dividir o conjunto de dados para formação set(0.3) set(0.7) e teste"

1. Para selecionar o algoritmo de aprendizagem, desmarque a caixa de pesquisa de paleta do módulo.

1. Expanda a **Machine Learning** , em seguida, expanda **inicializar modelo**. Isto apresenta várias categorias de módulos que podem ser utilizadas para inicializar algoritmos do Machine Learning.

1. Nesta experimentação, selecione **regressão** > **regressão Linear** e arraste-o para a tela da experimentação.

    ![Captura de ecrã que mostra a configuração correta do painel de propriedades. Os valores de "Split Data" deve ser "Dividir linhas", 0,7, aleatória dividida, 0, False.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Localize e arraste a **Train Model** módulo para a tela da experimentação. Ligue a saída do módulo de regressão Linear à entrada da esquerda do módulo preparar modelo e ligue a saída de dados de formação (porta esquerda) dos **Split Data** módulo para entrada da direita o **Train Model**módulo.

    ![Captura de ecrã que mostra a configuração correta do módulo preparar modelo. O módulo de regressão Linear que se conecta ao módulo do esquerda porta de Train Model e o módulo de dividir dados liga-se à direita porta de preparar modelo](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Selecione o **Train Model** módulo. No painel de propriedades, selecione iniciar Seletor de colunas e, em seguida, escreva **preço** junto a **incluem nomes de coluna**. Preço é o valor que o seu modelo irá prever

    ![Captura de ecrã que mostra a configuração correta para o módulo de Seletor de coluna. Com as regras > incluem nomes de coluna > "preço"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    A experimentação deverá ter este aspeto:

    ![Captura de ecrã que mostra a configuração correta da experimentação depois de adicionar o módulo preparar modelo.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Execute a experimentação de preparação

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Pontuar e avaliar o modelo

Agora que Treinou o modelo usando 70 por cento dos seus dados, pode usá-lo para pontuar os outros 30 por cento dos dados para ver quão bem as suas funções de modelo.

1. Tipo **modelo de pontuação** na caixa de pesquisa para localizar o **Score Model** módulo e arraste o módulo para a tela da experimentação. Ligue a saída dos **Train Model** porta de entrada do módulo para a esquerda **Score Model**. Ligue a saída de dados teste (porta direita) a do **Split Data** porta de entrada do módulo para a direita **Score Model**.

1. Tipo **avaliar** na caixa de pesquisa para localizar o **Evaluate Model** e arraste o módulo para a tela da experimentação. Ligue a saída dos **modelo de pontuação** módulo à entrada da esquerda de **Evaluate Model**. A experimentação final deve ter este aspeto:

    ![Captura de ecrã que mostra a configuração correta final da experimentação.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Execute a experimentação com o mesmo destino de computação utilizado anteriormente.

1. Ver o resultado do **modelo de pontuação** módulo ao selecionar a porta de saída **modelo de pontuação** e selecione **Visualize**. O resultado mostra os valores previstos para os preços e os valores conhecidos dos dados do teste.

    ![Captura de ecrã da visualização de saída Realce a coluna "Classificada a etiqueta"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Para ver o resultado do módulo avaliar modelo, selecione a porta de saída e, em seguida, selecione o visualizar.

    ![Captura de ecrã que mostra os resultados da avaliação para a experimentação final.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

As estatísticas seguintes são apresentadas para o modelo:

* **Significa que o erro absoluto (MAE)** : A média dos erros absolutos (um erro é a diferença entre o valor previsto e o valor real).
* **Média de raiz ao quadrado erro (RMSE)** : A raiz quadrada da média dos erros ao quadrado das predições efetuadas no conjunto de dados de teste.
* **Erro relativo absoluto**: A média dos erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Relativo ao quadrado erro**: A média dos erros ao quadrado relativos à diferença ao quadrado entre os valores reais e a média de todos os valores reais.
* **Coeficiente de determinação**: Também conhecido como o R ao quadrado valor, esta é uma métrica de estatística que indica o quão bem um modelo é adequado para os dados.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. Um valor mais pequeno indica que as predições mais detalhadas correspondem aos valores reais. Para o coeficiente de determinação, quanto mais próximo seu valor é para um (1.0), melhores predições.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Gerir as experimentações na área de trabalho do serviço Azure Machine Learning

As experimentações que cria na interface do visual podem ser geridas a partir da área de trabalho do serviço do Azure Machine Learning. Utilize a área de trabalho para ver informações mais detalhadas, tais como indivíduos experimentação é executada, os registos de diagnóstico, gráficos de execução e muito mais.

1. Abra a área de trabalho do [portal do Azure](https://portal.azure.com/).  

1. Na área de trabalho, selecione **experimentações**. Em seguida, selecione a experimentação que criou.

    ![Captura de ecrã que mostra como navegar para experimentações no portal do Azure](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    Nesta página, verá uma descrição geral da experimentação e suas execuções mais recente.

    ![Descrição geral do captura de ecrã que mostra estatísticas de experimentação no portal do Azure](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Selecione um número de execuções para ver mais detalhes sobre uma execução específica.

    ![Execute o relatório de detalhes de captura de ecrã](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    Execute o relatório é atualizado em tempo real. Se utilizou uma **executar Script do Python** módulo na sua experimentação, pode especificar os logs de script para a saída no **registos** separador.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Na primeira parte deste tutorial, concluiu estes passos:

* Criar uma experimentação
* Preparar os dados
* Dar formação sobre o modelo
* Pontuar e avaliar o modelo

Na segunda parte, aprenderá a implementar o seu modelo como um serviço web do Azure.

> [!div class="nextstepaction"]
> [Continuar a implementação de modelos](ui-tutorial-automobile-price-deploy.md)
