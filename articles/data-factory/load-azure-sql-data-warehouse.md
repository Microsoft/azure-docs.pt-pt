---
title: Carregar dados em Azure Synapse Analytics
description: Utilize a Azure Data Factory para copiar dados em Azure Synapse Analytics
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/29/2020
ms.openlocfilehash: 2f3932f3374367e260685ae5145da8858384c3a2
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194757"
---
# <a name="load-data-into-azure-synapse-analytics-by-using-azure-data-factory"></a>Carregue os dados no Azure Synapse Analytics utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[A azure Synapse Analytics (anteriormente SQL DW)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é uma base de dados baseada em nuvem e escala que é capaz de processar volumes maciços de dados, tanto relacionais como não relacionais. O Azure Synapse Analytics é construído sobre a arquitetura de processamento massivamente paralelo (MPP) que é otimizada para cargas de trabalho de armazém de dados empresariais. Oferece elasticidade em nuvem com a flexibilidade para escalar o armazenamento e calcular de forma independente.

Começar com a Azure Synapse Analytics é agora mais fácil do que nunca quando se utiliza a Azure Data Factory. AZure Data Factory é um serviço de integração de dados totalmente gerido na nuvem. Pode utilizar o serviço para preencher um Azure Synapse Analytics com dados do seu sistema existente e economizar tempo na construção das suas soluções de análise.

A Azure Data Factory oferece os seguintes benefícios para o carregamento de dados no Azure Synapse Analytics:

* **Fácil de configurar:** Um assistente intuitivo de 5 passos sem necessidade de scripts.
* Suporte rico em lojas de **dados**: Suporte incorporado para um conjunto rico de lojas de dados no local e lojas de dados baseadas em nuvem. Para obter uma lista detalhada, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
* **Seguro e em conformidade:** Os dados são transferidos sobre HTTPS ou ExpressRoute. A presença global de serviços garante que os seus dados nunca saiam da fronteira geográfica.
* **Desempenho incomparável utilizando a PolyBase**: A polibase é a forma mais eficiente de mover dados para a Azure Synapse Analytics. Utilize a função de blob de preparação para obter altas velocidades de carga de todos os tipos de lojas de dados, incluindo o armazenamento de Azure Blob e a Data Lake Store. (A Polybase suporta o armazenamento Azure Blob e a Azure Data Lake Store por padrão.) Para mais detalhes, consulte [o desempenho da atividade da Copy](copy-activity-performance.md).

Este artigo mostra-lhe como utilizar a ferramenta Data Data copy data da data factory para _carregar dados da Base de Dados Azure SQL em Azure Synapse Analytics_. Pode seguir passos semelhantes para copiar dados de outros tipos de lojas de dados.

