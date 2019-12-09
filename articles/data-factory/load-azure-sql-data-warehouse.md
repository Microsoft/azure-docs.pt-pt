---
title: Carregar dados para o Azure SQL Data Warehouse
description: Usar Azure Data Factory para copiar dados para o Azure SQL Data Warehouse
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/22/2018
ms.openlocfilehash: 732d5d170ee647dc0dfdbf4d09a12617c8c9bcce
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931516"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Carregar dados no Azure SQL Data Warehouse usando Azure Data Factory

O [SQL data warehouse do Azure](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é um banco de dados baseado em nuvem e escalável que é capaz de processar grandes volumes de data, relacionais e não relacionais. O SQL Data Warehouse é criado na arquitetura MPP (processamento paralelo maciço) otimizada para cargas de trabalho de data warehouse empresarial. Ele oferece elasticidade de nuvem com a flexibilidade para dimensionar o armazenamento e a computação de forma independente.

A introdução ao Azure SQL Data Warehouse agora é mais fácil do que nunca quando você usa Azure Data Factory. Azure Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado. Você pode usar o serviço para popular um SQL Data Warehouse com dados do seu sistema existente e economizar tempo ao criar soluções de análise.

O Azure Data Factory oferece os seguintes benefícios para carregar dados no Azure SQL Data Warehouse:

* **Fácil de configurar**: um assistente intuitivo de 5 etapas sem nenhum script necessário.
* **Suporte a armazenamento de dados avançado**: suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem. Para obter uma lista detalhada, consulte a tabela de [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro e em conformidade**: os dados são transferidos por HTTPS ou ExpressRoute. A presença do serviço global garante que seus dados nunca saiam do limite geográfico.
* **Desempenho incomparável usando o polybase**: o polybase é a maneira mais eficiente de mover dados para o Azure SQL data warehouse. Use o recurso de blob de preparo para obter altas velocidades de carga de todos os tipos de armazenamentos de dados, incluindo o armazenamento de BLOBs do Azure e o Data Lake Store. (O polybase dá suporte ao armazenamento de BLOBs do Azure e Azure Data Lake Store por padrão.) Para obter detalhes, consulte [desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta de Copiar Dados de Data Factory para _carregar dados do banco de dados SQL do Azure para o azure SQL data warehouse_. Você pode seguir etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

> [!NOTE]
> Para obter mais informações, consulte [copiar dados de ou para o Azure SQL data warehouse usando Azure data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* SQL Data Warehouse do Azure: a data warehouse contém os dados que são copiados do SQL Database. Se você não tiver uma SQL Data Warehouse do Azure, consulte as instruções em [criar um SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Banco de dados SQL do Azure: Este tutorial copia o dado de um banco de dados SQL do Azure com o Adventure Works LT data Sample. Você pode criar um banco de dados SQL seguindo as instruções em [criar um banco de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md). 
* Conta de armazenamento do Azure: o armazenamento do Azure é usado como o blob de _preparo_ na operação de cópia em massa. Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu à esquerda, selecione **criar um recurso** > **dados + análise** > **Data Factory**: 
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **novo data Factory** , forneça valores para os campos mostrados na imagem a seguir:
      
   ![Página Nova fábrica de dados](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Nome**: Insira um nome globalmente exclusivo para sua data Factory do Azure. Se você receber o erro "o nome do data Factory \"LoadSQLDWDemo\" não está disponível", insira um nome diferente para o data factory. Por exemplo, você pode usar o nome _**Your**_ name**ADFTutorialDataFactory**. Tente criar o data factory novamente. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione sua assinatura do Azure na qual criar o data Factory. 
    * **Grupo de recursos**: selecione um grupo de recursos existente na lista suspensa ou selecione a opção **criar novo** e insira o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: selecione **v2**.
    * **Local**: selecione o local para o data Factory. Apenas são apresentadas as localizações suportadas na lista pendente. Os armazenamentos de dados usados pelo data factory podem estar em outros locais e regiões. Esses armazenamentos de dados incluem Azure Data Lake Store, armazenamento do Azure, banco de dados SQL do Azure e assim por diante.

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o data factory. Você verá o home page de **Data Factory** conforme mostrado na imagem a seguir:
   
   ![Home page da fábrica de dados](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Selecione o mosaico **Criar e Monitorizar** para iniciar a Aplicação de Integração de Dados num separador à parte.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados para o Azure SQL Data Warehouse

1. Na página de **introdução** , selecione o bloco **copiar dados** para iniciar a ferramenta de copiar dados:

   ![Mosaico ferramenta Copiar Dados](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. Na página **Propriedades** , especifique **CopyFromSQLToSQLDW** para o campo **nome da tarefa** e selecione **Avançar**:

    ![Página Propriedades](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Na página **armazenamento de dados de origem** , conclua as seguintes etapas:

    a. clique em **+ criar nova conexão**:

    ![Página de arquivo de dados de origem](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Selecione **banco de dados SQL do Azure** na galeria e selecione **continuar**. Você pode digitar "SQL" na caixa de pesquisa para filtrar os conectores.

    ![Selecionar BD SQL do Azure](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Na página **novo serviço vinculado** , selecione o nome do servidor e o nome do BD na lista suspensa e especifique o nome de usuário e a senha. Clique em **Testar ligação** para validar as definições e, em seguida, selecione **Concluir**.
   
    ![Configurar BD SQL do Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selecione o serviço ligado criado recentemente como origem e, em seguida, clique em **Seguinte**.

    ![Selecionar serviço ligado de origem](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. Na página **selecionar tabelas da qual copiar os dados ou usar uma consulta personalizada** , insira **tabela SalesLT** para filtrar as tabelas. Escolha a caixa **(selecionar tudo)** para usar todas as tabelas da cópia e, em seguida, selecione **Avançar**: 

    ![Selecionar tabelas de origem](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Na página **armazenamento de dados de destino** , conclua as seguintes etapas:

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação

    ![Página armazenamento de dados do coletor](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Selecione **Azure SQL data warehouse** na galeria e selecione **Avançar**.

    ![Selecione Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Na página **novo serviço vinculado** , selecione o nome do servidor e o nome do BD na lista suspensa e especifique o nome de usuário e a senha. Clique em **Testar ligação** para validar as definições e, em seguida, selecione **Concluir**.
   
    ![Configurar o SQL DW do Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selecione o serviço ligado criado recentemente como sink e, em seguida, clique em **Seguinte**.

    ![Selecionar serviço ligado de sink](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. Na página **mapeamento de tabela** , examine o conteúdo e selecione **Avançar**. Um mapeamento de tabela inteligente é exibido. As tabelas de origem são mapeadas para as tabelas de destino com base nos nomes de tabela. Se uma tabela de origem não existir no destino, Azure Data Factory criará uma tabela de destino com o mesmo nome por padrão. Você também pode mapear uma tabela de origem para uma tabela de destino existente. 

   > [!NOTE]
   > A criação automática de tabela para o coletor de SQL Data Warehouse aplica-se quando SQL Server ou banco de dados SQL do Azure é a origem. Se você copiar dados de outro armazenamento de dados de origem, precisará criar previamente o esquema no Azure SQL Data Warehouse do coletor antes de executar a cópia de dados.

   ![Página de mapeamento de tabela](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Na página **mapeamento de esquema** , examine o conteúdo e selecione **Avançar**. O mapeamento de tabela inteligente é baseado no nome da coluna. Se você permitir que Data Factory criem automaticamente as tabelas, a conversão de tipo de dados poderá ocorrer quando houver incompatibilidades entre os repositórios de origem e de destino. Se houver uma conversão de tipo de dados sem suporte entre a coluna de origem e de destino, você verá uma mensagem de erro ao lado da tabela correspondente.

    ![Página de mapeamento de esquema](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Na página **configurações** , conclua as seguintes etapas:

    a. Na seção **configurações de preparo** , clique em **+ novo** para novo armazenamento de preparo. O armazenamento é usado para preparar os dados antes que eles sejam carregados no SQL Data Warehouse usando o polybase. Depois que a cópia for concluída, os dados provisórios no armazenamento do Azure serão limpos automaticamente. 

    ![Configurar preparo](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Na página **novo serviço vinculado** , selecione sua conta de armazenamento e selecione **concluir**.
   
    ![Configurar o armazenamento do Azure](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. Na seção **Configurações avançadas** , desmarque a opção **usar tipo padrão** e, em seguida, selecione **Avançar**.

    ![Configurar o polybase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Na página **Resumo** , examine as configurações e selecione **Avançar**:

    ![Página de resumo](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Na **página implantação**, selecione **Monitor** para monitorar o pipeline (tarefa):

    ![Página de implementação](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **ações** inclui links para exibir detalhes da execução da atividade e executar novamente o pipeline: 

    ![Monitorizar execuções de pipeline](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Para exibir as execuções de atividade que estão associadas à execução do pipeline, selecione o link **Exibir execuções de atividade** na coluna **ações** . Para voltar para a exibição de execuções de pipeline, selecione o link **pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **detalhes** em **ações** na exibição monitoramento de atividade. Você pode monitorar detalhes como o volume de dados copiados da origem para o coletor, a taxa de transferência de dados, as etapas de execução com a duração correspondente e as configurações usadas:

    ![Detalhes da execução da atividade de monitor](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Passos seguintes

Avance para o seguinte artigo para saber mais sobre o suporte do Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Conector de SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md)
