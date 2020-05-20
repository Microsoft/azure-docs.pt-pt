---
title: Utilize dados de referência da Base de Dados SQL num trabalho de Análise de Fluxo soro
description: Este artigo descreve como usar uma Base de Dados SQL como entrada de dados de referência para um trabalho de Azure Stream Analytics no portal Azure e no Estúdio Visual.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 9f780ad3d2c95f9d23ea9a0b675b59ba22e25016
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651945"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Utilize dados de referência de uma base de dados SQL para um trabalho de Azure Stream Analytics

O Azure Stream Analytics suporta a Base de Dados Azure SQL como fonte de entrada para dados de referência. Pode utilizar a Base de Dados SQL como dados de referência para o seu trabalho de Stream Analytics no portal Azure e no Estúdio Visual com ferramentas Stream Analytics. Este artigo demonstra como fazer ambos os métodos.

## <a name="azure-portal"></a>Portal do Azure

Utilize os seguintes passos para adicionar a Base de Dados Azure SQL como fonte de entrada de referência utilizando o portal Azure:

### <a name="portal-prerequisites"></a>Pré-requisitos do portal

1. Criar uma tarefa do Stream Analytics.

2. Crie uma conta de armazenamento para ser usada pelo trabalho de Stream Analytics.

3. Crie a sua Base de Dados Azure SQL com um conjunto de dados a ser utilizado como dados de referência pelo trabalho do Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Definir entrada de dados de referência da Base de Dados SQL

1. No seu trabalho de Stream Analytics, selecione **Inputs** em **topologia de trabalho**. Clique em **Adicionar entrada de referência** e escolha base de dados **SQL**.

   ![Entrada de trabalho stream Analytics](./media/sql-reference-data/stream-analytics-inputs.png)

