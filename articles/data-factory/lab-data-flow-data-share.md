---
title: Integração de dados utilizando a Azure Data Factory e a Azure Data Share
description: Copiar, transformar e partilhar dados usando a Azure Data Factory e a Azure Data Share
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 7d453b2724c308e48366d653a51d9e6aa8e82c96
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415932"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Integração de dados utilizando a Azure Data Factory e a Azure Data Share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

À medida que os clientes embarcam nos seus modernos projetos de armazenamento de dados e analíticos, exigem não só mais dados, mas também mais visibilidade nos seus dados através da sua propriedade de dados. Este workshop mergulha na forma como as melhorias na Azure Data Factory e na Azure Data Share simplificam a integração e gestão de dados no Azure. 

Desde permitir a ETL/ELT sem código sem código sem código sem código sem código sem código sem códigos até criar uma visão abrangente sobre os seus dados, as melhorias na Azure Data Factory capacitarão os seus engenheiros de dados a trazerem com confiança mais dados e, portanto, mais valor, para a sua empresa. A Azure Data Share permitir-lhe-á fazer negócios com a partilha de negócios de forma regida.

Neste workshop, utilizará a Azure Data Factory (ADF) para ingerir dados de uma base de dados Azure SQL (SQL DB) no Azure Data Lake Storage gen2 (ADLS gen2). Assim que aterrar os dados no lago, irá transformá-lo através de fluxos de dados de mapeamento, serviço de transformação nativa da fábrica de dados, e afundá-lo em Azure Synapse Analytics (anteriormente SQL DW). Em seguida, partilhará a tabela com dados transformados juntamente com alguns dados adicionais usando a Partilha de Dados do Azure. 

