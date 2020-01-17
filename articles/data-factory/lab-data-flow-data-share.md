---
title: Integração de dados usando o Azure Data Factory e o compartilhamento de dados do Azure
description: Copiar, transformar e compartilhar dados usando o Azure Data Factory e o compartilhamento de dados do Azure
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 6c501205812ac72da8cd970b61b71e493888cef1
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156731"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Integração de dados usando o Azure Data Factory e o compartilhamento de dados do Azure

À medida que os clientes iniciam em seus projetos modernos de data warehouse e análise, eles exigem não apenas mais dados, mas também mais visibilidade em seus dados em todo o seu espaço de dados. Este workshop aprofunda-se em como as melhorias no Azure Data Factory e no compartilhamento de dados do Azure simplificam a integração e o gerenciamento de dados no Azure. Desde a habilitação do ETL/ELT sem código até a criação de uma exibição abrangente sobre seus dados, as melhorias no Azure Data Factory capacitarão seus engenheiros de dados a apresentarem com segurança mais dados e, portanto, mais valor, à sua empresa. O compartilhamento de dados do Azure permitirá que você faça negócios para o compartilhamento de negócios de maneira controlada.

Neste workshop, você usará o Azure Data Factory (ADF) para ingerir dados de um BD SQL do Azure (banco de dados SQL) em Azure Data Lake Storage Gen2 (ADLS Gen2). Depois de colocar os dados no Lake, você o transformará por meio do mapeamento de fluxos de dados, do serviço de transformação nativo do data factory e do seu coletor no Azure Synapse Analytics (anteriormente conhecido como SQL DW). Em seguida, você compartilhará a tabela com os dados transformados junto com alguns dados adicionais usando o compartilhamento de dados do Azure. 

Os dados usados neste laboratório são dados de táxi da cidade de Nova York. Para importá-lo para o banco de dados SQL do Azure, baixe o [arquivo de táxi-data bacpac](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac).

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

