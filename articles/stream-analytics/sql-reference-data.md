---
title: Usar dados de referência de banco de dado SQL em um trabalho Azure Stream Analytics
description: Este artigo descreve como usar um banco de dados SQL como entrada de dado de referência para um trabalho de Azure Stream Analytics no portal do Azure e no Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 733ac7d1ff1d50e5fdcfa0dec2ad3fd3f30f6d86
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72926681"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Usar dados de referência de um banco de dado SQL para um trabalho Azure Stream Analytics

O Azure Stream Analytics dá suporte ao banco de dados SQL do Azure como uma fonte de entrada para o dado de referência. Você pode usar o banco de dados SQL como dado de referência para seu trabalho de Stream Analytics no portal do Azure e no Visual Studio com ferramentas de Stream Analytics. Este artigo demonstra como executar ambos os métodos.

## <a name="azure-portal"></a>Portal do Azure

Use as etapas a seguir para adicionar o banco de dados SQL do Azure como uma fonte de entrada de referência usando o portal do Azure:

### <a name="portal-prerequisites"></a>Pré-requisitos do portal

1. Criar um trabalho de Stream Analytics.

2. Crie uma conta de armazenamento a ser usada pelo trabalho de Stream Analytics.

3. Crie seu banco de dados SQL do Azure com um conjunto de dados a ser usado como dados de referência pelo trabalho de Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Definir entrada de dados de referência de banco de dado SQL

1. Em seu trabalho de Stream Analytics, selecione **entradas** em **topologia do trabalho**. Clique em **Adicionar entrada de referência** e escolha **banco de dados SQL**.

   ![Stream Analytics entrada do trabalho](./media/sql-reference-data/stream-analytics-inputs.png)