Os dados usados neste laboratório são dados de táxi de Nova Iorque. Para o importar na sua base de dados Azure SQL, descarregue o [ficheiro bacpac de dados de táxi.](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

* **Base de Dados Azure SQL**: Se não tiver um DB SQL, aprenda a [criar uma conta SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

* Conta de armazenamento do **Lago Azure Data Gen2**: Se não tiver uma conta de armazenamento ADLS Gen2, aprenda a criar uma conta de [armazenamento ADLS Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

* **Azure Synapse Analytics (anteriormente SQL DW)**: Se não tiver um Azure Synapse Analytics (ex-SQL DW), aprenda a [criar uma instância azure Synapse Analytics](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal).

* **Azure Data Factory**: Se ainda não criou uma fábrica de dados, veja como criar uma fábrica de [dados.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)

* **Azure Data Share**: Se não criou uma partilha de dados, veja como [criar uma partilha](https://docs.microsoft.com/azure/data-share/share-your-data#create-a-data-share-account)de dados .

## <a name="set-up-your-azure-data-factory-environment"></a>Instale o seu ambiente azure data factory

Nesta secção, você vai aprender a aceder à experiência de utilizador da Azure Data Factory (ADF UX) a partir do portal Azure. Uma vez no ADF UX, irá configurar três serviços ligados para cada uma das lojas de dados que estamos a utilizar: Azure SQL DB, ADLS Gen2 e Azure Synapse Analytics.

Na Azure Data Factory os serviços ligados definem a informação de ligação a recursos externos. A Azure Data Factory suporta atualmente mais de 85 conectores.

### <a name="open-the-azure-data-factory-ux"></a>Abra a Fábrica de Dados Azure UX

1. Abra o [portal Azure](https://portal.azure.com) no Microsoft Edge ou no Google Chrome.
1. Utilizando a barra de pesquisa no topo da página, procure por 'Fábricas de Dados'

    ![Portal](media/lab-data-flow-data-share/portal1.png)
1. Clique no seu recurso de fábrica de dados para abrir a sua lâmina de recursos.

    ![Portal](media/lab-data-flow-data-share/portal2.png)
1. Clique em **Autor e Monitor** para abrir o ADF UX. O ADF UX também pode ser acedido em adf.azure.com.

    ![Portal](media/lab-data-flow-data-share/portal3.png)
1. Será redirecionado para a página inicial do ADF UX. Esta página contém arranques rápidos, vídeos instrutivos e links para tutoriais para aprender conceitos de fábrica de dados. Para começar a autoria, clique no ícone do lápis na barra lateral esquerda.

    ![Portal](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço ligado à base de dados Azure SQL

1. A página de autoria é onde cria recursos de fábrica de dados, tais como oleodutos, conjuntos de dados, fluxos de dados, gatilhos e serviços ligados. Para criar um serviço ligado, clique no botão **Ligações** no canto inferior direito.

    ![Portal](media/lab-data-flow-data-share/configure2.png)
1. No separador de ligações, clique em **New** para adicionar um novo serviço ligado.

    ![Portal](media/lab-data-flow-data-share/configure3.png)
1. O primeiro serviço ligado que irá configurar é um Azure SQL DB. Pode utilizar a barra de pesquisa para filtrar a lista de lojas de dados. Clique no azulejo **Azure SQL Database** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/configure4.png)
1. No painel de configuração SQL DB, introduza 'SQLDB' como o seu nome de serviço ligado. Insira as suas credenciais para permitir que a fábrica de dados se conectem à sua base de dados. Se estiver a utilizar a autenticação SQL, introduza no nome do servidor, na base de dados, no seu nome de utilizador e na sua palavra-passe. Pode verificar se as suas informações de ligação estão corretas clicando na **ligação**do Teste . Clique em **Criar** quando terminar.

    ![Portal](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Criar um serviço ligado à Azure Synapse Analytics

1. Repita o mesmo processo para adicionar um serviço ligado à Azure Synapse Analytics. No separador de ligações, clique em **Novo**. Selecione o azulejo **Azure Synapse Analytics (anteriormente SQL DW)** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/configure6.png)
1. No painel de configuração do serviço ligado, introduza 'SQLDW' como o seu nome de serviço ligado. Insira as suas credenciais para permitir que a fábrica de dados se conectem à sua base de dados. Se estiver a utilizar a autenticação SQL, introduza no nome do servidor, na base de dados, no seu nome de utilizador e na sua palavra-passe. Pode verificar se as suas informações de ligação estão corretas clicando na **ligação**do Teste . Clique em **Criar** quando terminar.

    ![Portal](media/lab-data-flow-data-share/configure7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Criar um serviço ligado ao armazenamento do lago de dados Azure Gen2

1. O último serviço ligado necessário para este laboratório é um Azure Data Lake Storage gen2.  No separador de ligações, clique em **Novo**. Selecione o azulejo **Azure Data Lake Storage Gen2** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/configure8.png)
1. No painel de configuração do serviço ligado, introduza 'ADLSGen2' como o seu nome de serviço ligado. Se estiver a utilizar a autenticação da chave conta, selecione a sua conta de armazenamento adls gen2 a partir do dropdown do nome da **conta de armazenamento.** Pode verificar se as suas informações de ligação estão corretas clicando na **ligação**do Teste . Clique em **Criar** quando terminar.

    ![Portal](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Ativar o modo de depuração de fluxo de dados

Na secção Transform estão os dados utilizando o fluxo de dados de *mapeamento,* estará a construir fluxos de dados de mapeamento. Uma boa prática antes de construir fluxos de dados de mapeamento é ativar o modo de depuração, o que lhe permite testar a lógica de transformação em segundos num cluster de faíscas ativa.

Para ligar o depurador, clique no deslider **de depurador** de fluxo de dados na barra superior da fábrica. Clique bem quando os pop-ups de diálogo de confirmação. O aglomerado levará cerca de 5 a 7 minutos para começar. Continue a *utilizar os dados da Ingest do Azure SQL DB para o ADLS gen2 utilizando a atividade* de cópia enquanto está a inicializar.

![Portal](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Ingerir dados utilizando a atividade de cópia

Nesta secção, você vai criar um pipeline com uma atividade de cópia que ingere uma tabela de um Azure SQL DB em uma conta de armazenamento aDLS gen2. Você vai aprender a adicionar um pipeline, configurar um conjunto de dados e depurar um pipeline através do ADF UX. O padrão de configuração utilizado nesta secção pode ser aplicável à cópia de uma loja de dados relacional para uma loja de dados baseada em ficheiros.

Na Azure Data Factory, um oleoduto é um agrupamento lógico de atividades que, em conjunto, realizam uma tarefa. Uma atividade define uma operação para executar nos seus dados. Um conjunto de dados aponta para os dados que pretende utilizar num serviço ligado.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Criar um pipeline com uma atividade de cópia

1. No painel de recursos da fábrica, clique no ícone plus para abrir o novo menu de recursos. Selecione **Pipeline**.

    ![Portal](media/lab-data-flow-data-share/copy1.png)
1. No separador **Geral** da tela do gasoduto, nomeie o seu pipeline algo descritivo como 'IngestAndTransformTaxiData'.

    ![Portal](media/lab-data-flow-data-share/copy2.png)
1. No painel de atividades da tela do gasoduto, abra o acordeão **Move and Transform** e arraste a atividade de dados **copy** para a tela. Dê à atividade da cópia um nome descritivo como 'IngestIntoADLS'.

    ![Portal](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Configure Conjunto de dados de fonte SQL DB do Azure

1. Clique no separador **Fonte** da atividade da cópia. Para criar um novo conjunto de dados, clique em **New**. A sua fonte será a mesa'dbo. TripData' localizado no serviço ligado 'SQLDB' configurado anteriormente.

    ![Portal](media/lab-data-flow-data-share/copy4.png)
1. Procure a **Base de Dados Azure SQL** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/copy5.png)
1. Ligue para o seu conjunto de dados 'TripData'. Selecione 'SQLDB' como serviço ligado. Selecione o nome da tabela 'dbo. TripData' do nome da mesa dropdown. Importar o esquema **A partir da ligação/loja**. Clique ok quando terminar.

    ![Portal](media/lab-data-flow-data-share/copy6.png)

Criou com sucesso o seu conjunto de dados de origem. Certifique-se de que nas definições de origem, a **tabela** de valor predefinido é selecionada no campo de consulta de utilização.

### <a name="configure-adls-gen-2-sink-dataset"></a>Configure Conjunto de dados adls gen 2

1. Clique no **separador Sink** da atividade da cópia. Para criar um novo conjunto de dados, clique em **New**.

    ![Portal](media/lab-data-flow-data-share/copy7.png)
1. Procure o **Azure Data Lake Storage Gen2** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/copy8.png)
1. No painel de formato selecionado, selecione **DelimitedText** como está a escrever para um ficheiro CSV. Clique em continuar.

    ![Portal](media/lab-data-flow-data-share/copy9.png)
1. Nomeie o seu conjunto de dados do lavatório 'TripDataCSV'. Selecione 'ADLSGen2' como serviço ligado. Insira onde quiser escrever o seu ficheiro CSV. Por exemplo, pode escrever os `trip-data.csv` seus `staging-container`dados para arquivar no recipiente . Desloque a **primeira linha como cabeçalho** para verdade, pois pretende que os seus dados de saída tenham cabeçalhos. Uma vez que ainda não existe nenhum ficheiro no destino, coloque **o esquema de importação** para **nenhum.** Clique ok quando terminar.

    ![Portal](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Teste a atividade da cópia com uma corrida de depuração de gasodutos

1. Para verificar se a sua atividade de cópia está a funcionar corretamente, clique em **Debug** na parte superior da tela do gasoduto para executar uma execução de depuração. Uma execução de depuração permite-lhe testar o seu pipeline de ponta a ponta ou até um ponto de rutura antes de o publicar no serviço de fábrica de dados.

    ![Portal](media/lab-data-flow-data-share/copy11.png)
1. Para monitorizar a sua execução de depuração, dirija-se ao separador **de saída** da tela do gasoduto. O ecrã de monitorização atualiza-se automaticamente a cada 20 segundos ou quando clicar manualmente no botão de atualização. A atividade da cópia tem uma visão de monitorização especial que pode ser acedida clicando no ícone dos óculos na coluna **Ações.**

    ![Portal](media/lab-data-flow-data-share/copy12.png)
1. A visão de monitorização da cópia dá os detalhes de execução da atividade e as características de desempenho. Pode ver informações como leitura/escrita de dados, linhas lidas/escritas, ficheiros lidos/escritos e entradas. Se configurar tudo corretamente, deve ver 49.999 linhas escritas num ficheiro na pia ADLS.

    ![Portal](media/lab-data-flow-data-share/copy13.png)
1. Antes de passar para a secção seguinte, sugere-se que publique as suas alterações no serviço de fábrica de dados clicando em **publicar tudo** na barra superior da fábrica. Apesar de não estar abrangida por este laboratório, a Azure Data Factory suporta a integração completa do git. A integração git permite o controlo de versão, a poupança iterativa num repositório e a colaboração numa fábrica de dados. Para mais informações, consulte o [controlo de origem na Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#troubleshooting-git-integration).

    ![Portal](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Transformar dados com o fluxo de dados de mapeamento

Agora que copiou com sucesso dados para o Armazenamento do Lago De Dados Azure, está na hora de juntar e agregar esses dados num armazém de dados. Usaremos o fluxo de dados de mapeamento, o serviço de transformação da Azure Data Factory projetado visualmente. Os fluxos de dados de mapeamento permitem que os utilizadores desenvolvam códigos lógicos de transformação e executem-nos em clusters de faíscas geridos pelo serviço ADF.

O fluxo de dados criado neste passo interior junta-se ao conjunto de dados 'TripDataCSV' criado na secção anterior com uma tabela 'dbo. TripFares' armazenados em 'SQLDB' com base em quatro colunas-chave. Em seguida, os dados `payment_type` são agregados com base na coluna para calcular a média de certos campos e escritos numa tabela Azure Synapse Analytics.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Adicione uma atividade de fluxo de dados ao seu pipeline

1. No painel de atividades da tela do oleoduto, abra o acordeão **Move and Transform** e arraste a atividade de fluxo de **dados** para a tela.

    ![Portal](media/lab-data-flow-data-share/dataflow1.png)
1. No painel lateral que se abre, selecione **Criar novos fluxos** de dados e escolha o fluxo de **dados de Mapeamento**. Clique em **OK**.

    ![Portal](media/lab-data-flow-data-share/dataflow2.png)
1. Será direcionado para a tela de fluxo de dados onde estará a construir a sua lógica de transformação. No separador geral, nomeie o seu fluxo de dados 'JoinAndAggregateData'.

    ![Portal](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Configure a sua fonte de dados de viagem csv

1. A primeira coisa que quer fazer é configurar as suas duas transformações de origem. A primeira fonte apontará para o conjunto de dados 'TripDataCSV' DelimitedText. Para adicionar uma transformação de origem, clique na caixa **Add Source** na tela.

    ![Portal](media/lab-data-flow-data-share/dataflow4.png)
1. Nomeie a sua fonte 'TripDataCSV' e selecione o conjunto de dados 'TripDataCSV' a partir da queda de origem. Se bem se lembra, não importou um esquema inicialmente ao criar este conjunto de dados, uma vez que não havia dados. Uma `trip-data.csv` vez que existe agora, clique em **Editar** para ir ao separador de definições de conjunto de dados.

    ![Portal](media/lab-data-flow-data-share/dataflow5.png)
1. Vá a tab **Schema** e clique **em importar esquemas**. Selecione **entre ligar/armazenar** para importar diretamente da loja de ficheiros. Devem aparecer 14 colunas de cadeia de tipo.

    ![Portal](media/lab-data-flow-data-share/dataflow6.png)
1. Volte ao fluxo de dados 'JoinAndAggregateData'. Se o seu cluster de depuração tiver começado (indicado por um círculo verde ao lado do desordeiro), pode obter uma imagem instantânea dos dados no separador **'Pré-visualização** de dados.Clique em **Refresh'** para obter uma pré-visualização de dados.

    ![Portal](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> A pré-visualização de dados não escreve dados.

### <a name="configure-your-trip-fares-sql-db-source"></a>Configure as suas tarifas de viagem Fonte SQL DB

1. A segunda fonte que está a adicionar vai apontar para a tabela SQL DB 'dbo. TripFares. Sob a sua fonte 'TripDataCSV', haverá outra caixa **Add Source.** Clique nele para adicionar uma nova transformação de origem.

    ![Portal](media/lab-data-flow-data-share/dataflow8.png)
1. Nomeie esta fonte 'TripFaresSQL'. Clique em **Novo** ao lado do campo de conjunto de dados de origem para criar um novo conjunto de dados SQL DB.

    ![Portal](media/lab-data-flow-data-share/dataflow9.png)
1. Selecione o azulejo **azure SQL Database** e clique em continuar. *Nota: Pode notar que muitos dos conectores na fábrica de dados não são suportados no fluxo de dados de mapeamento. Para transformar dados de uma dessas fontes, ingere-os numa fonte suportada utilizando a atividade da cópia*.

    ![Portal](media/lab-data-flow-data-share/dataflow10.png)
1. Ligue para o seu conjunto de dados 'TripFares'. Selecione 'SQLDB' como serviço ligado. Selecione o nome da tabela 'dbo. TripFares do nome da mesa dropdown. Importar o esquema **A partir da ligação/loja**. Clique ok quando terminar.

    ![Portal](media/lab-data-flow-data-share/dataflow11.png)
1. Para verificar os seus dados, marque uma pré-visualização de dados no separador **'Pré-visualização de dados'.**

    ![Portal](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Interior junte-se tripDataCSV e TripFaresSQL

1. Para adicionar uma nova transformação, clique no ícone plus no canto inferior direito de 'TripDataCSV'. Em **várias entradas/saídas,** selecione **Juntar**.

    ![Portal](media/lab-data-flow-data-share/join1.png)
1. Nomeie a sua transformação de join 'InnerJoinWithTripFares'. Selecione 'TripFaresSQL' a partir do fluxo direito. Selecione **Interior** como o tipo de união. Para saber mais sobre os diferentes tipos de adesão no fluxo de dados de mapeamento, consulte [tipos de união](https://docs.microsoft.com/azure/data-factory/data-flow-join#join-types).

    Selecione quais as colunas que pretende combinar a partir de cada fluxo através da queda das **condições de União.** Para adicionar uma condição adicional de união, clique no ícone plus ao lado de uma condição existente. Por predefinição, todas as condições de adesão são combinadas com um operador e um operador, o que significa que todas as condições devem ser satisfeitas para uma correspondência. Neste laboratório, queremos combinar em `medallion` `hack_license`colunas, e `vendor_id``pickup_datetime`

    ![Portal](media/lab-data-flow-data-share/join2.png)
1. Verifique se se juntou com sucesso a 25 colunas juntamente com uma pré-visualização de dados.

    ![Portal](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Agregado por payment_type

1. Depois de completar a sua transformação de join, adicione uma transformação agregada clicando no ícone plus ao lado de 'InnerJoinWithTripFares. Escolha **agregado** sob **modificador schema**.

    ![Portal](media/lab-data-flow-data-share/agg1.png)
1. Nomeie a sua transformação agregada 'AggregateByPaymentType'. Selecione `payment_type` como o grupo por coluna.

    ![Portal](media/lab-data-flow-data-share/agg2.png)
1. Vá ao separador **Agregados.** Aqui, você vai especificar duas agregações:
    * A tarifa média agruparada por tipo de pagamento
    * A distância total da viagem agruparada por tipo de pagamento

    Primeiro, vai criar a expressão média das tarifas. Na caixa de texto rotulada **Adicionar ou selecionar uma coluna,** introduza 'average_fare'.

    ![Portal](media/lab-data-flow-data-share/agg3.png)
1. Para introduzir uma expressão de agregação, clique na expressão **de enter da**caixa azul . Isto abrirá o construtor de expressão de fluxo de dados, uma ferramenta usada para criar visualmente expressões de fluxo de dados utilizando esquema de entrada, funções e operações incorporadas e parâmetros definidos pelo utilizador. Para obter mais informações sobre as capacidades do construtor de expressão, consulte a [documentação](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder)do construtor de expressão .

    Para obter a tarifa média, use a `avg()` função `total_amount` de agregação para `toInteger()`agregar o molde da coluna a um inteiro com . Na linguagem de expressão de fluxo `avg(toInteger(total_amount))`de dados, isto é definido como . Clique em **Guardar e terminar** quando terminar.

    ![Portal](media/lab-data-flow-data-share/agg4.png)
1. Para adicionar uma expressão adicional de agregação, `average_fare`clique no ícone plus ao lado de . Selecione **Adicionar coluna**.

    ![Portal](media/lab-data-flow-data-share/agg5.png)
1. Na caixa de texto rotulada **Adicionar ou selecionar uma coluna,** introduza 'total_trip_distance'. Como no último passo, abra o construtor de expressão para entrar na expressão.

    Para obter a distância total `sum()` da viagem, utilize `trip_distance` a função de `toInteger()`agregação da coluna para um inteiro com . Na linguagem de expressão de fluxo `sum(toInteger(trip_distance))`de dados, isto é definido como . Clique em **Guardar e terminar** quando terminar.

    ![Portal](media/lab-data-flow-data-share/agg6.png)
1. Teste a sua lógica de transformação no separador Visualização de **Dados.** Como pode ver, há significativamente menos linhas e colunas do que anteriormente. Apenas as três colunas de grupo por e agregação definidas nesta transformação continuam a jusante. Como existem apenas cinco grupos de tipo de pagamento na amostra, apenas cinco linhas são saídas.

    ![Portal](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Configure-o Azure Synapse Analytics afunda

1. Agora que terminamos a nossa lógica de transformação, estamos prontos para afundar os nossos dados numa tabela Azure Synapse Analytics. Adicione uma transformação de pia sob a secção **Destino.**

    ![Portal](media/lab-data-flow-data-share/sink1.png)
1. Nomeie a pia 'SQLDWSink'. Clique em **Novo** ao lado do campo de conjunto de dados do lavatório para criar um novo conjunto de dados Azure Synapse Analytics.

    ![Portal](media/lab-data-flow-data-share/sink2.png)

1. Selecione o azulejo **Azure Synapse Analytics (anteriormente SQL DW)** e clique em continuar.

    ![Portal](media/lab-data-flow-data-share/sink3.png)
1. Ligue para o seu conjunto de dados 'AggregatedTaxiData'. Selecione 'SQLDW' como serviço ligado. Selecione **Criar nova tabela** e nomear a nova tabela dbo. AgregadoTaxiData. Clique OK quando terminar

    ![Portal](media/lab-data-flow-data-share/sink4.png)
1. Vá ao separador **Definições** da pia. Uma vez que estamos a criar uma nova tabela, precisamos de selecionar a **tabela Recreate** em ação de mesa. Desseleccionar **A encenação ,** que alterna se estamos a inserir fila a linha ou em lote.

    ![Portal](media/lab-data-flow-data-share/sink5.png)

Criou com sucesso o seu fluxo de dados. Agora é hora de executá-lo em uma atividade de oleoduto.

### <a name="debug-your-pipeline-end-to-end"></a>Desinime o seu oleoduto de ponta a ponta

1. Volte ao separador para o pipeline **IngestAndTransformData.** Note a caixa verde na atividade de cópia 'IngestIntoADLS'. Arraste-o para a atividade de fluxo de dados 'JoinAndAggregateData'. Isto cria um "sobre o sucesso", o que faz com que a atividade de fluxo de dados só seja executada se a cópia for bem sucedida.

    ![Portal](media/lab-data-flow-data-share/pipeline1.png)
1. Como fizemos para a atividade de cópia, clique em **Debug** para executar uma execução de depuração. Para as corridas de depuração, a atividade de fluxo de dados utilizará o cluster de depuração ativo em vez de girar um novo cluster. Este oleoduto levará pouco mais de um minuto para ser executado.

    ![Portal](media/lab-data-flow-data-share/pipeline2.png)
1. Tal como a atividade da cópia, o fluxo de dados tem uma visão especial de monitorização acedida pelo ícone dos óculos no final da atividade.

    ![Portal](media/lab-data-flow-data-share/pipeline3.png)
1. Na vista de monitorização, pode ver um gráfico de fluxo de dados simplificado juntamente com os tempos de execução e as linhas em cada fase de execução. Se for feito corretamente, deverá ter agregado 49.999 linhas em cinco linhas nesta atividade.

    ![Portal](media/lab-data-flow-data-share/pipeline4.png)
1. Pode clicar numa transformação para obter detalhes adicionais sobre a sua execução, tais como informações de divisão e colunas novas/atualizadas/largadas.

    ![Portal](media/lab-data-flow-data-share/pipeline5.png)

Já completou a parte da fábrica de dados deste laboratório. Publique os seus recursos se quiser operacionalizá-los com gatilhos. Você executou com sucesso um pipeline que ingeriu dados da Base de Dados Azure SQL para o Armazenamento do Lago De dados Azure usando a atividade de cópia e, em seguida, agregado esses dados em um Azure Synapse Analytics. Pode verificar se os dados foram escritos com sucesso olhando para o próprio Servidor SQL.

## <a name="share-data-using-azure-data-share"></a>Partilhar dados usando a Partilha de Dados do Azure

Nesta secção, você vai aprender a configurar uma nova partilha de dados usando o portal Azure. Isto implicará a criação de uma nova partilha de dados que conterá conjuntos de dados da Azure Data Lake Store Gen2 e do Azure SQL Data Warehouse. Em seguida, configurará um calendário instantâneo, o que dará aos consumidores de dados a opção de atualizar automaticamente os dados que estão a ser partilhados com eles. Em seguida, convidará os destinatários para a sua parte de dados. 

Depois de ter criado uma partilha de dados, trocará os chapéus e tornar-se-á o consumidor de *dados.* Como consumidor de dados, você vai caminhar através do fluxo de aceitação de um convite de partilha de dados, configurando onde você gostaria que os dados fossem recebidos e mapeando conjuntos de dados para diferentes locais de armazenamento. Em seguida, irá desencadear uma imagem instantânea, que copiará os dados partilhados consigo para o destino especificado. 

### <a name="sharing-data-data-provider-flow"></a>Partilha de dados (fluxo de fornecedor de dados)

1. Abra o portal Azure no Microsoft Edge ou no Google Chrome.

1. Utilizando a barra de pesquisa no topo da página, procure **Por Partilhas** de Dados

    ![Portal](media/lab-data-flow-data-share/portal-ads.png)

1. Selecione a conta de partilha de dados com 'Fornecedor' no nome. Por exemplo, **DataProvider0102**. 

1. Selecione **Começar a partilhar os seus dados**

    ![Começar a partilhar](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Selecione **+Criar** para começar a configurar a sua nova partilha de dados. 

1. Sob **o nome de Partilha,** especifique um nome à sua escolha. Este é o nome da partilha que será visto pelo seu consumidor de dados, por isso certifique-se de dar-lhe um nome descritivo como TaxiData.

1. Em **Descrição,** coloque uma frase que descreva o conteúdo da parte dos dados. A partilha de dados conterá dados mundiais de viagem de táxi que são armazenados em várias lojas, incluindo o Azure SQL Data Warehouse e a Azure Data Lake Store. 

1. Nos **termos de utilização,** especifique um conjunto de termos que gostaria que o seu consumidor de dados aderisse. Alguns exemplos incluem "Não distribua estes dados fora da sua organização" ou "Consulte o acordo legal". 

    ![Partilhar detalhes](media/lab-data-flow-data-share/ads-details.png)

1. Selecione **Continuar**. 

1. **Selecione Adicionar conjuntos** de dados 

    ![Adicionar conjunto de dados](media/lab-data-flow-data-share/add-dataset.png)

1. Selecione **Azure SQL Data Warehouse** para selecionar uma tabela do Armazém de Dados Azure SQL onde as suas transformações ADF aterraram.

    ![Adicionar conjunto de dados](media/lab-data-flow-data-share/add-dataset-sql.png)

> [!NOTE]
> Azure SQL Data Warehouse é agora conhecido como Azure Synapse Analytics

1. Será-lhe dado um guião para executar antes de poder prosseguir. O script fornecido cria um utilizador na base de dados SQL para permitir que o Azure Data Share MSI autentifique em seu nome. 

> [!IMPORTANT]
> Antes de executar o script, deve definir-se como o Administrador de Diretório Ativo para o Servidor SQL. 

1. Abra um novo separador e navegue para o portal Azure. Copie o script fornecido para criar um utilizador na base de dados a que pretende partilhar dados. Faça-o iniciando sessão na base de dados EDW utilizando o Query Explorer (pré-visualização) utilizando a autenticação AAD. 

    Terá de modificar o script para que o utilizador criado esteja contido dentro dos suportes. Por exemplo:
    
    criar o utilizador [dataprovider-xxxx] a partir de login externo;  sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Volte para a Partilha de Dados do Azure, onde estava a adicionar conjuntos de dados à sua quota de dados. 

1. Selecione **EDW** para o Armazém de Dados SQL e selecione **AggregatedTaxiData** para a tabela. 

1. **Selecione Adicionar conjunto de dados**

    Temos agora uma tabela SQL que faz parte do nosso conjunto de dados. Em seguida, adicionaremos conjuntos de dados adicionais da Azure Data Lake Store. 

1. **Selecione Adicionar conjunto de dados** e selecione **Azure Data Lake Store Gen2**

    ![Adicionar conjunto de dados](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Selecione **Next**

1. Expandir *wwtaxidata*. Expandir *os dados do táxi de Boston.* Note que pode partilhar para o nível de ficheiro. 

1. Selecione a pasta *Boston Taxi Data* para adicionar toda a pasta à sua partilha de dados. 

1. **Selecione Adicionar conjuntos** de dados

1. Reveja os conjuntos de dados que foram adicionados. Deve ter uma tabela SQL e uma pasta ADLSGen2 adicionadas à sua partilha de dados. 

1. Selecione **Continuar**

1. Neste ecrã, pode adicionar destinatários à sua partilha de dados. Os destinatários que adicionar receberão convites para a sua quota de dados. Para efeitos deste laboratório, deve adicionar 2 endereços de e-mail:

    1. O endereço de e-mail da subscrição azure em que está. 

        ![Adicionar destinatários](media/lab-data-flow-data-share/add-recipients.png)

    1. Adicione o consumidor de *janedoe@fabrikam.com*dados fictícios chamado .

1. Neste ecrã, pode configurar uma Definição de Instantâneo para o seu consumidor de dados. Isto permitir-lhes-á receber atualizações regulares dos seus dados num intervalo definido por si. 

1. Verifique o **Snapshot Schedule** e configure uma atualização horária dos seus dados utilizando a queda da *Recurrence.*  

1. Selecione **Criar**.

    Agora tem uma parte de dados ativa. Permite rever o que pode ver como fornecedor de dados quando cria uma partilha de dados. 

1. Selecione a partilha de dados que criou, intitulada **DataProvider**. Pode navegar para ele selecionando **Ações Enviadas** em Partilha de **Dados**. 

1. Clique na programação do Snapshot. Pode desativar o horário de instantâneo, se quiser. 

1. Em seguida, selecione o separador **Datasets.** Pode adicionar conjuntos de dados adicionais a esta partilha de dados depois de ter sido criado. 

1. Selecione o separador **de subscrições Partilhar.** Ainda não existem subscrições de ações porque o seu consumidor de dados ainda não aceitou o seu convite.

1. Navegue para o separador **Convites.** Aqui, você verá uma lista de convites pendentes. 

    ![Convites pendentes](media/lab-data-flow-data-share/pending-invites.png)

1. Selecione *janedoe@fabrikam.com*o convite para . Selecione Eliminar. Se o seu destinatário ainda não aceitou o convite, eles não poderão mais fazê-lo. 

1. Selecione o separador **História.** Nada é mostrado ainda porque o seu consumidor de dados ainda não aceitou o seu convite e desencadeou uma foto instantânea. 

### <a name="receiving-data-data-consumer-flow"></a>Dados de receção (Fluxo de consumo de dados)

Agora que revimos a nossa quota de dados, estamos prontos para mudar de contexto e usar o nosso chapéu de consumidor de dados. 

Deverá agora ter um convite Azure Data Share na sua caixa de entrada do Microsoft Azure. Inicie o Outlook Web Access (outlook.com) e inicie sessão utilizando as credenciais fornecidas para a sua subscrição Do Azure.

No e-mail que deveria ter recebido, clique em "Ver convite >". Neste momento, vai simular a experiência do consumidor de dados ao aceitar um convite dos fornecedores de dados para a sua partilha de dados. 

![Convite por e-mail](media/lab-data-flow-data-share/email-invite.png)

Pode ser solicitado a selecionar uma subscrição. Certifique-se de selecionar a subscrição em que tem trabalhado para este laboratório. 

1. Clique no convite intitulado *DataProvider*. 

1. Neste ecrã de Convite, você vai notar vários detalhes sobre a partilha de dados que configurau anteriormente como fornecedor de dados. Reveja os detalhes e aceite os termos de utilização se fornecido.

1. Selecione o Grupo de Subscrição e Recursos que já existe para o seu laboratório. 

1. Para **conta de partilha de dados,** selecione **DataConsumer**. Também pode criar uma nova conta de partilha de dados. 

1. Ao lado do **nome da partilha recebida,** você vai notar que o nome da partilha por defeito é o nome especificado pelo fornecedor de dados. Dê à partilha um nome amigável que descreva os dados que está prestes a receber, por **exemplo, TaxiDataShare**.

    ![Convite aceita](media/lab-data-flow-data-share/consumer-accept.png)

1. Pode optar por **aceitar e configurar agora** ou aceitar e **configurar mais tarde**. Se optar por aceitar e configurar agora, irá especificar uma conta de armazenamento onde todos os dados devem ser copiados. Se optar por aceitar e configurar mais tarde, os conjuntos de dados da parte serão desmapeados e terá de os mapear manualmente. Vamos optar por isso mais tarde. 

1. **Selecione Aceitar e configurar mais tarde**. 

    Ao configurar esta opção, é criada uma subscrição de ações, mas não há lugar para os dados aterrarem uma vez que nenhum destino foi mapeado. 

    Em seguida, configuraremos mapeamentos de conjuntode dados para a partilha de dados. 

1. Selecione a Parte Recebida (o nome especificado no passo 5).

    **O gatilho** está acinzentado, mas a parte é ativa. 

1. Selecione o separador **Datasets.** Note que cada conjunto de dados está não mapeado, o que significa que não tem destino a copiar dados. 

    ![conjuntos de dados não mapeados](media/lab-data-flow-data-share/unmapped.png)

1. Selecione a tabela de depósito de dados SQL e, em seguida, selecione **+ Mapa para o alvo**.

1. No lado direito do ecrã, selecione a queda do Tipo de **Dados-Alvo.** 

    Pode mapear os dados do SQL para uma vasta gama de lojas de dados. Neste caso, vamos mapear para uma base de dados Azure SQL.

    ![mapeamento](media/lab-data-flow-data-share/mapping-options.png)
    
    (Opcional) Selecione **Azure Data Lake Store Gen2** como o tipo de dados alvo. 
    
    (Opcional) Selecione a conta de Subscrição, Grupo de Recursos e Armazenamento em que tem trabalhado. 
    
    (Opcional) Pode optar por receber os dados no seu lago de dados em formato CSV ou parquet. 

1. Ao lado do tipo de **dados Target,** selecione Base de Dados Azure SQL. 

1. Selecione a conta de Subscrição, Grupo de Recursos e Armazenamento em que tem trabalhado. 

    ![mapa para sql](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Antes de poder prosseguir, terá de criar um novo utilizador no Servidor SQL executando o script fornecido. Primeiro, copie o guião fornecido à sua prancheta. 

1. Abra um novo separador de portal Azure. Não feche o separador existente, pois terá de voltar a ele dentro de momentos. 

1. No novo separador que abriu, navegue para bases de **dados SQL**.

1. Selecione a base de dados SQL (só deve haver uma na sua subscrição). Tenha cuidado para não selecionar o Armazém de Dados SQL. 

1. Selecione **Editor de Consulta (pré-visualização)**

1. Utilize a autenticação AAD para iniciar sessão no editor da Consulta. 

1. Execute a consulta fornecida na sua partilha de dados (copiada para a sobre-placa no passo 14). 

    Este comando permite que o serviço Azure Data Share utilize identidades geridas para os Serviços Azure para autenticar o Servidor SQL para poder copiar dados para o mesmo. 

1. Volte ao separador original e selecione **Map para o alvo**.

1. Em seguida, selecione a pasta Azure Data Lake Gen2 que faz parte do conjunto de dados e mapeie-a para uma conta de Armazenamento De Blob Azure. 

    ![storage](media/lab-data-flow-data-share/storage-map.png)

    Com todos os conjuntos de dados mapeados, está agora pronto para começar a receber dados do fornecedor de dados. 

    ![mapeado](media/lab-data-flow-data-share/all-mapped.png)
    
1. Selecione **Detalhes**. 

    Note que o **snapshot do Trigger** já não está acinzentado, uma vez que a partilha de dados tem agora destinos para copiar.

1. Selecione trigger snapshot -> Cópia Completa. 

    ![gatilho](media/lab-data-flow-data-share/trigger-full.png)

    Isto começará a copiar dados na sua nova conta de partilha de dados. Num cenário real, estes dados viriam de terceiros. 

    Levará aproximadamente 3-5 minutos para os dados serem dados. Pode monitorizar o progresso clicando no separador **História.** 

    Enquanto espera, navegue para a partilha original de dados (DataProvider) e veja o estado do separador **Subscrições** de Partilha e **Histórico.** 

1. Navegue de volta para a partilha de dados do consumidor de dados. Assim que o estado do gatilho for bem sucedido, navegue até à base de dados SQL de destino e ao lago de dados para ver se os dados aterraram nas respetivas lojas. 

Parabéns, já completou o laboratório!


