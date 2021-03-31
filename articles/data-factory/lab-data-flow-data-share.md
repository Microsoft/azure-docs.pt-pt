---
title: Integração de dados utilizando a Azure Data Factory e a Azure Data Share
description: Copiar, transformar e partilhar dados usando a Azure Data Factory e a Azure Data Share
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: fa424f7e1f5e1f885dd433b8abc8aae1dc1bc206
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97006202"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Integração de dados utilizando a Azure Data Factory e a Azure Data Share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

À medida que os clientes embarcam no seu moderno armazém de dados e projetos de análise, eles exigem não só mais dados, mas também mais visibilidade nos seus dados através do seu espólio de dados. Este workshop mergulha na forma como as melhorias na Azure Data Factory e na Azure Data Share simplificam a integração e gestão de dados no Azure. 

Desde permitir que a ETL/ELT sem códigos crie uma visão abrangente sobre os seus dados, as melhorias na Azure Data Factory capacitarão os seus engenheiros de dados a trazerem com confiança mais dados e, portanto, mais valor, para a sua empresa. A Azure Data Share permitir-lhe-á fazer negócios para a partilha de negócios de forma governada.

Neste workshop, você usará Azure Data Factory (ADF) para ingerir dados da Azure SQL Database em Azure Data Lake Storage Gen2 (ADLS Gen2). Assim que aterrar os dados no lago, irá transformá-lo através do mapeamento dos fluxos de dados, o serviço de transformação nativa da fábrica de dados, e afundá-lo em Azure Synapse Analytics. Em seguida, partilhará a tabela com dados transformados, juntamente com alguns dados adicionais utilizando a Azure Data Share. 

