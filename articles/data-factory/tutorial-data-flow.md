---
title: Transformar dados usando um fluxo de dados de mapeamento
description: Este tutorial fornece instruções passo a passo para usar a Azure Data Factory para transformar dados com fluxo de dados de mapeamento
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/16/2021
ms.openlocfilehash: f8570c8b252fae91986508abd3725cbbedd361a0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565428"
---
# <a name="transform-data-using-mapping-data-flows"></a>Transformar dados com o fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você usará a interface de utilizador da Azure Data Factory (UX) para criar um pipeline que copia e transforma dados de uma fonte de Armazenamento de Lago de Dados (ADLS) da Azure Para uma pia ADLS Gen2 usando o fluxo de dados de mapeamento. O padrão de configuração neste tutorial pode ser expandido ao transformar dados usando o fluxo de dados de mapeamento

 >[!NOTE]
   >Este tutorial destina-se a mapear fluxos de dados em geral. Os fluxos de dados estão disponíveis tanto na Azure Data Factory como em Pipelines Synapse. Se é novo nos fluxos de dados nos oleodutos Azure Synapse, siga o [Fluxo de Dados utilizando os gasodutos Azure Synapse](../synapse-analytics/concepts-data-flow-overview.md) 
   
Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um oleoduto com uma atividade de Fluxo de Dados.
> * Construa um fluxo de dados de mapeamento com quatro transformações.
> * Testar a execução do pipeline.
> * Monitorizar uma atividade de Fluxo de Dados

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura Azure**. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure**. Utiliza o armazenamento ADLS como *fonte* e *lava* datas. Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) para seguir os passos para criar uma.