2. Preencha as configurações de entrada de análise de fluxo. Escolha o nome da base de dados, nome do servidor, nome de utilizador e palavra-passe. Se pretender que a sua entrada de dados de referência atualização periodicamente, escolha "On" para especificar a taxa de atualização em DD:HH:MM. Se tiver grandes conjuntos de dados com uma taxa de atualização curta, pode utilizar uma [consulta delta](sql-reference-data.md#delta-query).

   ![Configuração de referência de base de dados SQL](./media/sql-reference-data/sql-input-config.png)

3. Teste a consulta instantânea no editor de consulta SQL. Para mais informações, consulte [Use o editor de consulta SQL do portal Azure para conectar e consultar dados](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Especificar a conta de armazenamento no Job config

Navegue para **as definições da conta de armazenamento** em **Configurar** e selecione **Adicionar a conta de armazenamento**.

   ![Definições da conta de armazenamento stream Analytics](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Iniciar a tarefa

Depois de configurar outras inputs, saídas e consultas, pode iniciar o trabalho de Stream Analytics.

## <a name="tools-for-visual-studio"></a>Ferramentas para o Visual Studio

Utilize os seguintes passos para adicionar a Base de Dados Azure SQL como fonte de entrada de referência utilizando o Visual Studio:

### <a name="visual-studio-prerequisites"></a>Pré-requisitos do Estúdio Visual

1. [Instale as ferramentas Stream Analytics para Estúdio Visual](stream-analytics-tools-for-visual-studio-install.md). As seguintes versões do Estúdio Visual são suportadas:

   * Visual Studio 2015
   * Visual Studio 2019

2. Familiarize-se com as [ferramentas Stream Analytics para o Estúdio Visual](stream-analytics-quick-create-vs.md) quickstart.

3. Criar uma conta de armazenamento.

### <a name="create-a-sql-database-table"></a>Criar uma tabela de bases de dados SQL

Utilize o Estúdio de Gestão de Servidores SQL para criar uma tabela para armazenar os seus dados de referência. Consulte [o Design da sua primeira base de dados Azure SQL utilizando SSMS](../sql-database/sql-database-design-first-database.md) para mais detalhes.

A tabela de exemplos utilizada no seguinte exemplo foi criada a partir da seguinte declaração:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Escolher a sua subscrição

1. No Visual Studio, no menu **Ver**, selecione **Explorador de Servidores**.

2. Clique no **Azure,** selecione **Connect to Microsoft Azure Subscription**, e inscreva-se na sua conta Azure.

### <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. Selecione **Ficheiro > Novo Projeto**. 

2. Na lista de modelos, no lado esquerdo, selecione **Stream Analytics** e, em seguida, selecione **Aplicação do Azure Stream Analytics**. 

3. Introduza o **nome**do projeto, **localização**e **nome solução,** e selecione **OK**.

   ![Novo projeto Stream Analytics no Estúdio Visual](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definir entrada de dados de referência da Base de Dados SQL

1. Crie uma nova entrada.

   ![Nova entrada de Stream Analytics no Estúdio Visual](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Clique duas vezes **em Entrada.json** no Explorador de **Soluções**.

3. Preencha a configuração de entrada de **análise de fluxo.** Escolha o nome da base de dados, nome do servidor, tipo de atualização e taxa de atualização. Especifique a taxa de atualização no formato `DD:HH:MM` .

   ![Configuração de entrada stream Analytics no Estúdio Visual](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Se escolher "Executar apenas uma vez" ou "Executar periodicamente", um ficheiro SQL CodeBehind chamado **[Input Alias].snapshot.sql** é gerado no projeto sob o nó de **ficheiroS Input.json.**

   ![Código de entrada para trás no Estúdio Visual](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Se escolher "Refresh Periodly with Delta", serão gerados dois ficheiros SQL CodeBehind: **[Input Alias].snapshot.sql** e **[Input Alias].delta.sql**.

   ![Código por trás no explorador de soluções](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Abra o ficheiro SQL no editor e escreva a consulta SQL.

5. Se estiver a utilizar o Visual Studio 2019 e tiver instalado ferramentas de Dados do Servidor SQL, pode testar a consulta clicando em **Executar**. Uma janela do assistente aparecerá para ajudá-lo a ligar-se à base de dados SQL e o resultado da consulta aparecerá na janela na parte inferior.

### <a name="specify-storage-account"></a>Especificar conta de armazenamento

Abra **jobConfig.json** para especificar a conta de armazenamento para armazenar instantâneos de referência SQL.

   ![Configuração de trabalho de Stream Analytics em Estúdio Visual](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Teste localmente e desloque-se para Azure

Antes de implementar o trabalho para o Azure, pode testar a lógica de consulta localmente contra dados de entrada ao vivo. Para obter mais informações sobre esta funcionalidade, consulte os dados ao vivo do [Teste localmente utilizando ferramentas Azure Stream Analytics para Estúdio Visual (Pré-visualização)](stream-analytics-live-data-local-testing.md). Quando terminar os testes, clique em **Submeter para Azure**. Refira-se à [Create a Stream Analytics utilizando as ferramentas Azure Stream Analytics para o Estúdio Visual](stream-analytics-quick-create-vs.md) rapidamente começar a aprender a iniciar o trabalho.

## <a name="delta-query"></a>Consulta delta

Ao utilizar a consulta delta, recomenda-se a utilização da consulta delta, recomenda-se a utilização da base de [dados Azure SQL.](../sql-database/sql-database-temporal-tables.md)

1. Crie uma tabela temporal na Base de Dados Azure SQL.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Autor da consulta instantânea. 

   Utilize o parâmetro ** \@ snapshotTime** para instruir o tempo de execução do Stream Analytics para obter o conjunto de dados de referência da tabela temporal da base de dados SQL válido no momento do sistema. Se não fornecer este parâmetro, arrisca-se a obter um conjunto de dados de referência de base impreciso devido a distorções do relógio. Um exemplo de consulta instantânea completa é mostrado abaixo:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Autor da consulta delta. 
   
   Esta consulta recupera todas as linhas da sua base de dados SQL que foram inseridas ou eliminadas dentro de um tempo de início, ** \@ deltaStartTime**, e um tempo final ** \@ deltaEndTime**. A consulta delta deve devolver as mesmas colunas que a consulta instantânea, bem como a **_operação_** da coluna . Esta coluna define se a linha é inserida ou eliminada entre ** \@ deltaStartTime** e ** \@ deltaEndTime**. As linhas resultantes são sinalizadas como **1** se os registos foram inseridos, ou **2** se eliminados. A consulta também deve adicionar marca de **água** do lado do SQL Server para garantir que todas as atualizações no período delta são capturadas adequadamente. A utilização de uma consulta delta sem **marca de água** pode resultar num conjunto de dados de referência incorreto.  

   Para registos que foram atualizados, a tabela temporal faz contabilidade capturando uma operação de inserção e eliminação. O tempo de execução do Stream Analytics aplicará então os resultados da consulta delta ao instantâneo anterior para manter os dados de referência atualizados. Um exemplo de consulta delta é mostrado abaixo:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, ValidFrom as _watermark_, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, ValidTo as _watermark_, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Note que o tempo de execução do Stream Analytics pode periodicamente executar a consulta instantânea para além da consulta delta para armazenar pontos de verificação.

## <a name="test-your-query"></a>Testar a consulta
   É importante verificar se a sua consulta está a devolver o conjunto de dados esperado que o trabalho do Stream Analytics utilizará como dados de referência. Para testar a sua consulta, vá ao Input sob a secção de Topologia de Emprego no portal. Em seguida, pode selecionar dados de amostra na sua entrada de Referência de Base de Dados SQL. Depois da amostra ficar disponível, pode descarregar o ficheiro e verificar se os dados que estão a ser devolvidos são como esperado. Se quiser otimizar as suas iterações de desenvolvimento e teste, é aconselhável utilizar as [ferramentas Stream Analytics para O Estúdio Visual](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install). Também pode qualquer outra ferramenta da sua preferência para primeiro garantir que a consulta está devolvendo os resultados certos da sua Base de Dados Azure SQL e, em seguida, usá-lo no seu trabalho de Stream Analytics. 

## <a name="faqs"></a>FAQs

**Incorrerei em custos adicionais utilizando a entrada de dados de referência SQL no Azure Stream Analytics?**

Não há [custos](https://azure.microsoft.com/pricing/details/stream-analytics/) adicionais por unidade de streaming no trabalho de Stream Analytics. No entanto, o trabalho da Stream Analytics deve ter uma conta de armazenamento Azure associada. O trabalho stream Analytics consulta o SQL DB (durante o intervalo de arranque e atualização) para recuperar o conjunto de dados de referência e armazena esse instantâneo na conta de armazenamento. Armazenar estas imagens incorrerá em encargos adicionais detalhados na [página de preços](https://azure.microsoft.com/pricing/details/storage/) da conta de armazenamento Do Azure.

**Como sei que o snapshot de dados de referência está a ser consultado a partir do SQL DB e usado no trabalho do Azure Stream Analytics?**

Existem duas métricas filtradas pelo Nome Lógico (no Portal Metrics Azure) que pode utilizar para monitorizar a saúde da entrada de dados de referência da base de dados SQL.

   * InputEvents: Esta métrica mede o número de registos carregados no conjunto de dados de referência da base de dados SQL.
   * InputEventBytes: Esta métrica mede o tamanho do instantâneo de dados de referência carregado em memória do trabalho do Stream Analytics. 

A combinação de ambas as métricas pode ser usada para inferir se o trabalho estiver consultando a base de dados SQL para obter o conjunto de dados de referência e, em seguida, carregá-lo para a memória.

**Vou precisar de um tipo especial de Base de Dados Azure SQL?**

O Azure Stream Analytics trabalhará com qualquer tipo de Base de Dados Azure SQL. No entanto, é importante entender que a taxa de atualização definida para a sua entrada de dados de referência pode afetar a sua carga de consulta. Para utilizar a opção de consulta delta, recomenda-se a utilização de tabelas temporais na Base de Dados Azure SQL.

**Porque é que o Azure Stream Analytics armazena fotografias na conta de Armazenamento Azure?**

O Stream Analytics garante exatamente um processamento de eventos e pelo menos uma entrega de eventos. Nos casos em que problemas transitórios afetam o seu trabalho, uma pequena quantidade de repetição é necessária para restaurar o estado. Para permitir a repetição, é necessário que estas fotos sejam armazenadas numa conta de Armazenamento Azure. Para obter mais informações sobre a repetição do checkpoint, consulte [o Checkpoint e repita os conceitos em trabalhos de Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Passos seguintes

* [Utilização de dados de referência para procuras no Stream Analytics](stream-analytics-use-reference-data.md)
* [Início Rápido: Criar uma tarefa do Stream Analytics com as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
* [Teste dados ao vivo localmente utilizando ferramentas Azure Stream Analytics para Estúdio Visual (Pré-visualização)](stream-analytics-live-data-local-testing.md)