> [!NOTE]
> Para obter mais informações, consulte [copiar dados para ou a partir da Azure Synapse Analytics utilizando a Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Azure Synapse Analytics: O armazém de dados contém os dados que são copiados da base de dados SQL. Se não tiver um Azure Synapse Analytics, consulte as instruções em [Criar um Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Base de Dados Azure SQL: Este tutorial copia dados de uma base de dados Azure SQL com dados de amostras Adventure Works LT. Pode criar uma base de dados SQL seguindo as instruções na [Criação de uma base de dados Azure SQL](../azure-sql/database/single-database-create-quickstart.md).
* Conta de armazenamento Azure: O Azure Storage é utilizado como bolha de _paragem_ na operação de cópia a granel. Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, **selecione Criar um recurso**Data +  >  **Analytics**  >  **Data Factory**:

2. Na nova página de **fábrica de dados,** forneça valores para os seguintes itens:

    * **Nome**: Introduza *loadSQLDWDemo* para o nome. O nome da sua fábrica de dados deve ser *globalmente único. Se receber o erro "O nome da fábrica de dados 'LoadSQLDWDemo' não está disponível", insira um nome diferente para a fábrica de dados. Por exemplo, pode utilizar o nome _**do seu nome**_**ADFTutorialDataFactory**. Tente criar a fábrica de dados de novo. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição Azure na qual criar a fábrica de dados. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente da lista de drop-down, ou selecione a nova opção **Criar** e insira o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização para a fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. As lojas de dados que são utilizadas pela fábrica de dados podem estar noutros locais e regiões. Estas lojas de dados incluem Azure Data Lake Store, Azure Storage, Azure SQL Database, e assim por diante.

3. Selecione **Criar**.
4. Depois de a criação estar completa, vá à sua fábrica de dados. Veja a página inicial da **Data Factory** como mostrado na seguinte imagem:

   ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)

   Selecione o mosaico **Criar e Monitorizar** para iniciar a Aplicação de Integração de Dados num separador à parte.

## <a name="load-data-into-azure-synapse-analytics"></a>Carregar dados em Azure Synapse Analytics

1. Na página **Introdução**, selecione o mosaico **Copiar Dados** para iniciar a ferramenta Copiar Dados.

1. Na página **Propriedades,** especifique **copyFromSQLToSQLDW** para o campo **de nome de tarefa** e selecione **Seguinte**.

    ![Página Propriedades](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Na página **'Source data store',** complete os seguintes passos:
    >[!TIP]
    >Neste tutorial, utiliza *a autenticação SQL* como tipo de autenticação para a sua loja de dados de origem, mas pode escolher outros métodos de autenticação suportados:*Principal de Serviço* e Identidade *Gerida,* se necessário. Consulte as secções correspondentes [neste artigo](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) para obter mais detalhes.
    >Para armazenar segredos para lojas de dados de forma segura, também é recomendado usar um Cofre de Chaves Azure. Consulte [este artigo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para obter ilustrações detalhadas.

    a. clique **+ Criar nova ligação**.

    b. Selecione **Azure SQL Database** da galeria e selecione **Continue**. Pode escrever "SQL" na caixa de pesquisa para filtrar os conectores.

    ![Selecionar BD SQL do Azure](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Na página **New Linked Service,** selecione o nome do servidor e o nome DB da lista de dropdown e especifique o nome de utilizador e a palavra-passe. Clique **na ligação de teste** para validar as definições e, em seguida, selecione **Criar**.

    ![Configurar BD SQL do Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selecione o serviço ligado criado recentemente como origem e, em seguida, clique em **Seguinte**.

1. Nas **tabelas Select a partir das quais para copiar os dados ou utilizar uma** página de consulta personalizada, insira o **SalesLT** para filtrar as tabelas. Escolha a caixa **(Selecione todas)** para utilizar todas as tabelas para a cópia e, em seguida, selecione **Seguinte**.

    ![Selecione tabelas de origem](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Na página do **filtro Aplicar,** especifique as definições ou selecione **Seguinte**.

1. Na página da **loja de dados destino,** complete os seguintes passos:
    >[!TIP]
    >Neste tutorial, utiliza *a autenticação SQL* como tipo de autenticação para a sua loja de dados de destino, mas pode escolher outros métodos de autenticação suportados:*Principal de Serviço* e Identidade *Gerida,* se necessário. Consulte as secções correspondentes [neste artigo](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#linked-service-properties) para obter mais detalhes.
    >Para armazenar segredos para lojas de dados de forma segura, também é recomendado usar um Cofre de Chaves Azure. Consulte [este artigo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para obter ilustrações detalhadas.

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação

    b. Selecione **Azure Synapse Analytics (anteriormente SQL DW)** da galeria, e **selecione Continue**. Pode escrever "SQL" na caixa de pesquisa para filtrar os conectores.

    ![Selecione Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Na página **New Linked Service,** selecione o nome do servidor e o nome DB da lista de dropdown e especifique o nome de utilizador e a palavra-passe. Clique **na ligação de teste** para validar as definições e, em seguida, selecione **Criar**.

    ![Configurar Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selecione o serviço ligado criado recentemente como sink e, em seguida, clique em **Seguinte**.

1. Na página de mapeamento da **tabela,** reveja o conteúdo e selecione **Seguinte**. Um mapeamento de mesa inteligente exibe. As tabelas de origem são mapeadas para as tabelas de destino com base nos nomes das tabelas. Se uma tabela de origem não existir no destino, a Azure Data Factory cria uma tabela de destino com o mesmo nome por defeito. Também pode mapear uma tabela de origem para uma tabela de destino existente.

   > [!NOTE]
   > A criação automática de mesas para o lavatório Azure Synapse Analytics aplica-se quando o SQL Server ou a Azure SQL Database são a fonte. Se copiar dados de outra loja de dados de origem, tem de pré-criar o esquema no sink Azure Synapse Analytics antes de executar a cópia de dados.

   ![Página de mapeamento de tabela](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Na página **de mapeamento da Coluna,** reveja o conteúdo e selecione **Seguinte**. O mapeamento de mesa inteligente baseia-se no nome da coluna. Se permitir que a Data Factory crie automaticamente as tabelas, a conversão do tipo de dados pode ocorrer quando há incompatibilidades entre as lojas de origem e destino. Se houver uma conversão do tipo de dados não suportada entre a coluna de origem e destino, vê uma mensagem de erro ao lado da tabela correspondente.

    ![Página de mapeamento de coluna](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Na página **Definições,** complete os seguintes passos:

    a. Na secção **de definições de encenação,** clique **em + Novo** para novo armazenamento de encenação. O armazenamento é utilizado para a paragem dos dados antes de ser carregado no Azure Synapse Analytics utilizando o PolyBase. Após a conclusão da cópia, os dados provisórios no Azure Blob Storage são automaticamente limpos.

    b. Na página **'Serviço Novo Ligado',** selecione a sua conta de armazenamento e selecione **Criar** para implementar o serviço ligado.

    c. Na secção **Definições Avançadas,** desmarcar a opção **por defeito do tipo Utilização** e, em seguida, selecione **Seguinte**.

    ![Configure PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Na página **Resumo,** reveja as definições e selecione **Seguinte**.

    ![Página de resumo](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Na **página 'Implantação',** selecione **Monitor** para monitorizar o pipeline (tarefa).

1. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. Quando o curso do gasoduto estiver concluído com sucesso, selecione a ligação **CopyFromSQLToSQLDW** sob a coluna **PIPELINE NAME** para ver os detalhes da atividade e para refazer o pipeline.

    [![O gasoduto de monitorização corre](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)
1. Para voltar à vista do gasoduto, selecione a ligação **de todas as tubagem** na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Monitorização de execuções de atividade](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Para monitorizar os detalhes de execução de cada atividade de cópia, selecione o link **Details** (ícone de óculos) em **NOME DE ATIVIDADE** na vista de execuções de atividade. Pode monitorizar detalhes como o volume de dados copiados da fonte para a pia, saída de dados, etapas de execução com a duração correspondente e configurações usadas.
    ![Monitorize detalhes da atividade](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Monitorize detalhes da atividade](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>Passos seguintes

Avance para o seguinte artigo para saber sobre o suporte Azure Synapse Analytics:

> [!div class="nextstepaction"]
>[Conector Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
