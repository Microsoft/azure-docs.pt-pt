---
title: 'Início rápido: Preparar e visualize os dados sem escrever código'
titleSuffix: Azure Machine Learning service
description: Crie uma experimentação do machine learning para preparar e visualize os seus dados com uma interface do usuário de arrastar e soltar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 07b86138a95853673b5d54e272b40af41d58f418
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475956"
---
# <a name="quickstart-prepare-and-visualize-data-without-writing-code-in-azure-machine-learning"></a>Início rápido: Preparar e visualize os dados sem escrever código no Azure Machine Learning

Prepare e visualize os seus dados na interface visual de arrastar e soltar (pré-visualização) para o Azure Machine Learning. Os dados que usará incluem entradas para vários automóveis individuais, incluindo informações como a marca, modelo, as especificações técnicas e preço. Depois de concluir este início rápido, estará pronto para utilizar estes dados para prever o preço de um automóvel. 

Antes de preparar um modelo de machine learning, terá de compreender e preparar os dados.  Neste início rápido irá:

- Criar a sua primeira experiência para adicionar e pré-visualizar dados
- Preparar os dados ao remover valores em falta
- Executar a experimentação
- Visualizar os dados resultantes

Se é novato Machine learning, a série de vídeos [ciência de dados para principiantes](https://docs.microsoft.com/azure/machine-learning/studio/data-science-for-beginners-the-5-questions-data-science-answers) é uma excelente introdução ao machine learning.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

### <a name="create-a-workspace"></a>Criar uma área de trabalho

Se tiver uma área de trabalho do serviço do Azure Machine Learning, avance para o [próxima seção](#start). Caso contrário, crie uma agora.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="start"></a> Abra a página Web de visual interface

1. Abra a área de trabalho do [portal do Azure](https://portal.azure.com/).  

1. Na área de trabalho, selecione **Visual interface**.  Em seguida, selecione **interface visual do lançamento**.  
 
    ![Iniciar a visual interface](./media/ui-quickstart-run-experiment/launch-ui.png)

    Abre a página Web de interface numa nova página do browser.  

## <a name="create-your-first-experiment"></a>Criar a sua primeira experimentação

A ferramenta de visual interface fornece um ponto de partida interativo e visual para facilmente criar, testar e iterar um modelo de Análise Preditiva. Arrastar e largar módulos de conjuntos de dados e análise em telas interativas, ligá-los em conjunto para formar uma _experimentar_.  Crie a sua primeira experiência agora.

1. No canto inferior esquerdo, selecione **adicionar novo**.
![Adicionar nova experimentação](./media/ui-quickstart-run-experiment/add-new.png)

1. Selecione **em branco experimentação**.

1. Sua experiência é atribuída um nome predefinido. Selecione este texto e renomeá-lo para o "Guia de início rápido-explorar dados". Este nome não tem de ser exclusivo.

1. O **mapa de Mini** na parte inferior da tela é útil para visualizar grandes experiências.  Não precisará neste guia de introdução por isso, clique na seta na parte superior para minimizar a ele.  

    ![Mudar o nome de experimentação](./media/ui-quickstart-run-experiment/rename.png)

## <a name="add-data"></a>Adicionar dados

A primeira coisa que precisa para machine learning é os dados. Existem vários conjuntos de dados exemplo incluídos nesta interface que pode utilizar, ou pode importar dados a partir de várias origens. Neste exemplo, vai utilizar o conjunto de dados de exemplo **dados de preço do automóvel (bruto)** . 

1. À esquerda da tela da experimentação existe uma paleta de conjuntos de dados e módulos. Selecione **conjuntos de dados guardado** , em seguida, selecione **exemplos** para ver os conjuntos de dados de exemplo disponíveis.

1. Selecione o conjunto de dados **dados de preço do automóvel (brutos)** e arraste-o para a tela.

   ![Arraste os dados à tela](./media/ui-quickstart-run-experiment/drag-data.png)


## <a name="select-columns"></a>Selecionar colunas

Selecione as colunas de dados para trabalhar com.  Para começar, configure o módulo para mostrar todas as colunas disponíveis.

> [!TIP]
> Se souber o nome dos dados ou módulo que pretende, use a barra de pesquisa na parte superior da paleta do, para o encontrar rapidamente.  O resto do início rápido, irá utilizar este atalho.

1. Tipo **selecionar** na caixa de pesquisa para localizar o **Select Columns in Dataset** módulo.

1. Clique e arraste a **Select Columns in Dataset** para a tela. Remova o módulo abaixo o conjunto de dados que adicionou anteriormente.

1. Ligar o conjunto de dados para o **Select Columns in Dataset**: clique na porta de saída do conjunto de dados, arraste para a porta de entrada de **selecionar colunas no conjunto de dados**, em seguida, largue o botão do mouse. O módulo e o conjunto de dados permanecem conectados, mesmo se move qualquer um na tela.

    > [!TIP]
    > Os conjuntos de dados e os módulos têm portas de entrada e de saída, representadas por pequenos círculos. As portas de entrada estão em cima e as de saída estão em baixo. Criar um fluxo de dados através da experimentação quando se liga a porta de saída de um módulo a uma porta de entrada de outro.
    >
    > Se tiver problemas em ligar módulos, tente arrastar todo o caminho para o nó que está a ligar.

    ![Ligue-se módulos](./media/ui-quickstart-run-experiment/connect-modules.gif) 

    O ponto de exclamação vermelho indica que ainda não o defina as propriedades para o módulo ainda. Fazer de seguida.
   
1. Selecione o **Select Columns in Dataset** módulo.

1. Na **propriedades** painel à direita da tela, selecione **editar colunas**.

    Na **selecionar colunas** caixa de diálogo, selecione **todas as colunas** e incluir **todas as funcionalidades**. A caixa de diálogo deve ter este aspeto:

     ![Seletor de colunas](./media/ui-quickstart-run-experiment/select-all.png)

1. No canto inferior direito, selecione **OK** para fechar o Seletor de colunas.

## <a name="run-the-experiment"></a>Executar a experimentação

Em qualquer altura, clique na porta de saída de um conjunto de dados ou o módulo para ver o que o aspeto dos dados nesse ponto no fluxo de dados.  Se o **Visualize** opção está desativada, tem primeiro de executar a experimentação.  Fazer de seguida.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Depois do destino de computação estiver disponível, a experimentação é executada. Quando a execução estiver concluída, é apresentada uma marca de verificação verde em cada módulo.

![Ver estado](./media/ui-quickstart-run-experiment/status1.png)

## <a name="preview-the-data"></a>Os dados de pré-visualização

Agora que tem de executar a experimentação inicial, pode visualizar os dados para saber mais sobre as informações que tem que trabalhar com.

1. Selecione a porta de saída na parte inferior a **Select Columns in Dataset** , em seguida, selecione **Visualize**.

1. Clique em colunas diferentes na janela de dados para ver informações sobre essa coluna.  

    Este conjunto de dados, cada linha representa um automóvel e as variáveis associadas a cada um destes aparecem como colunas.    Existem 205 linhas e 26 colunas este conjunto de dados.

     Sempre que clicar numa coluna de dados, o **estatísticas** informações e **visualização** imagem dessa coluna é apresentada à esquerda.  Por exemplo, quando clica em **NÚM. de portas** vir tem 2 valores exclusivos e 2 valores em falta.  Desloque para baixo para ver os valores: duas e quatro portas.

     ![Os dados de pré-visualização](./media/ui-quickstart-run-experiment/preview-data.gif)

1. Clique em cada coluna para saber mais sobre o conjunto de dados e pensar se estas colunas serão úteis para prever o preço de um automóvel.

## <a name="prepare-data"></a>Preparar dados

Normalmente, um conjunto de dados requer alguns pré-processamentos antes de poder ser analisado. Poderá ter reparado nos valores em falta presentes nas colunas de várias linhas. Estes valores em falta têm de ser apagados para que o modelo possa analisar os dados corretamente. Irá remover quaisquer linhas que tenham valores em falta. Além disso, o **normalized-losses** coluna tem uma grande proporção de valores em falta, portanto, vou excluir essa coluna do modelo completamente.

> [!TIP]
> Apagar os valores em falta a partir dos dados de entrada é um pré-requisito para utilizar a maioria dos módulos.  

### <a name="remove-column"></a>Remover coluna

Primeiro, remova os **normalized-losses** coluna completamente.

1. Selecione o **Select Columns in Dataset** módulo.

1. Na **propriedades** painel à direita da tela, selecione **editar colunas**.

    * Deixe **com as regras** e **todas as colunas** selecionado.

    * Nas listas pendentes, selecione **Excluir** e os **nomes das colunas** e, em seguida, clique dentro da caixa de texto. Tipo **normalized-losses**.

    * No canto inferior direito, selecione **OK** para fechar o Seletor de colunas.

    ![Excluir uma coluna](./media/ui-quickstart-run-experiment/exclude-column.png)
        
    Agora o painel de propriedades para selecionar colunas no conjunto de dados indica que este irá percorrer todas as colunas do conjunto de dados, exceto **normalized-losses**.
        
    O painel de propriedades mostra que o **normalized-losses** excluir a coluna.
        
    ![Painel de propriedades](./media/ui-quickstart-run-experiment/property-pane.png)
        
    Pode adicionar um comentário a um módulo, fazendo duplo clique no módulo e introduzindo o texto. Isto pode ajudá-lo a ver rapidamente o que o módulo está a fazer na sua experimentação. 

1. Clique duas vezes o **Select Columns in Dataset** módulo e escreva o comentário "excluem perdas normalizadas." 
    
    Depois de escrever o comentário, clique em fora do módulo.  É apresentada uma seta para baixo mostrar que o módulo contenha um comentário.

1. Clique na seta para baixo para apresentar o comentário.

    O módulo mostra agora uma seta para cima para ocultar o comentário.
        
    ![Comentários](./media/ui-quickstart-run-experiment/comments.png)

### <a name="clean-missing-data"></a>Apagar dados em falta

Quando preparar um modelo, terá de fazer algo sobre os dados que estão em falta.  Neste caso, adicionará um módulo para remover todas as linhas restantes que tenham dados em falta.  

1. Tipo **Clean** na caixa de pesquisa para localizar o **Clean Missing Data** módulo.

1. Arrastar o **apagar dados em falta** módulo para a experimentação tela e ligá-la para o **Select Columns in Dataset** módulo. 

1. No painel de propriedades, selecione **remover toda a linha** sob **modo de limpeza**.

    Estas opções direct **Clean Missing Data** para limpar os dados ao remover as linhas que tenham valores em falta.

1. Clique duas vezes no módulo e escreva o comentário "Remover linhas de valor em falta".
 
    ![Remover linhas](./media/ui-quickstart-run-experiment/remove-rows.png)

    A experimentação deverá agora ser semelhante ao seguinte:
    
    ![Selecionar coluna](./media/ui-quickstart-run-experiment/experiment-clean.png)

## <a name="visualize-the-results"></a>Visualizar os resultados

Uma vez que as alterações que efetuou para os módulos na sua experimentação, o estado foi alterado para "No rascunho".  Para visualizar os novos dados limpos, primeiro tem de executar novamente a experimentação.

1. Selecione **executar** na parte inferior para executar a experimentação.

    Desta vez, pode reutilizar o destino de computação que criou anteriormente.  

1. Selecione **executar** na caixa de diálogo.

   ![Execute experimentação](./media/ui-quickstart-run-experiment/select-compute.png)

1. Quando a execução for concluída, clique com botão direito sobre a **Clean Missing Data** módulo para visualizar os novos dados limpos.  

    ![Visualizar dados limpos](./media/ui-quickstart-run-experiment/visualize-cleaned.png)

1. Clique em colunas diferentes na janela de dados limpo para ver como os dados foram alterados.  

    ![Visualizar dados limpos](media/ui-quickstart-run-experiment/visualize-result.png)

    Existem agora 193 linhas e colunas de 25.

    Quando clica em **NÚM. de portas** verá que ele ainda tem 2 valores exclusivos, mas agora tem 0 valor em falta. Clique no resto das colunas para ver que existem não existem valores em falta no conjunto de dados. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a:

- Criar a sua primeira experiência para adicionar e pré-visualizar dados
- Preparar os dados ao remover valores em falta
- Visualizar os dados preparados

Avance para o tutorial para utilizar estes dados para prever o preço de um automóvel.

> [!div class="nextstepaction"]
> [Tutorial: Prever o preço de automóvel com a interface visual](ui-tutorial-automobile-price-train-score.md)
