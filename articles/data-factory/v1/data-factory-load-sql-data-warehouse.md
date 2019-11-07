---
title: Carregar terabytes de dados em SQL Data Warehouse
description: Demonstra como 1 TB de dados podem ser carregados no Azure SQL Data Warehouse menos de 15 minutos com Azure Data Factory
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
ms.openlocfilehash: 05dcff2276a799b1debc76e4f85fbbac6606eb59
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682538"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Carregue 1 TB no Azure SQL Data Warehouse menos de 15 minutos com Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [copiar dados de ou para o Azure SQL data warehouse usando data Factory](../connector-azure-sql-data-warehouse.md).


O [Azure SQL data warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é um banco de dados baseado em nuvem, escalável e em expansão capaz de processar grandes volumes de data, relacionais e não relacionais.  Baseado na arquitetura MPP (processamento paralelo maciço), a SQL Data Warehouse é otimizada para cargas de trabalho de data warehouse empresarial.  Ele oferece elasticidade de nuvem com a flexibilidade para dimensionar o armazenamento e a computação de forma independente.

A introdução ao SQL Data Warehouse do Azure agora está mais fácil do que nunca usando **Azure data Factory**.  Azure Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado, que pode ser usado para preencher um SQL Data Warehouse com os dados do seu sistema existente e economizar tempo valioso ao avaliar SQL Data Warehouse e criar sua análise soluções. Aqui estão os principais benefícios de carregar dados no Azure SQL Data Warehouse usando Azure Data Factory:

* **Fácil de configurar**: assistente intuitivo de 5 etapas sem nenhum script necessário.
* **Suporte a armazenamento de dados avançado**: suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem.
* **Seguro e em conformidade**: os dados são transferidos por HTTPS ou ExpressRoute, e a presença do serviço global garante que seus dados nunca saiam do limite geográfico
* O **desempenho inigualável usando o polybase** – usar o polybase é a maneira mais eficiente de mover dados para o Azure SQL data warehouse. Usando o recurso de blob de preparo, você pode obter altas velocidades de carga de todos os tipos de armazenamentos de dados além do armazenamento de BLOBs do Azure, ao qual o polybase dá suporte por padrão.

Este artigo mostra como usar Data Factory assistente de cópia para carregar dados de 1 TB do armazenamento de BLOBs do Azure para o Azure SQL Data Warehouse em menos de 15 minutos, com mais de 1,2 GBps de taxa de transferência.

Este artigo fornece instruções passo a passo para mover dados para o Azure SQL Data Warehouse usando o assistente de cópia.

> [!NOTE]
>  Para obter informações gerais sobre os recursos de Data Factory na movimentação de dados de/para o Azure SQL Data Warehouse, consulte [mover dados de e para o azure SQL data warehouse usando](data-factory-azure-sql-data-warehouse-connector.md) o artigo Azure data Factory.
>
> Você também pode criar pipelines usando o Visual Studio, o PowerShell, etc. Consulte [tutorial: copiar dados do blob do Azure para o banco](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) de dados SQL do Azure para obter uma rápida explicação com instruções passo a passo para usar a atividade de cópia no Azure data Factory.  
>
>

## <a name="prerequisites"></a>Pré-requisitos
* Armazenamento de BLOBs do Azure: esse experimento usa o armazenamento de BLOBs do Azure (GRS) para armazenar o conjunto de testes de TPC-H.  Se você não tiver uma conta de armazenamento do Azure, saiba [como criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md).
* Dados do [TPC-h](http://www.tpc.org/tpch/) : vamos usar o TPC-h como o conjunto de dados de teste.  Para fazer isso, você precisa usar `dbgen` do kit de ferramentas do TPC-H, que ajuda a gerar o conjunto de um.  Você pode baixar o código-fonte para `dbgen` de [Ferramentas do TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) e compilá-lo por conta própria ou baixar o binário compilado do [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Execute o dbgen. exe com os seguintes comandos para gerar um arquivo simples de 1 TB para a tabela `lineitem` se espalhar em 10 arquivos:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * ...
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Agora, copie os arquivos gerados para o blob do Azure.  Consulte [mover dados de e para um sistema de arquivos local usando Azure data Factory](data-factory-onprem-file-system-connector.md) para saber como fazer isso usando a cópia do ADF.    
* SQL Data Warehouse do Azure: este experimento carrega dados no Azure SQL Data Warehouse criado com 6.000 DWUs

    Consulte [criar uma SQL data warehouse do Azure](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) para obter instruções detalhadas sobre como criar um banco de dados SQL data warehouse.  Para obter o melhor desempenho de carga possível em SQL Data Warehouse usando o polybase, escolhemos o número máximo de DWUs (unidades de data warehouse) permitido na configuração de desempenho, que é 6.000 DWUs.

  > [!NOTE]
  > Ao carregar do blob do Azure, o desempenho do carregamento de dados é diretamente proporcional ao número de DWUs que você configura no SQL Data Warehouse:
  >
  > O carregamento de 1 TB em 1.000 DWU SQL Data Warehouse leva 87 minutos (~ 200 MBps de taxa de transferência) carregando 1 TB em 2.000 DWU SQL Data Warehouse leva de 46 minutos (~ 380 de taxa de transferência) carregando 1 TB em 6.000 DWU SQL Data Warehouse leva 14 minutos (~ 1,2 de taxa de transferência)
  >
  >

    Para criar um SQL Data Warehouse com 6.000 DWUs, mova o controle deslizante de desempenho até o lado direito:

    ![Controle deslizante de desempenho](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Para um banco de dados existente que não está configurado com 6.000 DWUs, você pode dimensioná-lo usando portal do Azure.  Navegue até o banco de dados em portal do Azure, e há um botão de **escala** no painel **visão geral** mostrado na imagem a seguir:

    ![Botão de escala](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Clique no botão **escala** para abrir o painel a seguir, mova o controle deslizante para o valor máximo e clique no botão **salvar** .

    ![Caixa de diálogo dimensionar](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Esse experimento carrega dados no Azure SQL Data Warehouse usando `xlargerc` classe de recurso.

    Para obter a melhor taxa de transferência possível, a cópia precisa ser executada usando um SQL Data Warehouse usuário pertencente `xlargerc` classe de recurso.  Saiba como fazer isso seguindo [alterar um exemplo de classe de recurso de usuário](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Crie um esquema de tabela de destino no banco de dados SQL Data Warehouse do Azure, executando a seguinte instrução DDL:

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
  Com as etapas de pré-requisito concluídas, agora estamos prontos para configurar a atividade de cópia usando o assistente de cópia.

## <a name="launch-copy-wizard"></a>Inicie o Assistente de Cópia
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **criar um recurso** no canto superior esquerdo, clique em **inteligência + análise**e clique em **Data Factory**.
3. No painel **novo data Factory** :

   1. Digite **LoadIntoSQLDWDataFactory** para o **nome**.
       O nome do Azure Data Factory deve ser globalmente exclusivo. Se você receber o erro: o **nome do data Factory "LoadIntoSQLDWDataFactory" não está disponível**, altere o nome do data Factory (por exemplo, yournameLoadIntoSQLDWDataFactory) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.  
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

## <a name="step-1-configure-data-loading-schedule"></a>Etapa 1: configurar o agendamento de carregamento de dados
A primeira etapa é configurar o agendamento de carregamento de dados.  

Na página **Propriedades**:

1. Insira **CopyFromBlobToAzureSqlDataWarehouse** para o **nome da tarefa**
2. Selecione a opção **executar uma vez agora** .   
3. Clique em **Seguinte**.  

    ![Assistente de cópia – página Propriedades](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Etapa 2: configurar a origem
Esta seção mostra as etapas para configurar a origem: BLOB do Azure que contém os arquivos de item de linha de 1 TB TPC-H.

1. Selecione o **armazenamento de BLOBs do Azure** como o armazenamento de dados e clique em **Avançar**.

    ![Assistente de cópia – selecionar página de origem](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Preencha as informações de conexão para a conta de armazenamento de BLOBs do Azure e clique em **Avançar**.

    ![Assistente de cópia-informações de conexão de origem](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Escolha a **pasta** que contém os arquivos de item de linha TPC-H e clique em **Avançar**.

    ![Assistente de cópia – selecionar pasta de entrada](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Quando você clicar em **Avançar**, as configurações de formato de arquivo serão detectadas automaticamente.  Verifique se o delimitador de coluna é ' | ' em vez da vírgula padrão ', '.  Clique em **Avançar** depois de visualizar os dados.

    ![Assistente de cópia – configurações de formato de arquivo](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Etapa 3: configurar o destino
Esta seção mostra como configurar a tabela de destino: `lineitem` no banco de dados de SQL Data Warehouse do Azure.

1. Escolha **Azure SQL data warehouse** como o repositório de destino e clique em **Avançar**.

    ![Assistente de cópia – selecionar armazenamento de dados de destino](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Preencha as informações de conexão do SQL Data Warehouse do Azure.  Certifique-se de especificar o usuário que é membro da função `xlargerc` (consulte a seção **pré-requisitos** para obter instruções detalhadas) e clique em **Avançar**.

    ![Assistente de cópia-informações de conexão de destino](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Escolha a tabela de destino e clique em **Avançar**.

    ![Assistente de cópia – página mapeamento de tabela](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na página mapeamento de esquema, deixe a opção "aplicar mapeamento de coluna" desmarcada e clique em **Avançar**.

## <a name="step-4-performance-settings"></a>Etapa 4: configurações de desempenho

A opção **permitir polybase** é marcada por padrão.  Clique em **Seguinte**.

![Assistente de cópia – página mapeamento de esquema](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Etapa 5: implantar e monitorar os resultados da carga
1. Clique no botão **concluir** para implantar.

    ![Assistente de cópia – página Resumo](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Após a conclusão da implantação, clique em `Click here to monitor copy pipeline` para monitorar o andamento da execução da cópia. Selecione o pipeline de cópia que você criou na lista **janelas de atividades** .

    ![Assistente de cópia – página Resumo](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Você pode exibir os detalhes da execução de cópia no **Gerenciador de janelas de atividade** no painel direito, incluindo o volume de dados lido da origem e gravado no destino, a duração e a taxa de transferência média para a execução.

    Como você pode ver na captura de tela a seguir, a cópia de 1 TB do armazenamento de BLOBs do Azure em SQL Data Warehouse levou 14 minutos, atingindo efetivamente a taxa de transferência de 1,22 GBps!

    ![Assistente de cópia-diálogo com êxito](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Melhores práticas
Aqui estão algumas práticas recomendadas para executar o banco de dados SQL Data Warehouse do Azure:

* Use uma classe de recursos maior ao carregar em um índice COLUMNSTORE CLUSTERIZAdo.
* Para junções mais eficientes, considere usar a distribuição de hash por uma coluna Select em vez da distribuição de round robin padrão.
* Para velocidades de carga mais rápidas, considere o uso de heap para dados transitórios.
* Crie estatísticas depois de concluir o carregamento do SQL Data Warehouse do Azure.

Consulte [práticas recomendadas para o Azure SQL data warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) para obter detalhes.

## <a name="next-steps"></a>Passos seguintes
* [Data Factory assistente de cópia](data-factory-copy-wizard.md) – este artigo fornece detalhes sobre o assistente de cópia.
* [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md) – este artigo contém as medições de desempenho de referência e o guia de ajuste.
