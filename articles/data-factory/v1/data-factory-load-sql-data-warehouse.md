---
title: Carregue terabytes de dados no Armazém de Dados SQL
description: Demonstra como 1 TB de dados pode ser carregado no Armazém de Dados Azure SQL em menos de 15 minutos com a Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0bef6b5e87e7f0964989db371014c305b97f1d12
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419311"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Carregue 1 TB no Armazém de Dados Azure SQL com menos de 15 minutos com data factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte os dados da Cópia de ou para o [Armazém de Dados Azure SQL utilizando](../connector-azure-sql-data-warehouse.md)a Data Factory .


[O Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é uma base de dados baseada em nuvem, baseada em escala, capaz de processar volumes maciços de dados, tanto relacionais como não relacionais.  Construído com base na arquitetura de processamento massivamente paralelo (MPP), o SQL Data Warehouse está otimizado para cargas de trabalho de armazém de dados empresariais.  Oferece elasticidade em nuvem com a flexibilidade para escalar o armazenamento e calcular de forma independente.

Começar com o Azure SQL Data Warehouse é agora mais fácil do que nunca usando a **Azure Data Factory**.  A Azure Data Factory é um serviço de integração de dados totalmente gerido em nuvem, que pode ser usado para povoar um Armazém de Dados SQL com os dados do seu sistema existente, e economizando-lhe tempo valioso enquanto avalia o SQL Data Warehouse e constrói as suas soluções de análise. Aqui estão os principais benefícios de carregar dados no Armazém de Dados Azure SQL utilizando a Azure Data Factory:

* **Fácil de configurar**: Assistente intuitivo de 5 passos sem necessidade de scripts.
* **Suporte rico em lojas**de dados : suporte incorporado para um conjunto rico de lojas de dados no local e baseadas na nuvem.
* **Seguro e conforme:** os dados são transferidos sobre HTTPS ou ExpressRoute, e a presença global de serviçogarante que os seus dados nunca saem da fronteira geográfica
* **Desempenho inigualável utilizando a PolyBase** – A utilização da Polybase é a forma mais eficiente de mover dados para o Azure SQL Data Warehouse. Utilizando a função de "staging blob", pode obter altas velocidades de carga de todos os tipos de lojas de dados para além do armazenamento Azure Blob, que a Polybase suporta por padrão.

Este artigo mostra-lhe como usar o Data Factory Copy Wizard para carregar dados de 1 TB do Armazenamento De Azure Blob no Armazém de Dados Azure SQL em menos de 15 minutos, a mais de 1,2 GBps.

Este artigo fornece instruções passo a passo para transferir dados para o Armazém de Dados Azure SQL utilizando o Assistente de Cópia.

> [!NOTE]
>  Para obter informações gerais sobre as capacidades da Data Factory na movimentação de dados de/para o Armazém de Dados Azure SQL, consulte os dados de e para o [Armazém de Dados Azure SQL utilizando](data-factory-azure-sql-data-warehouse-connector.md) o artigo da Azure Data Factory.
>
> Também pode construir oleodutos usando visual studio, PowerShell, etc. Ver [Tutorial: Copie os dados de Azure Blob para Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para uma rápida passagem com instruções passo a passo para a utilização da Atividade de Cópia na Fábrica de Dados Azure.  
>
>

## <a name="prerequisites"></a>Pré-requisitos
* Armazenamento Azure Blob: esta experiência utiliza o Armazenamento De Blob Azure (GRS) para armazenar o conjunto de dados de teste TPC-H.  Se não tiver uma conta de armazenamento Azure, aprenda a criar uma conta de [armazenamento.](../../storage/common/storage-account-create.md)
* [Dados tPC-H:](http://www.tpc.org/tpch/) vamos usar o TPC-H como conjunto de dados de teste.  Para isso, é necessário `dbgen` utilizar a partir do conjunto de ferramentas TPC-H, o que o ajuda a gerar o conjunto de dados.  Você pode baixar código `dbgen` fonte para [ferramentas TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) e compilá-lo você mesmo, ou baixar o binário compilado do [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Executar dbgen.exe com os seguintes comandos `lineitem` para gerar 1 TB ficheiro plano para tabela distribuída por 10 ficheiros:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Agora copie os ficheiros gerados para O Blob Azure.  Consulte a [Move de e para um sistema de ficheiros no local utilizando](data-factory-onprem-file-system-connector.md) a Azure Data Factory para saber como fazê-lo usando a ADF Copy.    
* Azure SQL Data Warehouse: esta experiência carrega dados no Azure SQL Data Warehouse criado com 6.000 DWUs

    Consulte a Create a [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) para obter instruções detalhadas sobre como criar uma base de dados de Depósito de Dados SQL.  Para obter o melhor desempenho de carga possível no SQL Data Warehouse usando a Polybase, escolhemos o número máximo de Unidades de Armazém de Dados (DWUs) permitidas na definição de Desempenho, que é de 6.000 DWUs.

  > [!NOTE]
  > Ao carregar a partir de Azure Blob, o desempenho de carregamento de dados é diretamente proporcional ao número de DWUs que configura no Armazém de Dados SQL:
  >
  > Carregar 1 TB em 1.000 DWU SQL Data Warehouse demora 87 minutos (~200 MBps de entrada) Carregar 1 TB em 2.000 DWU SQL Data Warehouse demora 46 minutos (~380 MBps) Carregar 1 TB em 6.000 DWU SQL Data Warehouse demora 14 minutos (~1,2 GBps através da entrada)
  >
  >

    Para criar um Armazém de Dados SQL com 6.000 DWUs, mova o slider performance todo o caminho para a direita:

    ![Deslize de desempenho](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Para uma base de dados existente que não esteja configurada com 6.000 DWUs, pode dimensioná-la utilizando o portal Azure.  Navegue para a base de dados no portal Azure, e há um botão **Escala** no painel **de visão geral** mostrado na seguinte imagem:

    ![Botão de escala](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Clique no botão **Escala** para abrir o painel seguinte, mova o slider para o valor máximo e clique em **Guardar** o botão.

    ![Diálogo de escala](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Esta experiência carrega dados no Azure `xlargerc` SQL Data Warehouse utilizando a classe de recursos.

    Para obter a melhor entrada possível, a cópia precisa de `xlargerc` ser realizada utilizando um utilizador do SQL Data Warehouse pertencente à classe de recursos.  Aprenda a fazê-lo seguindo alterar um exemplo de [classe de recursos do utilizador](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Crie esquema de tabela de destino na base de dados do Armazém de Dados Azure SQL, executando a seguinte declaração dDL:

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
  Com os passos pré-requisitos concluídos, estamos agora prontos para configurar a atividade de cópia utilizando o Assistente de Cópia.

## <a name="launch-copy-wizard"></a>Inicie o Assistente de Cópia
1. Faça login no [portal Azure.](https://portal.azure.com)
2. Clique **em Criar um recurso** a partir do canto superior esquerdo, clique em Inteligência + **análise,** e clique em **Data Factory**.
3. No painel da nova fábrica de **dados:**

   1. Introduza **loadIntoSQLDWDataFactory** para o **nome**.
       O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: O nome de **fábrica de dados "LoadIntoSQLDWDataFactory" não está disponível,** altere o nome da fábrica de dados (por exemplo, o seu nomeLoadIntoSQLDWDataFactory) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.  
   2. Selecione a sua **subscrição** do Azure.
   3. No Grupo de Recursos, siga um destes passos:
      1. Selecione **Utilizar existente** para selecionar um grupo de recursos já existente.
      2. Selecione **Criar novo** para introduzir um nome para um grupo de recursos.
   4. Selecione uma **localização** para a fábrica de dados.
   5. Selecione a caixa de verificação **Afixar ao dashboard**, na parte inferior do painel.  
   6. Clique em **Criar**.
4. Após a criação estar concluída, vê a lâmina **data Factory** como mostrado na seguinte imagem:

   ![Home page da fábrica de dados](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na home page do Data Factory, clique no mosaico **Copiar dados** para iniciar o **Assistente de Cópia**.

   > [!NOTE]
   > Se vir que o browser bloqueia enquanto estiver a "A autorizar…", desative/desmarque a definição **Bloquear cookies de terceiros e dados do site** (ou) mantenha-a ativada e crie uma exceção para **login.microsoftonline.com** e, em seguida, tente iniciar novamente o assistente.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Passo 1: Configurar o calendário de carregamento de dados
O primeiro passo é configurar o calendário de carregamento de dados.  

Na página **Propriedades**:

1. Insira **CopyFromBlobToAzureSqlDataWarehouse** para nome de **tarefa**
2. Selecione **Executar uma vez agora** opção.   
3. Clique em **Seguinte**.  

    ![Copy Wizard - Página de propriedades](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Passo 2: Configurar a fonte
Esta secção mostra-lhe os passos para configurar a fonte: Azure Blob contendo os ficheiros de itens de linha TPC-H de 1 TB.

1. Selecione o **Armazenamento De Blob Azure** como a loja de dados e clique **em Next**.

    ![Copy Wizard - Selecione página de origem](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Preencha as informações de ligação para a conta de armazenamento Do Blob Azure e clique em **Next**.

    ![Assistente de cópia - Informações de ligação de origem](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Escolha a **pasta** que contém os ficheiros de itens da linha TPC-H e clique em **Next**.

    ![Copy Wizard - selecione pasta de entrada](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Ao clicar **em Seguinte,** as definições do formato de ficheiro são detetadas automaticamente.  Verifique se o delimitador da coluna está '/' em vez da vírem predefinida ''.  Clique **em Seguir** depois de ter pré-visualizado os dados.

    ![Copy Wizard - definições de formato de ficheiro](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Passo 3: Destino de configuração
Esta secção mostra-lhe como configurar o destino: `lineitem` tabela na base de dados do Armazém de Dados Azure SQL.

1. Escolha o **Azure SQL Data Warehouse** como loja de destino e clique **em Next**.

    ![Copy Wizard - selecione loja de dados de destino](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Preencha as informações de ligação para o Azure SQL Data Warehouse.  Certifique-se de especificar o utilizador que `xlargerc` é membro da função (consulte a secção **de pré-requisitos** para instruções detalhadas) e clique em **Seguinte**.

    ![Copy Wizard - informações de ligação ao destino](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Escolha a tabela de destino e clique **em Seguinte**.

    ![Copy Wizard - página de mapeamento de tabela](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na página de mapeamento de Schema, deixe a opção "Aplicar o mapeamento da coluna" sem ser controlada e clique **em Next**.

## <a name="step-4-performance-settings"></a>Passo 4: Definições de desempenho

**Permitir que a polibase** seja verificada por defeito.  Clique em **Seguinte**.

![Copy Wizard - página de mapeamento de esquemas](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Passo 5: Implementar e monitorizar os resultados da carga
1. Clique no botão **Terminar** para desdobrar.

    ![Copy Wizard - página sumária](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Depois de concluída a `Click here to monitor copy pipeline` implementação, clique para monitorizar o progresso da execução da cópia. Selecione o pipeline de cópia que criou na lista Do Windows de **Atividade.**

    ![Copy Wizard - página sumária](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Pode visualizar os detalhes da execução da cópia no Activity **Window Explorer** no painel certo, incluindo o volume de dados lido a partir de fonte e escrito no destino, duração e a produção média para a execução.

    Como pode ver na seguinte imagem, copiar 1 TB do Armazenamento Azure Blob para o SQL Data Warehouse demorou 14 minutos, efetivamente alcançando a entrada de 1,22 GBps!

    ![Copy Wizard - diálogo bem sucedido](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Melhores práticas
Aqui estão algumas boas práticas para executar a sua base de dados Azure SQL Data Warehouse:

* Utilize uma classe de recursos maior ao carregar num ÍNDICE DE COLUNAS CLUSTERED.
* Para juntar-se mais eficiente, considere utilizar a distribuição de hash por uma coluna selecionada em vez da distribuição de robin redondo padrão.
* Para velocidades de carga mais rápidas, considere usar heap para dados transitórios.
* Crie estatísticas depois de terminar de carregar o Azure SQL Data Warehouse.

Consulte [as melhores práticas para o Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes
* [Data Factory Copy Wizard](data-factory-copy-wizard.md) - Este artigo fornece detalhes sobre o Assistente de Cópia.
* Guia de [desempenho e afinação](data-factory-copy-activity-performance.md) da atividade da cópia - Este artigo contém as medições de desempenho de referência e o guia de afinação.