2. Preencha as configurações de entrada do Stream Analytics. Escolha o nome do banco de dados, nome do servidor, nome de usuário e senha. Se você quiser que a entrada de dados de referência seja atualizada periodicamente, escolha "ativado" para especificar a taxa de atualização em DD: HH: MM. Se você tiver grandes conjuntos de dados com uma taxa de atualização curta, poderá usar uma [consulta Delta](sql-reference-data.md#delta-query).

   ![Configuração de referência do banco de dados SQL](./media/sql-reference-data/sql-input-config.png)

3. Teste a consulta de instantâneo no editor de consultas SQL. Para obter mais informações, consulte [usar o editor de consultas SQL do portal do Azure para se conectar e consultar dados](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Especificar conta de armazenamento na configuração do trabalho

Navegue até **configurações da conta de armazenamento** em **Configurar** e selecione **adicionar conta de armazenamento**.

   ![Stream Analytics configurações de conta de armazenamento](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Iniciar a tarefa

Depois de configurar outras entradas, saídas e consultas, você pode iniciar o trabalho de Stream Analytics.

## <a name="tools-for-visual-studio"></a>Ferramentas para o Visual Studio

Use as etapas a seguir para adicionar o banco de dados SQL do Azure como uma fonte de entrada de referência usando o Visual Studio:

### <a name="visual-studio-prerequisites"></a>Pré-requisitos do Visual Studio

1. [Instale o Stream Analytics Tools para Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Há suporte para as seguintes versões do Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2019

2. Familiarize-se com as ferramentas de Stream Analytics para o início rápido [do Visual Studio](stream-analytics-quick-create-vs.md) .

3. Criar uma conta de armazenamento.

### <a name="create-a-sql-database-table"></a>Criar uma tabela de banco de dados SQL

Use SQL Server Management Studio para criar uma tabela para armazenar seus dados de referência. Consulte [criar seu primeiro banco de dados SQL do Azure usando o SSMS](../sql-database/sql-database-design-first-database.md) para obter detalhes.

A tabela de exemplo usada no exemplo a seguir foi criada a partir da seguinte instrução:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Escolher a sua subscrição

1. No Visual Studio, no menu **Ver**, selecione **Explorador de Servidores**.

2. Clique com o botão direito do mouse em **Azure**, selecione **conectar-se a Microsoft Azure assinatura**e entre com sua conta do Azure.

### <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. Selecione **Ficheiro > Novo Projeto**. 

2. Na lista de modelos, no lado esquerdo, selecione **Stream Analytics** e, em seguida, selecione **Aplicação do Azure Stream Analytics**. 

3. Insira o **nome**do projeto, o **local**e o **nome da solução**e selecione **OK**.

   ![Novo projeto Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definir entrada de dados de referência de banco de dado SQL

1. Crie uma nova entrada.

   ![Nova entrada de Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Clique duas vezes em **Input. JSON** na **Gerenciador de soluções**.

3. Preencha a **configuração de entrada de Stream Analytics**. Escolha o nome do banco de dados, o nome do servidor, o tipo de atualização e a taxa de atualização. Especifique a taxa de atualização no formato `DD:HH:MM`.

   ![Stream Analytics a configuração de entrada no Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Se você escolher "executar apenas uma vez" ou "executar periodicamente", um arquivo code-behind SQL chamado **[alias de entrada]. snapshot. SQL** é gerado no projeto no nó de arquivo **Input. JSON** .

   ![Código de entrada por trás do Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Se você escolher "atualizar periodicamente com Delta", dois arquivos code-behind SQL serão gerados: **[alias de entrada]. snapshot. SQL** e **[alias de entrada]. Delta. SQL**.

   ![Code-behind no Gerenciador de soluções](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Abra o arquivo SQL no editor e grave a consulta SQL.

5. Se você estiver usando o Visual Studio 2019 e tiver instalado o SQL Server Data Tools, poderá testar a consulta clicando em **executar**. Uma janela do assistente será exibida para ajudá-lo a se conectar ao banco de dados SQL e o resultado da consulta será exibido na janela na parte inferior.

### <a name="specify-storage-account"></a>Especificar conta de armazenamento

Abra **JobConfig. JSON** para especificar a conta de armazenamento para armazenar instantâneos de referência SQL.

   ![Stream Analytics a configuração de trabalho no Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testar localmente e implantar no Azure

Antes de implantar o trabalho no Azure, você pode testar a lógica de consulta localmente em relação aos dados de entrada ao vivo. Para obter mais informações sobre esse recurso, consulte [testar dados dinâmicos localmente usando o Azure Stream Analytics Tools para Visual Studio (versão prévia)](stream-analytics-live-data-local-testing.md). Quando você terminar de testar, clique em **Enviar para o Azure**. Faça referência ao guia de início rápido [criar um Stream Analytics usando as ferramentas de Azure Stream Analytics para o Visual Studio](stream-analytics-quick-create-vs.md) para saber como iniciar o trabalho.

## <a name="delta-query"></a>Consulta Delta

Ao usar a consulta Delta, as [tabelas temporais no banco de dados SQL do Azure](../sql-database/sql-database-temporal-tables.md) são recomendadas.

1. Crie uma tabela temporal no banco de dados SQL do Azure.
   
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
2. Crie a consulta de instantâneo. 

   Use o **\@parâmetro SnapshotTime** para instruir o tempo de execução do Stream Analytics a obter o conjunto de dados de referência da tabela temporal do banco do dados SQL válida no momento do sistema. Se você não fornecer esse parâmetro, correrá o risco de obter um conjunto de dados de referência de base impreciso devido a distorções de relógio. Um exemplo de consulta de instantâneo completo é mostrado abaixo:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Crie a consulta Delta. 
   
   Essa consulta recupera todas as linhas no banco de dados SQL que foram inseridas ou excluídas em uma hora de início, **\@deltaStartTime**e uma hora de término **\@deltaEndTime**. A consulta Delta deve retornar as mesmas colunas que a consulta de instantâneo, bem como a **_operação_** de coluna. Esta coluna define se a linha é inserida ou excluída entre **\@deltaStartTime** e **\@deltaEndTime**. As linhas resultantes serão sinalizadas como **1** se os registros foram inseridos ou **2** se forem excluídos. 

   Para os registros que foram atualizados, a tabela temporal faz a escrituração do acordo com a captura de uma operação de inserção e exclusão. O tempo de execução de Stream Analytics aplicará os resultados da consulta Delta ao instantâneo anterior para manter os dados de referência atualizados. Um exemplo de consulta Delta é mostrado abaixo:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Observe que Stream Analytics tempo de execução pode executar periodicamente a consulta de instantâneo, além da consulta Delta, para armazenar pontos de verificação.

## <a name="test-your-query"></a>Testar a consulta
   É importante verificar se a consulta está retornando o conjunto de dados esperado que o trabalho de Stream Analytics será usado como dado de referência. Para testar sua consulta, vá para entrada na seção topologia de trabalho no Portal. Em seguida, você pode selecionar dados de exemplo em sua entrada de referência de banco do dados SQL. Depois que o exemplo for disponibilizado, você poderá baixar o arquivo e verificar se os dados que estão sendo retornados são os esperados. Se você quiser otimizar seu desenvolvimento e iterações de teste, é recomendável usar as [ferramentas de Stream Analytics para o Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install). Você também pode qualquer outra ferramenta de sua preferência para garantir que a consulta esteja retornando os resultados certos do banco de dados SQL do Azure e, em seguida, usá-la em seu trabalho de Stream Analytics. 

## <a name="faqs"></a>FAQ

**Eu incorreria em custo adicional usando a entrada de dados de referência SQL no Azure Stream Analytics?**

Não há nenhum [custo adicional por unidade de streaming](https://azure.microsoft.com/pricing/details/stream-analytics/) no trabalho de Stream Analytics. No entanto, o trabalho de Stream Analytics deve ter uma conta de armazenamento do Azure associada. O trabalho de Stream Analytics consulta o BD SQL (durante o início do trabalho e o intervalo de atualização) para recuperar o conjunto de dados de referência e armazena esse instantâneo na conta de armazenamento. O armazenamento desses instantâneos incorrerá em encargos adicionais detalhados na [página de preços](https://azure.microsoft.com/pricing/details/storage/) da conta de armazenamento do Azure.

**Como fazer o instantâneo de dados de referência sabe que está sendo consultado do BD SQL e usado no trabalho de Azure Stream Analytics?**

Há duas métricas filtradas pelo nome lógico (no portal do Azure de métricas) que você pode usar para monitorar a integridade da entrada de dados de referência do banco do dados SQL.

   * InputEvents: essa métrica mede o número de registros carregados no do conjunto de dados de referência do banco de dado SQL.
   * InputEventBytes: essa métrica mede o tamanho do instantâneo de dados de referência carregado na memória do trabalho de Stream Analytics. 

A combinação dessas duas métricas pode ser usada para inferir se o trabalho está consultando o banco de dados SQL para buscar o conjunto de dado de referência e, em seguida, carregá-lo na memória.

**Eu precisarei de um tipo especial de banco de dados SQL do Azure?**

Azure Stream Analytics funcionará com qualquer tipo de banco de dados SQL do Azure. No entanto, é importante entender que a taxa de atualização definida para sua entrada de dados de referência pode afetar o carregamento da consulta. Para usar a opção de consulta Delta, é recomendável usar tabelas temporais no banco de dados SQL do Azure.

**Por que Azure Stream Analytics armazenar instantâneos na conta de armazenamento do Azure?**

O Stream Analytics garante exatamente um processamento de eventos e pelo menos uma entrega de eventos. Nos casos em que problemas transitórios impactam seu trabalho, uma pequena quantidade de reprodução é necessária para restaurar o estado. Para habilitar a reprodução, é necessário que esses instantâneos sejam armazenados em uma conta de armazenamento do Azure. Para obter mais informações sobre reprodução de ponto de verificação, consulte [conceitos de ponto de verificação e reprodução em trabalhos de Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Passos seguintes

* [Usando dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md)
* [Início rápido: criar um trabalho de Stream Analytics usando o Azure Stream Analytics Tools para Visual Studio](stream-analytics-quick-create-vs.md)
* [Testar dados dinâmicos localmente usando o Azure Stream Analytics Tools para Visual Studio (visualização)](stream-analytics-live-data-local-testing.md)
