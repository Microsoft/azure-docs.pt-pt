---
title: Transforme dados com uma Azure Data Factory gerido fluxo de dados de mapeamento VNet
description: Este tutorial fornece instruções passo a passo para usar a Azure Data Factory para transformar dados com fluxo de dados de mapeamento
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: 5515f6c4dfbc5d3c0e391373e763597d7fdc89ed
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532320"
---
# <a name="transform-data-securely-using-mapping-data-flows"></a>Transformar dados de forma segura usando fluxos de dados de mapeamento

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

Neste tutorial, utilizará a interface de utilizador da Azure Data Factory (UX) para criar um pipeline que copia e transforma dados **de uma fonte de Armazenamento de Lagos de Dados (ADLS) da Azure Para um lavatório ADLS Gen2 (ambos permitindo o acesso a redes selecionadas)** utilizando o fluxo de dados de mapeamento na [Azure Data Factory gerida pela Rede Virtual.](managed-virtual-network-private-endpoint.md) O padrão de configuração neste tutorial pode ser expandido ao transformar dados usando o fluxo de dados de mapeamento.

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
>
> * Criar uma fábrica de dados.
> * Criar um oleoduto com uma atividade de Fluxo de Dados.
> * Construa um fluxo de dados de mapeamento com quatro transformações.
> * Testar a execução do pipeline.
> * Monitorizar uma atividade de Fluxo de Dados

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura Azure**. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure**. Utiliza o armazenamento ADLS como *fonte* e *lava* datas. Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) para seguir os passos para criar uma. **Certifique-se de que a conta de Armazenamento permite o acesso apenas a partir de 'Redes Selecionadas'.** 

