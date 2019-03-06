---
title: Carregar dados para o Azure SQL Data Warehouse com o Azure Data Factory | Documentos da Microsoft
description: Utilizar o Azure Data Factory para copiar dados para o Azure SQL Data Warehouse
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 7a478a9f73edae463a5dace1b1a28180e5d09bdc
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437739"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Carregar dados para o Azure SQL Data Warehouse com o Azure Data Factory

[O Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é a base de dados com base na cloud, de escalamento horizontal que é capaz de processar grandes volumes de dados tanto relacionais como não relacionais. O SQL Data Warehouse baseia-se a arquitetura de processamento paralelo em massa (MPP) que está otimizada para cargas de trabalho de armazém de dados de empresariais. Ele oferece flexibilidade na cloud com a flexibilidade para aumentar o armazenamento e computação de forma independente.

Introdução ao Azure SQL Data Warehouse agora é mais fácil do que nunca ao utilizar Azure Data Factory. O Azure Data Factory é um serviço de integração de dados totalmente gerido com base na cloud. Pode utilizar o serviço para preencher um SQL Data Warehouse com dados do sistema existente e poupar tempo quando criar as suas soluções de análise.

O Azure Data Factory oferece as seguintes vantagens para carregar dados para o Azure SQL Data Warehouse:

* **Fácil de configurar**: Um assistente passo 5 intuitivo com nenhum script necessário.
* **Suporte de arquivo de dados de rich**: Suporte interno para um conjunto avançado de arquivos de dados com base na cloud e no local. Para obter uma lista detalhada, consulte a tabela de [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
* **Segura e em conformidade**: Dados são transferidos através de HTTPS ou ExpressRoute. A presença de serviço global assegura que seus dados nunca sai do limite geográfico.
* **Desempenho sem paralelo com o PolyBase**: O Polybase é a maneira mais eficiente para mover dados para o Azure SQL Data Warehouse. Utilize a funcionalidade de blob de transição para atingir velocidades de carga elevada de todos os tipos de arquivos de dados, incluindo o armazenamento de Blobs do Azure e o Data Lake Store. (O Polybase suporta armazenamento de Blobs do Azure e Azure Data Lake Store por predefinição.) Para obter detalhes, consulte [copie o desempenho de atividade](copy-activity-performance.md).

Este artigo mostra-lhe como utilizar a ferramenta copiar dados do Data Factory para _carregar dados do SQL Database do Azure para o Azure SQL Data Warehouse_. Pode seguir passos semelhantes para copiar dados de outros tipos de arquivos de dados.

> [!NOTE]
> Para obter mais informações, consulte [copiar dados de ou para o Azure SQL Data Warehouse com o Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Armazém de dados SQL do Azure: O armazém de dados contém os dados que são copiados através da base de dados SQL. Se não tiver um armazém de dados SQL do Azure, veja as instruções em [criar um SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Base de Dados SQL do Azure: Neste tutorial copia dados de uma base de dados SQL do Azure com dados de exemplo do Adventure Works LT. Pode criar uma base de dados do SQL ao seguir as instruções em [criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md). 
* Conta do Storage do Azure: O armazenamento do Azure é utilizado como o _teste_ blob na operação de cópia em massa. Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu da esquerda, selecione **New** > **dados + análise** > **Data Factory**: 
   
   ![Criar uma nova fábrica de dados](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
1. Na **nova fábrica de dados** página, fornecer valores para os campos que são mostrados na imagem seguinte:
      
   ![Página Nova fábrica de dados](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente exclusivo para a fábrica de dados do Azure. Se receber o erro "nome do Data factory \"LoadSQLDWDemo\" não está disponível," insira um nome diferente para a fábrica de dados. Por exemplo, poderia usar o nome  _**yourname**_**ADFTutorialDataFactory**. Tente criar a fábrica de dados novamente. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
    * **Grupo de recursos**: Selecione um grupo de recursos existente na lista pendente ou selecione o **criar novo** opção e introduza o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados que são utilizados pelo data factory podem estar noutras localizações e regiões. Esses arquivos de dados incluem o Azure Data Lake Store, armazenamento do Azure, base de dados do Azure SQL e assim por diante.

1. Selecione **Criar**.
1. Depois de concluída a criação, vá para a fábrica de dados. Verá o **fábrica de dados** home page do conforme mostrado na imagem seguinte:
   
   ![Home page da fábrica de dados](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Selecione o **criar e monitorizar** mosaico para iniciar o aplicativo de integração de dados num separador à parte.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados para o Azure SQL Data Warehouse

1. Na **começar** página, selecione a **copiar dados** mosaico para iniciar a ferramenta copiar dados:

   ![Mosaico ferramenta Copiar Dados](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. Na **propriedades** , especifique **CopyFromSQLToSQLDW** para o **nome da tarefa** campo e selecione **seguinte**:

    ![Página Propriedades](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Na **o arquivo de dados de origem** página, conclua os seguintes passos:

    a. Clique em **+ criar nova ligação**:

    ![Página de arquivo de dados de origem](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Selecione **Azure SQL Database** da Galeria e selecione **continuar**. Pode digitar "SQL" na caixa de pesquisa para filtrar os conectores.

    ![Selecionar BD SQL do Azure](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Na **novo serviço ligado** página, selecione o nome do servidor e o nome de DB na lista pendente e especificar o nome de utilizador e palavra-passe. Clique em **Testar ligação** para validar as definições, em seguida, selecione **concluir**.
   
    ![Configurar BD SQL do Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selecione o serviço ligado criado recentemente como origem e, em seguida, clique em **Seguinte**.

    ![Selecionar serviço ligado de origem](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. Na **selecionar tabelas a partir das quais copiar os dados ou utilizar uma consulta personalizada** página, introduza **SalesLT** para filtrar as tabelas. Escolha o **(Selecionar tudo)** caixa para utilizar todas as tabelas para a cópia e, em seguida, selecione **próxima**: 

    ![Selecione as tabelas de origem](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Na **o arquivo de dados de destino** página, conclua os seguintes passos:

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação

    ![Página arquivo de dados de sink](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Selecione **do Azure SQL Data Warehouse** da Galeria e selecione **próxima**.

    ![Selecione o armazém de dados SQL do Azure](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Na **novo serviço ligado** página, selecione o nome do servidor e o nome de DB na lista pendente e especificar o nome de utilizador e palavra-passe. Clique em **Testar ligação** para validar as definições, em seguida, selecione **concluir**.
   
    ![Configurar o armazém de dados SQL do Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selecione o serviço ligado criado recentemente como sink e, em seguida, clique em **Seguinte**.

    ![Selecionar serviço ligado de sink](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. Na **mapeamento de tabelas** página, reveja o conteúdo e selecione **próxima**. Apresenta um mapeamento de tabela inteligente. As tabelas de origem estão mapeadas para as tabelas de destino com base nos nomes de tabela. Se uma tabela de origem não existe no destino, o Azure Data Factory cria uma tabela de destino com o mesmo nome por predefinição. Também pode mapear uma tabela de origem para uma tabela de destino existente. 

   > [!NOTE]
   > Criação da tabela automática para o sink do SQL Data Warehouse aplica-se ao SQL Server ou SQL Database do Azure é a origem. Se copiar dados de outro arquivo de dados de origem, terá de criar previamente o esquema no Azure SQL Data Warehouse de sink de antes de executar a cópia de dados.

   ![Página de mapeamento de tabela](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Na **mapeamento de esquema** página, reveja o conteúdo e selecione **próxima**. O mapeamento de tabela inteligente baseia-se o nome da coluna. Se deixar que o Data Factory cria automaticamente as tabelas, conversão de tipo de dados pode ocorrer quando houver incompatibilidades entre os arquivos de origem e de destino. Se houver uma conversão de tipo de dados sem suporte entre a coluna de origem e de destino, verá uma mensagem de erro ao lado da tabela correspondente.

    ![Página de mapeamento de esquema](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Na **definições** página, conclua os seguintes passos:

    a. Na **definições de teste** secção, clique em **+ novo** a um novo armazenamento de teste. O armazenamento é utilizado para sincronizar os dados antes de o carrega no SQL Data Warehouse com o PolyBase. Depois da cópia estiver concluída, os dados provisórias no armazenamento do Azure é automaticamente limpa. 

    ![Configurar a transição](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Na **novo serviço ligado** página, selecione a sua conta de armazenamento e selecione **concluir**.
   
    ![Configurar o armazenamento do Azure](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. Na **definições avançadas** secção, desmarcar a **utilizar predefinição de tipo** opção, em seguida, selecione **seguinte**.

    ![Configurar o PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Na **resumo** página, reveja as definições e selecione **próxima**:

    ![Página de resumo](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Na **página de implementação**, selecione **Monitor** para monitorizar o pipeline (tarefa):

    ![Página de implementação](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. O **ações** coluna inclui ligações para ver os detalhes da execução da atividade e voltar a executar o pipeline: 

    ![Monitorizar execuções de pipeline](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Para ver as execuções de atividades que estão associadas à execução do pipeline, selecione o **ver execuções de atividades** ligação na **ações** coluna. Para regressar à vista de execuções de pipeline, selecione o **Pipelines** link na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Para monitorizar os detalhes da execução para cada atividade de cópia, selecione o **detalhes** ligação sob **ações** na atividade de vista de monitorização. Pode monitorizar detalhes como o volume de dados copiados da origem para o sink, taxa de transferência de dados, os passos de execução com duração correspondente e utilizado configurações:

    ![Monitorizar a atividade de detalhes da execução](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Passos Seguintes

Avance para o seguinte artigo para saber mais sobre o suporte do Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Conector de armazém de dados SQL do Azure](connector-azure-sql-data-warehouse.md)
