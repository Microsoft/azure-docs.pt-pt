---
title: Utilizar dados de referência de uma base de dados SQL para uma tarefa do Azure Stream Analytics (pré-visualização)
description: Este artigo descreve como utilizar uma base de dados SQL como entrada de dados de referência para uma tarefa do Azure Stream Analytics no portal do Azure e no Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: efd450edb87316e75fc240cac80eda93151a22b3
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765089"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job-preview"></a>Utilizar dados de referência de uma base de dados SQL para uma tarefa do Azure Stream Analytics (pré-visualização)

O Azure Stream Analytics oferece suporte a SQL Database do Azure como uma origem de entrada para dados de referência. Pode usar a base de dados SQL como dados de referência para a sua tarefa do Stream Analytics no portal do Azure e no Visual Studio com as ferramentas do Stream Analytics. Este artigo demonstra como fazer ambos os métodos.

## <a name="azure-portal"></a>Portal do Azure

Utilize os seguintes passos para adicionar a base de dados SQL do Azure como uma origem de entrada de referência no portal do Azure:

### <a name="portal-prerequisites"></a>Pré-requisitos do portais

1. Crie uma tarefa do Stream Analytics.

2. Crie uma conta de armazenamento para ser utilizado por tarefa do Stream Analytics.

3. Crie a sua base de dados do SQL do Azure com um conjunto de dados a serem utilizados como dados de referência por tarefa do Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Definir a entrada de dados de referência de base de dados SQL

1. Na sua tarefa do Stream Analytics, selecione **entradas** sob **topologia da tarefa**. Clique em **Adicionar referência de entrada** e escolha **base de dados SQL**.

   ![Entrada da tarefa do Stream Analytics](./media/sql-reference-data/stream-analytics-inputs.png)

