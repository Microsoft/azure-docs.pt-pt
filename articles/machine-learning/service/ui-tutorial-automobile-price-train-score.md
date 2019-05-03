---
title: 'Tutorial: Prever o preço de automóvel com a interface visual'
titleSuffix: Azure Machine Learning service
description: Saiba como dar formação e classificação e implementar um modelo de aprendizagem automática através de uma interface visual de arrastar e soltar. Este tutorial é a parte de uma série de duas partes sobre prever preços de automóveis com regressão linear.
author: peterlu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 1dc905b2ac89667dcb5800b0f539cb3e9f3acdcd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026873"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Tutorial: Prever o preço de automóvel com a interface visual

Neste tutorial, fazer uma observação expandida ao desenvolver uma solução preditiva na interface visual de serviço do Azure Machine Learning. No final deste tutorial, terá uma solução que pode prever o preço de qualquer carro com base nas especificações técnicas que enviá-lo.

Este tutorial [continua a partir do início rápido](ui-quickstart-run-experiment.md) e é **parte um de uma série de tutoriais de duas partes**. No entanto, não tem de concluir o início rápido antes de iniciar.

Parte um da série do tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar e limpar dados (as mesmas etapas como o início rápido)
> * Preparar um modelo de machine learning
> * Pontuar e avaliar um modelo

Na [parte dois](ui-tutorial-automobile-price-deploy.md) da série do tutorial, irá aprender a implementar o seu modelo preditivo como um serviço web do Azure.

> [!NOTE]
> Uma versão concluída deste tutorial está disponível como uma experimentação de exemplo.
> A partir da página de experimentações, aceda a **adicionar novo** > **exemplo 1 - regressão: Preço de automóveis Prediction(Basic)**