O ficheiro que estamos a transformar neste tutorial é MoviesDB.csv, que pode ser encontrado [aqui.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Para recuperar o ficheiro do GitHub, copie o conteúdo para um editor de texto à sua escolha para guardar localmente como um ficheiro .csv. Para fazer o upload do ficheiro para a sua conta de armazenamento, consulte [as bolhas de upload com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Os exemplos serão referentes a um contentor chamado "dados de amostra".

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Neste passo, cria-se uma fábrica de dados e abre-se o Data Factory UX para criar um pipeline na fábrica de dados.

1. Abra **o Microsoft Edge** ou o Google **Chrome**. Atualmente, a Data Factory UI é suportada apenas nos navegadores Web Microsoft Edge e Google Chrome.
2. No menu esquerdo, **selecione Criar uma** Fábrica de  >  Dados de **Integração de** Recursos  >  :

   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

   O nome da fábrica de dados Azure deve ser *globalmente único.* Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. (por exemplo, o seu nomeADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nova mensagem de erro da fábrica de dados para nome duplicado.":::
4. Selecione a **subscrição** do Azure na qual pretende criar a fábrica de dados.
5. Em **Grupo de Recursos**, efetue um destes passos:

    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md). 
6. Em **Versão**, selecione **V2**.
7. Em **Localização**, selecione uma localização para a fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. As lojas de dados (por exemplo, Azure Storage e SQL Database) e os cálculos (por exemplo, Azure HDInsight) utilizados pela fábrica de dados podem estar noutras regiões.
8. Selecione **Criar**.
9. Depois de concluída a criação, vê o aviso no Centro de Notificações. Selecione **Ir para o recurso** para navegar para a página de fábrica de dados.
10. Selecione **Criar e Monitorizar** para iniciar a IU do Data Factory num separador à parte.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Criar um oleoduto com uma atividade de Fluxo de Dados

Neste passo, irá criar um oleoduto que contenha uma atividade de Fluxo de Dados.

1. Na página **Vamos começar**, selecione **Criar pipeline**.

   ![Criar pipeline](./media/doc-common-process/get-started-page.png)

1. No separador **Geral** do gasoduto, **insira TransformMovies** para **nome** do gasoduto.
1. No painel **de atividades,** expanda o **acordeão Move and Transform.** Arraste e deixe cair a atividade do Fluxo de **Dados** do painel para a tela do gasoduto.

    ![Screenshot que mostra a tela do gasoduto onde pode deixar cair a atividade do Data Flow.](media/tutorial-data-flow/activity1.png)
1. No pop-up do Fluxo de **Dados adicionando,** selecione **Criar novo fluxo de dados** e, em seguida, nomeie o seu fluxo de **dados TransformMovies**. Clique em Terminar quando terminar.

    ![Screenshot que mostra onde nomeia o fluxo de dados quando cria um novo fluxo de dados.](media/tutorial-data-flow/activity2.png)
1. Na barra superior da tela do gasoduto, deslize o **depurador de fluxo de dados.** O modo Debug permite testes interativos da lógica de transformação contra um cluster de Faíscas ao vivo. Os clusters Data Flow demoram 5 a 7 minutos a aquecer e os utilizadores são recomendados a ligar primeiro o depurg se planeiam fazer o desenvolvimento do Fluxo de Dados. Para mais informações, consulte [o Modo Debug](concepts-data-flow-debug-mode.md).

    ![Atividade do fluxo de dados](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Construir lógica de transformação na tela do fluxo de dados

Assim que criar o fluxo de dados, será automaticamente enviado para a tela de fluxo de dados. Neste passo, você vai construir um fluxo de dados que leva o moviesDB.csv no armazenamento ADLS e agrega a classificação média de comédias de 1910 a 2000. Em seguida, irá escrever este ficheiro de volta para o armazenamento ADLS.

1. Na tela de fluxo de dados, adicione uma fonte clicando na caixa **Add Source.**

    ![Screenshot que mostra a caixa Add Source.](media/tutorial-data-flow/dataflow2.png)
1. Diga o nome da sua fonte **MoviesDB**. Clique em **Novo** para criar um novo conjunto de dados de origem.

    ![Screenshot que mostra onde seleciona New depois de nomear a sua fonte.](media/tutorial-data-flow/dataflow3.png)
1. Escolha **Azure Data Lake Storage Gen2**. Clique em Continue (Continuar).

    ![Screenshot que mostra onde está o azulejo Azure Data Lake Storage Gen2.](media/tutorial-data-flow/dataset1.png)
1. Escolha **DelimitedText**. Clique em Continue (Continuar).

    ![Screenshot que mostra o azulejo DelimitedText.](media/tutorial-data-flow/dataset2.png)
1. Nomeie o seu conjunto de **dados MoviesDB**. No dropdown de serviço ligado, escolha **New**.

    ![Screenshot que mostra a lista de dropdown de serviço linked.](media/tutorial-data-flow/dataset3.png)
1. No ecrã de criação de serviços ligado, nomeie o seu serviço ligado **ADLS gen2 ADLSGen2** e especifique o seu método de autenticação. Em seguida, insira as suas credenciais de ligação. Neste tutorial, estamos a usar a chave conta para ligar à nossa conta de armazenamento. Pode clicar na **ligação de teste** para verificar se as suas credenciais foram corretamente inseridas. Clique em Criar quando terminar.

    ![Serviço Ligado](media/tutorial-data-flow/ls1.png)
1. Assim que voltar ao ecrã de criação do conjunto de dados, insira onde o seu ficheiro está localizado no campo **do caminho do Ficheiro.** Neste tutorial, o ficheiro moviesDB.csv está localizado em dados de amostras de contentores. Como o ficheiro tem cabeçalhos, verifique **a primeira linha como cabeçalho**. Selecione **Da ligação/loja** para importar o esquema do cabeçalho diretamente do ficheiro armazenado. Clique em OK quando terminar.

    ![Conjuntos de dados](media/tutorial-data-flow/dataset4.png)
1. Se o seu cluster de depuração tiver começado, vá ao **separador Des preview** de Dados da transformação da fonte e clique em **Refresh** para obter uma imagem dos dados. Pode utilizar a pré-visualização de dados para verificar se a sua transformação está configurada corretamente.

    ![Screenshot que mostra onde pode visualizar os seus dados para verificar se a sua transformação está configurada corretamente.](media/tutorial-data-flow/dataflow4.png)
1. Ao lado do seu nó de origem na tela de fluxo de dados, clique no ícone plus para adicionar uma nova transformação. A primeira transformação que está a adicionar é um **Filtro.**

    ![Tela de fluxo de dados](media/tutorial-data-flow/dataflow5.png)
1. Nomeie o seu filter transformation **FilterYears**. Clique na caixa de expressão ao lado **do Filtro para** abrir o construtor de expressão. Aqui irá especificar a sua condição de filtragem.

    ![Screenshot que mostra o filtro na caixa de expressão.](media/tutorial-data-flow/filter1.png)
1. O construtor de expressão de fluxo de dados permite-lhe construir expressões interativas para usar em várias transformações. As expressões podem incluir funções incorporadas, colunas do esquema de entrada e parâmetros definidos pelo utilizador. Para obter mais informações sobre como construir expressões, consulte o [construtor de expressão Data Flow.](concepts-data-flow-expression-builder.md)

    Neste tutorial, quer filtrar filmes de comédia de género que saiu entre os anos de 1910 e 2000. Como o ano é atualmente uma corda, você precisa convertê-lo em um inteiro usando a ```toInteger()``` função. Utilize os operadores maiores ou iguais (>=) e inferiores ou iguais a (<=) para comparar com os valores do ano literal de 1910 e 200-. União estas expressões juntamente com o operador e (&&). A expressão sai como:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Para descobrir quais filmes são comédias, você pode usar a ```rlike()``` função para encontrar o padrão 'Comédia' nos géneros das colunas. União a expressão rlike com a comparação do ano para obter:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Se tiver um cluster de depuração ativo, pode verificar a sua lógica clicando em **Refresh** para ver a saída da expressão em comparação com as entradas utilizadas. Há mais do que uma resposta certa sobre como pode realizar esta lógica usando a linguagem de expressão de fluxo de dados.

    ![Filtro](media/tutorial-data-flow/filter2.png)

    Clique **em Guardar e Terminar** assim que terminar com a sua expressão.

1. Pegue uma **pré-visualização de dados** para verificar se o filtro está a funcionar corretamente.

    ![Screenshot que mostra a pré-visualização de dados que recolheu.](media/tutorial-data-flow/filter3.png)
1. A próxima transformação que vai adicionar é uma transformação **agregada** sob **o modificador Schema.**

    ![Screenshot que mostra o modificador de esquemas agregado.](media/tutorial-data-flow/agg1.png)
1. Nomeie a sua transformação **agregada AgregaçãoRatings**. No **Grupo por** conta, selecione **ano** da queda para agrupar as agregações até ao ano em que o filme saiu.

    ![Screenshot que mostra a opção do ano no Grupo por separador em Definições Agregadas.](media/tutorial-data-flow/agg2.png)
1. Vá ao **separador Agregados.** Na caixa de texto esquerda, nomeie a coluna agregada **AverageComedyRating**. Clique na caixa de expressão certa para introduzir a expressão agregada através do construtor de expressão.

    ![Screenshot que mostra a opção do ano no separador Agregados em Definições Agregadas.](media/tutorial-data-flow/agg3.png)
1. Para obter a média de **classificação** de coluna, utilize a ```avg()``` função agregada. Como **o Rating** é uma corda e requer uma entrada ```avg()``` numérica, temos de converter o valor num número através da ```toInteger()``` função. Esta é a expressão parece:

    ```avg(toInteger(Rating))```

    Clique **em Guardar e Terminar** quando terminar.

    ![Screenshot que mostra a expressão guardada.](media/tutorial-data-flow/agg4.png)
1. Aceda ao **separador Data Preview** para ver a saída de transformação. Note que apenas existem duas colunas, **ano** e **Média ComedyRating**.

    ![Agregação](media/tutorial-data-flow/agg3.png)
1. Em seguida, você quer adicionar uma transformação **de Sink** em **Destino**.

    ![Screenshot que mostra onde adicionar uma transformação de pia em Destino.](media/tutorial-data-flow/sink1.png)
1. Diga o nome da **pia.** Clique em **Novo** para criar o conjunto de dados da pia.

    ![Screenshot que mostra onde você pode nomear a sua pia e criar um novo conjunto de dados de pia.](media/tutorial-data-flow/sink2.png)
1. Escolha **Azure Data Lake Storage Gen2**. Clique em Continue (Continuar).

    ![Screenshot que mostra o azulejo Azure Data Lake Storage Gen2 que pode escolher.](media/tutorial-data-flow/dataset1.png)
1. Escolha **DelimitedText**. Clique em Continue (Continuar).

    ![Conjunto de dados](media/tutorial-data-flow/dataset2.png)
1. Nomeie o seu conjunto de dados de pia **MoviesSink**. Para um serviço ligado, escolha o serviço ligado ao gen2 ADLS que criou no passo 6. Introduza uma pasta de saída para escrever os seus dados para. Neste tutorial, estamos escrevendo para a pasta 'output' em contentor 'dados de amostra'. A pasta não precisa de existir previamente e pode ser criada dinamicamente. Definir **a primeira linha como cabeçalho** como verdadeiro e selecione **Nenhum** para o esquema **de importação**. Clique em Concluir.

    ![Sink](media/tutorial-data-flow/sink3.png)

Agora já terminou de construir o seu fluxo de dados. Está pronto para correr no seu oleoduto.

## <a name="running-and-monitoring-the-data-flow"></a>Executar e monitorizar o Fluxo de Dados

Pode depurar um oleoduto antes de publicá-lo. Neste passo, vais desencadear uma fuga de depuramento do fluxo de dados. Embora a pré-visualização de dados não escreva dados, uma corrida de depurar escreverá dados para o seu destino de pia.

1. Vai para a tela do oleoduto. Clique em **Debug** para desencadear uma corrida de depurador.

    ![Screenshot que mostra a tela do gasoduto com Debug em destaque.](media/tutorial-data-flow/pipeline1.png)
1. O depuração do pipeline das atividades do Data Flow utiliza o cluster de depuração ativo, mas ainda demora pelo menos um minuto a inicializar. Pode acompanhar o progresso através do separador **Saída.** Uma vez que a execução seja bem sucedida, clique no ícone dos óculos para abrir o painel de monitorização.

    ![Pipeline](media/tutorial-data-flow/pipeline2.png)
1. No painel de monitorização, pode ver o número de linhas e tempo gastos em cada passo de transformação.

    ![Screenshot que mostra o painel de monitorização onde você pode ver o número de linhas e tempo gasto em cada passo de transformação.](media/tutorial-data-flow/pipeline3.png)
1. Clique numa transformação para obter informações detalhadas sobre as colunas e a divisão dos dados.

    ![Monitorização](media/tutorial-data-flow/pipeline4.png)

Se seguiu corretamente este tutorial, deverá ter escrito 83 linhas e 2 colunas na sua pasta da pia. Pode verificar se os dados estão corretos verificando o armazenamento do seu blob.

## <a name="next-steps"></a>Passos seguintes

O oleoduto neste tutorial executa um fluxo de dados que agrega a classificação média das comédias de 1910 a 2000 e escreve os dados para a ADLS. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um oleoduto com uma atividade de Fluxo de Dados.
> * Construa um fluxo de dados de mapeamento com quatro transformações.
> * Testar a execução do pipeline.
> * Monitorizar uma atividade de Fluxo de Dados

Saiba mais sobre a linguagem de expressão do [fluxo de dados.](data-flow-expression-functions.md)