2. Preencha as configurações de entrada do Stream Analytics. Escolha o nome de base de dados, nome do servidor, nome de utilizador e palavra-passe. Se pretender que os seus dados de referência de entrada para atualizar periodicamente, escolha "Ativado" especificar a taxa de atualização na DD:HH:MM. Se tiver grandes conjuntos de dados com uma taxa de atualização curto, pode utilizar um [consultas delta](sql-reference-data.md#delta-query).

   ![Configuração de referência de base de dados SQL](./media/sql-reference-data/sql-input-config.png)

3. Teste a consulta do instantâneo no editor de consultas SQL. Para obter mais informações, consulte [utilize o editor de consultas SQL do portal do Azure para ligar e consultar dados](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Especifique a conta de armazenamento na configuração de tarefa

Navegue para **definições de conta de armazenamento** sob **configurar** e selecione **adicionar conta de armazenamento**.

   ![Definições de conta de armazenamento do Stream Analytics](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Iniciar a tarefa

Depois de configurar outras entradas, saídas e a consulta, pode iniciar a tarefa do Stream Analytics.

## <a name="tools-for-visual-studio"></a>Ferramentas para o Visual Studio

Utilize os seguintes passos para adicionar a base de dados SQL do Azure como uma origem de entrada de referência usando o Visual Studio:

### <a name="visual-studio-prerequisites"></a>Pré-requisitos do Visual Studio

1. Se estiver a utilizar o Visual Studio 2017, atualize a 15.8.2 ou superior. Tenha em atenção que 16.0 e acima não são suportadas neste momento.

2. [Instalar as ferramentas do Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md). São suportadas as seguintes versões do Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2017

3. Familiarizar-se com o [ferramentas do Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md) início rápido.

4. Criar uma conta de armazenamento.

### <a name="create-a-sql-database-table"></a>Criar uma tabela de base de dados SQL

Utilize o SQL Server Management Studio para criar uma tabela para armazenar os dados de referência. Ver [conceber a sua base de dados SQL do Azure primeiro com o SSMS](../sql-database/sql-database-design-first-database.md) para obter detalhes.

A tabela de exemplo utilizada no exemplo a seguir foi criada a partir da seguinte instrução:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Escolher a sua subscrição

1. No Visual Studio, no menu **Ver**, selecione **Explorador de Servidores**.

2. Clique com o botão direito do rato em **Azure**, selecione **ligar a subscrição do Microsoft Azure**e inicie sessão com a sua conta do Azure.

### <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. Selecione **Ficheiro > Novo Projeto**. 

2. Na lista de modelos, no lado esquerdo, selecione **Stream Analytics** e, em seguida, selecione **Aplicação do Azure Stream Analytics**. 

3. Introduza o projeto **Name**, **localização**, e **nome da solução**e selecione **OK**.

   ![Novo projeto do Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definir a entrada de dados de referência de base de dados SQL

1. Crie uma nova entrada.

   ![Nova entrada do Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Faça duplo clique em **Input** no **Explorador de soluções**.

3. Preencha os **a configuração de entrada do Stream Analytics**. Escolha o nome de base de dados, o nome do servidor, tipo de atualização e frequência de atualização. Especifique a taxa de atualização no formato `DD:HH:MM`.

   ![Configuração de entrada Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Se escolher "Executar apenas uma vez" ou "Executar periodicamente", um arquivo de Code-behind do SQL com o nome **[Alias de entrada].snapshot.sql** é gerado no projeto sob a **Input** nó de ficheiros.

   ![Entrada de code-behind no Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Se optar por "Atualizar periodicamente com Delta", serão gerados dois arquivos Code-behind de SQL: **[Alias de entrada].snapshot.sql** e **[Alias de entrada].delta.sql**.

   ![Por trás de código no Explorador de soluções](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Abra o ficheiro SQL no editor e escrever a consulta SQL.

5. Se estiver a utilizar o Visual Studio 2017 e tiver instalado as ferramentas de dados do SQL Server, pode testar a consulta ao clicar em **Execute**. Uma janela do assistente será exibida para o ajudar a ligar à base de dados SQL e o resultado da consulta irão aparecer na janela na parte inferior.

### <a name="specify-storage-account"></a>Especifique a conta de armazenamento

Open **JobConfig.json** para especificar a conta de armazenamento para o armazenamento de instantâneos de referência do SQL.

   ![Configuração de tarefa do Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testar localmente e implementar no Azure

Antes de implementar a tarefa para o Azure, pode testar a lógica de consulta localmente com dados de entrada em direto. Para obter mais informações sobre esta funcionalidade, consulte [testar localmente, utilizando ferramentas do Azure Stream Analytics para Visual Studio (pré-visualização) de dados dinâmicos](stream-analytics-live-data-local-testing.md). Quando terminar de teste, clique em **submeter para o Azure**. Referência a [criar do Stream Analytics com as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md) início rápido para saber como iniciar a tarefa.

## <a name="delta-query"></a>Consulta delta

Ao utilizar a consulta de delta [tabelas temporais na base de dados do Azure SQL](../sql-database/sql-database-temporal-tables.md) são recomendadas.

1. Crie uma tabela temporal no SQL Database do Azure.
   
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
2. Crie a consulta do instantâneo. 

   Utilize o  **\@snapshotTime** parâmetro para instruir o tempo de execução do Stream Analytics para obter o conjunto de dados de referência do SQL da base de dados a tabela temporal válida na hora do sistema. Se não fornecer este parâmetro, corre o risco de obtenção de um conjunto de dados de referência base incorretos devido a desvios de relógio. Um exemplo de consulta do instantâneo completo é mostrado abaixo:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Crie a consulta de delta. 
   
   Esta consulta obtém todas as linhas na base de dados do SQL que foram inseridas ou eliminadas dentro de uma hora de início  **\@deltaStartTime**e uma hora de fim  **\@deltaEndTime**. A consulta de delta tem de devolver as mesmas colunas como a consulta do instantâneo, bem como a coluna  **_operação_**. Esta coluna define se a linha é inserida ou eliminada entre  **\@deltaStartTime** e  **\@deltaEndTime**. As linhas resultantes são sinalizadas como **1** se os registos foram inseridos, ou **2** se eliminada. 

   Para os registos que foram atualizados, a tabela temporal faz contábeis capturando uma operação de inserção e eliminação. O tempo de execução do Stream Analytics, em seguida, aplicará os resultados da consulta delta para o instantâneo anterior para manter os dados de referência atualizadas. Um exemplo de consulta delta é mostrada abaixo:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
  Tenha em atenção que o tempo de execução do Stream Analytics pode executar periodicamente a consulta do instantâneo para além da consulta delta para armazenar os pontos de verificação.

## <a name="faqs"></a>FAQ

**Irá implicar custos adicionais ao utilizar a entrada de dados de referência do SQL no Azure Stream Analytics?**

Existem não adicionais [custo por unidade de transmissão em fluxo](https://azure.microsoft.com/pricing/details/stream-analytics/) da tarefa do Stream Analytics. No entanto, a tarefa do Stream Analytics tem de ter uma conta de armazenamento do Azure associado. A tarefa do Stream Analytics consulta o banco de dados do SQL (durante a tarefa de início e o intervalo de atualização) para obter o conjunto de dados de referência e arquivos de que a conta de armazenamento de instantâneos. Armazenar estes instantâneos incorrerá em encargos adicionais detalhados na [página de preços](https://azure.microsoft.com/pricing/details/storage/) para a conta de armazenamento do Azure.

**Como posso saber instantâneo de dados de referência está a ser consultadas a partir de BD SQL e utilizados na tarefa do Azure Stream Analytics?**

Existem duas métricas filtradas por nome lógico (no Portal do Azure de métricas) que pode ser usado para monitorar a integridade dos dados de referência de base de dados SQL de entrada.

   * InputEvents: Esta métrica mede o número de registos carregados no, a partir do conjunto de dados de referência de base de dados SQL.
   * InputEventBytes: Esta métrica mede o tamanho do instantâneo de dados de referência carregado na memória da tarefa do Stream Analytics. 

A combinação de ambas estas métricas pode ser utilizada para inferir se a tarefa é consultar a base de dados SQL para obter o conjunto de dados de referência e, em seguida, carregá-lo para a memória.

**Irá exigir um tipo especial de base de dados do Azure SQL?**

O Azure Stream Analytics irá funcionar com qualquer tipo de base de dados do Azure SQL. No entanto, é importante compreender que a taxa de atualização definidas para a sua entrada de dados de referência pode afetar a carga de consulta. Para utilizar a opção de consulta delta, é recomendado utilizar tabelas temporais na base de dados do Azure SQL.

**Pode, entrada de dados de referência de base de dados SQL de entrada de exemplo?**

Esta funcionalidade não está disponível.

**Por que do Azure Stream Analytics armazenar instantâneos na conta de armazenamento do Azure?**

O Stream Analytics garante exatamente um processamento de eventos e pelo menos uma entrega de eventos. Em casos onde transitório emite impacto seu trabalho, uma pequena quantidade de repetição é necessária restaurar o estado. Para ativar a repetição, é necessário ter estes instantâneos armazenados numa conta de armazenamento do Azure. Para obter mais informações sobre a repetição de ponto de verificação, consulte [conceitos de ponto de verificação e repetição em tarefas do Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Passos Seguintes

* [Utilização de dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md)
* [Quickstart: Criar uma tarefa de Stream Analytics, utilizando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
* [Dados em direto de teste localmente, utilizando ferramentas do Azure Stream Analytics para Visual Studio (pré-visualização)](stream-analytics-live-data-local-testing.md)
