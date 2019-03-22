---
title: Carregar terabytes de dados para o SQL Data Warehouse | Documentos da Microsoft
description: Demonstra como 1 TB de dados podem ser carregado para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e275411f9fd9dfb672bb0815e83e37bcd5d1dda9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077025"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [copiar dados de ou para o Azure SQL Data Warehouse com o Data Factory](../connector-azure-sql-data-warehouse.md).


[O Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é uma base de dados com base na cloud, de escalamento horizontal capaz de processar grandes volumes de dados tanto relacionais como não relacionais.  Construido numa arquitetura de processamento paralelo em massa (MPP), o SQL Data Warehouse é otimizado para cargas de trabalho de armazém de dados de empresariais.  Ele oferece flexibilidade na cloud com a flexibilidade para aumentar o armazenamento e computação de forma independente.

Introdução ao Azure SQL Data Warehouse agora é mais fácil do que nunca usando **do Azure Data Factory**.  O Azure Data Factory é um serviço de integração de dados com base na cloud totalmente gerido, que pode ser utilizado para preencher um armazém de dados SQL com os dados do sistema existente e economizando o seu valioso tempo ao avaliar o SQL Data Warehouse e criar as suas análises soluções. Aqui estão os principais benefícios do carregamento de dados para o Azure SQL Data Warehouse com o Azure Data Factory:

* **Fácil de configurar**: passo 5 assistente intuitivo com nenhum script necessário.
* **Suporte de arquivo de dados avançados**: suporte interno para um conjunto avançado de arquivos de dados com base na cloud e no local.
* **Segura e em conformidade**: os dados são transferidos através de HTTPS ou o ExpressRoute e presença global do serviço garante que os dados nunca sai do limite geográfico
* **Desempenho sem paralelo com o PolyBase** – utilizar o Polybase é a maneira mais eficiente para mover dados para o Azure SQL Data Warehouse. Utilizar a funcionalidade de blob de teste, pode atingir velocidades de carga elevada de todos os tipos de arquivos de dados, além de armazenamento de Blobs do Azure, o que o Polybase suporta por predefinição.

Este artigo mostra-lhe como utilizar o Assistente de cópia do Data Factory para carregar dados de 1 TB de armazenamento de Blobs do Azure para o Azure SQL Data Warehouse em menos de 15 minutos, a mais de 1,2 GBps débito.

Este artigo fornece instruções passo a passo para mover dados para o Azure SQL Data Warehouse, utilizando o Assistente de cópia.

> [!NOTE]
>  Para obter informações gerais sobre as capacidades da fábrica de dados de mover dados de/para o Azure SQL Data Warehouse, consulte [mover dados para e do armazém de dados SQL do Azure com o Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) artigo.
>
> Também pode criar pipelines com o portal do Azure, Visual Studio, PowerShell, etc. Consulte [Tutorial: Copiar dados de Blobs do Azure para a base de dados do Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para uma passo a passo rápido com instruções passo a passo para usar a atividade de cópia no Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Pré-requisitos
* Armazenamento de Blobs do Azure: desta experiência utiliza o armazenamento de Blobs do Azure (GRS) para armazenar o conjunto de dados de teste TPC-H.  Se não tiver uma conta de armazenamento do Azure, aprender [como criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md).
* [TPC-H](http://www.tpc.org/tpch/) dados: vamos utilizar o TPC-H como o conjunto de dados de teste.  Para tal, tem de utilizar `dbgen` do Kit de ferramentas do TPC-H, que ajuda a gerar o conjunto de dados.  Pode baixar código-fonte `dbgen` partir [TPC ferramentas](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) e compilá-lo por conta própria ou transferir o binário compilado da [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Executar dbgen.exe com os seguintes comandos para gerar o ficheiro simples de 1 TB para `lineitem` disseminação de tabela em 10 ficheiros:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Agora copie os arquivos gerados para BLOBs do Azure.  Consulte a [mover dados de e para um sistema de ficheiros no local com o Azure Data Factory](data-factory-onprem-file-system-connector.md) para saber como fazê-lo utilizando a cópia do ADF.    
* O Azure SQL Data Warehouse: desta experiência carrega dados para o Azure SQL Data Warehouse criado com 6.000 DWUs

    Consulte a [criar um Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) para obter instruções detalhadas sobre como criar uma base de dados do SQL Data Warehouse.  Para obter o melhor desempenho de carga possível para o SQL Data Warehouse com o Polybase, escolhemos o número máximo de unidades de armazém de dados (DWUs) permitido na definição de desempenho, o que é 6.000 DWUs.

  > [!NOTE]
  > Ao carregar Blob do Azure, o desempenho de carregamento de dados são diretamente proporcionais ao número de DWUs que configurar no armazém de dados SQL:
  >
  > Carregar 1 TB para 1.000 DWU SQL Data Warehouse tem 87 minutos (aproximadamente 200 MBps débito) a carregar 1 TB em 2.000 DWU SQL Data Warehouse assume 46 minutos (~ 380 MBps débito) a carregar 1 TB para 6.000 DWU o SQL Data Warehouse demora 14 minutos (~1.2 GBps débito)
  >
  >

    Para criar um SQL Data Warehouse com 6.000 DWUs, mova o controlo de deslize de desempenho para a direita:

    ![Controlo de deslize de desempenho](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Para uma base de dados existente que não está configurado com 6.000 DWUs, pode dimensioná-lo através do portal do Azure.  Navegue para a base de dados no portal do Azure e há uma **escala** botão no **descrição geral** painel mostrado na imagem seguinte:

    ![Botão de escala](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Clique nas **escala** botão para abrir o painel seguinte, mova o controlo de deslize para o valor máximo e clique em **guardar** botão.

    ![Caixa de diálogo de dimensionamento](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Esta experiência carrega dados para utilizar o Azure SQL Data Warehouse `xlargerc` classe de recursos.

    Para obter o melhor débito possíveis, cópia tem de ser efetuada utilizando um utilizador do SQL Data Warehouse que pertencem a `xlargerc` classe de recursos.  Saiba como fazê-lo ao seguir [alterar um exemplo de classe de recursos de utilizador](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Crie o esquema da tabela de destino na base de dados do Azure SQL Data Warehouse, ao executar a seguinte instrução DDL:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  Com os passos de pré-requisitos foi concluídos, podemos agora está prontos para configurar a atividade de cópia com o Assistente para copiar.

## <a name="launch-copy-wizard"></a>Inicie o Assistente de Cópia
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **criar um recurso** no canto superior esquerdo, clique em **inteligência + análise**e clique em **Data Factory**.
3. Na **nova fábrica de dados** painel:

   1. Introduza **LoadIntoSQLDWDataFactory** para o **nome**.
       O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: **Nome da fábrica de dados "LoadIntoSQLDWDataFactory" não está disponível**, altere o nome da fábrica de dados (por exemplo, yournameLoadIntoSQLDWDataFactory) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.  
   2. Selecione a sua **subscrição** do Azure.
   3. No Grupo de Recursos, siga um destes passos:
      1. Selecione **Utilizar existente** para selecionar um grupo de recursos já existente.
      2. Selecione **Criar novo** para introduzir um nome para um grupo de recursos.
   4. Selecione uma **localização** para a fábrica de dados.
   5. Selecione a caixa de verificação **Afixar ao dashboard**, na parte inferior do painel.  
   6. Clique em **Criar**.
4. Após concluir a criação, verá o painel **Data Factory**, conforme apresentado na imagem seguinte:

   ![Home page da fábrica de dados](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na home page do Data Factory, clique no mosaico **Copiar dados** para iniciar o **Assistente de Cópia**.

   > [!NOTE]
   > Se vir que o browser bloqueia enquanto estiver a "A autorizar…", desative/desmarque a definição **Bloquear cookies de terceiros e dados do site** (ou) mantenha-a ativada e crie uma exceção para **login.microsoftonline.com** e, em seguida, tente iniciar novamente o assistente.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Passo 1: Configurar a agenda de carregamento de dados
A primeira etapa é configurar o agendamento de carregamento de dados.  

Na página **Propriedades**:

1. Introduza **CopyFromBlobToAzureSqlDataWarehouse** para **nome da tarefa**
2. Selecione **executar uma vez agora** opção.   
3. Clique em **Seguinte**.  

    ![Assistente para copiar - página de propriedades](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Passo 2: Configurar origem
Esta secção mostra-lhe os passos para configurar a origem: BLOBs do Azure que contém a 1 TB TPC-arquivos de item de linha de H.

1. Selecione o **armazenamento de Blobs do Azure** à medida que os dados armazenar e clique em **próxima**.

    ![Assistente para copiar - página selecionar origem](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Preencha as informações de ligação para a conta de armazenamento de Blobs do Azure e clique em **seguinte**.

    ![Assistente para copiar - informações de ligação de origem](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Escolha o **pasta** que contém o TPC-H arquivos de item de linha e clique em **próxima**.

    ![Assistente para copiar - selecionar pasta de entrada](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Após clicar em **seguinte**, as definições do formato de ficheiro são detetadas automaticamente.  Certifique-se de que delimitador de colunas é ' | 'em vez de vírgulas predefinido','.  Clique em **seguinte** depois de ter visualizado os dados.

    ![Assistente para copiar - definições do formato de ficheiro](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Passo 3: Configurar o destino
Esta secção mostra-lhe como configurar o destino: `lineitem` tabela na base de dados Azure SQL Data Warehouse.

1. Escolher **do Azure SQL Data Warehouse** como o destino armazenar e clique em **próxima**.

    ![Assistente para copiar - arquivo de dados de destino selecione](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Preencha as informações de ligação para o Azure SQL Data Warehouse.  Certifique-se de especificar o utilizador que seja membro da função `xlargerc` (consulte a **pré-requisitos** seção para obter instruções detalhadas) e clique em **seguinte**.

    ![Assistente para copiar - informações de ligação de destino](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Escolha a tabela de destino e clique em **seguinte**.

    ![Assistente - página de mapeamento de tabela de cópia](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na página de mapeamento de esquema, deixe a opção de "Aplicar o mapeamento de colunas" desmarcada e clique em **seguinte**.

## <a name="step-4-performance-settings"></a>Passo 4: Definições de desempenho

**Permitir polybase** é marcada por padrão.  Clique em **Seguinte**.

![Assistente - página de mapeamento de esquema de cópia](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Passo 5: Implementar e monitorizar os resultados de carga
1. Clique em **concluir** botão para implementar.

    ![Assistente para copiar - página de resumo](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Após a implementação estiver concluída, clique em `Click here to monitor copy pipeline` para monitorizar a cópia de executar o progresso. Selecione o pipeline de cópia que criou o **atividade Windows** lista.

    ![Assistente para copiar - página de resumo](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Pode ver a cópia de detalhes da execução **Explorador de janelas de atividade** no painel à direita, incluindo o volume de dados de leitura de origem e gravados no destino, a duração e o débito médio para a execução.

    Como pode ver na captura de ecrã seguinte, copie 1 TB de armazenamento de Blobs do Azure para o SQL Data Warehouse demorou 14 minutos, com eficiência um 1.22 de GBps de débito!

    ![Assistente para copiar - caixa de diálogo com êxito](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Melhores práticas
Seguem-se algumas melhores práticas para executar a sua base de dados do Azure SQL Data Warehouse:

* Utilize uma classe de recursos maior ao carregar em índices COLUMNSTORE em cluster.
* Para associações mais eficientes, considere a utilização de distribuição de hash por uma seleção de colunas em vez da predefinição arredondar distribuição round robin.
* Para velocidades de carga, considere a utilização de área dinâmica para dados para dados transitórios.
* Crie estatísticas após ter concluído a carregar o Azure SQL Data Warehouse.

Ver [melhores práticas do Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) para obter detalhes.

## <a name="next-steps"></a>Passos Seguintes
* [Assistente de cópia do Data Factory](data-factory-copy-wizard.md) -este artigo fornece detalhes sobre o Assistente de cópia.
* [Copie o guia de sintonização de desempenho de atividade e](data-factory-copy-activity-performance.md) -este artigo contém as medidas de desempenho de referência e o guia de ajuste.
