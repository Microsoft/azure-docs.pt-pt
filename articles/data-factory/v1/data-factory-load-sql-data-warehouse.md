---
title: Carregue terabytes de dados em Azure Synapse Analytics
description: Demonstra como 1 TB de dados pode ser carregado no Azure Synapse Analytics em menos de 15 minutos com Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5acae7c90efbf178fad199177fa6e0886e497fdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371214"
---
# <a name="load-1-tb-into-azure-synapse-analytics-under-15-minutes-with-data-factory"></a>Carregue 1 TB em Azure Synapse Analytics em menos de 15 minutos com data factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [os dados de Copy para ou a partir do Azure Synapse Analytics utilizando a Data Factory](../connector-azure-sql-data-warehouse.md).


[A Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é uma base de dados baseada na nuvem, capaz de processar volumes maciços de dados, tanto relacionais como não relacionais.  Construído sobre a arquitetura de processamento massivamente paralelo (MPP), o Azure Synapse Analytics está otimizado para a carga de trabalho do armazém de dados da empresa.  Oferece elasticidade em nuvem com a flexibilidade para escalar o armazenamento e calcular de forma independente.

Começar com a Azure Synapse Analytics é agora mais fácil do que nunca usando **a Azure Data Factory**.  A Azure Data Factory é um serviço de integração de dados totalmente gerido em nuvem, que pode ser usado para povoar o Azure Synapse Analytics com os dados do seu sistema existente, e economizar tempo valioso enquanto avalia o Azure Synapse Analytics e construir as suas soluções de análise. Aqui estão os principais benefícios de carregar dados em Azure Synapse Analytics usando Azure Data Factory:

* **Fácil de configurar:** assistente intuitivo de 5 etapas sem necessidade de scripts.
* Suporte rico em **lojas** de dados: suporte incorporado para um conjunto rico de lojas de dados no local e lojas de dados baseadas em nuvem.
* **Seguro e compatível:** os dados são transferidos sobre HTTPS ou ExpressRoute, e a presença global de serviços garante que os seus dados nunca saiam da fronteira geográfica
* **Desempenho incomparável utilizando o PolyBase** – Usar a Polybase é a forma mais eficiente de mover dados para a Azure Synapse Analytics. Utilizando a função blob de preparação, pode obter altas velocidades de carga de todos os tipos de lojas de dados, além do armazenamento Azure Blob, que a Base Polibase suporta por padrão.

Este artigo mostra-lhe como utilizar o Assistente de Cópia da Fábrica de Dados para carregar dados de 1-TB do Azure Blob Storage em Azure Synapse Analytics em menos de 15 minutos, a mais de 1,2 GBps.

Este artigo fornece instruções passo a passo para mover dados para a Azure Synapse Analytics utilizando o Copy Wizard.

> [!NOTE]
>  Para obter informações gerais sobre as capacidades da Data Factory na deslocação de dados de/para a Azure Synapse Analytics, consulte [os dados do Movimento de e para a Azure Synapse Analytics utilizando o artigo da Azure Data Factory.](data-factory-azure-sql-data-warehouse-connector.md)
>
> Também pode construir oleodutos utilizando o Visual Studio, PowerShell, etc. Consulte [Tutorial: Copie os dados da Azure Blob para a Base de Dados Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para uma rápida passagem com instruções passo a passo para a utilização da Atividade de Cópia na Fábrica de Dados Azure.  
>
>

## <a name="prerequisites"></a>Pré-requisitos
* Azure Blob Storage: esta experiência utiliza o Azure Blob Storage (GRS) para armazenar conjunto de dados de teste TPC-H.  Se não tiver uma conta de armazenamento Azure, aprenda [a criar uma conta de armazenamento.](../../storage/common/storage-account-create.md)
* [Dados TPC-H:](http://www.tpc.org/tpch/) vamos usar o TPC-H como conjunto de dados de teste.  Para isso, é necessário utilizar a `dbgen` partir do kit de ferramentas TPC-H, o que o ajuda a gerar o conjunto de dados.  Você pode baixar código fonte para `dbgen` a partir de [Ferramentas TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications5.asp) e compilá-lo por si mesmo, ou baixar o binário compilado do [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Executar dbgen.exe com os seguintes comandos para gerar 1 ficheiro plano de TB para `lineitem` tabela espalhada por 10 ficheiros:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Agora copie os ficheiros gerados para a Azure Blob.  Consulte para [mover dados de e para um sistema de ficheiros no local, utilizando a Azure Data Factory](data-factory-onprem-file-system-connector.md) para saber como fazê-lo usando a Cópia ADF.    
* Azure Synapse Analytics: esta experiência carrega dados em Azure Synapse Analytics criado com 6.000 DWUs

    Consulte a [Create a Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) para obter instruções detalhadas sobre como criar uma base de dados Azure Synapse Analytics.  Para obter o melhor desempenho de carga possível no Azure Synapse Analytics usando a Polybase, escolhemos o número máximo de Unidades de Armazém de Dados (DWUs) permitidas na definição de Desempenho, que é de 6.000 DWUs.

  > [!NOTE]
  > Ao carregar a partir de Azure Blob, o desempenho de carregamento de dados é diretamente proporcional ao número de DWUs que configura para Azure Synapse Analytics:
  >
  > Carregar 1 TB em 1.000 DWU Azure Synapse Analytics demora 87 minutos (produção~200 MBps) Carregamento 1 TB em 2.000 Sinaps DWU Azure oe Analytics demora 46 minutos (produção~380 MBps) O carregamento de 1 TB em 6.000 DWU Azure Synapse Analytics demora 14 minutos (produção~1,2 GBps)
  >
  >

    Para criar uma piscina SQL dedicada com 6.000 DWUs, mova o slider performance todo o caminho para a direita:

    ![Slider de desempenho](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Para uma base de dados existente que não esteja configurada com 6.000 DWUs, pode escaloná-la usando o portal Azure.  Navegue para a base de dados no portal Azure, e existe um botão **escala** no painel **de visão geral** mostrado na seguinte imagem:

    ![Botão de escala](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Clique no botão **Escala** para abrir o painel seguinte, mova o slider para o valor máximo e clique no botão **Guardar.**

    ![Diálogo de escala](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Esta experiência carrega dados em Azure Synapse Analytics usando `xlargerc` a classe de recursos.

    Para obter o melhor resultado possível, a cópia precisa de ser executada utilizando um utilizador Azure Synapse Analytics pertencente à `xlargerc` classe de recursos.  Aprenda a fazê-lo seguindo [O exemplo da classe de recursos do utilizador.](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md)  
* Criar esquema de tabela de destino na base de dados Azure Synapse Analytics, executando a seguinte declaração DDL:

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
  Com os passos pré-requisitos concluídos, estamos agora prontos para configurar a atividade da cópia utilizando o Copy Wizard.

## <a name="launch-copy-wizard"></a>Inicie o Assistente de Cópia
1. Faça login no [portal Azure](https://portal.azure.com).
2. Clique **em Criar um recurso** a partir do canto superior esquerdo, clique em Inteligência + **análise,** e clique em **Data Factory**.
3. No novo painel de **fábrica de dados:**

   1. **Insira LoadIntoSQLDWDataFactory** para o **nome**.
       O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: O **nome da fábrica de dados "LoadIntoSQLDWDataFactory" não está disponível,** altere o nome da fábrica de dados (por exemplo, o seu nomeLoadIntoSQLDWDataFactory) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.  
   2. Selecione a sua **subscrição Azure**.
   3. No Grupo de Recursos, siga um destes passos:
      1. Selecione **Utilizar existente** para selecionar um grupo de recursos já existente.
      2. Selecione **Criar novo** para introduzir um nome para um grupo de recursos.
   4. Selecione uma **localização** para a fábrica de dados.
   5. Selecione a caixa de verificação **Afixar ao dashboard**, na parte inferior do painel.  
   6. Clique em **Criar**.
4. Após a criação estar concluída, vê a lâmina da **Fábrica de Dados** como mostrado na seguinte imagem:

   ![Home page da fábrica de dados](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na home page do Data Factory, clique no mosaico **Copiar dados** para iniciar o **Assistente de Cópia**.

   > [!NOTE]
   > Se vir que o browser bloqueia enquanto estiver a "A autorizar…", desative/desmarque a definição **Bloquear cookies de terceiros e dados do site** (ou) mantenha-a ativada e crie uma exceção para **login.microsoftonline.com** e, em seguida, tente iniciar novamente o assistente.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Passo 1: Configurar o calendário de carregamento de dados
O primeiro passo é configurar o calendário de carregamento de dados.  

Na página **Propriedades**:

1. **Insira copyFromBlobToAzureSqlDataWarehouse** para **nome de tarefa**
2. Selecione **Executar uma vez agora** a opção.   
3. Clique em **Seguinte**.  

    ![Copy Wizard - Página de propriedades](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Passo 2: Configure fonte
Esta secção mostra-lhe os passos para configurar a fonte: Azure Blob contendo os ficheiros de item da linha 1-TB TPC-H.

1. Selecione o **Azure Blob Storage** como a loja de dados e clique em **Seguinte**.

    ![Copy Wizard - Selecione a página de origem](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Preencha as informações de ligação para a conta de armazenamento Azure Blob e clique em **Seguinte**.

    ![Copy Wizard - Informações de ligação de origem](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Escolha a **pasta** que contém os ficheiros de item da linha TPC-H e clique em **Seguinte**.

    ![Copy Wizard - selecione a pasta de entrada](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Ao clicar em **Seguinte,** as definições do formato de ficheiro são detetadas automaticamente.  Verifique se o delimiter da coluna é "|" em vez da vírgula padrão '','.  Clique **em Seguinte** depois de ter pré-visualizado os dados.

    ![Copy Wizard - definições de formato de ficheiro](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Passo 3: Destino configure
Esta secção mostra-lhe como configurar o destino: `lineitem` tabela na base de dados Azure Synapse Analytics.

1. Escolha **a Azure Synapse Analytics** como loja de destino e clique em **Seguinte**.

    ![Copy Wizard - selecione a loja de dados de destino](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Preencha as informações de ligação para a Azure Synapse Analytics.  Certifique-se de que especifica o utilizador que é membro da função `xlargerc` (consulte a secção **pré-requisitos** para instruções detalhadas) e clique em **Seguinte**.

    ![Copy Wizard - informação de conexão de destino](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Escolha a tabela de destino e clique em **Seguinte.**

    ![Copy Wizard - página de mapeamento de mesa](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na página de mapeamento de Schema, deixe a opção "Apply column mapping" desmarcada e clique em **Seguinte**.

## <a name="step-4-performance-settings"></a>Passo 4: Definições de desempenho

**Permitir que a base polibase** seja verificada por defeito.  Clique em **Seguinte**.

![Copy Wizard - página de mapeamento de esquema](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Passo 5: Implementar e monitorizar os resultados da carga
1. Clique no botão **Terminar** para implementar.

    ![Copy Wizard - resumo página 1](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Depois de concluída a implementação, clique `Click here to monitor copy pipeline` para monitorizar o progresso da execução da cópia. Selecione o pipeline de cópias criado na lista **'Windows' de atividade.**

    ![Copy Wizard - resumo página 2](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Pode ver os detalhes da execução da cópia no Explorador de **Janelas** de Atividade no painel direito, incluindo o volume de dados lido a partir da fonte e escrito no destino, duração e a produção média para a execução.

    Como pode ver na imagem seguinte, copiar 1 TB do Azure Blob Storage para a Azure Synapse Analytics demorou 14 minutos, alcançando efetivamente 1,22 GBps de produção!

    ![Copy Wizard - diálogo bem sucedido](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Melhores práticas
Aqui estão algumas boas práticas para executar a sua base de dados Azure Synapse Analytics:

* Utilize uma classe de recursos maior ao carregar num ÍNDICE DE LOJA DE COLUNAS AGRUPADA.
* Para uma junção mais eficiente, considere a utilização da distribuição de haxixe por uma coluna selecionada em vez de distribuição de robin redondo predefinido.
* Para velocidades de carga mais rápidas, considere usar a pilha para dados transitórios.
* Crie estatísticas depois de terminar o carregamento para a Azure Synapse Analytics.

Consulte [as melhores práticas para a Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes
* [Data Factory Copy Wizard](data-factory-copy-wizard.md) - Este artigo fornece detalhes sobre o Assistente de Cópia.
* [Copy Activity performance and afinaning guide](data-factory-copy-activity-performance.md) - Este artigo contém as medidas de desempenho de referência e o guia de afinação.