---
title: Utilize dados de referência da Base de Dados SQL num trabalho de Azure Stream Analytics
description: Este artigo descreve como usar uma Base de Dados SQL como entrada de dados de referência para um trabalho de Azure Stream Analytics no portal Azure e no Visual Studio.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: e7d16de8a7a5c6f5353d64e25580b19845ce96c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016410"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Utilize dados de referência de uma base de dados SQL para um trabalho de Azure Stream Analytics

O Azure Stream Analytics suporta a Base de Dados Azure SQL como fonte de entrada para dados de referência. Pode utilizar a Base de Dados SQL como dados de referência para o seu trabalho stream Analytics no portal Azure e no Visual Studio com ferramentas Stream Analytics. Este artigo demonstra como fazer ambos os métodos.

## <a name="azure-portal"></a>Portal do Azure

Utilize os seguintes passos para adicionar a Base de Dados Azure SQL como fonte de entrada de referência utilizando o portal Azure:

### <a name="portal-prerequisites"></a>Pré-requisitos do portal

1. Criar uma tarefa do Stream Analytics.

2. Crie uma conta de armazenamento para ser usada pelo trabalho Stream Analytics.

3. Crie a sua Base de Dados Azure SQL com um conjunto de dados a utilizar como dados de referência pelo trabalho stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Definir entrada de dados de referência de base de dados SQL

1. No seu trabalho stream Analytics, selecione **Inputs** em **Topology Job**. Clique **em Adicionar entrada de referência** e escolha base de **dados SQL.**

   ![As entradas são selecionadas no painel de navegação esquerdo. Nas Entradas, é selecionada a entrada de referência + Adicionar, revelando uma lista de drop-down que mostra os valores de armazenamento blob e base de dados SQL.](./media/sql-reference-data/stream-analytics-inputs.png)

