---
title: 'Quickstart: Transforme dados usando um fluxo de dados de mapeamento'
description: Este tutorial fornece instruções passo a passo para a utilização do Azure Synapse Analytics para transformar dados com fluxo de dados de mapeamento.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: 207679ad5b508b687c9cad372d144839fcaa501d
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94743852"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Quickstart: Transforme dados usando fluxos de dados de mapeamento

Neste quickstart, você usará a Azure Synapse Analytics para criar um pipeline que transforma dados de uma fonte de Armazenamento de Data Lake Gen2 (ADLS Gen2) para uma pia ADLS Gen2 usando o fluxo de dados de mapeamento. O padrão de configuração neste quickstart pode ser expandido ao transformar dados usando o fluxo de dados de mapeamento

Neste arranque rápido, faça os seguintes passos:

> [!div class="checklist"]
> * Crie um oleoduto com uma atividade de Fluxo de Dados em Azure Synapse Analytics.
> * Construa um fluxo de dados de mapeamento com quatro transformações.
> * Testar a execução do pipeline.
> * Monitorizar uma atividade de Fluxo de Dados

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Espaço de trabalho Azure Synapse**: Criar um espaço de trabalho synapse utilizando o portal Azure seguindo as instruções em [Quickstart: Criar um espaço de trabalho Synapse](quickstart-create-workspace.md).
* **Conta de armazenamento Azure**: Utiliza o armazenamento ADLS como lojas de dados *de origem* e *afunda.* Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) para seguir os passos para criar uma.

    O ficheiro que estamos a transformar neste tutorial é MoviesDB.csv, que pode ser encontrado [aqui.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Para recuperar o ficheiro do GitHub, copie o conteúdo para um editor de texto à sua escolha para guardar localmente como um ficheiro .csv. Para fazer o upload do ficheiro para a sua conta de armazenamento, consulte [as bolhas de upload com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Os exemplos serão referentes a um contentor chamado "dados de amostra".

### <a name="navigate-to-the-synapse-studio"></a>Navegue até ao Estúdio Synapse

Depois de criar o seu espaço de trabalho Azure Synapse, tem duas formas de abrir o Synapse Studio:

* Abra o seu espaço de trabalho sinapse no [portal Azure](https://ms.portal.azure.com/#home). Selecione **Abrir** no cartão Open Synapse Studio em "Começar".
* Abra [o Azure Synapse Analytics](https://web.azuresynapse.net/) e inscreva-se no seu espaço de trabalho.

Neste arranque rápido, usamos o espaço de trabalho chamado "adftest2020" como exemplo. Irá navegar automaticamente para a página inicial do Synapse Studio.

![Página inicial do Estúdio de Sinaapse](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Criar um oleoduto com uma atividade de Fluxo de Dados

Um oleoduto contém o fluxo lógico para uma execução de um conjunto de atividades. Nesta secção, irá criar um pipeline que contém uma atividade de Fluxo de Dados.

1. Aceda ao **separador Integração.** Selecione no ícone mais ao lado do cabeçalho dos oleodutos e selecione Pipeline.

   ![Criar um novo oleoduto](media/doc-common-process/new-pipeline.png)

1. Na página de definições de **propriedades** do pipeline, **insira TransformMovies** for **Name**.

1. Em *Movimento e Transformar* no painel de *atividades,* arraste o **fluxo de dados** para a tela do gasoduto.

1. Na página de fluxo de **dados adicionando** pop-up, selecione **Criar novo fluxo de**  ->  **dados Fluxo de dados**. Clique **em OK** quando terminar.

   ![Criar um fluxo de dados](media/quickstart-data-flow/new-data-flow.png)

1. Nomeie o seu fluxo de **dados TransformMovies** na página **Propriedades.**

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Construir lógica de transformação na tela do fluxo de dados

Assim que criar o fluxo de dados, será automaticamente enviado para a tela de fluxo de dados. Neste passo, você vai construir um fluxo de dados que leva o MoviesDB.csv no armazenamento ADLS e agrega a classificação média de comédias de 1910 a 2000. Em seguida, irá escrever este ficheiro de volta para o armazenamento ADLS.

1. Acima da tela de fluxo de dados, deslize o **depurador de fluxo de dados.** O modo Debug permite testes interativos da lógica de transformação contra um cluster de Faíscas ao vivo. Os clusters Data Flow demoram 5 a 7 minutos a aquecer e os utilizadores são recomendados a ligar primeiro o depurg se planeiam fazer o desenvolvimento do Fluxo de Dados. Para mais informações, consulte [o Modo Debug](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

    ![Deslize o depurg](media/quickstart-data-flow/debug-on.png)

1. Na tela de fluxo de dados, adicione uma fonte clicando na caixa **Add Source.**

1. Diga o nome da sua fonte **MoviesDB**. Clique em **Novo** para criar um novo conjunto de dados de origem.

    ![Criar um novo conjunto de dados de origem](media/quickstart-data-flow/new-source-dataset.png)

1. Escolha **Azure Data Lake Storage Gen2**. Clique em Continue (Continuar).

    ![Escolha Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. Escolha **DelimitedText**. Clique em Continue (Continuar).

1. Nomeie o seu conjunto de **dados MoviesDB**. No dropdown de serviço ligado, escolha **New**.

1. No ecrã de criação de serviços ligado, nomeie o seu serviço ligado **ADLS Gen2 ADLSGen2** e especifique o seu método de autenticação. Em seguida, insira as suas credenciais de ligação. Neste arranque rápido, estamos a usar a chave conta para ligar à nossa conta de armazenamento. Pode clicar na **ligação de teste** para verificar se as suas credenciais foram corretamente inseridas. Clique em **Criar** quando terminar.

    ![Criar um serviço ligado à fonte](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. Assim que voltar ao ecrã de criação do conjunto de dados, no campo **do caminho do Ficheiro,** insira onde o seu ficheiro está localizado. Neste arranque rápido, o ficheiro "MoviesDB.csv" encontra-se no recipiente "dados de amostra". Como o ficheiro tem cabeçalhos, verifique **a primeira linha como cabeçalho**. Selecione **Da ligação/loja** para importar o esquema do cabeçalho diretamente do ficheiro armazenado. Clique **em OK** quando terminar.

    ![Definições do conjunto de dados de origem](media/quickstart-data-flow/source-dataset-properties.png)

1. Se o seu cluster de depuração tiver começado, vá ao **separador Des preview** de Dados da transformação da fonte e clique em **Refresh** para obter uma imagem dos dados. Pode utilizar a pré-visualização de dados para verificar se a sua transformação está configurada corretamente.

    ![Pré-visualização dos dados](media/quickstart-data-flow/data-preview.png)

1. Ao lado do seu nó de origem na tela de fluxo de dados, clique no ícone plus para adicionar uma nova transformação. A primeira transformação que está a adicionar é um **Filtro.**

    ![Adicionar um filtro](media/quickstart-data-flow/add-filter.png)

1. Nomeie o seu filter transformation **FilterYears**. Clique na caixa de expressão ao lado **do Filtro para** abrir o construtor de expressão. Aqui irá especificar a sua condição de filtragem.

1. O construtor de expressão de fluxo de dados permite-lhe construir expressões interativas para usar em várias transformações. As expressões podem incluir funções incorporadas, colunas do esquema de entrada e parâmetros definidos pelo utilizador. Para obter mais informações sobre como construir expressões, consulte o [construtor de expressão Data Flow.](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    Neste arranque rápido, você quer filtrar filmes de comédia de género que saiu entre os anos 1910 e 2000. Como o ano é atualmente uma corda, você precisa convertê-lo em um inteiro usando a ```toInteger()``` função. Utilize os operadores maiores ou iguais (>=) e inferiores ou iguais a (<=) para comparar com os valores do ano literal de 1910 e 200-. União estas expressões juntamente com o operador e (&&). A expressão sai como:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Para descobrir quais filmes são comédias, você pode usar a ```rlike()``` função para encontrar o padrão 'Comédia' nos géneros das colunas. União a expressão rlike com a comparação do ano para obter:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Especificar condição de filtragem](media/quickstart-data-flow/visual-expression-builder.png)

    Se tiver um cluster de depuração ativo, pode verificar a sua lógica clicando em **Refresh** para ver a saída da expressão em comparação com as entradas utilizadas. Há mais do que uma resposta certa sobre como pode realizar esta lógica usando a linguagem de expressão de fluxo de dados.

    Clique **em Guardar e Terminar** assim que terminar com a sua expressão.

1. Pegue uma **pré-visualização de dados** para verificar se o filtro está a funcionar corretamente.

1. A próxima transformação que vai adicionar é uma transformação **agregada** sob **o modificador Schema.**

    ![Adicione um Agregado](media/quickstart-data-flow/add-aggregate.png)

1. Nomeie a sua transformação **agregada AgregaçãoRatings**. No **Grupo por** conta, selecione **ano** da queda para agrupar as agregações até ao ano em que o filme saiu.

    ![Configurações agregadas 1](media/quickstart-data-flow/aggregate-settings.png)

1. Vá ao **separador Agregados.** Na caixa de texto esquerda, nomeie a coluna agregada **AverageComedyRating**. Clique na caixa de expressão certa para introduzir a expressão agregada através do construtor de expressão.

    ![Configurações agregadas 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Para obter a média de **classificação** de coluna, utilize a ```avg()``` função agregada. Como **o Rating** é uma corda e requer uma entrada ```avg()``` numérica, temos de converter o valor num número através da ```toInteger()``` função. Esta expressão parece:

    ```avg(toInteger(Rating))```

    Clique **em Guardar e Terminar** quando terminar.

    ![Classificação média de comédia](media/quickstart-data-flow/average-comedy-rating.png)

1. Aceda ao **separador Data Preview** para ver a saída de transformação. Note que apenas existem duas colunas, **ano** e **Média ComedyRating**.

    ![Pré-visualização de dados agregados](media/quickstart-data-flow/transformation-output.png)

1. Em seguida, você quer adicionar uma transformação **de Sink** em **Destino**.

    ![Adicione uma pia](media/quickstart-data-flow/add-sink.png)

1. Diga o nome da **pia.** Clique em **Novo** para criar o conjunto de dados da pia.

1. Escolha **Azure Data Lake Storage Gen2**. Clique em Continue (Continuar).

1. Escolha **DelimitedText**. Clique em Continue (Continuar).

1. Nomeie o seu conjunto de dados de pia **MoviesSink**. Para um serviço ligado, escolha o serviço ligado à ADLS Gen2 que criou no passo 7. Introduza uma pasta de saída para escrever os seus dados para. Neste arranque rápido, estamos a escrever para a pasta 'output' no contentor 'dados de amostra'. A pasta não precisa de existir previamente e pode ser criada dinamicamente. Definir **a primeira linha como cabeçalho** como verdadeiro e selecione **Nenhum** para o esquema **de importação**. Clique **em OK** quando terminar.

    ![Propriedades do conjunto de dados do lavatório](media/quickstart-data-flow/sink-dataset-properties.png)

Agora já terminou de construir o seu fluxo de dados. Está pronto para correr no seu oleoduto.

## <a name="running-and-monitoring-the-data-flow"></a>Executar e monitorizar o Fluxo de Dados

Pode depurar um oleoduto antes de publicá-lo. Neste passo, vais desencadear uma fuga de depuramento do fluxo de dados. Embora a pré-visualização de dados não escreva dados, uma corrida de depurar escreverá dados para o seu destino de pia.

1. Vai para a tela do oleoduto. Clique em **Debug** para desencadear uma corrida de depurador.

    ![Gasoduto de depuragem](media/quickstart-data-flow/debug-pipeline.png)

1. O depuração do pipeline das atividades do Data Flow utiliza o cluster de depuração ativo, mas ainda demora pelo menos um minuto a inicializar. Pode acompanhar o progresso através do separador **Saída.** Uma vez que a execução seja bem sucedida, clique no ícone dos óculos para abrir o painel de monitorização.

    ![Depurando a produção](media/quickstart-data-flow/debugging-output.png)

1. No painel de monitorização, pode ver o número de linhas e tempo gastos em cada passo de transformação.

    ![Monitorização da transformação](media/quickstart-data-flow/4-transformations.png)

1. Clique numa transformação para obter informações detalhadas sobre as colunas e a divisão dos dados.

    ![Detalhes da transformação](media/quickstart-data-flow/transformation-details.png)

Se seguiu corretamente este arranque rápido, devia ter escrito 83 linhas e 2 colunas na sua pasta da pia. Pode verificar os dados verificando o armazenamento do seu blob.


## <a name="next-steps"></a>Passos seguintes

Avance para os seguintes artigos para saber sobre o suporte Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Gasoduto e atividades](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 
>  [Visão geral](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 
>  do fluxo de dados de mapeamento [Linguagem de expressão de fluxo de dados](https://docs.microsoft.com/azure/data-factory/data-flow-expression-functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
