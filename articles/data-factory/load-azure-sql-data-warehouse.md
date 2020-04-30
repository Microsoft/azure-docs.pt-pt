---
title: Carregar dados para o Azure SQL Data Warehouse
description: Utilize a Azure Data Factory para copiar dados no Armazém de Dados Azure SQL
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/16/2020
ms.openlocfilehash: 1a764f392402acf9aa405468470d0fb6f680d755
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461113"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Carregue dados para o Azure SQL Data Warehouse com o Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[O Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é uma base de dados baseada em nuvem e com escala para fora que é capaz de processar volumes maciços de dados, tanto relacionais como não relacionais. O SQL Data Warehouse é construído sobre a arquitetura de processamento massivamente paralelo (MPP) que está otimizada para cargas de trabalho de armazém de dados empresariais. Oferece elasticidade em nuvem com a flexibilidade para escalar o armazenamento e calcular de forma independente.

Começar com o Azure SQL Data Warehouse é agora mais fácil do que nunca quando se utiliza a Azure Data Factory. A Azure Data Factory é um serviço de integração de dados totalmente gerido em nuvem. Pode utilizar o serviço para povoar um Armazém de Dados SQL com dados do seu sistema existente e economizar tempo ao construir as suas soluções de análise.

A Azure Data Factory oferece os seguintes benefícios para o carregamento de dados no Azure SQL Data Warehouse:

* **Fácil de configurar**: Um intuitivo assistente de 5 passos sem necessidade de scripts.
* **Suporte rico em lojas**de dados : Suporte incorporado para um conjunto rico de lojas de dados no local e baseadas na nuvem. Para obter uma lista detalhada, consulte a tabela de lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro e conforme:** Os dados são transferidos para HTTPS ou ExpressRoute. A presença global de serviço garante que os seus dados nunca saem da fronteira geográfica.
* **Desempenho inigualável utilizando polyBase**: Polybase é a forma mais eficiente de mover dados para o Azure SQL Data Warehouse. Utilize a função de "staging blob" para obter altas velocidades de carga de todos os tipos de lojas de dados, incluindo o armazenamento Azure Blob e data Lake Store. (A Polybase suporta o armazenamento azure blob e a Azure Data Lake Store por padrão.) Para mais detalhes, consulte [o desempenho da atividade do Copy.](copy-activity-performance.md)

Este artigo mostra-lhe como utilizar a ferramenta Data Copy Data da Fábrica de Dados para carregar dados da Base de _Dados Azure SQL no Armazém de Dados Azure SQL_. Pode seguir passos semelhantes para copiar dados de outros tipos de lojas de dados.

> [!NOTE]
> Para mais informações, consulte os dados da Cópia de ou para o [Armazém de Dados Azure SQL utilizando](connector-azure-sql-data-warehouse.md)a Azure Data Factory .

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure: Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Armazém de Dados Azure SQL: O armazém de dados contém os dados copiados da base de dados SQL. Se não tiver um Armazém de Dados Azure SQL, consulte as instruções em [Create a SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Base de Dados Azure SQL: Este tutorial copia dados de uma base de dados Azure SQL com dados da amostra Adventure Works LT. Pode criar uma base de dados SQL seguindo as instruções em Criar uma base de [dados Azure SQL](../sql-database/sql-database-get-started-portal.md).
* Conta de armazenamento Azure: O Armazenamento Azure é utilizado como a bolha de _preparação_ na operação de cópia a granel. Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, selecione **Criar um recurso** > **Data + Analytics** > **Data Factory**:

2. Na página da nova fábrica de **dados,** forneça valores para os seguintes itens:

    * **Nome**: Introduza *loadSQLDWDemo* para o nome. O nome da sua fábrica de dados deve ser *globalmente único. Se receber o erro "O nome da fábrica de dados 'LoadSQLDWDemo' não está disponível", introduza um nome diferente para a fábrica de dados. Por exemplo, pode utilizar o nome _**yourname**_**aDFTutorialDataFactory**. Tente criar a fábrica de dados de novo. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição Azure para criar a fábrica de dados. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente da lista de drop-down, ou selecione a nova opção **Criar** e introduza o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. As lojas de dados que são utilizadas pela fábrica de dados podem estar noutros locais e regiões. Estas lojas de dados incluem Azure Data Lake Store, Azure Storage, Azure SQL Database, e assim por diante.

3. Selecione **Criar**.
4. Depois de a criação estar completa, vá à sua fábrica de dados. Vê a página inicial da **Data Factory** como mostra a seguinte imagem:

   ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)

   Selecione o mosaico **Criar e Monitorizar** para iniciar a Aplicação de Integração de Dados num separador à parte.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados para o Azure SQL Data Warehouse

1. Na página **Introdução**, selecione o mosaico **Copiar Dados** para iniciar a ferramenta Copiar Dados.