O ficheiro que estamos a transformar neste tutorial é MoviesDB.csv, que pode ser encontrado [aqui.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Para recuperar o ficheiro do GitHub, copie o conteúdo para um editor de texto à sua escolha para guardar localmente como ficheiro .csv. Para fazer o upload do ficheiro para a sua conta de armazenamento, consulte [as bolhas de upload com o portal Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Os exemplos serão referentes a um contentor chamado "dados de amostra".

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Neste passo, cria-se uma fábrica de dados e abre-se o Data Factory UX para criar um pipeline na fábrica de dados.

1. Abra **o Microsoft Edge** ou o Google **Chrome**. Atualmente, a Data Factory UI é suportada apenas nos navegadores Web Microsoft Edge e Google Chrome.
2. No menu esquerdo, **selecione Criar uma**Fábrica de  >  **Analytics**  >  **Dados**de Análise de Recursos .
3. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

   O nome da fábrica de dados Azure deve ser *globalmente único.* Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. (por exemplo, o seu nomeADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).

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

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Criar um tempo de execução de integração Azure na Rede Virtual Gerida ADF
Neste passo, cria-se um Tempo de Execução de Integração Azure e ativar a Rede Virtual Gerida.

1. No portal ADF, vá ao **Manage Hub** e clique em **New** para criar um novo Tempo de Execução de Integração Azure.
   ![Criar novo tempo de execução de integração Azure](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Opte por criar um Tempo de Execução de Integração **Azure.**
   ![Novo tempo de integração da Azure](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Ativar **a Rede Virtual**.
   ![Novo tempo de integração da Azure](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Selecione **Criar**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Criar um oleoduto com uma atividade de Fluxo de Dados

Neste passo, irá criar um oleoduto que contenha uma atividade de Fluxo de Dados.

1. Na página **Vamos começar**, selecione **Criar pipeline**.

   ![Criar pipeline](./media/doc-common-process/get-started-page.png)

1. No painel **de propriedades** para o oleoduto, **insira TransformMovies** para **nome** do oleoduto.
1. Na barra superior da fábrica, deslize o **depurador de fluxo de dados.** O modo Debug permite testes interativos da lógica de transformação contra um cluster de Faíscas ao vivo. Os clusters Data Flow demoram 5 a 7 minutos a aquecer e os utilizadores são recomendados a ligar primeiro o depurg se planeiam fazer o desenvolvimento do Fluxo de Dados. Para mais informações, consulte [o Modo Debug](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode).

    ![Depurar fluxo de dados](media/tutorial-data-flow-private/dataflow-debug.png)
1. No painel **de atividades,** expanda o **acordeão Move and Transform.** Arraste e deixe cair a atividade do Fluxo de **Dados** do painel para a tela do gasoduto.

1. No pop-up do fluxo de **dados de adicionar,** selecione **Criar novo fluxo de dados** e, em seguida, selecione Mapping Data **Flow**. Clique **em OK** quando terminar.

    ![Fluxo de Dados de Mapeamento](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Nomeie o seu fluxo de **dados TransformMovies** in Properties.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Construir lógica de transformação na tela do fluxo de dados

Assim que criar o fluxo de dados, será automaticamente enviado para a tela de fluxo de dados. Neste passo, você vai construir um fluxo de dados que leva o moviesDB.csv no armazenamento ADLS e agrega a classificação média de comédias de 1910 a 2000. Em seguida, irá escrever este ficheiro de volta para o armazenamento ADLS.

### <a name="add-the-source-transformation"></a>Adicione a transformação da fonte

Neste passo, você configura Azure Data Lake Storage Gen2 como fonte.

1. Na tela de fluxo de dados, adicione uma fonte clicando na caixa **Add Source.**

1. Diga o nome da sua fonte **MoviesDB**. Clique em **Novo** para criar um novo conjunto de dados de origem.

1. Escolha **Azure Data Lake Storage Gen2**. Clique em Continue (Continuar).

1. Escolha **DelimitedText**. Clique em Continue (Continuar).

1. Nomeie o seu conjunto de **dados MoviesDB**. No dropdown de serviço ligado, escolha **New**.

1. No ecrã de criação de serviços ligado, nomeie o seu serviço ligado **ADLS gen2 ADLSGen2** e especifique o seu método de autenticação. Em seguida, insira as suas credenciais de ligação. Neste tutorial, estamos a usar a chave conta para ligar à nossa conta de armazenamento. 

1. Certifique-se de que ativa **a Autoria Interativa.** Pode levar cerca de 1 minuto para ser ativado.

    ![Autoria interativa](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Selecione **a ligação de teste,** deve falhar porque a Conta de Armazenamento não permite o acesso à sua sem a criação e aprovação de um Ponto Final Privado. Na mensagem de erro, deverá ver um link para criar um **ponto final privado** que pode seguir para criar um ponto final privado gerido. *Uma alternativa é ir diretamente ao separador Gerir e seguir as instruções [nesta secção](#create-a-managed-private-endpoint) para criar um ponto final privado gerido*

1. Mantenha a caixa de diálogo aberta e, em seguida, vá à sua Conta de Armazenamento selecionada acima.

1. Siga as instruções [nesta secção](#approval-of-a-private-link-in-storage-account) para aprovar o link privado.

1. Volta para a caixa de diálogo. **Volte a testar a ligação** e selecione **Criar** para implementar o serviço ligado.

1. Assim que voltar ao ecrã de criação do conjunto de dados, insira onde o seu ficheiro está localizado no campo **do caminho do Ficheiro.** Neste tutorial, o ficheiro moviesDB.csv está localizado em dados de amostras de contentores. Como o ficheiro tem cabeçalhos, verifique **a primeira linha como cabeçalho**. Selecione **Da ligação/loja** para importar o esquema do cabeçalho diretamente do ficheiro armazenado. Clique em OK quando terminar.

    ![Caminho de origem](media/tutorial-data-flow-private/source-file-path.png)

1. Se o seu cluster de depuração tiver começado, vá ao **separador Des preview** de Dados da transformação da fonte e clique em **Refresh** para obter uma imagem dos dados. Pode utilizar a pré-visualização de dados para verificar se a sua transformação está configurada corretamente.

    ![Pré-visualização dos dados](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Criar um ponto final privado gerido

Caso não tenha clicando na hiperligação ao testar a ligação acima, siga o seguinte caminho. Agora precisa de criar um ponto final privado gerido que irá ligar ao serviço ligado acima.

1. Vá ao separador Gerir.
> [!NOTE]
> O separador de gestão pode não estar disponível para todas as instâncias da fábrica de dados. Se não o vir, ainda pode aceder a Private Endpoints através do separador '**Author**- > '**Connections**' -- > '**Private Endpoint**'
1. Aceda à secção de pontos finais privados geridos.
1. Selecione **+ Novos** em pontos finais privados geridos.

    ![Novo ponto final privado gerido](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Selecione o azulejo Azure Data Lake Storage Gen2 da lista e selecione **Continue**.
1. Insira o nome da Conta de Armazenamento que criou acima.
1. Selecione **Criar**.
1. Você deve ver depois de esperar alguns segundos que o link privado criado precisa de uma aprovação.
1. Selecione o Ponto Final Privado que criou acima. Pode ver uma hiperligação que o levará a aprovar o Ponto Final Privado ao nível da Conta de Armazenamento.

    ![Gerir o ponto final privado](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Aprovação de um link privado na conta de armazenamento

1. Na Conta de Armazenamento, aceda a **ligações de ponto final privado na** secção Definições.

1. Marque o ponto final privado que criou acima e selecione **Aprovar**.

    ![Aprovar ponto final privado](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Adicione uma descrição e clique **em sim**.
1. Volte para a secção de **pontos finais privados geridos** do **separador Managed** na Azure Data Factory.
1. Deve levar cerca de 1 minuto para que a aprovação seja refletida para o seu ponto final privado.

### <a name="add-the-filter-transformation"></a>Adicione a transformação do filtro

1. Ao lado do seu nó de origem na tela de fluxo de dados, clique no ícone plus para adicionar uma nova transformação. A primeira transformação que está a adicionar é um **Filtro.**

    ![Adicionar filtro](media/tutorial-data-flow-private/add-filter.png)
1. Nomeie o seu filter transformation **FilterYears**. Clique na caixa de expressão ao lado **do Filtro para** abrir o construtor de expressão. Aqui irá especificar a sua condição de filtragem.

    ![Anos de filtragem](media/tutorial-data-flow-private/filter-years.png)
1. O construtor de expressão de fluxo de dados permite-lhe construir expressões interativas para usar em várias transformações. As expressões podem incluir funções incorporadas, colunas do esquema de entrada e parâmetros definidos pelo utilizador. Para obter mais informações sobre como construir expressões, consulte o [construtor de expressão Data Flow.](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder)

    * Neste tutorial, quer filtrar filmes de comédia de género que saiu entre os anos de 1910 e 2000. Como o ano é atualmente uma corda, você precisa convertê-lo em um inteiro usando a ```toInteger()``` função. Utilize os operadores maiores ou iguais (>=) e inferiores ou iguais a (<=) para comparar com os valores do ano literal de 1910 e 200-. União estas expressões juntamente com o operador e (&&). A expressão sai como:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Para descobrir quais filmes são comédias, você pode usar a ```rlike()``` função para encontrar o padrão 'Comédia' nos géneros das colunas. União a expressão rlike com a comparação do ano para obter:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Se tiver um cluster de depuração ativo, pode verificar a sua lógica clicando em **Refresh** para ver a saída da expressão em comparação com as entradas utilizadas. Há mais do que uma resposta certa sobre como pode realizar esta lógica usando a linguagem de expressão de fluxo de dados.

        ![Expressão de filtro](media/tutorial-data-flow-private/filter-expression.png)

    * Clique **em Guardar e Terminar** assim que terminar com a sua expressão.

1. Pegue uma **pré-visualização de dados** para verificar se o filtro está a funcionar corretamente.

    ![Visualização de dados de filtro](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Adicione a transformação agregada

1. A próxima transformação que vai adicionar é uma transformação **agregada** sob **o modificador Schema.**

    ![Adicionar agregado](media/tutorial-data-flow-private/add-aggregate.png)
1. Nomeie a sua transformação **agregada AgregaçãoRatings**. No **Grupo por** conta, selecione **ano** da queda para agrupar as agregações até ao ano em que o filme saiu.

    ![Grupo agregado](media/tutorial-data-flow-private/group-by-year.png)
1. Vá ao **separador Agregados.** Na caixa de texto esquerda, nomeie a coluna agregada **AverageComedyRating**. Clique na caixa de expressão certa para introduzir a expressão agregada através do construtor de expressão.

    ![Nome da coluna agregada](media/tutorial-data-flow-private/name-column.png)
1. Para obter a média de **classificação**de coluna, utilize a ```avg()``` função agregada. Como **o Rating** é uma corda e requer uma entrada ```avg()``` numérica, temos de converter o valor num número através da ```toInteger()``` função. Esta é a expressão parece:

    ```avg(toInteger(Rating))```

    Clique **em Guardar e Terminar** quando terminar.

    ![Salvar agregado](media/tutorial-data-flow-private/save-aggregate.png)
1. Aceda ao **separador Data Preview** para ver a saída de transformação. Note que apenas existem duas colunas, **ano** e **Média ComedyRating**.

### <a name="add-the-sink-transformation"></a>Adicione a transformação do lavatório

1. Em seguida, você quer adicionar uma transformação **de Sink** em **Destino**.

    ![Adicione pia](media/tutorial-data-flow-private/add-sink.png)
1. Diga o nome da **pia.** Clique em **Novo** para criar o conjunto de dados da pia.

    ![Criar pia](media/tutorial-data-flow-private/create-sink.png)
1. Na nova página de conjunto de dados, escolha **Azure Data Lake Storage Gen2**. Clique em Continue (Continuar).

1. Na página de formato Select, escolha **DelimitedText**. Clique em Continue (Continuar).

1. Nomeie o seu conjunto de dados de pia **MoviesSink**. Para o serviço ligado, escolha o mesmo serviço ligado ADLSGen2 que criou para a transformação de fontes. Introduza uma pasta de saída para escrever os seus dados para. Neste tutorial, estamos escrevendo para a pasta 'output' em contentor 'dados de amostra'. A pasta não precisa de existir previamente e pode ser criada dinamicamente. Definir **a primeira linha como cabeçalho** como verdadeiro e selecione **Nenhum** para o esquema **de importação**. Clique em OK.

    ![Caminho do lavatório](media/tutorial-data-flow-private/sink-file-path.png)

Agora já terminou de construir o seu fluxo de dados. Está pronto para correr no seu oleoduto.

## <a name="running-and-monitoring-the-data-flow"></a>Executar e monitorizar o Fluxo de Dados

Pode depurar um oleoduto antes de publicá-lo. Neste passo, vais desencadear uma fuga de depuramento do fluxo de dados. Embora a pré-visualização de dados não escreva dados, uma corrida de depurar escreverá dados para o seu destino de pia.

1. Vai para a tela do oleoduto. Clique em **Debug** para desencadear uma corrida de depurador.

1. O depuração do pipeline das atividades do Data Flow utiliza o cluster de depuração ativo, mas ainda demora pelo menos um minuto a inicializar. Pode acompanhar o progresso através do separador **Saída.** Uma vez que a execução seja bem sucedida, clique no ícone de óculos para obter detalhes de execução.

1. Na página Detalhes, pode ver o número de linhas e tempo gastos em cada passo de transformação.

    ![Execução de monitorização](media/tutorial-data-flow-private/run-details.png)
1. Clique numa transformação para obter informações detalhadas sobre as colunas e a divisão dos dados.

Se seguiu corretamente este tutorial, deverá ter escrito 83 linhas e 2 colunas na sua pasta da pia. Pode verificar se os dados estão corretos verificando o armazenamento do seu blob.

## <a name="summary"></a>Resumo

Neste tutorial, você usará a interface de utilizador da Azure Data Factory (UX) para criar um pipeline que copia e transforma dados **de uma fonte de Armazenamento de Lagos de Dados (ADLS) da Azure Para um lavatório ADLS Gen2 (ambos permitindo o acesso a apenas redes selecionadas)** usando o fluxo de dados de mapeamento na [Azure Data Factory gerida pela Rede Virtual.](managed-virtual-network-private-endpoint.md)