> [!VIDEO https://www.youtube.com/embed/VDKpDNX96aA]


## <a name="create-a-workspace"></a>Criar uma área de trabalho

Se tiver uma área de trabalho do serviço do Azure Machine Learning, avance para o [próxima seção](#open-the-visual-interface-webpage). Caso contrário, crie uma agora.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Abra a página Web de visual interface

1. Abra a área de trabalho do [portal do Azure](https://portal.azure.com/).  

1. Na área de trabalho, selecione **Visual interface**.  Em seguida, selecione **interface visual do lançamento**.  

    ![Captura de ecrã do portal do Azure mostra como acessar a interface Visual a partir de uma área de trabalho do serviço de Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    Abre a página Web de interface numa nova página do browser.  

## <a name="import-and-clean-your-data"></a>Importar e limpar os dados

A primeira coisa que precisa é limpar dados. Se concluiu o guia de introdução, é possível reutilizar a sua experimentação preparação de dados aqui. Se ainda não concluiu o guia de introdução, ignore a próxima seção e [iniciar a partir de uma nova experimentação](#start-from-a-new-experiment).

### <a name="reuse-the-quickstart-experiment"></a>Reutilizar a experimentação de início rápido

1. Abra a sua experiência de início rápido.

1. Selecione **guardar como** na parte inferior da janela.

1. Dê um nome novo na caixa de diálogo pop-up que aparece.

    ![Captura de ecrã que mostra como mudar o nome de uma experimentação para "Tutorial – prever Automobile Price"](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. A experimentação deverá agora ser semelhante ao seguinte:

    ![Captura de ecrã que mostra o estado esperado da experimentação. O conjunto de dados de automóvel liga-se para o módulo de selecionar colunas que se liga a apagar dados em falta](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

Se reutilizados com êxito a experiência de início rápido, ignore a secção seguinte para começar [seu modelo de formação](#train-the-model).

### <a name="start-from-a-new-experiment"></a>Iniciar a partir de uma nova experimentação

Se não concluir o início rápido, siga estes passos para criar rapidamente uma nova experimentação que importa e limpa o conjunto de dados de automóvel.

1. Criar uma nova experimentação selecionando **+ novo** na parte inferior da janela de visual interface.

1. Selecione **experimentação** >  **em branco experimentação**.

1. Selecione o nome predefinido da experimentação **"as minhas experiências criado em...** "na parte superior da tela e renomeá-lo para algo significativo. Por exemplo, **predição do preço automóvel**. O nome não tem de ser exclusivo.

1. À esquerda da tela da experimentação existe uma paleta de conjuntos de dados e módulos. Para localizar os módulos, utilize a caixa de pesquisa na parte superior da paleta do módulo. Tipo **automóvel** na caixa de pesquisa para encontrar o conjunto de dados denominado **dados de preço do automóvel (bruto)**. Arraste este conjunto de dados para a tela da experimentação.

    ![Captura de ecrã como encontrar o conjunto de dados de preços de automóveis](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    Agora que tem os dados, pode adicionar um módulo que remove o **normalized-losses** coluna completamente. Em seguida, adicione outro módulo que remove todas as linhas que tenham dados em falta.

1. Tipo **selecionar colunas** na caixa de pesquisa para localizar o **Select Columns in Dataset** módulo. Em seguida, arraste-o para a tela da experimentação. Este módulo permite-lhe selecionar as colunas de dados que pretende incluir ou excluir no modelo.

1. Ligue a porta de saída a **dados de preço do automóvel (bruto)** conjunto de dados para a porta de entrada de selecionar colunas no conjunto de dados.

    ![Animados gif que mostra como ligar o módulo de Automobile Price Data para o módulo de selecionar colunas](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Selecione selecionar colunas no módulo de conjunto de dados e selecione **iniciar Seletor de colunas** no **propriedades** painel.

   1. No lado esquerdo, selecione **com regras**

   1. Junto a **começar com**, selecione **todas as colunas**. Estas regras em direto **Select Columns in Dataset** a passar por todas as colunas (exceto as que estamos prestes a excluir).

   1. A partir de listas pendentes, selecione **excluir** e **os nomes das colunas**e, em seguida, escreva **normalized-losses** dentro da caixa de texto.

   1. Selecione o botão OK para fechar o Seletor de colunas (no canto inferior direito).

     Agora, o painel de propriedades de **Select Columns in Datase** indica que o módulo vai percorrer todas as colunas do conjunto de dados, exceto **normalized-losses**.

1. Adicionar um comentário para o **Select Columns in Dataset** módulo clicar duas vezes o módulo e introduzindo o "Excluir normalized losses.". Isso pode ajudá-lo a ver, rapidamente, o que o módulo está a fazer na sua experimentação.

    ![Captura de ecrã que mostra o configuração correta do módulo selecionar colunas](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. Tipo **Clean** na caixa de pesquisa para localizar o **Clean Missing Data** módulo. Arrastar o **apagar dados em falta** módulo para a experimentação tela e ligá-la para o **Select Columns in Dataset** módulo.

1. No painel **Properties**, selecione **Remove entire row (Remover linha inteira)**, em **Cleaning mode (Modo de limpeza)**. Estas opções direct **Clean Missing Data** para limpar os dados ao remover as linhas que tenham valores em falta. Clique duas vezes no módulo e escreva o comentário "Remover linhas de valor em falta".

![Captura de ecrã que mostra o configuração correta do módulo apagar dados em falta](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

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

    Agora, a experimentação deverá ser semelhante.
    ![Captura de ecrã que mostra a configuração correta da experimentação depois de adicionar o módulo preparar modelo.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Execute a experimentação de preparação

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Pontuar e avaliar o modelo

Agora que Treinou o modelo usando 70 por cento dos seus dados, pode usá-lo para pontuar os outros 30 por cento dos dados para ver quão bem as suas funções de modelo.

1. Tipo **modelo de pontuação** na caixa de pesquisa para localizar o **Score Model** módulo e arraste o módulo para a tela da experimentação. Ligue a saída dos **Train Model** porta de entrada do módulo para a esquerda **Score Model**. Ligue a saída de dados teste (porta direita) a do **Split Data** porta de entrada do módulo para a direita **Score Model**.

1. Tipo **avaliar** na caixa de pesquisa para localizar o **Evaluate Model** e arraste o it módulo para a tela da experimentação. Ligue a saída dos **modelo de pontuação** módulo à entrada da esquerda de **Evaluate Model**. A experimentação final deve ter este aspeto:

    ![Captura de ecrã que mostra a configuração correta final da experimentação.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Execute a experimentação com o mesmo destino de computação utilizado anteriormente.

1. Ver o resultado do **modelo de pontuação** módulo ao selecionar a porta de saída **modelo de pontuação** e selecione **Visualize**. O resultado mostra os valores previstos para os preços e os valores conhecidos dos dados do teste.

    ![Captura de ecrã da visualização de saída Realce a coluna "Classificada a etiqueta"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Para ver o resultado do módulo avaliar modelo, selecione a porta de saída e, em seguida, selecione o visualizar.

    ![Captura de ecrã que mostra os resultados da avaliação para a experimentação final.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

As estatísticas seguintes são apresentadas para o modelo:

* **Significa que o erro absoluto (MAE)**: A média dos erros absolutos (um erro é a diferença entre o valor previsto e o valor real).
* **Média de raiz ao quadrado erro (RMSE)**: A raiz quadrada da média dos erros ao quadrado das predições efetuadas no conjunto de dados de teste.
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

* Reutilizar a experimentação que criou no guia de introdução
* Preparar os dados
* Dar formação sobre o modelo
* Pontuar e avaliar o modelo

Na segunda parte, aprenderá a implementar o seu modelo como um serviço web do Azure.

> [!div class="nextstepaction"]
> [Continuar a implementação de modelos](ui-tutorial-automobile-price-deploy.md)