2. Preencha as Configurações de Entrada de Stream Analytics. Escolha o nome da base de dados, o nome do servidor, o nome de utilizador e a palavra-passe. Se pretender que a entrada de dados de referência se asseque periodicamente, escolha "On" para especificar a taxa de atualização em DD:HH:MM. Se tiver grandes conjuntos de dados com uma taxa de atualização curta, pode utilizar uma [consulta delta](sql-reference-data.md#delta-query).

   ![Quando a Base de Dados SQL é selecionada, aparece a nova página de entrada SQL Database. Há uma forma de configuração no painel esquerdo, e uma consulta snapshot no painel direito.](./media/sql-reference-data/sql-input-config.png)

3. Teste a consulta instantânea no editor de consulta SQL. Para obter mais informações, consulte [o editor de consulta SQL do portal Azure para ligar e consultar dados](../azure-sql/database/connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Especificar a conta de armazenamento em Job config

Navegue para **as definições da conta de armazenamento** em **Configuração** e selecione **Adicionar conta de armazenamento**.

   ![As definições da conta de armazenamento são selecionadas no painel esquerdo. Existe um botão de conta de armazenamento Adicionar no painel direito.](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Iniciar a tarefa

Uma vez configuradas outras entradas, saídas e consultas, pode iniciar o trabalho stream Analytics.

## <a name="tools-for-visual-studio"></a>Ferramentas para o Visual Studio

Utilize os seguintes passos para adicionar a Base de Dados Azure SQL como fonte de entrada de referência utilizando o Visual Studio:

### <a name="visual-studio-prerequisites"></a>Pré-requisitos do Estúdio Visual

1. [Instale as ferramentas Stream Analytics para o Visual Studio](stream-analytics-tools-for-visual-studio-install.md). As seguintes versões do Visual Studio são suportadas:

   * Visual Studio 2015
   * Visual Studio 2019

2. Familiarize-se com as [ferramentas Stream Analytics para](stream-analytics-quick-create-vs.md) o arranque rápido do Visual Studio.

3. Criar uma conta de armazenamento.

### <a name="create-a-sql-database-table"></a>Criar uma tabela de base de dados SQL

Utilize o SQL Server Management Studio para criar uma tabela para armazenar os seus dados de referência. Consulte [a sua primeira Base de Dados Azure SQL utilizando SSMS](../azure-sql/database/design-first-database-tutorial.md) para mais detalhes.

A tabela de exemplos utilizada no exemplo a seguir foi criada a partir da seguinte declaração:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Escolher a sua subscrição

1. No Visual Studio, no menu **Ver**, selecione **Explorador de Servidores**.

2. Clique no **Azure,** **selecione Connect to Microsoft Azure Subscription** e inscreva-se na sua conta Azure.

### <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. Selecione **Ficheiro > Novo Projeto**. 

2. Na lista de modelos, no lado esquerdo, selecione **Stream Analytics** e, em seguida, selecione **Aplicação do Azure Stream Analytics**. 

3. Introduza o **nome** do projeto, **localização** e **nome de solução,** e selecione **OK**.

   ![O modelo stream Analytics é selecionado, Azure Stream Analytics Application é selecionado, e as caixas de nomes, localização e solução são destacadas.](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definir entrada de dados de referência de base de dados SQL

1. Criar uma nova entrada.

   ![No Add New Item, a entrada é selecionada.](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Clique duas **vezesInput.jsno** **Solution Explorer**.

3. Preencha a **Configuração de Entrada de Stream Analytics**. Escolha o nome da base de dados, o nome do servidor, o tipo de atualização e a taxa de atualização. Especificar a taxa de atualização no `DD:HH:MM` formato.

   ![Na Configuração de Entrada de Stream Analytics, os valores são introduzidos ou selecionados a partir de listas de drop-down.](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Se escolher "Executar apenas uma vez" ou "Executar periodicamente", um ficheiro SQL CodeBehind denominado **[Input Alias].snapshot.sql** é gerado no projeto ao abrigo do **Input.jsno** nó de ficheiro.

   ![Destaca-se o ficheiro SQL CodeBehind Chemicals.snapshot.sql.](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Se escolher "Refresh Periodicamente com a Delta", serão gerados dois ficheiros SQL CodeBehind: **[Input Alias].snapshot.sql** e **[Input Alias].delta.sql**.

   ![Destacam-se os ficheiros SQL CodeBehind Chemicals.delta.sql e Chemicals.snapshot.sql.](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Abra o ficheiro SQL no editor e escreva a consulta SQL.

5. Se estiver a utilizar o Visual Studio 2019 e tiver instalado ferramentas de dados do SQL Server, pode testar a consulta clicando em **Executar**. Aparecerá uma janela de assistente para o ajudar a ligar-se à Base de Dados SQL e o resultado da consulta aparecerá na janela na parte inferior.

### <a name="specify-storage-account"></a>Especifique a conta de armazenamento

Abra **JobConfig.js** para especificar a conta de armazenamento para armazenar instantâneos de referência SQL.

   ![A configuração de configuração de configuração de trabalho de streaming analytics é mostrada com valores predefinidos. As Definições Globais de Armazenamento estão em destaque.](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Teste local e implemente para Azure

Antes de implementar o trabalho para Azure, pode testar a lógica de consulta localmente contra dados de entrada ao vivo. Para obter mais informações sobre esta funcionalidade, consulte [teste de dados ao vivo localmente utilizando ferramentas Azure Stream Analytics para Visual Studio (Preview)](stream-analytics-live-data-local-testing.md). Quando terminar os testes, clique em **Enviar por Azure**. Consulte o [Create a Stream Analytics utilizando as ferramentas Azure Stream Analytics para o Arranque](stream-analytics-quick-create-vs.md) rápido do Visual Studio para aprender a iniciar o trabalho.

## <a name="delta-query"></a>Consulta delta

Ao utilizar a consulta delta, recomenda-se a utilização [de tabelas temporais na Base de Dados Azure SQL.](../azure-sql/temporal-tables.md)

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

   Utilize o parâmetro **\@ snapshotTime** para instruir o tempo de funcionamento do Stream Analytics para obter o conjunto de dados de referência da tabela temporal SQL Database válido na hora do sistema. Se não fornecer este parâmetro, corre o risco de obter um conjunto de dados de referência de base impreciso devido a distorções do relógio. Um exemplo de consulta instantânea completa é mostrado abaixo:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Autor da consulta delta. 
   
   Esta consulta recupera todas as linhas na Base de Dados SQL que foram inseridas ou eliminadas dentro de uma hora de início, **\@ deltaStartTime,** e um fim de tempo **\@ deltaEndTime**. A consulta delta deve devolver as mesmas colunas que a consulta de instantâneo, bem como a **_operação_** da coluna . Esta coluna define se a linha é inserida ou eliminada entre **\@ o DeltaStartTime** e **\@ o deltaEndTime**. As linhas resultantes são sinalizadas como **1** se os registos foram inseridos, ou **2** se eliminados. A consulta também deve adicionar **marca de água** do lado do SQL Server para garantir que todas as atualizações no período delta são capturadas adequadamente. A utilização de consultas delta sem **marca de água** pode resultar num conjunto de dados de referência incorreto.  

   Para registos que foram atualizados, a tabela temporal faz contabilidade capturando uma operação de inserção e eliminação. O tempo de funcionamento do Stream Analytics aplicará então os resultados da consulta delta à snapshot anterior para manter os dados de referência atualizados. Um exemplo de consulta delta é mostrado abaixo:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, ValidFrom as _watermark_, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, ValidTo as _watermark_, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Note que o tempo de funcionamento do Stream Analytics pode executar periodicamente a consulta instantânea, além da consulta delta para armazenar pontos de verificação.

## <a name="test-your-query"></a>Testar a consulta
   É importante verificar se a sua consulta está a devolver o conjunto de dados esperado que o trabalho stream Analytics utilizará como dados de referência. Para testar a sua consulta, aceda à entrada na secção Job Topology no portal. Em seguida, pode selecionar dados de amostra na sua entrada de referência de base de dados SQL. Depois de a amostra ficar disponível, pode descarregar o ficheiro e verificar se os dados que estão a ser devolvidos são como esperado. Se quiser otimizar o seu desenvolvimento e testar iterações, recomenda-se a utilização das [ferramentas Stream Analytics para o Visual Studio](./stream-analytics-tools-for-visual-studio-install.md). Também pode ser qualquer outra ferramenta da sua preferência para primeiro garantir que a consulta está a devolver os resultados certos da base de dados Azure SQL e, em seguida, usá-la no seu trabalho stream Analytics. 

### <a name="test-your-query-with-visual-studio-code"></a>Teste a sua consulta com o Código do Estúdio Visual

   Instale [as ferramentas de análise do Azure Stream](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) e [o SQL Server (mssql)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) no Código do Estúdio Visual e crie o seu projeto ASA. Para obter mais informações, consulte [Quickstart: Crie um trabalho Azure Stream Analytics no Código do Estúdio Visual](./quick-create-visual-studio-code.md) e no tutorial de [extensão SQL Server (mssql).](/sql/tools/visual-studio-code/sql-server-develop-use-vscode)

1. Configure a entrada de dados de referência SQL.
   
   ![Um editor de Código de Estúdio Visual (separador) mostra ReferenceSQLDatabase.js.](./media/sql-reference-data/configure-sql-reference-data-input.png)

2. Selecione o ícone SQL Server e clique em **Adicionar Ligação**.
   
   ![+ Add Connection aparece no painel esquerdo e é realçado.](./media/sql-reference-data/add-sql-connection.png)

3. Preencha a informação de ligação.
   
   ![As duas caixas para a base de dados e informações do servidor estão em destaque.](./media/sql-reference-data/fill-connection-information.png)

4. Clique com o botão direito no SQL de referência e selecione **Executar Consulta**.
   
   ![A Consulta de Execução é destacada no menu de contexto.](./media/sql-reference-data/execute-query.png)

5. Escolha a sua ligação.
   
   ![A caixa de diálogo diz "Criar um perfil de ligação a partir da lista abaixo", e a lista tem uma entrada, que é detetado.](./media/sql-reference-data/choose-connection.png)

6. Reveja e verifique o resultado da sua consulta.
   
   ![Os resultados da pesquisa de consulta estão num separador editor do VS Code.](./media/sql-reference-data/verify-result.png)


## <a name="faqs"></a>FAQs

**Incorrerei em custos adicionais utilizando a entrada de dados de referência SQL no Azure Stream Analytics?**

Não há [custos](https://azure.microsoft.com/pricing/details/stream-analytics/) adicionais por unidade de streaming no trabalho stream Analytics. No entanto, o trabalho stream Analytics deve ter uma conta de armazenamento Azure associada. O trabalho stream Analytics consulta o SQL DB (durante o intervalo de início e atualização de trabalho) para recuperar o conjunto de dados de referência e armazena o instantâneo na conta de armazenamento. Armazenar estas imagens incorrerá em encargos adicionais detalhados na [página de preços](https://azure.microsoft.com/pricing/details/storage/) da conta de armazenamento Azure.

**Como é que sei que o instantâneo de dados de referência está a ser consultado a partir do SQL DB e usado no trabalho do Azure Stream Analytics?**

Existem duas métricas filtradas pelo Nome Lógico (no portal Metrics Azure) que pode utilizar para monitorizar a saúde da entrada de dados de referência da Base de Dados SQL.

   * InputEvents: Esta métrica mede o número de registos carregados a partir do conjunto de dados de referência sql Database.
   * InputEventBytes: Esta métrica mede o tamanho do instantâneo de dados de referência carregado em memória do trabalho stream Analytics. 

A combinação de ambas as métricas pode ser usada para inferir se o trabalho está consultando a Base de Dados SQL para pegar o conjunto de dados de referência e, em seguida, carregá-lo para a memória.

**Vou precisar de um tipo especial de Base de Dados Azure SQL?**

O Azure Stream Analytics funcionará com qualquer tipo de Base de Dados Azure SQL. No entanto, é importante entender que o conjunto de taxa de atualização para a entrada de dados de referência pode afetar a sua carga de consulta. Para utilizar a opção de consulta delta, recomenda-se a utilização de tabelas temporais na Base de Dados Azure SQL.

**Porque é que o Azure Stream Analytics armazena fotografias na conta de Armazenamento Azure?**

O Stream Analytics garante exatamente um processamento de eventos e pelo menos uma entrega de eventos. Nos casos em que as questões transitórias afetam o seu trabalho, uma pequena quantidade de repetição é necessária para restaurar o estado. Para ativar a repetição, é necessário ter estas imagens armazenadas numa conta de Armazenamento Azure. Para obter mais informações sobre a repetição do checkpoint, consulte [Checkpoint e reproduzir conceitos em trabalhos Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Passos seguintes

* [Utilização de dados de referência para procuras no Stream Analytics](stream-analytics-use-reference-data.md)
* [Início Rápido: Criar uma tarefa do Stream Analytics com as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
* [Teste dados ao vivo localmente usando ferramentas Azure Stream Analytics para Estúdio Visual (Pré-visualização)](stream-analytics-live-data-local-testing.md)