---
title: Transforme dados usando um fluxo de dados de mapeamento
description: Este tutorial fornece instruções passo a passo para usar a Azure Data Factory para transformar dados com fluxo de dados de mapeamento
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: e6ca8007a96cc63b51b4f79b69029cbf0799e71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979185"
---
# <a name="transform-data-using-mapping-data-flows"></a>Transformar dados usando fluxos de dados de mapeamento

Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, utilizará a interface de utilizador da Azure Data Factory (UX) para criar um pipeline que copia e transforma dados de uma fonte de Gen2 de Armazenamento de Lagos de Dados Azure (ADLS) para um afundado ADLS Gen2 utilizando o fluxo de dados de mapeamento. O padrão de configuração neste tutorial pode ser expandido ao transformar dados usando fluxo de dados de mapeamento

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um pipeline com uma atividade de Fluxo de Dados.
> * Construa um fluxo de dados de mapeamento com quatro transformações.
> * Testar a execução do pipeline.
> * Monitorizar uma atividade de Fluxo de Dados

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura Azure.** Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**. Usa o armazenamento ADLS como *fonte* e *afunda* lojas de dados. Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) para seguir os passos para criar uma.

O ficheiro que estamos a transformar neste tutorial é moviesDB.csv, que pode ser encontrado [aqui.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Para recuperar o ficheiro do GitHub, copie o conteúdo para um editor de texto à sua escolha para guardar localmente como um ficheiro .csv. Para fazer o upload do ficheiro para a sua conta de armazenamento, consulte [o Upload blobs com o Portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Os exemplos serão a referência a um recipiente chamado "dados de amostra".

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Neste passo, cria-se uma fábrica de dados e abre-se o Data Factory UX para criar um oleoduto na fábrica de dados.

1. Abra **o Microsoft Edge** ou o Google **Chrome.** Atualmente, data Factory UI é suportado apenas nos navegadores web Microsoft Edge e Google Chrome.
2. No menu esquerdo, selecione **Criar um recurso** > **Analytics** > **Data Factory:**

   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

   O nome da fábrica de dados Azure deve ser *globalmente único.* Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. (por exemplo, o seu nomeADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).

     ![Nova fábrica de dados](./media/doc-common-process/name-not-available-error.png)
4. Selecione a **subscrição** do Azure na qual pretende criar a fábrica de dados.
5. Em **Grupo de Recursos**, efetue um destes passos:

    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md). 
6. Em **Versão**, selecione **V2**.
7. Em **Localização**, selecione uma localização para a fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. As lojas de dados (por exemplo, o Armazenamento Azure e a Base de Dados SQL) e os cálculos (por exemplo, Azure HDInsight) utilizados pela fábrica de dados podem ser noutras regiões.
8. Selecione **Criar**.
9. Depois de concluída a criação, vê o aviso no Centro de Notificações. Selecione **Ir ao recurso** para navegar na página da fábrica de Dados.
10. Selecione **Criar e Monitorizar** para iniciar a IU do Data Factory num separador à parte.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Criar um oleoduto com uma atividade de Fluxo de Dados

Neste passo, criará um pipeline que contenha uma atividade de Fluxo de Dados.

1. Na página **Vamos começar**, selecione **Criar pipeline**.

   ![Criar pipeline](./media/doc-common-process/get-started-page.png)

1. No separador **Geral** para o oleoduto, **introduza TransformMovies** para **nome** do oleoduto.
1. Na barra superior da fábrica, deslize o deslize de **depurador do Fluxo** de Dados. O modo Debug permite testar interactivamente a lógica de transformação contra um cluster Spark vivo. Os clusters data Flow demoram 5 a 7 minutos a aquecer e recomenda-se que os utilizadores liguem o depuramento primeiro se planeiam fazer o desenvolvimento do Fluxo de Dados. Para mais informações, consulte [debug mode](concepts-data-flow-debug-mode.md).

    ![Atividade de Fluxo de Dados](media/tutorial-data-flow/dataflow1.png)
1. No painel **de Atividades,** expanda o acordeão **Move and Transform.** Arraste e deixe cair a atividade do Fluxo de **Dados** do painel para a tela do gasoduto.

    ![Atividade de Fluxo de Dados](media/tutorial-data-flow/activity1.png)
1. No pop-up **Add Data Flow,** selecione **Criar um novo Fluxo** de Dados e, em seguida, nomear o seu fluxo de dados **TransformMovies**. Clique em Terminar quando terminar.

    ![Atividade de Fluxo de Dados](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Construir lógica de transformação na tela de fluxo de dados

Assim que criar o seu Fluxo de Dados, será automaticamente enviado para a tela de fluxo de dados. Neste passo, você vai construir um fluxo de dados que leva o moviesDB.csv no armazenamento ADLS e agrega a classificação média de comedas de 1910 a 2000. Em seguida, escreverá este ficheiro de volta para o armazenamento ADLS.

1. Na tela de fluxo de dados, adicione uma fonte clicando na caixa **Add Source.**

    ![Tela de fluxo de dados](media/tutorial-data-flow/dataflow2.png)
1. Nomeie a sua fonte **MoviesDB**. Clique em **Novo** para criar um novo conjunto de dados de origem.

    ![Tela de fluxo de dados](media/tutorial-data-flow/dataflow3.png)
1. Escolha **o Azure Data Lake Storage Gen2**. Clique em Continue (Continuar).

    ![Conjunto de dados](media/tutorial-data-flow/dataset1.png)
1. Escolha **Texto Delimitado**. Clique em Continue (Continuar).

    ![Conjunto de dados](media/tutorial-data-flow/dataset2.png)
1. Nomeie o seu conjunto de dados **MoviesDB**. No serviço ligado, escolha **New**.

    ![Conjunto de dados](media/tutorial-data-flow/dataset3.png)
1. No ecrã de criação de serviço ligado, nomeie o seu serviço ligado a **ADLS gen2 ADLSGen2** e especifique o seu método de autenticação. Em seguida, introduza as suas credenciais de ligação. Neste tutorial, estamos a usar a chave da Conta para ligar à nossa conta de armazenamento. Pode clicar na **ligação de teste** para verificar se as suas credenciais foram introduzidas corretamente. Clique em Criar quando terminar.

    ![Serviço Vinculado](media/tutorial-data-flow/ls1.png)
1. Assim que voltar ao ecrã de criação do conjunto de dados, introduza onde o seu ficheiro está localizado sob o campo de caminho do **Ficheiro.** Neste tutorial, o ficheiro moviesDB.csv está localizado em dados de amostras de contentores. Como o ficheiro tem cabeçalhos, verifique a **primeira fila como cabeçalho**. Selecione **entre ligar/armazenar** para importar o esquema do cabeçalho diretamente do ficheiro armazenado. Clique bem quando estiver pronto.

    ![Conjuntos de dados](media/tutorial-data-flow/dataset4.png)
1. Se o seu cluster de depuração tiver começado, vá ao separador **'Visualização** de Dados' da transformação de origem e clique em **Refresh** para obter uma foto dos dados. Pode utilizar a pré-visualização de dados para verificar se a sua transformação está configurada corretamente.

    ![Tela de fluxo de dados](media/tutorial-data-flow/dataflow4.png)
1. Ao lado do nó de origem na tela de fluxo de dados, clique no ícone plus para adicionar uma nova transformação. A primeira transformação que está a adicionar é um **Filtro.**

    ![Tela de fluxo de dados](media/tutorial-data-flow/dataflow5.png)
1. Nomeie o seu filtro de transformação **FilterYears**. Clique na caixa de expressão ao lado do **Filter para** abrir o construtor de expressão. Aqui irá especificar o seu estado de filtragem.

    ![Filtro](media/tutorial-data-flow/filter1.png)
1. O construtor de expressão de fluxo de dados permite-lhe construir expressões interativamente para usar em várias transformações. As expressões podem incluir funções incorporadas, colunas do esquema de entrada e parâmetros definidos pelo utilizador. Para obter mais informações sobre como construir expressões, consulte o construtor de [expressão data Flow](concepts-data-flow-expression-builder.md).

    Neste tutorial, você quer filtrar filmes de comédia de género que saiu entre os anos de 1910 e 2000. Como o ano é atualmente uma corda, você precisa ```toInteger()``` convertê-lo em um inteiro usando a função. Utilize o maior ou igual a (>=) e inferior ou igual a (<=) operadores para comparar com os valores do ano literal 1910 e 200-. Unir estas expressões juntamente com o operador e (&&). A expressão sai como:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Para descobrir quais filmes são comédias, ```rlike()``` você pode usar a função para encontrar padrão 'Comédia' nos géneros da coluna. Unifique a expressão rlike com a comparação do ano para obter:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Se tiver um cluster de depuração ativo, pode verificar a sua lógica clicando em **Refresh** para ver a saída de expressão em comparação com as inputs utilizadas. Há mais do que uma resposta certa sobre como pode realizar esta lógica usando a linguagem de expressão de fluxo de dados.

    ![Filtro](media/tutorial-data-flow/filter2.png)

    Clique em **Guardar e Terminar** assim que terminar a sua expressão.

1. Repara numa **Pré-visualização** de dados para verificar se o filtro está a funcionar corretamente.

    ![Filtro](media/tutorial-data-flow/filter3.png)
1. A próxima transformação que irá adicionar é uma transformação **agregada** sob **modificador de Schema.**

    ![Agregação](media/tutorial-data-flow/agg1.png)
1. Nomeie a sua transformação agregada **AggregateComedyRatings**. No **Grupo por** tab, selecione **ano** desde a queda para agrupar as agregações até ao ano em que o filme saiu.

    ![Agregação](media/tutorial-data-flow/agg2.png)
1. Vá ao separador **Agregados.** Na caixa de texto esquerda, nomeie a coluna agregada **AverageComedyRating**. Clique na caixa de expressão certa para introduzir a expressão agregada através do construtor de expressão.

    ![Agregação](media/tutorial-data-flow/agg3.png)
1. Para obter a média de ```avg()``` **classificação**da coluna, use a função agregada. Como a **Classificação** é uma corda e ```avg()``` recebe uma entrada numérica, ```toInteger()``` temos de converter o valor para um número através da função. Esta é a expressão parece:

    ```avg(toInteger(Rating))```

    Clique em **Guardar e Terminar** quando terminar.

    ![Agregação](media/tutorial-data-flow/agg4.png)
1. Vá ao separador **'Visualização** de Dados' para ver a saída de transformação. Note que apenas duas colunas estão lá, **ano** e **AverageComedyRating**.

    ![Agregação](media/tutorial-data-flow/agg3.png)
1. Em seguida, você quer adicionar uma transformação **de Sink** no **Destino**.

    ![Sink](media/tutorial-data-flow/sink1.png)
1. Diga o nome **da pia.** Clique em **Novo** para criar o seu conjunto de dados de pia.

    ![Sink](media/tutorial-data-flow/sink2.png)
1. Escolha **o Azure Data Lake Storage Gen2**. Clique em Continue (Continuar).

    ![Conjunto de dados](media/tutorial-data-flow/dataset1.png)
1. Escolha **Texto Delimitado**. Clique em Continue (Continuar).

    ![Conjunto de dados](media/tutorial-data-flow/dataset2.png)
1. Nomeie o seu conjunto de dados da pia **MoviesSink**. Para o serviço ligado, escolha o serviço ligado ao ADLS gen2 que criou no passo 6. Introduza uma pasta de saída para escrever os seus dados. Neste tutorial, estamos a escrever para pasta 'output' em 'dados de amostra'. A pasta não precisa de existir previamente e pode ser criada dinamicamente. Coloque a **primeira linha como** verdadeira e selecione **Nenhuma** para **importar esquema**. Clique em Concluir.

    ![Sink](media/tutorial-data-flow/sink3.png)

Agora terminou de construir o seu fluxo de dados. Está pronto para executá-lo no seu oleoduto.

## <a name="running-and-monitoring-the-data-flow"></a>Funcionamento e monitorização do Fluxo de Dados

Pode desinver um oleoduto antes de publicá-lo. Neste passo, vais desencadear uma série de depuração do gasoduto de fluxo de dados. Embora a pré-visualização de dados não escreva dados, uma execução de depuração irá escrever dados para o seu destino de sink.

1. Vai para a tela do oleoduto. Clique em **Debug** para desencadear uma corrida de depuração.

    ![Pipeline](media/tutorial-data-flow/pipeline1.png)
1. O depuramento do gasoduto das atividades do Fluxo de Dados utiliza o cluster de depuração ativo, mas ainda leva pelo menos um minuto para inicializar. Pode acompanhar o progresso através do separador **Output.** Assim que a corrida for bem sucedida, clique no ícone dos óculos para abrir o painel de monitorização.

    ![Pipeline](media/tutorial-data-flow/pipeline2.png)
1. No painel de monitorização, pode ver o número de linhas e tempo gasto em cada passo de transformação.

    ![Monitorização](media/tutorial-data-flow/pipeline3.png)
1. Clique numa transformação para obter informações detalhadas sobre as colunas e a partilha dos dados.

    ![Monitorização](media/tutorial-data-flow/pipeline4.png)

Se seguiu corretamente este tutorial, deve ter escrito 83 linhas e 2 colunas na sua pasta de lavatório. Pode verificar se os dados estão corretos verificando o seu armazenamento de blob.

## <a name="next-steps"></a>Passos seguintes

O pipeline neste tutorial executa um fluxo de dados que agrega a classificação média das ededies de 1910 a 2000 e escreve os dados para a ADLS. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um pipeline com uma atividade de Fluxo de Dados.
> * Construa um fluxo de dados de mapeamento com quatro transformações.
> * Testar a execução do pipeline.
> * Monitorizar uma atividade de Fluxo de Dados

Saiba mais sobre a linguagem de expressão do fluxo de [dados.](data-flow-expression-functions.md)