* **Banco de dados SQL do Azure**: se você não tiver um BD SQL, saiba como [criar uma conta do BD SQL](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

* **Azure data Lake Storage Gen2 conta de armazenamento**: se você não tiver uma conta de armazenamento de ADLS Gen2, saiba como [criar uma conta de armazenamento de ADLS Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

* **Azure Synapse Analytics (anteriormente conhecido como SQL DW)** : se você não tiver uma análise de Synapse do Azure (anteriormente SQL DW), saiba como [criar uma instância do Azure Synapse Analytics](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal).

* **Azure data Factory**: se você ainda não criou uma data Factory, consulte como [criar uma data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).

* **Compartilhamento de dados do Azure**: se você não tiver criado um compartilhamento de dados, consulte como [criar um compartilhamento de dados](https://docs.microsoft.com/azure/data-share/share-your-data#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Configurar seu ambiente de Azure Data Factory

Nesta seção, você aprenderá a acessar o Azure Data Factory experiência do usuário (ADF UX) da portal do Azure. Uma vez na UX do ADF, você configurará três serviços vinculados para cada um dos armazenamentos de dados que estamos usando: Azure SQL DB, ADLS Gen2 e Azure Synapse Analytics.

Em Azure Data Factory serviços vinculados definem as informações de conexão para recursos externos. O Azure Data Factory atualmente dá suporte a conectores 85.

### <a name="open-the-azure-data-factory-ux"></a>Abrir o Azure Data Factory UX

1. Abra o [portal do Azure](https://portal.azure.com) no Microsoft Edge ou no Google Chrome.
1. Usando a barra de pesquisa na parte superior da página, procure por ' Data factories '

    ![Portal](media/lab-data-flow-data-share/portal1.png)
1. Clique em seu recurso de data factory para abrir sua folha de recursos.

    ![Portal](media/lab-data-flow-data-share/portal2.png)
1. Clique em **autor e monitor** para abrir a UX do ADF. O ADF UX também pode ser acessado em adf.azure.com.

    ![Portal](media/lab-data-flow-data-share/portal3.png)
1. Você será redirecionado para a Home Page da UX do ADF. Esta página contém inícios rápidos, vídeos instrutivos e links para tutoriais para aprender data factory conceitos. Para iniciar a criação, clique no ícone de lápis na barra lateral esquerda.

    ![Portal](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço vinculado do banco de dados SQL do Azure

1. A página criação é o local em que você cria data factory recursos como pipelines, conjuntos de dados, fluxos e serviços vinculados. Para criar um serviço vinculado, clique no botão **conexões** no canto inferior direito.

    ![Portal](media/lab-data-flow-data-share/configure2.png)
1. Na guia conexões, clique em **novo** para adicionar um novo serviço vinculado.

    ![Portal](media/lab-data-flow-data-share/configure3.png)
1. O primeiro serviço vinculado que você configurará é um banco de BD SQL do Azure. Você pode usar a barra de pesquisa para filtrar a lista de armazenamento de dados. Clique no bloco **banco de dados SQL do Azure** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/configure4.png)
1. No painel configuração do BD SQL, insira ' SQLDB ' como o nome do serviço vinculado. Insira suas credenciais para permitir que data factory se conectem ao seu banco de dados. Se você estiver usando a autenticação do SQL, insira o nome do servidor, o banco de dados, seu nome de usuário e senha. Você pode verificar se as informações de conexão estão corretas clicando em **testar conexão**. Clique em **criar** quando terminar.

    ![Portal](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Criar um serviço vinculado do Azure Synapse Analytics

1. Repita o mesmo processo para adicionar um serviço vinculado do Azure Synapse Analytics. Na guia conexões, clique em **novo**. Selecione o bloco **Azure Synapse Analytics (anteriormente conhecido como SQL DW)** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/configure6.png)
1. No painel configuração de serviço vinculado, insira ' SQLDW ' como o nome do serviço vinculado. Insira suas credenciais para permitir que data factory se conectem ao seu banco de dados. Se você estiver usando a autenticação do SQL, insira o nome do servidor, o banco de dados, seu nome de usuário e senha. Você pode verificar se as informações de conexão estão corretas clicando em **testar conexão**. Clique em **criar** quando terminar.

    ![Portal](media/lab-data-flow-data-share/configure7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Criar um serviço vinculado Azure Data Lake Storage Gen2

1. O último serviço vinculado necessário para este laboratório é um Azure Data Lake Storage Gen2.  Na guia conexões, clique em **novo**. Selecione o bloco **Azure data Lake Storage Gen2** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/configure8.png)
1. No painel configuração de serviço vinculado, insira ' ADLSGen2 ' como o nome do serviço vinculado. Se você estiver usando a autenticação de chave de conta, selecione sua conta de armazenamento do ADLS Gen2 na lista suspensa **nome da conta de armazenamento** . Você pode verificar se as informações de conexão estão corretas clicando em **testar conexão**. Clique em **criar** quando terminar.

    ![Portal](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Ativar o modo de depuração do fluxo de dados

Na seção *transformar dados usando o fluxo de dados de mapeamento*, você criará fluxos de dados de mapeamento. Uma prática recomendada antes de criar fluxos de dados de mapeamento é ativar o modo de depuração, que permite testar a lógica de transformação em segundos em um cluster do Spark ativo.

Para ativar a depuração, clique no controle deslizante de **depuração do fluxo de dados** na barra superior da fábrica. Clique em OK quando os pop-ups da caixa de diálogo de confirmação. O cluster levará cerca de 5-7 minutos para ser inicializado. Continue a *ingerir dados do BD SQL do Azure para ADLS Gen2 usando a atividade de cópia* enquanto ela está sendo inicializada.

![Portal](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Ingerir dados usando a atividade de cópia

Nesta seção, você criará um pipeline com uma atividade de cópia que ingere uma tabela de um BD SQL do Azure em uma conta de armazenamento ADLS Gen2. Você aprenderá como adicionar um pipeline, configurar um conjunto de um e depurar um pipeline por meio da UX do ADF. O padrão de configuração usado nesta seção pode ser aplicado à cópia de um armazenamento de dados relacional para um armazenamento de dados baseado em arquivo.

No Azure Data Factory, um pipeline é um agrupamento lógico de atividades que juntos executam uma tarefa. Uma atividade define uma operação a ser executada em seus dados. Um DataSet aponta para os dados que você deseja usar em um serviço vinculado.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Criar um pipeline com uma atividade de cópia

1. No painel recursos de fábrica, clique no ícone de adição para abrir o menu novo recurso. Selecione **pipeline**.

    ![Portal](media/lab-data-flow-data-share/copy1.png)
1. Na guia **geral** da tela do pipeline, nomeie seu pipeline como algo descritivo, como ' IngestAndTransformTaxiData '.

    ![Portal](media/lab-data-flow-data-share/copy2.png)
1. No painel atividades da tela do pipeline, abra o acorde **e transforme** o adparador e arraste a atividade **copiar dados** para a tela. Dê um nome descritivo à atividade de cópia, como ' IngestIntoADLS '.

    ![Portal](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Configurar o conjunto de fonte do BD SQL do Azure

1. Clique na guia **origem** da atividade de cópia. Para criar um novo conjunto de um, clique em **novo**. Sua origem será a tabela ' dbo. TripData ' localizado no serviço vinculado ' SQLDB ' configurado anteriormente.

    ![Portal](media/lab-data-flow-data-share/copy4.png)
1. Pesquise o **banco de dados SQL do Azure** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/copy5.png)
1. Chame o conjunto de seus ' TripData '. Selecione ' SQLDB ' como seu serviço vinculado. Selecione o nome da tabela ' dbo. TripData ' na lista suspensa nome da tabela. Importe o esquema **do repositório/conexão**. Clique em OK quando terminar.

    ![Portal](media/lab-data-flow-data-share/copy6.png)

Você criou com êxito o conjunto de fonte de origem. Verifique se as configurações de origem, a **tabela** valor padrão, está selecionada no campo usar consulta.

### <a name="configure-adls-gen-2-sink-dataset"></a>Configurar o conjunto de ADLS Gen 2 Sink

1. Clique na guia **coletor** da atividade de cópia. Para criar um novo conjunto de um, clique em **novo**.

    ![Portal](media/lab-data-flow-data-share/copy7.png)
1. Procure **Azure data Lake Storage Gen2** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/copy8.png)
1. No painel Selecionar formato, selecione **DelimitedText** enquanto estiver gravando em um arquivo CSV. Clique em continuar.

    ![Portal](media/lab-data-flow-data-share/copy9.png)
1. Nomeie o conjunto de seus conjuntos de coleta ' TripDataCSV '. Selecione ' ADLSGen2 ' como seu serviço vinculado. Insira onde você deseja gravar o arquivo CSV. Por exemplo, você pode gravar seus dados no arquivo `trip-data.csv` no contêiner `staging-container`. Defina a **primeira linha como o cabeçalho** como true, pois você deseja que os dados de saída tenham cabeçalhos. Como nenhum arquivo existe no destino ainda, defina **importar esquema** como **nenhum**. Clique em OK quando terminar.

    ![Portal](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Testar a atividade de cópia com uma execução de depuração de pipeline

1. Para verificar se a atividade de cópia está funcionando corretamente, clique em **depurar** na parte superior da tela do pipeline para executar uma execução de depuração. Uma execução de depuração permite que você teste seu pipeline de ponta a ponta ou até um ponto de interrupção antes de publicá-lo no serviço de data factory.

    ![Portal](media/lab-data-flow-data-share/copy11.png)
1. Para monitorar a execução de depuração, vá para a guia **saída** da tela do pipeline. A tela de monitoramento será atualizada automaticamente a cada 20 segundos ou quando você clicar manualmente no botão atualizar. A atividade de cópia tem uma exibição de monitoramento especial que pode ser acessada clicando no ícone de óculos na coluna **ações** .

    ![Portal](media/lab-data-flow-data-share/copy12.png)
1. O modo de exibição de monitoramento de cópia fornece os detalhes de execução da atividade e as características de desempenho. Você pode ver informações como dados lidos/gravados, linhas lidas/gravadas, arquivos lidos/gravados e taxa de transferência. Se você tiver configurado tudo corretamente, verá 49.999 linhas gravadas em um arquivo em seu coletor ADLS.

    ![Portal](media/lab-data-flow-data-share/copy13.png)
1. Antes de passar para a próxima seção, é recomendável que você publique suas alterações no serviço de data factory clicando em **publicar tudo** na barra superior da fábrica. Embora não seja abordado neste laboratório, Azure Data Factory dá suporte à integração completa do git. A integração do git permite o controle de versão, o salvamento iterativo em um repositório e a colaboração em um data factory. Para obter mais informações, consulte [controle do código-fonte em Azure data Factory](https://docs.microsoft.com/azure/data-factory/source-control#troubleshooting-git-integration).

    ![Portal](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Transformar dados com o fluxo de dados de mapeamento

Agora que você copiou dados com êxito para Azure Data Lake Storage, é hora de ingressar e agregar esses dados em um data warehouse. Usaremos o fluxo de dados de mapeamento, Azure Data Factory serviço de transformação projetado visualmente. O mapeamento de fluxos de dados permite que os usuários desenvolvam código de lógica de transformação e os executem em clusters Spark gerenciados pelo serviço ADF.

O fluxo de dados criado nesta etapa Inner une o conjunto de dado ' TripDataCSV ' criado na seção anterior com uma tabela ' dbo. TripFares ' armazenado em ' SQLDB ' com base em quatro colunas de chave. Em seguida, os dados são agregados com base na `payment_type` de coluna para calcular a média de determinados campos e gravados em uma tabela do Azure Synapse Analytics.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Adicionar uma atividade de fluxo de dados ao seu pipeline

1. No painel atividades da tela do pipeline, abra o acorde **e transforme** o preparador e arraste a atividade **fluxo de dados** para a tela.

    ![Portal](media/lab-data-flow-data-share/dataflow1.png)
1. No painel lateral que é aberto, selecione **criar novo fluxo de dados** e escolha **mapeando fluxo de dados**. Clique em **OK**.

    ![Portal](media/lab-data-flow-data-share/dataflow2.png)
1. Você será direcionado para a tela de fluxo de dados em que criará sua lógica de transformação. Na guia geral, nomeie o fluxo de dados como ' JoinAndAggregateData '.

    ![Portal](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Configurar sua origem de CSV de dados de viagem

1. A primeira coisa que você deseja fazer é configurar suas duas transformações de origem. A primeira fonte apontará para o conjunto de TripDataCSV ' DelimitedText '. Para adicionar uma transformação de origem, clique na caixa **Adicionar origem** na tela.

    ![Portal](media/lab-data-flow-data-share/dataflow4.png)
1. Nomeie a fonte ' TripDataCSV ' e selecione o conjunto de bits ' TripDataCSV ' na lista suspensa origem. Se você se lembrar, não importou um esquema inicialmente ao criar esse conjunto de dados, pois não havia nenhum dado lá. Como `trip-data.csv` existe agora, clique em **Editar** para ir para a guia Configurações de conjunto de dado.

    ![Portal](media/lab-data-flow-data-share/dataflow5.png)
1. Vá para a guia **esquema** e clique em **importar esquema**. Selecione **da conexão/armazenamento** para importar diretamente do repositório de arquivos. 14 colunas do tipo cadeia de caracteres devem aparecer.

    ![Portal](media/lab-data-flow-data-share/dataflow6.png)
1. Volte para o fluxo de dados ' JoinAndAggregateData '. Se o cluster de depuração for iniciado (indicado por um círculo verde ao lado do controle deslizante de depuração), você poderá obter um instantâneo dos dados na guia **visualização de dados** . clique em **Atualizar** para buscar uma visualização de dados.

    ![Portal](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> A visualização de dados não grava dados.

### <a name="configure-your-trip-fares-sql-db-source"></a>Configurar sua viagem origem do BD SQL

1. A segunda fonte que você está adicionando apontará para a tabela de banco de BD SQL ' dbo. TripFares'. Na fonte ' TripDataCSV ', haverá outra caixa **Adicionar fonte** . Clique para adicionar uma nova transformação de origem.

    ![Portal](media/lab-data-flow-data-share/dataflow8.png)
1. Nomeie esta fonte como ' TripFaresSQL '. Clique em **novo** ao lado do campo conjunto de fonte de origem para criar um novo conjunto de SQL DB.

    ![Portal](media/lab-data-flow-data-share/dataflow9.png)
1. Selecione o bloco **banco de dados SQL do Azure** e clique em continuar. *Observação: você pode observar que muitos dos conectores no data Factory não têm suporte no fluxo de dados de mapeamento. Para transformar dados de uma dessas fontes, ingerir-as em uma fonte com suporte usando a atividade de cópia*.

    ![Portal](media/lab-data-flow-data-share/dataflow10.png)
1. Chame o conjunto de seus ' TripFares '. Selecione ' SQLDB ' como seu serviço vinculado. Selecione o nome da tabela ' dbo. TripFares ' na lista suspensa nome da tabela. Importe o esquema **do repositório/conexão**. Clique em OK quando terminar.

    ![Portal](media/lab-data-flow-data-share/dataflow11.png)
1. Para verificar seus dados, busque uma visualização de dados na guia **visualização de dados** .

    ![Portal](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>TripDataCSV e TripFaresSQL de junção interna

1. Para adicionar uma nova transformação, clique no ícone de adição no canto inferior direito de ' TripDataCSV '. Em **várias entradas/saídas**, selecione **ingressar**.

    ![Portal](media/lab-data-flow-data-share/join1.png)
1. Nomeie a transformação de junção ' InnerJoinWithTripFares '. Selecione ' TripFaresSQL ' na lista suspensa fluxo à direita. Selecione **interno** como o tipo de junção. Para saber mais sobre os diferentes tipos de junção no fluxo de dados de mapeamento, consulte [tipos de junção](https://docs.microsoft.com/azure/data-factory/data-flow-join#join-types).

    Selecione as colunas que você deseja corresponder de cada fluxo por meio da lista suspensa **condições de junção** . Para adicionar uma condição de junção adicional, clique no ícone de adição ao lado de uma condição existente. Por padrão, todas as condições de junção são combinadas com um operador AND, o que significa que todas as condições devem ser atendidas para uma correspondência. Neste laboratório, desejamos corresponder às colunas `medallion`, `hack_license`, `vendor_id`e `pickup_datetime`

    ![Portal](media/lab-data-flow-data-share/join2.png)
1. Verifique se você ingressou em 25 colunas com êxito com uma visualização de dados.

    ![Portal](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Agregar por payment_type

1. Depois de concluir a transformação de junção, adicione uma transformação Agregação clicando no ícone de adição ao lado de ' InnerJoinWithTripFares. Escolha **agregação** em **modificador de esquema**.

    ![Portal](media/lab-data-flow-data-share/agg1.png)
1. Nomeie sua transformação agregada ' AggregateByPaymentType '. Selecione `payment_type` como a coluna Agrupar por.

    ![Portal](media/lab-data-flow-data-share/agg2.png)
1. Vá para a guia **agregações** . Aqui, você especificará duas agregações:
    * A tarifa média agrupada por tipo de pagamento
    * A distância de viagem total agrupada por tipo de pagamento

    Primeiro, você criará a expressão de tarifa média. Na caixa de texto rotulada **Adicionar ou selecionar uma coluna**, insira ' average_fare '.

    ![Portal](media/lab-data-flow-data-share/agg3.png)
1. Para inserir uma expressão de agregação, clique na caixa azul rotulada **Inserir expressão**. Isso abrirá o construtor de expressões de fluxo de dados, uma ferramenta usada para criar visualmente expressões de fluxo de dados usando o esquema de entrada, funções internas e operações e parâmetros definidos pelo usuário. Para obter mais informações sobre os recursos do construtor de expressões, consulte a [documentação do construtor de expressões](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder).

    Para obter a tarifa média, use a função de agregação `avg()` para agregar a conversão de coluna de `total_amount` a um inteiro com `toInteger()`. Na linguagem de expressão de fluxo de dados, isso é definido como `avg(toInteger(total_amount))`. Clique em **salvar e em concluir** quando terminar.

    ![Portal](media/lab-data-flow-data-share/agg4.png)
1. Para adicionar uma expressão de agregação adicional, clique no ícone de adição ao lado de `average_fare`. Selecione **adicionar coluna**.

    ![Portal](media/lab-data-flow-data-share/agg5.png)
1. Na caixa de texto rotulada **Adicionar ou selecionar uma coluna**, insira ' total_trip_distance '. Como na última etapa, abra o construtor de expressões para inserir na expressão.

    Para obter a distância total da corrida, use a função de agregação `sum()` para agregar a conversão de coluna `trip_distance` a um inteiro com `toInteger()`. Na linguagem de expressão de fluxo de dados, isso é definido como `sum(toInteger(trip_distance))`. Clique em **salvar e em concluir** quando terminar.

    ![Portal](media/lab-data-flow-data-share/agg6.png)
1. Teste a lógica de transformação na guia **visualização de dados** . Como você pode ver, há significativamente menos linhas e colunas do que antes. Somente as três colunas Group by e Aggregation definidas nesta transformação continuam downstream. Como há apenas cinco grupos de tipos de pagamento no exemplo, apenas cinco linhas são emitidas.

    ![Portal](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Configurar o coletor do Azure Synapse Analytics

1. Agora que concluímos nossa lógica de transformação, estamos prontos para coletar nossos dados em uma tabela do Azure Synapse Analytics. Adicione uma transformação coletor na seção **destino** .

    ![Portal](media/lab-data-flow-data-share/sink1.png)
1. Nomeie o coletor como ' SQLDWSink '. Clique em **novo** ao lado do campo conjunto de coleta para criar um novo conjunto de uma nova análise do Azure Synapse.

    ![Portal](media/lab-data-flow-data-share/sink2.png)

1. Selecione o bloco **Azure Synapse Analytics (anteriormente conhecido como SQL DW)** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/sink3.png)
1. Chame o conjunto de seus ' AggregatedTaxiData '. Selecione ' SQLDW ' como seu serviço vinculado. Selecione **criar nova tabela** e nomeie a nova tabela dbo. AggregateTaxiData. Clique em OK quando terminar

    ![Portal](media/lab-data-flow-data-share/sink4.png)
1. Vá para a guia **configurações** do coletor. Como estamos criando uma nova tabela, precisamos selecionar **recriar tabela** em ação de tabela. Desmarque **habilitar preparo**, que alterna se estamos inserindo linha por linha ou em lote.

    ![Portal](media/lab-data-flow-data-share/sink5.png)

Você criou o fluxo de dados com êxito. Agora é hora de executá-lo em uma atividade de pipeline.

### <a name="debug-your-pipeline-end-to-end"></a>Depure seu pipeline de ponta a ponta

1. Volte para a guia do pipeline **IngestAndTransformData** . Observe a caixa verde na atividade de cópia ' IngestIntoADLS '. Arraste-o para a atividade de fluxo de dados ' JoinAndAggregateData '. Isso cria um ' em caso de êxito ', que faz com que a atividade de fluxo de dados seja executada somente se a cópia for bem-sucedida.

    ![Portal](media/lab-data-flow-data-share/pipeline1.png)
1. Como fizemos para a atividade de cópia, clique em **depurar** para executar uma execução de depuração. Para execuções de depuração, a atividade de fluxo de dados usará o cluster de depuração ativa em vez de girar um novo cluster. Esse pipeline levará alguns minutos para ser executado.

    ![Portal](media/lab-data-flow-data-share/pipeline2.png)
1. Assim como a atividade de cópia, o fluxo de dados tem uma exibição de monitoramento especial acessada pelo ícone óculos na conclusão da atividade.

    ![Portal](media/lab-data-flow-data-share/pipeline3.png)
1. No modo de exibição monitoramento, você pode ver um grafo de fluxo de dados simplificado junto com os tempos de execução e as linhas em cada estágio de execução. Se for feito corretamente, você deverá ter 49.999 linhas agregadas em cinco linhas nessa atividade.

    ![Portal](media/lab-data-flow-data-share/pipeline4.png)
1. Você pode clicar em uma transformação para obter detalhes adicionais sobre sua execução, como informações de particionamento e colunas novas/atualizadas/removidas.

    ![Portal](media/lab-data-flow-data-share/pipeline5.png)

Agora você concluiu a parte data factory deste laboratório. Publique seus recursos se desejar operacionalá-los com gatilhos. Você executou com êxito um pipeline que ingeriu dados do banco de dados SQL do Azure para Azure Data Lake Storage usando a atividade de cópia e, em seguida, agregaram esses dados em uma análise de Synapse do Azure. Você pode verificar se os dados foram gravados com êxito examinando o SQL Server em si.

## <a name="share-data-using-azure-data-share"></a>Compartilhar dados usando o compartilhamento de dados do Azure

Nesta seção, você aprenderá a configurar um novo compartilhamento de dados usando o portal do Azure. Isso envolverá a criação de um novo compartilhamento de dados que conterá DataSets de Azure Data Lake Store Gen2 e SQL Data Warehouse do Azure. Em seguida, você configurará uma agenda de instantâneo, que fornecerá aos consumidores de dados uma opção para atualizar automaticamente os dados que estão sendo compartilhados com eles. Em seguida, você convidará os destinatários para seu compartilhamento de dados. 

Depois de criar um compartilhamento de dados, você alternará chapéus e se tornará o *consumidor de dados*. Como consumidor de dados, você percorrerá o fluxo de aceitação de um convite de compartilhamento de dados, configurando onde você gostaria que os dados sejam recebidos e mapeando os DataSets para diferentes locais de armazenamento. Em seguida, você irá disparar um instantâneo, que copiará os dados compartilhados com você para o destino especificado. 

### <a name="sharing-data-data-provider-flow"></a>Compartilhando dados (fluxo de Provedor de Dados)

1. Abra o portal do Azure no Microsoft Edge ou no Google Chrome.

1. Usando a barra de pesquisa na parte superior da página, pesquise **compartilhamentos de dados**

    ![Portal](media/lab-data-flow-data-share/portal-ads.png)

1. Selecione a conta de compartilhamento de dados com ' provedor ' no nome. Por exemplo, **DataProvider0102**. 

1. Selecione **começar a compartilhar seus dados**

    ![Iniciar compartilhamento](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Selecione **+ criar** para começar a configurar seu novo compartilhamento de dados. 

1. Em **nome do compartilhamento**, especifique um nome de sua escolha. Esse é o nome do compartilhamento que será visto pelo consumidor de dados, portanto, certifique-se de dar a ele um nome descritivo, como TaxiData.

1. Em **Descrição**, coloque uma frase que descreve o conteúdo do compartilhamento de dados. O compartilhamento de dados conterá dados de corrida de táxi de todo o mundo armazenados em vários repositórios, incluindo o Azure SQL Data Warehouse e Azure Data Lake Store. 

1. Em **termos de uso**, especifique um conjunto de termos que você deseja que seu consumidor de dados obedeça. Alguns exemplos incluem "não distribuir esses dados fora de sua organização" ou "consulte o contrato legal". 

    ![Detalhes do compartilhamento](media/lab-data-flow-data-share/ads-details.png)

1. Selecione **Continuar**. 

1. Selecione **Adicionar conjuntos** de os 

    ![Adicionar conjunto de dados](media/lab-data-flow-data-share/add-dataset.png)

1. Selecione **azure SQL data warehouse** para selecionar uma tabela do SQL data warehouse do Azure que suas transformações do ADF descarregou.

    ![Adicionar conjunto de dados](media/lab-data-flow-data-share/add-dataset-sql.png)

> [!NOTE]
> O Azure SQL Data Warehouse agora é conhecido como análise de Synapse do Azure

1. Você receberá um script para executar antes de continuar. O script fornecido cria um usuário no banco de dados SQL para permitir que o MSI do Azure data share seja autenticado em seu nome. 

> [!IMPORTANT]
> Antes de executar o script, você deve definir como administrador de Active Directory para o SQL Server. 

1. Abra uma nova guia e navegue até a portal do Azure. Copie o script fornecido para criar um usuário no banco de dados do qual você deseja compartilhar os dados. Faça isso fazendo logon no banco de dados EDW usando o Gerenciador de consultas (versão prévia) usando a autenticação do AAD. 

    Você precisará modificar o script para que o usuário criado esteja contido entre colchetes. Por exemplo:
    
    criar usuário [DataProvider-xxxx] de logon externo;  exec sp_addrolemember db_owner, [DataProvider-xxxx];
    
1. Alterne de volta para o compartilhamento de dados do Azure em que você estava Adicionando conjuntos de dados ao seu compartilhamento. 

1. Selecione **EDW** para a SQL data warehouse e selecione **AggregatedTaxiData** para a tabela. 

1. Selecionar **Adicionar conjunto** de um

    Agora temos uma tabela SQL que faz parte do nosso conjunto de nossos. Em seguida, adicionaremos conjuntos de valores adicionais de Azure Data Lake Store. 

1. Selecione **Adicionar conjunto** de e selecione **Azure data Lake Store Gen2**

    ![Adicionar conjunto de dados](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Selecione **Seguinte**

1. Expanda *wwtaxidata*. Expanda *dados de táxi de Boston*. Observe que você pode compartilhar com o nível de arquivo. 

1. Selecione a pasta de *dados de táxi de Boston* para adicionar a pasta inteira ao seu compartilhamento de dados. 

1. Selecione **Adicionar conjuntos** de os

1. Examine os conjuntos de valores que foram adicionados. Você deve ter uma tabela SQL e uma pasta ADLSGen2 adicionada ao seu compartilhamento de dados. 

1. Selecione **continuar**

1. Nessa tela, você pode adicionar destinatários ao seu compartilhamento de dados. Os destinatários que você adicionar receberão convites para seu compartilhamento de dados. Para fins deste laboratório, você deve adicionar em 2 endereços de email:

    1. O endereço de email da assinatura do Azure em que você está. 

        ![Adicionar destinatários](media/lab-data-flow-data-share/add-recipients.png)

    1. Adicione o consumidor de dados fictício chamado *janedoe@fabrikam.com* .

1. Nessa tela, você pode definir uma configuração de instantâneo para o consumidor de dados. Isso permitirá que eles recebam atualizações regulares de seus dados em um intervalo definido por você. 

1. Verifique a **agenda do instantâneo** e configure uma atualização por hora de seus dados usando a lista suspensa *recorrência* .  

1. Selecione **Criar**.

    Agora você tem um compartilhamento de dados ativo. Vamos examinar o que você pode ver como um provedor de dados ao criar um compartilhamento de dados. 

1. Selecione o compartilhamento de dados que você criou, intitulado **DataProvider**. Você pode navegar para ele selecionando **compartilhamentos enviados** no **compartilhamento de dados**. 

1. Clique em agenda de instantâneos. Você pode desabilitar o agendamento de instantâneos se escolher. 

1. Em seguida, selecione a guia **conjuntos de valores** . Você pode adicionar conjuntos de dados adicionais a esse compartilhamento de dado depois que ele tiver sido criado. 

1. Selecione a guia **compartilhar assinaturas** . Ainda não existe nenhuma assinatura de compartilhamento porque seu consumidor de dados ainda não aceitou seu convite.

1. Navegue até a guia **convites** . Aqui, você verá uma lista de convites pendentes. 

    ![Convites pendentes](media/lab-data-flow-data-share/pending-invites.png)

1. Selecione o convite para *janedoe@fabrikam.com* . Selecione Excluir. Se o destinatário ainda não tiver aceitado o convite, ele não poderá mais fazê-lo. 

1. Selecione a guia **histórico** . Nada é exibido como ainda porque seu consumidor de dados ainda não aceitou seu convite e disparou um instantâneo. 

### <a name="receiving-data-data-consumer-flow"></a>Recebendo dados (fluxo de consumidor de dados)

Agora que analisamos nosso compartilhamento de dados, estamos prontos para mudar o contexto e o desgaste de nossos data Consumer Hat. 

Agora você deve ter um convite de compartilhamento de dados do Azure em sua caixa de entrada de Microsoft Azure. Inicie o Outlook Acesso via Web (outlook.com) e faça logon usando as credenciais fornecidas para sua assinatura do Azure.

No email que você deveria ter recebido, clique em "Exibir > de convite". Neste ponto, você vai simular a experiência do consumidor de dados ao aceitar um convite de provedores de dados para seu compartilhamento de dados. 

![Convite por email](media/lab-data-flow-data-share/email-invite.png)

Você pode ser solicitado a selecionar uma assinatura. Certifique-se de selecionar a assinatura na qual você está trabalhando para este laboratório. 

1. Clique no convite intitulado *DataProvider*. 

1. Nesta tela de convite, você observará vários detalhes sobre o compartilhamento de dados que você configurou anteriormente como um provedor de dados. Examine os detalhes e aceite os termos de uso, se fornecido.

1. Selecione a assinatura e o grupo de recursos que já existem para seu laboratório. 

1. Para **conta de compartilhamento de dados**, selecione **dataconsumidor**. Você também pode criar uma nova conta de compartilhamento de dados. 

1. Ao lado de **nome de compartilhamento recebido**, você notará que o nome de compartilhamento padrão é o nome especificado pelo provedor de dados. Dê ao compartilhamento um nome amigável que descreva os dados que você está prestes a receber, por exemplo, **TaxiDataShare**.

    ![Aceitação de convite](media/lab-data-flow-data-share/consumer-accept.png)

1. Você pode optar por **aceitar e configurar agora** ou **aceitar e configurar mais tarde**. Se você optar por aceitar e configurar agora, você especificará uma conta de armazenamento onde todos os dados devem ser copiados. Se você optar por aceitar e configurar mais tarde, os conjuntos de valores no compartilhamento serão desmapeados e você precisará mapeá-los manualmente. Iremos optar por isso mais tarde. 

1. Selecione **aceitar e configurar mais tarde**. 

    Ao configurar essa opção, uma assinatura de compartilhamento é criada, mas há em lugar algum para que os dados sejam Landes, pois nenhum destino foi mapeado. 

    Em seguida, configuraremos mapeamentos de DataSet para o compartilhamento de dados. 

1. Selecione o compartilhamento recebido (o nome que você especificou na etapa 5).

    O **instantâneo do gatilho** está esmaecido, mas o compartilhamento está ativo. 

1. Selecione a guia **conjuntos** de dados. Observe que cada conjunto de dados é não mapeado, o que significa que ele não tem destino para copiar dados. 

    ![conjuntos de valores não mapeados](media/lab-data-flow-data-share/unmapped.png)

1. Selecione a tabela SQL Data Warehouse e, em seguida, selecione **+ mapear para o destino**.

1. No lado direito da tela, selecione o menu suspenso tipo de **dados de destino** . 

    Você pode mapear os dados SQL para uma ampla variedade de armazenamentos de dados. Nesse caso, vamos mapear para um banco de dados SQL do Azure.

    ![correlação](media/lab-data-flow-data-share/mapping-options.png)
    
    Adicional Selecione **Azure data Lake Store Gen2** como o tipo de dados de destino. 
    
    Adicional Selecione a assinatura, o grupo de recursos e a conta de armazenamento em que você está trabalhando. 
    
    Adicional Você pode optar por receber os dados em seu data Lake no formato CSV ou parquet. 

1. Ao lado de **tipo de dados de destino**, selecione Azure SQL Database. 

1. Selecione a assinatura, o grupo de recursos e a conta de armazenamento em que você está trabalhando. 

    ![mapear para SQL](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Antes de continuar, você precisará criar um novo usuário no SQL Server executando o script fornecido. Primeiro, copie o script fornecido para a área de transferência. 

1. Abra uma nova guia portal do Azure. não feche a guia existente, pois você precisará voltar a ela daqui a pouco. 

1. Na nova guia que você abriu, navegue até **bancos de dados SQL**.

1. Selecione o banco de dados SQL (deve haver apenas um em sua assinatura). Tenha cuidado para não selecionar o SQL Data Warehouse. 

1. Selecionar **Editor de consultas (visualização)**

1. Use a autenticação do AAD para fazer logon no editor de consultas. 

1. Execute a consulta fornecida em seu compartilhamento de dados (copiada para a área de transferência na etapa 14). 

    Esse comando permite que o serviço de compartilhamento de dados do Azure use identidades gerenciadas para serviços do Azure para autenticar no SQL Server para poder copiar dados para ele. 

1. Volte para a guia original e selecione **mapear para destino**.

1. Em seguida, selecione a pasta Azure Data Lake Gen2 que faz parte do conjunto de e mapeie-a para uma conta de armazenamento de BLOBs do Azure. 

    ![armazenamento](media/lab-data-flow-data-share/storage-map.png)

    Com todos os conjuntos de dados mapeados, agora você está pronto para começar a receber dados do provedor. 

    ![adequados](media/lab-data-flow-data-share/all-mapped.png)
    
1. Selecione **detalhes**. 

    Observe que o **instantâneo do gatilho** não está mais esmaecido, já que o compartilhamento de dados agora tem destinos para copiar.

1. Selecione disparar instantâneo-> cópia completa. 

    ![of](media/lab-data-flow-data-share/trigger-full.png)

    Isso iniciará a cópia de dados em sua nova conta de compartilhamento de dados. Em um cenário do mundo real, esses dados seriam provenientes de terceiros. 

    Levará aproximadamente 3-5 minutos para os dados chegarem. Você pode monitorar o andamento clicando na guia **histórico** . 

    Enquanto você aguarda, navegue até o compartilhamento de dados original (DataProvider) e exiba o status da guia de assinaturas e de **histórico** de **compartilhamento** . Observe que agora há uma assinatura ativa e, como um provedor de dados, você também pode monitorar quando o consumidor de dados começou a receber os dados compartilhados com eles. 

1. Navegue de volta para o compartilhamento de dados do consumidor de dados. Depois que o status do gatilho for bem-sucedido, navegue até o banco de dados SQL de destino e data Lake para ver se os dados têm descarregou nos respectivos armazenamentos. 

Parabéns, você concluiu o laboratório!