1. Na página **Propriedades,** especifique **CopyFromSQLToSQLDW** para o campo de **nome de tarefas** e selecione **Next**.

    ![Página Propriedades](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Na página da loja de **dados Fonte,** preencha os seguintes passos:
    >[!TIP]
    >Neste tutorial, utiliza a *autenticação SQL* como tipo de autenticação para a sua loja de dados de origem, mas pode escolher outros métodos de autenticação suportados: Diretor de*Serviço* e *Identidade Gerida,* se necessário. Consulte as secções correspondentes [neste artigo](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) para obter mais detalhes.
    >Para armazenar segredos para lojas de dados de forma segura, também é recomendado usar um Cofre chave Azure. Consulte [este artigo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para ilustrações detalhadas.

    a. clique **+ Criar nova ligação**.

    b. Selecione **Base de Dados Azure SQL** na galeria e selecione **Continuar**. Pode escrever "SQL" na caixa de pesquisa para filtrar os conectores.

    ![Selecionar BD SQL do Azure](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Na página **New Linked Service,** selecione o nome do seu servidor e o nome DB da lista de dropdown e especifique o nome de utilizador e a palavra-passe. Clique na **ligação Test** para validar as definições e, em seguida, selecione **Criar**.

    ![Configurar BD SQL do Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selecione o serviço ligado criado recentemente como origem e, em seguida, clique em **Seguinte**.

1. Nas **tabelas Select** a partir das quais copiar os dados ou utilizar uma página de consulta personalizada, introduza **o SalesLT** para filtrar as tabelas. Escolha a caixa **(Selecione todas)** para utilizar todas as tabelas para a cópia e, em seguida, selecione **Next**.

    ![Selecione tabelas de origem](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Na página do **filtro Aplicar,** especifique as definições ou selecione **Next**.

1. Na página da loja de **dados Destination,** preencha os seguintes passos:
    >[!TIP]
    >Neste tutorial, utiliza a *autenticação SQL* como tipo de autenticação para a sua loja de dados de destino, mas pode escolher outros métodos de autenticação suportados:*Diretor de Serviço* e Identidade *Gerida,* se necessário. Consulte as secções correspondentes [neste artigo](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#linked-service-properties) para obter mais detalhes.
    >Para armazenar segredos para lojas de dados de forma segura, também é recomendado usar um Cofre chave Azure. Consulte [este artigo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para ilustrações detalhadas.

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação

    b. Selecione **Azure Synapse Analytics (anteriormente SQL DW)** a partir da galeria, e selecione **Continue**. Pode escrever "SQL" na caixa de pesquisa para filtrar os conectores.

    ![Selecione Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Na página **New Linked Service,** selecione o nome do seu servidor e o nome DB da lista de dropdown e especifique o nome de utilizador e a palavra-passe. Clique na **ligação Test** para validar as definições e, em seguida, selecione **Criar**.

    ![Configure Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selecione o serviço ligado criado recentemente como sink e, em seguida, clique em **Seguinte**.

1. Na página de mapeamento da **tabela,** reveja o conteúdo e selecione **Next**. Um inteligente mostrando mapeamento de mesa. As tabelas de origem estão mapeadas para as tabelas de destino com base nos nomes das mesas. Se uma tabela de origem não existir no destino, a Azure Data Factory cria uma tabela de destino com o mesmo nome por padrão. Também pode mapear uma tabela de origem para uma tabela de destino existente.

   > [!NOTE]
   > A criação automática de tabelas para o depósito de dados SQL aplica-se quando o SQL Server ou a Base de Dados Azure SQL são a fonte. Se copiar dados de outra loja de dados de origem, tem de pré-criar o esquema no depósito de dados Azure SQL antes de executar a cópia de dados.

   ![Página de mapeamento de tabela](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Na página de mapeamento da **Coluna,** reveja o conteúdo e selecione **Next**. O mapeamento inteligente da tabela baseia-se no nome da coluna. Se deixar a Data Factory criar automaticamente as tabelas, a conversão do tipo de dados pode ocorrer quando há incompatibilidades entre as lojas de origem e destino. Se houver uma conversão não suportada do tipo de dados entre a fonte e a coluna de destino, verá uma mensagem de erro ao lado da tabela correspondente.

    ![Página de mapeamento de colunas](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Na página **Definições,** preencha os seguintes passos:

    a. Na secção **de definições de encenação,** clique **+ Novo** para um novo armazenamento de encenação. O armazenamento é utilizado para a realização dos dados antes de ser carregado para o Armazém de Dados SQL utilizando a PolyBase. Após a conclusão da cópia, os dados provisórios no Armazenamento De Blob Azure são automaticamente limpos.

    b. Na página **new Linked Service,** selecione a sua conta de armazenamento e selecione **Criar** para implementar o serviço ligado.

    c. Na secção **definições Avançadas,** desmarque a opção **predefinição** do tipo Use e, em seguida, selecione **Next**.

    ![Configure PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Na página **Resumo,** reveja as definições e selecione **Next**.

    ![Página de resumo](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Na **página de Implantação,** selecione **Monitor** para monitorizar o gasoduto (tarefa).

1. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. Quando o gasoduto estiver concluído com sucesso, selecione a ligação **CopyFromSQLToSQLDW** sob a coluna **PIPELINE NAME** para visualizar os detalhes de execução da atividade e para reexecutar o gasoduto.

    [![Monitorizar as corridas de gasodutos](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)
1. Para voltar à vista de funcionação do gasoduto, selecione o link de **funcionação de todas** as condutas na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Monitorização de execuções de atividade](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Para monitorizar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** (ícone de óculos) em nome de **atividade** na vista executa a atividade. Pode monitorizar detalhes como o volume de dados copiados da fonte para o lavatório, a entrada de dados, passos de execução com a duração correspondente e configurações usadas.
    ![Monitorizar detalhes de execução de atividade](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Monitorizar detalhes de execução de atividade](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>Passos seguintes

Avançar para o seguinte artigo para saber sobre o suporte do Armazém de Dados Azure SQL:

> [!div class="nextstepaction"]
>[Conector Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)