Os dados usados neste laboratório são dados de táxi de Nova Iorque. Para importá-lo na sua base de dados na Base de Dados SQL, descarregue o [ficheiro bacpac de dados de táxi.](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

* **Base de Dados Azure SQL**: Se não tiver um DB SQL, aprenda a [criar uma conta DB SQL](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)

* **Conta de armazenamento Azure Data Lake Storage Gen2**: Se não tiver uma conta de armazenamento ADLS Gen2, aprenda a [criar uma conta de armazenamento ADLS Gen2](../storage/common/storage-account-create.md).

* **Azure Synapse Analytics**: Se não tiver um Azure Synapse Analytics, aprenda a [criar um exemplo de Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* **Azure Data Factory**: Se ainda não criou uma fábrica de dados, veja como [criar uma fábrica de dados.](./quickstart-create-data-factory-portal.md)

* **Azure Data Share**: Se não criou uma partilha de dados, veja como [criar uma partilha de dados](../data-share/share-your-data.md#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Confiem do seu ambiente Azure Data Factory

Nesta secção, você vai aprender a aceder à experiência do utilizador da Azure Data Factory (ADF UX) a partir do portal Azure. Uma vez no ADF UX, irá configurar três serviços ligados para cada uma das lojas de dados que estamos a usar: Azure SQL DB, ADLS Gen2 e Azure Synapse Analytics.

Na Azure Data Factory os serviços ligados definem a informação de ligação a recursos externos. A Azure Data Factory suporta atualmente mais de 85 conectores.

### <a name="open-the-azure-data-factory-ux"></a>Abra o Azure Data Factory UX

1. Abra o [portal Azure](https://portal.azure.com) no Microsoft Edge ou no Google Chrome.
1. Utilizando a barra de pesquisa no topo da página, procure por 'Data Factorys'

    ![Portal 1](media/lab-data-flow-data-share/portal1.png)
1. Clique no seu recurso de fábrica de dados para abrir a sua lâmina de recurso.

    ![Portal 2](media/lab-data-flow-data-share/portal2.png)
1. Clique em **Author and Monitor** para abrir o ADF UX. O ADF UX também pode ser acedido a adf.azure.com.

    ![Portal 3](media/lab-data-flow-data-share/portal3.png)
1. Será redirecionado para a página inicial do ADF UX. Esta página contém quick-starts, vídeos instrutivos e links para tutoriais para aprender conceitos de fábrica de dados. Para começar a autorizar, clique no ícone do lápis na barra lateral esquerda.

    ![Configuração do portal](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço ligado da Base de Dados SQL do Azure

1. A página de autoria é onde se criam recursos de fábrica de dados, tais como oleodutos, conjuntos de dados, fluxos de dados, gatilhos e serviços ligados. Para criar um serviço ligado, clique no botão **Ligações** no canto inferior direito.

    ![Configuração do portal 2](media/lab-data-flow-data-share/configure2.png)
1. No separador de ligações, clique em **Novo** para adicionar um novo serviço ligado.

    ![Configuração do portal 3](media/lab-data-flow-data-share/configure3.png)
1. O primeiro serviço ligado que irá configurar é um Azure SQL DB. Pode utilizar a barra de pesquisa para filtrar a lista de data store. Clique no azulejo da **Base de Dados Azure SQL** e clique em continuar.

    ![Configuração do portal 4](media/lab-data-flow-data-share/configure-4.png)
1. No painel de configuração SQL DB, introduza 'SQLDB' como o nome de serviço ligado. Introduza as suas credenciais para permitir que a fábrica de dados se conecte à sua base de dados. Se estiver a utilizar a autenticação SQL, insira o nome do servidor, a base de dados, o nome de utilizador e a palavra-passe. Pode verificar se as informações de ligação estão corretas clicando na **ligação test**. Clique em **Criar** quando terminar.

    ![Configuração do portal 5](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Criar um serviço Azure Synapse Analytics ligado

1. Repita o mesmo processo para adicionar um serviço Azure Synapse Analytics ligado. No separador de ligações, clique em **Novo**. Selecione o azulejo **Azure Synapse Analytics** e clique em continuar.

    ![Configuração do portal 6](media/lab-data-flow-data-share/configure-6.png)
1. No painel de configuração de serviço ligado, introduza 'SQLDW' como o nome de serviço ligado. Introduza as suas credenciais para permitir que a fábrica de dados se conecte à sua base de dados. Se estiver a utilizar a autenticação SQL, insira o nome do servidor, a base de dados, o nome de utilizador e a palavra-passe. Pode verificar se as informações de ligação estão corretas clicando na **ligação test**. Clique em **Criar** quando terminar.

    ![Configuração do portal 7](media/lab-data-flow-data-share/configure-7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Criar um serviço ligado ao Azure Data Lake Storage Gen2

1. O último serviço ligado necessário para este laboratório é um Azure Data Lake Storage gen2.  No separador de ligações, clique em **Novo**. Selecione o azulejo **Azure Data Lake Storage Gen2** e clique em continuar.

    ![Configuração do portal 8](media/lab-data-flow-data-share/configure8.png)
1. No painel de configuração de serviço ligado, insira 'ADLSGen2' como o nome de serviço ligado. Se estiver a utilizar a autenticação da chave conta, selecione a sua conta de armazenamento ADLS Gen2 a partir do dropdown do **nome da conta de Armazenamento.** Pode verificar se as informações de ligação estão corretas clicando na **ligação test**. Clique em **Criar** quando terminar.

    ![Configuração do portal 9](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Ligue o modo de depuramento do fluxo de dados

Na secção *Transforme os dados usando o fluxo de dados de mapeamento,* você estará construindo fluxos de dados de mapeamento. Uma boa prática antes de construir fluxos de dados de mapeamento é ligar o modo de depuração, o que permite testar a lógica de transformação em segundos num cluster de faíscas ativa.

Para ligar o depurador, clique no slider **de depurador de fluxo de dados** na barra superior da fábrica. Clique em ok quando o diálogo de confirmação aparecer. O aglomerado levará cerca de 5-7 minutos para começar. Continue a *Ingerir dados do Azure SQL DB para a ADLS Gen2 utilizando a atividade da cópia* enquanto está a ser inicializado.

![Configuração do portal 10](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Ingerir dados usando a atividade de cópia

Nesta secção, você vai criar um pipeline com uma atividade de cópia que ingere uma mesa de um Azure SQL DB em uma conta de armazenamento ADLS Gen2. Você vai aprender a adicionar um pipeline, configurar um conjunto de dados e depurar um oleoduto através do ADF UX. O padrão de configuração utilizado nesta secção pode ser aplicado à cópia de uma loja de dados relacional para uma loja de dados baseada em ficheiros.

Na Azure Data Factory, um pipeline é um agrupamento lógico de atividades que juntos realizam uma tarefa. Uma atividade define uma operação para executar os seus dados. Um conjunto de dados aponta para os dados que pretende utilizar num serviço ligado.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Criar um oleoduto com uma atividade de cópia

1. No painel de recursos da fábrica, clique no ícone plus para abrir o novo menu de recursos. Selecione **Pipeline**.

    ![Cópia do portal 1](media/lab-data-flow-data-share/copy1.png)
1. No separador **Geral** da tela do gasoduto, nomeie o seu pipeline como 'IngestAndTransformTaxiData'.

    ![Cópia do portal 2](media/lab-data-flow-data-share/copy2.png)
1. No painel de atividades da tela do gasoduto, abra o acordeão **Move and Transform** e arraste a atividade de **dados copy** para a tela. Dê à atividade da cópia um nome descritivo como 'IngestIntoADLS'.

    ![Cópia do portal 3](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Configurar conjunto de dados de origem DB Azure SQL

1. Clique no separador **'Fonte'** da atividade da cópia. Para criar um novo conjunto de dados, clique em **New**. Sua fonte será a mesa dbo. TripData' localizado no serviço ligado 'SQLDB' configurado anteriormente.

    ![Cópia do portal 4](media/lab-data-flow-data-share/copy4.png)
1. Procure a **Base de Dados Azure SQL** e clique em continuar.

    ![Cópia do portal 5](media/lab-data-flow-data-share/copy-5.png)
1. Ligue para o seu conjunto de dados 'TripData'. Selecione 'SQLDB' como o seu serviço ligado. Selecione o nome da mesa 'dbo. TripData' do dropdown do nome da mesa. Importar o esquema **Da ligação/loja.** Clique em OK quando terminar.

    ![Cópia do portal 6](media/lab-data-flow-data-share/copy6.png)

Criou com sucesso o seu conjunto de dados de origem. Certifique-se de que, nas definições de origem, a **tabela** de valor predefinido é selecionada no campo de consulta de utilização.

### <a name="configure-adls-gen2-sink-dataset"></a>Configurar conjunto de dados de pia ADLS Gen2

1. Clique no **separador Sink** da atividade da cópia. Para criar um novo conjunto de dados, clique em **New**.

    ![Cópia do portal 7](media/lab-data-flow-data-share/copy7.png)
1. Procure **por Azure Data Lake Storage Gen2** e clique em continuar.

    ![Cópia do portal 8](media/lab-data-flow-data-share/copy8.png)
1. No painel de formato selecionado, selecione **DelimitedText** enquanto está a escrever para um ficheiro csv. Clique para continuar.

    ![Cópia do portal 9](media/lab-data-flow-data-share/copy9.png)
1. Nomeie o seu conjunto de dados da pia 'TripDataCSV'. Selecione 'ADLSGen2' como o seu serviço ligado. Insira onde pretende escrever o seu ficheiro csv. Por exemplo, pode escrever os seus dados para arquivar `trip-data.csv` no recipiente `staging-container` . Desa **estação como cabeçalho** para verdade, pois pretende que os seus dados de saída tenham cabeçalhos. Uma vez que ainda não existe nenhum ficheiro no destino, desemote **o esquema de importação** para **Nenhum**. Clique em OK quando terminar.

    ![Cópia do portal 10](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Teste a atividade da cópia com uma corrida de depuração de gasoduto

1. Para verificar se a sua atividade de cópia está a funcionar corretamente, clique em **Debug** na parte superior da tela do gasoduto para executar uma execução de depuração. Uma corrida de depuração permite-lhe testar o seu oleoduto de ponta a ponta ou até um ponto de rutura antes de publicá-lo no serviço de fábrica de dados.

    ![Cópia do portal 11](media/lab-data-flow-data-share/copy11.png)
1. Para monitorizar a sua execução de depuragem, vá ao **separador de saída** da tela do gasoduto. O ecrã de monitorização será automaticamente a cada 20 segundos ou quando clicar manualmente no botão de atualização. A atividade da cópia tem uma visão de monitorização especial, que pode ser acedido clicando no ícone de óculos na coluna **Ações.**

    ![Cópia do portal 12](media/lab-data-flow-data-share/copy12.png)
1. A visão de monitorização da cópia fornece os detalhes de execução da atividade e as características de desempenho. Pode ver informações como a leitura/escrita de dados, linhas lidas/escritas, ficheiros lidos/escritos e produção. Se configurar tudo corretamente, deverá ver 49.999 linhas escritas num único ficheiro na pia do ADLS.

    ![Cópia do portal 13](media/lab-data-flow-data-share/copy13.png)
1. Antes de passar para a secção seguinte, sugere-se que publique as suas alterações no serviço de fábrica de dados clicando em **Publicar tudo** na barra de topo da fábrica. Embora não seja abrangida por este laboratório, a Azure Data Factory suporta a integração total do git. A integração do Git permite o controlo da versão, a poupança iterativa num repositório e a colaboração numa fábrica de dados. Para mais informações, consulte [o controlo de fontes na Azure Data Factory](./source-control.md#troubleshooting-git-integration).

    ![Portal publicar 1](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Transformar dados com o fluxo de dados de mapeamento

Agora que copiou com sucesso dados para o Azure Data Lake Storage, é hora de juntar esses dados e agregar esses dados num armazém de dados. Utilizaremos o fluxo de dados de mapeamento, o serviço de transformação visualmente projetado pela Azure Data Factory. O mapeamento dos fluxos de dados permite que os utilizadores desenvolvam a lógica de transformação sem código e executem-nos em clusters de faíscas geridos pelo serviço ADF.

O fluxo de dados criado neste passo interior junta-se ao conjunto de dados 'TripDataCSV' criado na secção anterior com uma tabela 'dbo. TripFares' armazenado em 'SQLDB' com base em quatro colunas-chave. Em seguida, os dados são agregados com base na coluna `payment_type` para calcular a média de certos campos e escritos numa tabela Azure Synapse Analytics.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Adicione uma atividade de fluxo de dados ao seu pipeline

1. No painel de atividades da tela do gasoduto, abra o acordeão **Move and Transform** e arraste a atividade de fluxo de **dados** para a tela.

    ![Fluxo de dados do portal 1](media/lab-data-flow-data-share/dataflow1.png)
1. No painel lateral que se abre, **selecione Criar um novo fluxo de dados** e escolha o fluxo de **dados de mapeamento**. Clique em **OK**.

    ![Fluxo de dados do portal 2](media/lab-data-flow-data-share/dataflow2.png)
1. Você será direcionado para a tela de fluxo de dados onde você vai construir a sua lógica de transformação. No separador geral, nomeie o seu fluxo de dados 'JoinAndAggregateData'.

    ![Fluxo de dados do portal 3](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Configure a sua fonte de dados csv de dados de viagem

1. A primeira coisa que queres fazer é configurar as tuas duas transformações de origem. A primeira fonte irá apontar para o conjunto de dados 'TripDataCSV' DelimitedText. Para adicionar uma transformação de fonte, clique na caixa **Add Source** na tela.

    ![Fluxo de dados do portal 4](media/lab-data-flow-data-share/dataflow4.png)
1. Nomeie a sua fonte 'TripDataCSV' e selecione o conjunto de dados 'TripDataCSV' a partir da entrega de origem. Se se lembrar, não importou um esquema inicialmente ao criar este conjunto de dados, uma vez que não havia dados. Uma vez `trip-data.csv` que existe agora, clique em **Editar** para ir ao separador definições de conjunto de dados.

    ![Fluxo de dados do portal 5](media/lab-data-flow-data-share/dataflow5.png)
1. Vá ao separador **Schema** e clique em **Esquema de Importação**. Selecione **Da ligação/loja** para importar diretamente da loja de ficheiros. Devem aparecer 14 colunas de corda tipo.

    ![Fluxo de dados do portal 6](media/lab-data-flow-data-share/dataflow6.png)
1. Volte ao fluxo de dados 'JoinAndAggregateData'. Se o seu cluster de depurador tiver começado (indicado por um círculo verde ao lado do depurador de depurador), pode obter uma imagem instantânea dos dados no **separador Data Preview.** Clique em **Refresh** para obter uma pré-visualização de dados.

    ![Fluxo de dados do portal 7](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> A pré-visualização de dados não escreve dados.

### <a name="configure-your-trip-fares-sql-db-source"></a>Configure as suas tarifas de viagem SQL DB fonte

1. A segunda fonte que está a adicionar irá apontar para a mesa DB 'dbo' do SQL. TripFares. Sob a sua fonte 'TripDataCSV', haverá outra caixa **Add Source.** Clique nele para adicionar uma nova transformação de origem.

    ![Fluxo de dados do portal 8](media/lab-data-flow-data-share/dataflow8.png)
1. Nomeie esta fonte 'TripFaresSQL'. Clique em **Novo** ao lado do campo de conjunto de dados de origem para criar um novo conjunto de dados SQL DB.

    ![Fluxo de dados do portal 9](media/lab-data-flow-data-share/dataflow9.png)
1. Selecione o azulejo **da Base de Dados Azure SQL** e clique em continuar. *Nota: Pode notar que muitos dos conectores na fábrica de dados não são suportados no fluxo de dados de mapeamento. Para transformar dados de uma destas fontes, ingere-os numa fonte suportada utilizando a atividade de cópia*.

    ![Fluxo de dados do portal 10](media/lab-data-flow-data-share/dataflow-10.png)
1. Ligue para o seu conjunto de dados 'TripFares'. Selecione 'SQLDB' como o seu serviço ligado. Selecione o nome da mesa 'dbo. TripFares do dropdown do nome da mesa. Importar o esquema **Da ligação/loja.** Clique em OK quando terminar.

    ![Fluxo de dados do portal 11](media/lab-data-flow-data-share/dataflow11.png)
1. Para verificar os seus dados, ressaça uma pré-visualização de dados no separador **Data Preview.**

    ![Fluxo de dados do portal 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Junte-se ao TripDataCSV e TripFaresSQL

1. Para adicionar uma nova transformação, clique no ícone plus no canto inferior direito de 'TripDataCSV'. Em **várias entradas/saídas,** selecione **'Juntar-se'.**

    ![Portal aderir 1](media/lab-data-flow-data-share/join1.png)
1. Nomeie a sua transformação de junção 'InnerJoinWithTripFares'. Selecione 'TripFaresSQL' a partir do dropdown de fluxo certo. Selecione **Interior** como o tipo de junção. Para saber mais sobre os diferentes tipos de junção no fluxo de dados de mapeamento, consulte [os tipos de junção](./data-flow-join.md#join-types).

    Selecione quais as colunas que deseja combinar a partir de cada corrente através do dropdown **das condições de Junção.** Para adicionar uma condição adicional de junção, clique no ícone plus ao lado de uma condição existente. Por predefinição, todas as condições de junção são combinadas com um operador E, o que significa que todas as condições devem ser satisfeitas para uma correspondência. Neste laboratório, queremos combinar com `medallion` `hack_license` colunas, `vendor_id` e `pickup_datetime`

    ![Portal juntar 2](media/lab-data-flow-data-share/join2.png)
1. Verifique se juntou 25 colunas com sucesso juntamente com uma pré-visualização de dados.

    ![Portal aderir 3](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Agregado por payment_type

1. Depois de completar a sua transformação de junção, adicione uma transformação agregada clicando no ícone plus ao lado de 'InnerJoinWithTripFares. Escolha **agregado** sob **o modificador Schema**.

    ![Portal agg 1](media/lab-data-flow-data-share/agg1.png)
1. Nomeie a sua transformação agregada 'Agregação PorPaymentType'. Selecione `payment_type` como grupo por coluna.

    ![Portal agg 2](media/lab-data-flow-data-share/agg2.png)
1. Vá ao **separador Agregados.** Aqui, especificará duas agregações:
    * A tarifa média agrupada por tipo de pagamento
    * A distância total de viagem agrupada por tipo de pagamento

    Primeiro, vai criar a expressão de tarifa média. Na caixa de texto com a etiqueta **Adicionar ou selecionar uma coluna,** introduza 'average_fare'.

    ![Portal agg 3](media/lab-data-flow-data-share/agg3.png)
1. Para introduzir uma expressão de agregação, clique na caixa azul com a etiqueta **Entrar expressão**. Isto abrirá o construtor de expressão de fluxo de dados, uma ferramenta usada para criar visualmente expressões de fluxo de dados utilizando esquemas de entrada, funções e operações incorporadas e parâmetros definidos pelo utilizador. Para obter mais informações sobre as capacidades do construtor de expressão, consulte a documentação do [construtor de expressão.](./concepts-data-flow-expression-builder.md)

    Para obter a tarifa média, utilize a `avg()` função de agregação para agregar a `total_amount` coluna fundida a um inteiro com `toInteger()` . Na linguagem de expressão de fluxo de dados, isto é definido como `avg(toInteger(total_amount))` . Clique **em Guardar e termine** quando terminar.

    ![Portal agg 4](media/lab-data-flow-data-share/agg4.png)
1. Para adicionar uma expressão de agregação adicional, clique no ícone plus ao lado `average_fare` de . Selecione **Adicionar coluna**.

    ![Portal agg 5](media/lab-data-flow-data-share/agg5.png)
1. Na caixa de texto com a etiqueta **Adicionar ou selecionar uma coluna,** introduza 'total_trip_distance'. Como no último passo, abra o construtor de expressão para entrar na expressão.

    Para obter a distância total da viagem, utilize a `sum()` função de agregação para agregar a `trip_distance` coluna fundida a um inteiro com `toInteger()` . Na linguagem de expressão de fluxo de dados, isto é definido como `sum(toInteger(trip_distance))` . Clique **em Guardar e termine** quando terminar.

    ![Portal agg 6](media/lab-data-flow-data-share/agg6.png)
1. Teste a sua lógica de transformação no **separador Data Preview.** Como pode ver, há significativamente menos linhas e colunas do que anteriormente. Apenas os três grupos por e as colunas de agregação definidas nesta transformação continuam a jusante. Como existem apenas cinco grupos de pagamento na amostra, apenas cinco linhas são outputadas.

    ![Portal agg 7](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Configure-lhe Azure Synapse Analytics pia

1. Agora que terminámos a nossa lógica de transformação, estamos prontos para afundar os nossos dados numa tabela Azure Synapse Analytics. Adicione uma transformação de pia na secção **Destino.**

    ![Porta 1](media/lab-data-flow-data-share/sink1.png)
1. Diga o nome da pia 'SQLDWSink'. Clique em **Novo** ao lado do campo de conjunto de dados da pia para criar um novo conjunto de dados Azure Synapse Analytics.

    ![Porta 2](media/lab-data-flow-data-share/sink2.png)

1. Selecione o azulejo **Azure Synapse Analytics** e clique em continuar.

    ![Porta 3](media/lab-data-flow-data-share/sink-3.png)
1. Ligue para o seu conjunto de dados 'AggregatedTaxiData'. Selecione 'SQLDW' como o seu serviço ligado. **Selecione Criar uma nova tabela** e nomear o novo dbo de tabela. AgregaçãoTaxiData. Clique em OK quando terminar

    ![Porta 4](media/lab-data-flow-data-share/sink4.png)
1. Vá ao **separador Definições** da pia. Uma vez que estamos a criar uma nova tabela, precisamos de selecionar a **mesa Recreate** em ação de mesa. Desmarcar **Ativar a paragem,** que alterna quer estejamos a inserir linha a linha ou em lote.

    ![Porta 5](media/lab-data-flow-data-share/sink5.png)

Criou com sucesso o fluxo de dados. Agora é hora de executá-lo em uma atividade de oleoduto.

### <a name="debug-your-pipeline-end-to-end"></a>Depurar o seu oleoduto de ponta a ponta

1. Volte ao separador para o pipeline **IngestAndTransformData.** Note a caixa verde na atividade de cópia 'IngestIntoADLS'. Arraste-o para a atividade de fluxo de dados 'JoinAndAggregateData'. Isto cria um "sucesso", o que faz com que a atividade de fluxo de dados só seja executada se a cópia for bem sucedida.

    ![Oleoduto portal 1](media/lab-data-flow-data-share/pipeline1.png)
1. Como fizemos para a atividade de cópia, clique em **Debug** para executar uma corrida de depuração. Para a depuração, a atividade de fluxo de dados usará o cluster de depuração ativo em vez de girar um novo cluster. Este oleoduto levará um pouco mais de um minuto para ser executado.

    ![Oleoduto portal 2](media/lab-data-flow-data-share/pipeline2.png)
1. Tal como a atividade da cópia, o fluxo de dados tem uma vista de monitorização especial acedida pelo ícone de óculos no final da atividade.

    ![Oleoduto portal 3](media/lab-data-flow-data-share/pipeline3.png)
1. Na vista de monitorização, pode ver um gráfico de fluxo de dados simplificado juntamente com os tempos de execução e linhas em cada fase de execução. Se feito corretamente, deverá ter agregado 49.999 linhas em cinco linhas nesta atividade.

    ![Oleoduto portal 4](media/lab-data-flow-data-share/pipeline4.png)
1. Pode clicar numa transformação para obter detalhes adicionais sobre a sua execução, tais como informações de partição e colunas novas/atualizadas/largadas.

    ![Oleoduto portal 5](media/lab-data-flow-data-share/pipeline5.png)

Já completou a parte da fábrica de dados deste laboratório. Publique os seus recursos se quiser operacionalizá-los com gatilhos. Executou com sucesso um oleoduto que ingeriu dados da Azure SQL Database para Azure Data Lake Storage usando a atividade da cópia e, em seguida, agregado esses dados em um Azure Synapse Analytics. Pode verificar se os dados foram escritos com sucesso olhando para o próprio SQL Server.

## <a name="share-data-using-azure-data-share"></a>Partilhar dados com o Azure Data Share

Nesta secção, você vai aprender a configurar uma nova partilha de dados usando o portal Azure. Isto implicará a criação de uma nova partilha de dados que conterá conjuntos de dados da Azure Data Lake Store Gen2 e Azure Synapse Analytics. Em seguida, configurará um calendário instantâneo, que dará aos consumidores de dados a opção de atualizar automaticamente os dados que estão a ser partilhados com eles. Em seguida, convidará os destinatários para a sua partilha de dados. 

Uma vez criada uma partilha de dados, trocará de chapéus e tornar-se-á o *consumidor de dados.* Como consumidor de dados, você vai andar pelo fluxo de aceitar um convite de partilha de dados, configurando onde gostaria que os dados fossem recebidos e mapeando conjuntos de dados para diferentes locais de armazenamento. Em seguida, irá ativar uma imagem instantânea, que irá copiar os dados partilhados consigo para o destino especificado. 

### <a name="sharing-data-data-provider-flow"></a>Partilha de dados (fluxo de fornecedor de dados)

1. Abra o portal Azure no Microsoft Edge ou no Google Chrome.

1. Utilizando a barra de pesquisa no topo da página, procure por **Data Shares**

    ![Anúncios de portal](media/lab-data-flow-data-share/portal-ads.png)

1. Selecione a conta de partilha de dados com 'Fornecedor' no nome. Por exemplo, **DataProvider0102**. 

1. Selecione **Começar a partilhar os seus dados**

    ![Começar a partilhar](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Selecione **+Criar** para começar a configurar a sua nova partilha de dados. 

1. Em **nome de partilha,** especifique um nome à sua escolha. Este é o nome da partilha que será visto pelo seu consumidor de dados, por isso certifique-se de lhe dar um nome descritivo como TaxiData.

1. Em **Descrição**, coloque uma frase, que descreve o conteúdo da partilha de dados. A partilha de dados conterá dados mundiais de viagem de táxi que são armazenados em várias lojas, incluindo a Azure Synapse Analytics e a Azure Data Lake Store. 

1. Nos **Termos de Utilização**, especifique um conjunto de termos aos quais pretende que o seu consumidor de dados adira. Alguns exemplos incluem "Não distribua estes dados fora da sua organização" ou "Consulte o acordo legal". 

    ![Partilhar detalhes](media/lab-data-flow-data-share/ads-details.png)

1. Selecione **Continuar**. 

1. Selecione **Adicionar conjuntos de dados** 

    ![Adicionar conjunto de dados 1](media/lab-data-flow-data-share/add-dataset.png)

1. Selecione **Azure Synapse Analytics** para selecionar uma tabela da Azure Synapse Analytics em que as suas transformações ADF aterraram.

    ![Adicionar conjunto de dados sql](media/lab-data-flow-data-share/add-dataset-sql.png)


1. Será-lhe dado um guião para ser executado antes de poder prosseguir. O script fornecido cria um utilizador na base de dados SQL para permitir que o MSI Azure Data Share autente em seu nome. 

> [!IMPORTANT]
> Antes de executar o script, deve definir-se como o Administrador Ative Directory para o SQL Server. 

1. Abra um novo separador e navegue até ao portal Azure. Copie o script fornecido para criar um utilizador na base de dados a partir daí a que pretende partilhar dados. Faça-o iniciando sessão na base de dados EDW utilizando o Explorador de Consultas (pré-visualização) utilizando a autenticação AAD. 

    Terá de modificar o script para que o utilizador criado esteja contido dentro dos parênteses. Por exemplo:
    
    criar utilizador [dataprovider-xxxx] a partir de login externo;  executivo sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Volte para Azure Data Share onde estava a adicionar conjuntos de dados à sua partilha de dados. 

1. Selecione **EDW** e, em seguida, selecione **AggregatedTaxiData** para a tabela. 

1. Selecione **Adicionar conjunto de dados**

    Temos agora uma tabela SQL que faz parte do nosso conjunto de dados. Em seguida, adicionaremos conjuntos de dados adicionais da Azure Data Lake Store. 

1. Selecione **Adicionar conjunto de dados** e selecione **Azure Data Lake Store Gen2**

    ![Adicionar adls de conjunto de dados](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Selecione **Seguinte**

1. Expandir *wwtaxidata*. Expandir *dados de táxi de Boston.* Note que pode partilhar até o nível de ficheiro. 

1. Selecione a pasta *Dados de Táxi de Boston* para adicionar toda a pasta à sua partilha de dados. 

1. Selecione **Adicionar conjuntos de dados**

1. Reveja os conjuntos de dados que foram adicionados. Deverá ter uma tabela SQL e uma pasta ADLS Gen2 adicionadas à sua partilha de dados. 

1. Selecione **Continuar**

1. Neste ecrã, pode adicionar destinatários à sua partilha de dados. Os destinatários que adicionar receberão convites para a sua partilha de dados. Para efeitos deste laboratório, deve adicionar 2 endereços de e-mail:

    1. O endereço de e-mail da assinatura Azure em que está. 

        ![Adicionar destinatários](media/lab-data-flow-data-share/add-recipients.png)

    1. Adicione o consumidor de dados fictícios denominado *janedoe@fabrikam.com* .

1. Neste ecrã, pode configurar uma Definição instantânea para o seu consumidor de dados. Isto permitir-lhes-á receber atualizações regulares dos seus dados num intervalo definido por si. 

1. Verifique **o Horário instantâneo** e configuure uma atualização horária dos seus dados utilizando a queda de *Recorrência.*  

1. Selecione **Criar**.

    Tem agora uma partilha ativa de dados. Permite rever o que pode ver como um fornecedor de dados quando cria uma partilha de dados. 

1. Selecione a partilha de dados que criou, intitulada **DataProvider**. Pode navegar para ele selecionando **Ações Enviadas** em **Partilha de Dados.** 

1. Clique na programação snapshot. Pode desativar o horário de instantâneo se escolher. 

1. Em seguida, selecione o **separador Datasets.** Pode adicionar conjuntos de dados adicionais a esta partilha de dados depois de criado. 

1. Selecione o **separador 'Partilhar' subscrições.** Ainda não existem subscrições de ações porque o seu consumidor de dados ainda não aceitou o seu convite.

1. Navegue para o separador **Convites.** Aqui, você verá uma lista de convites pendentes. 

    ![Convites pendentes](media/lab-data-flow-data-share/pending-invites.png)

1. Selecione o convite para *janedoe@fabrikam.com* . Selecione Eliminar. Se o seu destinatário ainda não aceitou o convite, eles não poderão mais fazê-lo. 

1. Selecione o **separador Histórico.** Nada é mostrado ainda porque o seu consumidor de dados ainda não aceitou o seu convite e desencadeou uma foto. 

### <a name="receiving-data-data-consumer-flow"></a>Receber dados (Fluxo do consumidor de dados)

Agora que revimos a nossa partilha de dados, estamos prontos para mudar de contexto e usar o nosso chapéu de consumidor de dados. 

Deverá agora ter um convite Azure Data Share na sua caixa de entrada da Microsoft Azure. Lançar o Outlook Web Access (outlook.com) e iniciar sessão utilizando as credenciais fornecidas para a sua subscrição Azure.

No e-mail que deveria ter recebido, clique em "Ver convite >". Neste momento, vai simular a experiência do consumidor de dados ao aceitar um convite dos fornecedores de dados para a sua partilha de dados. 

![Convite por e-mail](media/lab-data-flow-data-share/email-invite.png)

Pode ser solicitado que selecione uma subscrição. Certifique-se de selecionar a subscrição em que tem trabalhado para este laboratório. 

1. Clique no convite intitulado *DataProvider*. 

1. Neste ecrã de Convite, irá notar vários detalhes sobre a partilha de dados que configuraram anteriormente como provedor de dados. Reveja os detalhes e aceite os termos de utilização se fornecido.

1. Selecione o Grupo de Subscrição e Recursos que já existe para o seu laboratório. 

1. Para **conta de partilha de dados**, selecione **DataConsumer**. Também pode criar uma nova conta de partilha de dados. 

1. Ao lado **do nome de partilha Recebido,** irá notar que o nome de ação padrão é o nome especificado pelo provedor de dados. Dê à partilha um nome amigável que descreva os dados que está prestes a receber, por **exemplo, TaxiDataShare**.

    ![Convite aceita](media/lab-data-flow-data-share/consumer-accept.png)

1. Pode optar por **aceitar e configurar agora** ou aceitar e **configurar mais tarde.** Se optar por aceitar e configurar agora, irá especificar uma conta de armazenamento onde todos os dados devem ser copiados. Se optar por aceitar e configurar mais tarde, os conjuntos de dados da partilha serão desapeitados e terá de os mapear manualmente. Vamos optar por isso mais tarde. 

1. Selecione **Aceitar e configurar mais tarde**. 

    Ao configurar esta opção, é criada uma subscrição de ações, mas não há lugar para os dados aterrarem uma vez que nenhum destino foi mapeado. 

    Em seguida, configuraremos os mapeamentos do conjunto de dados para a partilha de dados. 

1. Selecione a Partilha Recebida (o nome especificado no passo 5).

    **O instantâneo** do gatilho está acinzentado, mas a parte é Ativa. 

1. Selecione o **separador Datasets.** Note que cada conjunto de dados não é mapeado, o que significa que não tem destino para copiar dados. 

    ![conjuntos de dados não mapeados](media/lab-data-flow-data-share/unmapped.png)

1. Selecione a Tabela de Análise Sinapse Azure e, em seguida, selecione **+ Mapa para Alvo**.

1. No lado direito do ecrã, selecione o **tipo de dados alvo** para baixo. 

    Pode mapear os dados SQL para uma vasta gama de lojas de dados. Neste caso, vamos mapear para uma Base de Dados Azure SQL.

    ![mapeamento](media/lab-data-flow-data-share/mapping-options.png)
    
    (Opcional) Selecione **Azure Data Lake Store Gen2** como o tipo de dados-alvo. 
    
    (Opcional) Selecione a conta de Subscrição, Grupo de Recursos e Armazenamento em que tem estado a trabalhar. 
    
    (Opcional) Pode optar por receber os dados no seu lago de dados em formato csv ou parquet. 

1. Junto ao **tipo de dados Target**, selecione Azure SQL Database. 

1. Selecione a conta de Subscrição, Grupo de Recursos e Armazenamento em que tem estado a trabalhar. 

    ![mapa para sql](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Antes de poder prosseguir, terá de criar um novo utilizador no SQL Server executando o script fornecido. Primeiro, copie o guião fornecido para a sua área de transferência. 

1. Abra um novo separador do portal Azure. Não feche a conta existente, pois terá de voltar a ele num instante. 

1. No novo separador que abriu, navegue para **as bases de dados SQL**.

1. Selecione a base de dados SQL (deve haver apenas uma na sua subscrição). Tenha cuidado para não selecionar o armazém de dados. 

1. Selecione **editor de consulta (pré-visualização)**

1. Utilize a autenticação AAD para iniciar sessão no Editor de Consulta. 

1. Executar a consulta fornecida na sua partilha de dados (copiada para a área de transferência no passo 14). 

    Este comando permite que o serviço Azure Data Share utilize identidades geridas para serviços Azure para autenticar no SQL Server para poder copiar dados nele. 

1. Volte para o separador original e selecione **Map para o alvo**.

1. Em seguida, selecione a pasta Azure Data Lake Gen2 que faz parte do conjunto de dados e mapeeei-la para uma conta de Armazenamento Azure Blob. 

    ![storage](media/lab-data-flow-data-share/storage-map.png)

    Com todos os conjuntos de dados mapeados, está agora pronto para começar a receber dados do fornecedor de dados. 

    ![mapeado](media/lab-data-flow-data-share/all-mapped.png)
    
1. Selecione **Detalhes**. 

    Note que **o trigger snapshot** já não está acinzentado, uma vez que a partilha de dados tem agora destinos para copiar.

1. Selecione Trigger snapshot -> Full Copy. 

    ![gatilho](media/lab-data-flow-data-share/trigger-full.png)

    Isto começará a copiar dados na sua nova conta de partilha de dados. Num cenário real, estes dados viriam de terceiros. 

    Levará aproximadamente 3-5 minutos para os dados se cruzarem. Pode monitorizar o progresso clicando no **separador Histórico.** 

    Enquanto espera, navegue para a partilha de dados original (DataProvider) e veja o estado do separador Subscrições de **Ações** e **Histórico.** Note que existe agora uma subscrição ativa, e como fornecedor de dados, também pode monitorizar quando o consumidor de dados começou a receber os dados partilhados com eles. 

1. Volte para a partilha de dados do consumidor de dados. Assim que o estado do gatilho for bem sucedido, navegue até à base de dados SQL de destino e ao lago de dados para ver se os dados aterraram nas respetivas lojas. 

Parabéns, completou o laboratório!