---
title: Compreender as saídas do Azure Stream Analytics
description: Este artigo descreve as opções de saída de dados disponíveis no Azure Stream Analytics, incluindo o Power BI para resultados de análise.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/8/2020
ms.openlocfilehash: a7b2e2b14c6358d0dd9a6f8934e88c0fa89c8bd8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85833813"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Compreender as saídas do Azure Stream Analytics

Este artigo descreve os tipos de saídas disponíveis para um trabalho do Azure Stream Analytics. As saídas permitem armazenar e guardar os resultados do trabalho stream Analytics. Ao utilizar os dados de saída, pode fazer mais análises de negócios e armazenamento de dados dos seus dados.

Quando conceber a sua consulta Stream Analytics, consulte o nome da saída utilizando a [cláusula INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Pode utilizar uma única saída por trabalho, ou múltiplas saídas por trabalho de streaming (se precisar) fornecendo várias cláusulas INTO na consulta.

Para criar, editar e testar as saídas de trabalho stream Analytics, pode utilizar o [portal Azure](stream-analytics-quick-create-portal.md#configure-job-output) [, Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet) [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)e Visual [Studio](stream-analytics-quick-create-vs.md).

Alguns tipos de saídas [suportam a partição.](#partitioning) [Os tamanhos dos lotes de saída](#output-batch-size) variam para otimizar a produção.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics suporta [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage é um repositório de hiperescala em toda a empresa para grandes cargas analíticas de dados. Pode utilizar o Armazenamento do Lago de Dados para armazenar dados de qualquer tamanho, tipo e velocidade de ingestão para análises operacionais e exploratórias. Stream Analytics precisa ser autorizado a aceder ao armazenamento do Data Lake.

A produção de armazenamento de lagos de dados Azure da Stream Analytics não está atualmente disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International).

A tabela que se segue lista os nomes das propriedades e as suas descrições para configurar a sua saída do Data Lake Storage Gen 1.   

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável usado em consultas para direcionar a saída de consulta para data lake store. |
| Subscrição | A subscrição que contém a sua conta de Armazenamento de Data Lake Azure. |
| Nome da conta | O nome da conta Data Lake Store para onde está a enviar a sua produção. É-lhe apresentada uma lista pendente de contas da Data Lake Store que estão disponíveis na sua subscrição. |
| Padrão de prefixo do caminho | O caminho do ficheiro que é usado para escrever os seus ficheiros dentro da conta especificada data lake store. Pode especificar uma ou mais instâncias das variáveis {date} e {time}:<br /><ul><li>Exemplo 1: pasta1/logs/{date}/{time}</li><li>Exemplo 2: pasta1/logs/{date}</li></ul><br />O carimbo de tempo da estrutura da pasta criada segue a UTC e não a hora local.<br /><br />Se o padrão do caminho do ficheiro não contiver um corte de fuga (/), o último padrão no caminho do ficheiro é tratado como um prefixo de nome de ficheiro. <br /><br />Nestas circunstâncias são criados novos ficheiros:<ul><li>Alteração do esquema de saída</li><li>Reinício externo ou interno de um trabalho</li></ul> |
| Formato de data | Opcional. Se o token de data for utilizado no caminho do prefixo, pode selecionar o formato de data em que os seus ficheiros estão organizados. Exemplo: YYYY/MM/DD |
|Formato de tempo | Opcional. Se o token de tempo for utilizado no caminho do prefixo, especifique o formato de tempo em que os seus ficheiros estão organizados. Atualmente, o único valor suportado é o HH. |
| Formato de serialização de eventos | O formato de serialização para dados de saída. JSON, CSV e Avro são apoiados.|
| Encoding | Se estiver a utilizar o formato CSV ou JSON, deve ser especificado um codificamento. UTF-8 é o único formato de codificação suportado neste momento.|
| Delimitador | Aplicável apenas para serialização de CSV. Stream Analytics suporta uma série de delimiters comuns para serializar dados de CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical.|
| Formatar | Aplicável apenas para serialização JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido.  **A matriz** especifica que a saída é formatada como uma matriz de objetos JSON. Esta matriz só é fechada quando o trabalho parar ou stream Analytics passou para a janela da próxima vez. Em geral, é preferível usar JSON separados de linha, porque não requer qualquer manuseamento especial enquanto o ficheiro de saída ainda está a ser escrito.|
| Modo de autenticação | Pode autorizar o acesso à sua conta de Armazenamento de Data Lake utilizando [identidade gerida](stream-analytics-managed-identities-adls.md) ou ficha de utilizador. Uma vez que você concede acesso, você pode revogar o acesso alterando a palavra-passe da conta de utilizador, eliminando a saída de Armazenamento de Data Lake para este trabalho, ou eliminando o trabalho stream Analytics. |

## <a name="sql-database"></a>SQL Database

Pode utilizar [a Base de Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) como uma saída para dados de natureza relacional ou para aplicações que dependem do conteúdo alojado numa base de dados relacional. Os trabalhos de Stream Analytics escrevem para uma tabela existente na Base de Dados SQL. O esquema de mesa deve corresponder exatamente aos campos e aos seus tipos na produção do seu trabalho. Também pode especificar [o Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) como uma saída através da opção de saída SQL Database. Para aprender sobre formas de melhorar a produção de escrita, consulte o [Stream Analytics com Azure SQL Database como](stream-analytics-sql-output-perf.md) artigo de saída.

Também pode utilizar [a Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) como saída. Tem de [configurar o ponto final público em SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) e, em seguida, configurar manualmente as seguintes definições no Azure Stream Analytics. A máquina virtual Azure que executa o SQL Server com uma base de dados anexada também é suportada configurando manualmente as definições abaixo.

A tabela que se segue lista os nomes dos imóveis e a sua descrição para a criação de uma saída sql Database.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para esta base de dados. |
| Base de Dados | O nome da base de dados para onde está a enviar a sua produção. |
| Nome do servidor | O nome lógico do servidor SQL ou nome de instância gerido. Para a SQL Managed Instance, é necessário especificar a porta 3342. Por exemplo, *sampleserver.public.database.windows.net,3342* |
| Nome de utilizador | O nome de utilizador que tem acesso à base de dados. Stream Analytics suporta apenas a autenticação SQL. |
| Palavra-passe | A senha para ligar à base de dados. |
| Tabela | O nome da mesa onde a saída está escrita. O nome da mesa é sensível a maiôs. O esquema desta tabela deve corresponder exatamente ao número de campos e aos seus tipos que a sua saída de trabalho gera. |
|Herdar o esquema de partição| Uma opção para herdar o esquema de partição do seu passo de consulta anterior, para permitir topologia totalmente paralela com vários escritores à mesa. Para obter mais informações, consulte [a saída Azure Stream Analytics para a Base de Dados Azure SQL](stream-analytics-sql-output-perf.md).|
|Contagem de lotes max| O limite superior recomendado para o número de registos enviados com cada transação de inserção a granel.|

Existem dois adaptadores que permitem a saída do Azure Stream Analytics para a Azure Synapse Analytics (anteriormente SQL Data Warehouse): SQL Database e Azure Synapse. Recomendamos que escolha o adaptador Azure Synapse Analytics em vez do adaptador SQL Database se alguma das seguintes condições se mantiver:

* **Produção**: Se a sua produção esperada agora ou no futuro for superior a 10MB/seg, utilize a opção de saída Azure Synapse para um melhor desempenho.

* **Entradas Partições**: Se tiver oito ou mais divisórias de entrada, utilize a opção de saída Azure Synapse para uma melhor escala.

## <a name="azure-synapse-analytics-preview"></a>Azure Synapse Analytics (Pré-visualização)

[A Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (anteriormente SQL Data Warehouse) é um serviço de análise ilimitado que reúne o armazenamento de dados da empresa e a análise de Big Data. 

Os trabalhos do Azure Stream Analytics podem ser transmitidos para uma mesa de bilhar SQL em Azure Synapse Analytics e podem processar taxas de produção até 200MB/seg. Isto suporta as necessidades de análise em tempo real mais exigentes e necessidades de processamento de dados em percursos quentes para cargas de trabalho, tais como relatórios e dashboarding.  

A mesa de bilhar SQL deve existir antes de poder adicioná-la como saída ao seu trabalho stream Analytics. O esquema da mesa deve coincidir com os campos e os seus tipos na produção do seu trabalho. 

Para utilizar o Azure Synapse como saída, tem de se certificar de que tem a conta de armazenamento configurada. Navegue para as definições da conta de armazenamento para configurar a conta de armazenamento. Apenas são permitidos os tipos de conta de armazenamento que suportam tabelas: V2 de uso geral e V1 de uso geral. Selecione Apenas Standard Tier. O nível premium não é suportado.   

A tabela que se segue lista os nomes dos imóveis e as suas descrições para a criação da saída am Azure Synapse Analytics.

|Nome da propriedade|Descrição|
|-|-|
|Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para esta base de dados. |
|Base de Dados |Nome da piscina SQL para onde está a enviar a sua saída. |
|Nome do servidor |Nome do servidor Azure Synapse.  |
|Nome de utilizador |O nome de utilizador que tem acesso à base de dados. Stream Analytics suporta apenas a autenticação SQL. |
|Palavra-passe |A senha para ligar à base de dados. |
|Tabela  | O nome da mesa onde a saída está escrita. O nome da mesa é sensível a maiôs. O esquema desta tabela deve corresponder exatamente ao número de campos e aos seus tipos que a sua saída de trabalho gera.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Armazenamento de bolhas e Azure Data Lake Gen2

Data Lake Storage Gen2 faz do Azure Storage a base para a construção de lagos de dados empresariais em Azure. Projetado desde o início para o serviço de múltiplos petabytes de informação enquanto sustenta centenas de gigabits de produção, Data Lake Storage Gen2 permite-lhe gerir facilmente quantidades massivas de dados. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um espaço hierárquico de nomes para o armazenamento blob.

O armazenamento Azure Blob oferece uma solução rentável e escalável para armazenar grandes quantidades de dados não estruturados na nuvem. Para uma introdução no armazenamento blob e sua utilização, consulte [Upload, download e liste blobs com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

A tabela que se segue lista os nomes da propriedade e as suas descrições para a criação de uma saída blob ou ADLS Gen2.

| Nome da propriedade       | Descrição                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de saída        | Um nome amigável usado em consultas para direcionar a saída de consulta para este armazenamento de bolhas. |
| Conta de armazenamento     | O nome da conta de armazenamento para onde está a enviar a sua produção.               |
| Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento.                              |
| Recipiente de armazenamento   | Um agrupamento lógico para bolhas armazenadas no serviço Azure Blob. Quando fizer o upload de uma bolha para o serviço Blob, tem de especificar um recipiente para essa bolha. |
| Padrão do caminho | Opcional. O padrão do caminho do ficheiro que é usado para escrever as suas bolhas dentro do recipiente especificado. <br /><br /> No padrão do caminho, pode optar por utilizar uma ou mais instâncias das variáveis de data e hora para especificar a frequência que as bolhas estão escritas: <br /> {data}, {tempo} <br /><br />Pode utilizar a partição de bolhas personalizadas para especificar um nome personalizado {field} dos dados do evento para as bolhas de partição. O nome de campo é alfanumérico e pode incluir espaços, hífens e sublinhados. As restrições aos campos personalizados incluem: <ul><li>Os nomes de campo não são sensíveis a casos. Por exemplo, o serviço não pode diferenciar entre a coluna "ID" e a coluna "id".</li><li>Campos aninhados não são permitidos. Em vez disso, use um pseudónimo na consulta de trabalho para "achatar" o campo.</li><li>Expressões não podem ser usadas como nome de campo.</li></ul> <br />Esta funcionalidade permite a utilização de configurações de especificadores de data/hora personalizadas no caminho. Os formatos de data e hora personalizados devem ser especificados um de cada vez, incluídos na {data: \<specifier> } palavra-chave. As entradas admissíveis \<specifier> para são yyyy, MM, M, dd, d, HH, H, mm, m, ss, ou s. A {datatime: \<specifier> } a palavra-chave pode ser usada várias vezes no caminho para formar configurações personalizadas de data/hora. <br /><br />Exemplos: <ul><li>Exemplo 1: cluster1/logs/{date}/{time}</li><li>Exemplo 2: cluster1/logs/{date}</li><li>Exemplo 3: cluster1/{client_id}/{data}/{tempo}</li><li>Exemplo 4: cluster1/{datetime:ss}/{myField} onde a consulta é: SELECT data.myField AS myField FROM Input;</li><li>Exemplo 5: cluster1/ano={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />O carimbo de tempo da estrutura da pasta criada segue a UTC e não a hora local.<br /><br />O nome de ficheiros utiliza a seguinte convenção: <br /><br />{Padrão do prefixo do caminho}/schemaHashcode_Guid_Number.extensão<br /><br />Exemplo de ficheiros de saída:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Para obter mais informações sobre esta funcionalidade, consulte [a partição de saída personalizada do Azure Stream Analytics.](stream-analytics-custom-path-patterns-blob-storage-output.md) |
| Formato de data | Opcional. Se o token de data for utilizado no caminho do prefixo, pode selecionar o formato de data em que os seus ficheiros estão organizados. Exemplo: YYYY/MM/DD |
| Formato de tempo | Opcional. Se o token de tempo for utilizado no caminho do prefixo, especifique o formato de tempo em que os seus ficheiros estão organizados. Atualmente, o único valor suportado é o HH. |
| Formato de serialização de eventos | Formato de serialização para dados de saída. JSON, CSV, Avro e Parquet são apoiados. |
|Linhas mínimas |O número de filas mínimas por lote. Para o Parquet, cada lote criará um novo ficheiro. O valor predefinido atual é de 2.000 linhas e o máximo permitido é de 10.000 linhas.|
|Tempo máximo |O tempo máximo de espera por lote. Após este período, o lote será escrito para a saída mesmo que o requisito de linhas mínimas não seja cumprido. O valor predefinido atual é de 1 minuto e o máximo permitido é de 2 horas. Se a sua saída de bolha tiver frequência de padrão de trajetória, o tempo de espera não pode ser superior ao intervalo de tempo da partição.|
| Encoding    | Se estiver a utilizar o formato CSV ou JSON, deve ser especificado um codificamento. UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador   | Aplicável apenas para serialização de CSV. Stream Analytics suporta uma série de delimiters comuns para serializar dados de CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formatar      | Aplicável apenas para serialização JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **A matriz** especifica que a saída é formatada como uma matriz de objetos JSON. Esta matriz só é fechada quando o trabalho parar ou stream Analytics passou para a janela da próxima vez. Em geral, é preferível usar JSON separados de linha, porque não requer qualquer manuseamento especial enquanto o ficheiro de saída ainda está a ser escrito. |

Quando está a utilizar o armazenamento Blob como saída, é criado um novo ficheiro na bolha nos seguintes casos:

* Se o ficheiro exceder o número máximo de blocos permitidos (atualmente 50.000). Pode atingir o número máximo permitido de blocos sem atingir o tamanho máximo permitido de bolhas. Por exemplo, se a taxa de saída for elevada, pode ver mais bytes por bloco, e o tamanho do ficheiro é maior. Se a taxa de saída for baixa, cada bloco tem menos dados e o tamanho do ficheiro é menor.
* Se houver uma alteração de esquema na saída, e o formato de saída requer esquema fixo (CSV e Avro).
* Se um trabalho for reiniciado, seja externamente por um utilizador que o pare e o inicie, ou internamente para manutenção do sistema ou recuperação de erros.
* Se a consulta estiver totalmente dividida, e for criado um novo ficheiro para cada divisão de saída.
* Se o utilizador eliminar um ficheiro ou um recipiente da conta de armazenamento.
* Se a saída for dividida pelo tempo utilizando o padrão de prefixo do caminho, e uma nova bolha for usada quando a consulta se move para a hora seguinte.
* Se a saída for dividida por um campo personalizado, e uma nova bolha for criada por chave de partição se não existir.
* Se a saída for dividida por um campo personalizado onde a cardinalidade da chave de partição ultrapassa os 8.000, e uma nova bolha é criada por chave de partição.

## <a name="event-hubs"></a>Hubs de Eventos

O serviço [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) é um evento altamente escalável de publicação-subscrição. Pode recolher milhões de eventos por segundo. Um uso de um centro de eventos como saída é quando a saída de um trabalho stream Analytics torna-se a entrada de outro trabalho de streaming. Para obter informações sobre o tamanho máximo da mensagem e a otimização do tamanho do lote, consulte a secção de tamanho do [lote de saída.](#output-batch-size)

Você precisa de alguns parâmetros para configurar fluxos de dados de centros de eventos como uma saída.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável usado em consultas para direcionar a saída de consulta para este centro de eventos. |
| Espaço de nomes do hub de eventos | Um contentor para um conjunto de entidades de mensagens. Quando criou um novo centro de eventos, também criou um espaço de nomes de centros de eventos. |
| Nome do hub de eventos | O nome da saída do seu centro de eventos. |
| Nome da política do centro de eventos | A política de acesso partilhado, que pode criar no separador **Configure** do centro de eventos. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
| Chave de política do centro de eventos | A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nome do centro de eventos. |
| Coluna-chave de partição | Opcional. Uma coluna que contém a chave de partição para a saída do centro de eventos. |
| Formato de serialização de eventos | O formato de serialização para dados de saída. JSON, CSV e Avro são apoiados. |
| Encoding | Para cSV e JSON, UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador | Aplicável apenas para serialização de CSV. O Stream Analytics suporta uma série de delimiters comuns para serializar dados em formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formatar | Aplicável apenas para serialização JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **A matriz** especifica que a saída é formatada como uma matriz de objetos JSON.  |
| Colunas de propriedade | Opcional. Colunas separadas por vírgula que precisam de ser anexadas como propriedades do utilizador da mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção [Propriedades de metadados personalizados para a saída.](#custom-metadata-properties-for-output) |

## <a name="power-bi"></a>Power BI

Pode utilizar [o Power BI](https://powerbi.microsoft.com/) como uma saída para um trabalho stream Analytics para proporcionar uma experiência de visualização rica dos resultados da análise. Pode utilizar esta capacidade para dashboards operacionais, geração de relatórios e relatórios baseados em métricas.

A produção de Power BI do Stream Analytics não está atualmente disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International).

A tabela que se segue lista os nomes dos imóveis e as suas descrições para configurar a sua saída Power BI.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Forneça um nome amigável que seja usado em consultas para direcionar a saída de consulta para esta saída Power BI. |
| Espaço de trabalho em grupo |Para ativar a partilha de dados com outros utilizadores do Power BI, pode selecionar grupos dentro da sua conta Power BI ou escolher **o My Workspace** se não quiser escrever para um grupo. A atualização de um grupo existente requer a renovação da autenticação Power BI. |
| Nome do conjunto de dados |Forneça um nome de conjunto de dados que pretende que a saída Power BI utilize. |
| Nome da tabela |Forneça um nome de tabela sob o conjunto de dados da saída Power BI. Atualmente, a produção de Power BI a partir de trabalhos stream Analytics pode ter apenas uma tabela num conjunto de dados. |
| Autorizar a ligação | Tem de autorizar com o Power BI a configurar as definições de saída. Assim que conceder este acesso de saída ao seu dashboard Power BI, pode revogar o acesso alterando a palavra-passe da conta de utilizador, eliminando a saída do trabalho ou eliminando o trabalho stream Analytics. | 

Para uma passagem de configuração de uma saída de Power BI e painel de instrumentos, consulte o tutorial [Azure Stream Analytics e Power BI.](stream-analytics-power-bi-dashboard.md)

> [!NOTE]
> Não crie explicitamente o conjunto de dados e a tabela no painel Power BI. O conjunto de dados e a tabela são automaticamente povoados quando o trabalho é iniciado e o trabalho começa a bombear a saída para o Power BI. Se a consulta de trabalho não gerar resultados, o conjunto de dados e a tabela não são criados. Se o Power BI já tinha um conjunto de dados e uma tabela com o mesmo nome que o fornecido neste trabalho stream Analytics, os dados existentes são substituídos.
>

### <a name="create-a-schema"></a>Criar um esquema
O Azure Stream Analytics cria um conjunto de dados power BI e esquema de tabela para o utilizador se já não existirem. Em todos os outros casos, a tabela é atualizada com novos valores. Atualmente, apenas uma tabela pode existir dentro de um conjunto de dados. 

Power BI utiliza a política de retenção de primeira e primeira saída (FIFO). Os dados serão recolhidos numa tabela até atingirem as 200.000 filas.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converter um tipo de dados do Stream Analytics para Power BI
O Azure Stream Analytics atualiza o modelo de dados dinamicamente no tempo de execução se o esquema de saída mudar. Alterações no nome da coluna, alterações do tipo de coluna e a adição ou remoção de colunas são todas rastreadas.

Esta tabela cobre as conversões do tipo de dados do [Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) para os tipos de Data [Model (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)da Power BIEntity Data Model , se não existir um conjunto de dados e tabela power BI.

De Stream Analytics | Para Power BI
-----|-----
bigint | Int64
nvarchar(máx) | String
datetime | Datetime
float | Double
Matriz de registos | Tipo de corda, valor constante "IRecord" ou "IArray"

### <a name="update-the-schema"></a>Atualizar o esquema
O Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Mais tarde, se necessário, o esquema do modelo de dados é atualizado para acomodar eventos que possam não se encaixar no esquema original.

Evite a `SELECT *` consulta para evitar uma atualização dinâmica do esquema através de linhas. Além das potenciais implicações de desempenho, pode resultar na incerteza do tempo que os resultados demoram. Selecione os campos exatos que precisam de ser mostrados no painel Power BI. Além disso, os valores dos dados devem estar em conformidade com o tipo de dados escolhido.


Anterior/corrente | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Cadeia | Cadeia | Double
Double | Double | Cadeia | Cadeia | Double
Cadeia | Cadeia | Cadeia | Cadeia | Cadeia 
Datetime | Cadeia | Cadeia |  Datetime | String

## <a name="table-storage"></a>Table Storage

[O armazenamento da Azure Table](../storage/common/storage-introduction.md) oferece um armazenamento altamente disponível e massivamente escalável, para que uma aplicação possa escalar automaticamente para satisfazer a procura do utilizador. O armazenamento de mesa é a loja de chaves/atributos NoSQL da Microsoft, que pode utilizar para dados estruturados com menos restrições no esquema. O armazenamento da tabela Azure pode ser usado para armazenar dados para persistência e recuperação eficiente.

A tabela que se segue lista os nomes dos imóveis e as suas descrições para a criação de uma saída de tabela.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para este armazenamento de mesa. |
| Conta de armazenamento |O nome da conta de armazenamento para onde está a enviar a sua produção. |
| Chave da conta de armazenamento |A chave de acesso associada à conta de armazenamento. |
| Nome da tabela |O nome da mesa. A mesa é criada se não existir. |
| Chave de partição |O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador único para a partição dentro de uma tabela que forma a primeira parte da chave primária de uma entidade. É um valor de corda que pode ser até 1 KB de tamanho. |
| Chave de linha |O nome da coluna de saída que contém a tecla de linha. A chave da linha é um identificador único para uma entidade dentro de uma divisória. É a segunda parte da chave primária de uma entidade. A tecla de linha é um valor de corda que pode ser até 1 KB de tamanho. |
| Tamanho do lote |O número de registos de uma operação de lote. O incumprimento (100) é suficiente para a maioria dos postos de trabalho. Consulte a especificação de [funcionamento do lote de mesa](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) para obter mais detalhes sobre a modificação desta definição. |

## <a name="service-bus-queues"></a>Filas do Service Bus

[As filas de ônibus de serviço](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) oferecem uma entrega de mensagens FIFO a um ou mais consumidores concorrentes. Normalmente, as mensagens são recebidas e processadas pelos recetores na ordem temporal em que foram adicionadas à fila. Cada mensagem é recebida e processada por apenas um consumidor de mensagens.

No [nível de compatibilidade 1.2](stream-analytics-compatibility-level.md), o Azure Stream Analytics utiliza o protocolo de mensagens [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) para escrever para Service Bus Queues and Topics. A AMQP permite-lhe construir aplicações híbridas e inter-plataforma usando um protocolo padrão aberto.

A tabela que se segue lista os nomes dos imóveis e as suas descrições para a criação de uma saída de fila.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para esta fila de serviço. |
| Espaço de nome de ônibus de serviço |Um contentor para um conjunto de entidades de mensagens. |
| Nome da fila |O nome da fila do autocarro de serviço. |
| Nome da política da fila |Quando cria uma fila, também pode criar políticas de acesso partilhado no **separador Configurar** da fila. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
| Chave de política de fila |A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nomes do Service Bus. |
| Formato de serialização de eventos |O formato de serialização para dados de saída. JSON, CSV e Avro são apoiados. |
| Encoding |Para cSV e JSON, UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador |Aplicável apenas para serialização de CSV. O Stream Analytics suporta uma série de delimiters comuns para serializar dados em formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formatar |Aplicável apenas para o tipo JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **A matriz** especifica que a saída é formatada como uma matriz de objetos JSON. |
| Colunas de propriedade | Opcional. Colunas separadas por vírgula que precisam de ser anexadas como propriedades do utilizador da mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção [Propriedades de metadados personalizados para a saída.](#custom-metadata-properties-for-output) |
| Colunas de propriedade do sistema | Opcional. Os pares de valor chave das Propriedades do Sistema e os nomes correspondentes das colunas que precisam de ser anexados à mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção [Propriedades do Sistema para a Fila de Autocarros de Serviço e saídas de Tópicos](#system-properties-for-service-bus-queue-and-topic-outputs)  |

O número de divisórias [baseia-se no Serviço de Autocarros SKU e tamanho.](../service-bus-messaging/service-bus-partitioning.md) A chave de partição é um valor inteiro único para cada partição.

## <a name="service-bus-topics"></a>Tópicos de Service Bus
As filas de autocarros de serviço fornecem um método de comunicação um-para-um, desde o remetente ao recetor. [Os tópicos do Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornecem uma forma de comunicação de um a muitos.

A tabela que se segue lista os nomes dos imóveis e as suas descrições para a criação de uma saída de tópico de Service Bus.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para este tópico de Service Bus. |
| Espaço de nome de ônibus de serviço |Um contentor para um conjunto de entidades de mensagens. Quando criou um novo centro de eventos, também criou um espaço de nomes de Service Bus. |
| Nome tópico |Os tópicos são entidades de mensagens, semelhantes aos centros de eventos e às filas. São projetados para recolher streams de eventos de dispositivos e serviços. Quando um tópico é criado, também é dado um nome específico. As mensagens enviadas para um tópico não estão disponíveis a menos que uma subscrição seja criada, por isso certifique-se de que há uma ou mais subscrições sob o tema. |
| Nome da política de tópicos |Ao criar um tópico de Service Bus, também pode criar políticas de acesso partilhado no **separador Configurar** do tópico. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
| Chave de política de tópicos |A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nomes do Service Bus. |
| Formato de serialização de eventos |O formato de serialização para dados de saída. JSON, CSV e Avro são apoiados. |
| Encoding |Se estiver a utilizar o formato CSV ou JSON, deve ser especificado um codificamento. UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador |Aplicável apenas para serialização de CSV. O Stream Analytics suporta uma série de delimiters comuns para serializar dados em formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Colunas de propriedade | Opcional. Colunas separadas por vírgula que precisam de ser anexadas como propriedades do utilizador da mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção [Propriedades de metadados personalizados para a saída.](#custom-metadata-properties-for-output) |
| Colunas de propriedade do sistema | Opcional. Os pares de valor chave das Propriedades do Sistema e os nomes correspondentes das colunas que precisam de ser anexados à mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção [Propriedades do Sistema para a Fila de Autocarros de Serviço e saídas de Tópicos](#system-properties-for-service-bus-queue-and-topic-outputs) |

O número de divisórias [baseia-se no Serviço de Autocarros SKU e tamanho.](../service-bus-messaging/service-bus-partitioning.md) A chave de partição é um valor inteiro único para cada partição.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um serviço de base de dados distribuído globalmente que oferece escala elástica ilimitada em todo o mundo, consulta rica e indexação automática sobre modelos de dados schema-agnósticos. Para saber mais sobre as opções de recipientes DB do Azure Cosmos para stream analytics, consulte o [Stream Analytics com Azure Cosmos DB como](stream-analytics-documentdb-output.md) artigo de saída.

A produção de DB Azure Cosmos da Stream Analytics não está atualmente disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International).

> [!Note]
> Neste momento, o Azure Stream Analytics apenas suporta a ligação ao Azure Cosmos DB utilizando o SQL API.
> Outras APIs DB de Azure Cosmos ainda não estão apoiadas. Se apontar o Azure Stream Analytics para as contas DB do Azure Cosmos criadas com outras APIs, os dados podem não estar devidamente armazenados.

A tabela seguinte descreve as propriedades para criar uma saída DB Azure Cosmos.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um pseudónimo para remeter esta saída na sua consulta Stream Analytics. |
| Sink | Azure Cosmos DB. |
| Opção de Importar | Escolha **o Cosmos DB da sua subscrição** ou **forneça manualmente as definições de DB cosmos**.
| ID da Conta | O nome ou ponto final URI da conta DB da Azure Cosmos. |
| Chave de conta | A chave de acesso partilhada para a conta DB da Azure Cosmos. |
| Base de Dados | O nome da base de dados Azure Cosmos DB. |
| Nome do contentor | O nome do recipiente a ser usado, que deve existir em Cosmos DB. Exemplo:  <br /><ul><li> _MyContainer_: Deve existir um recipiente chamado "MyContainer".</li>|
| ID do documento |Opcional. O nome do campo em eventos de saída que são usados para especificar a chave primária em que inserem ou atualizar operações são baseadas.

## <a name="azure-functions"></a>Funções do Azure
Azure Functions é um serviço de computação sem servidor que pode utilizar para executar código a pedido sem ter de provisões explícitas ou gerir infraestruturas. Permite implementar código que é desencadeado por eventos que ocorrem em Azure ou serviços de parceiros. Esta capacidade de Azure Functions para responder aos gatilhos torna-a uma saída natural para o Azure Stream Analytics. Este adaptador de saída permite que os utilizadores conectem stream Analytics a Funções Azure e execute um script ou peça de código em resposta a uma variedade de eventos.

A produção de Funções Azure da Stream Analytics não está atualmente disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International).

A Azure Stream Analytics invoca funções Azure através de triggers HTTP. O adaptador de saída Azure Functions está disponível com as seguintes propriedades configuráveis:

| Nome da propriedade | Descrição |
| --- | --- |
| Function app |O nome da sua aplicação Azure Functions. |
| Função |O nome da função na sua aplicação Azure Functions. |
| Chave |Se pretender utilizar uma Função Azure a partir de outra subscrição, pode fazê-lo fornecendo a chave para aceder à sua função. |
| Tamanho do lote máximo |Uma propriedade que permite definir o tamanho máximo para cada lote de saída que é enviado para a sua função Azure. A unidade de entrada está em bytes. Por padrão, este valor é de 262.144 bytes (256 KB). |
| Contagem de lotes max  |Uma propriedade que permite especificar o número máximo de eventos em cada lote que é enviado para As Funções Azure. O valor predefinido é 100. |

O Azure Stream Analytics espera o estado HTTP 200 da aplicação Funções para lotes que foram processados com sucesso.

Quando o Azure Stream Analytics recebe uma exceção 413 ("http Request Entity Too Large") de uma função Azure, reduz o tamanho dos lotes que envia para as Funções Azure. No seu código de função Azure, utilize esta exceção para se certificar de que o Azure Stream Analytics não envia lotes de grandes dimensões. Além disso, certifique-se de que a contagem máxima de lotes e os valores de tamanho utilizados na função são consistentes com os valores introduzidos no portal Stream Analytics.

> [!NOTE]
> Durante a ligação ao teste, o Stream Analytics envia um lote vazio para as Funções Azure para testar se a ligação entre os dois funciona. Certifique-se de que a sua aplicação Funções lida com pedidos de lote vazios para garantir que a ligação de teste passa.

Além disso, numa situação em que não há eventos a aterrar numa janela temporal, não se gera saída. Como resultado, a função **computeResult** não é chamada. Este comportamento é consistente com as funções agregadas en janeladas incorporadas.

## <a name="custom-metadata-properties-for-output"></a>Propriedades de metadados personalizados para saída 

Pode anexar colunas de consulta como propriedades do utilizador às suas mensagens de saída. Estas colunas não entram na carga. As propriedades estão presentes na forma de um dicionário na mensagem de saída. *Chave* é o nome da coluna e *valor* é o valor da coluna no dicionário de propriedades. Todos os tipos de dados stream Analytics são suportados, exceto Record e Array.  

Saídas suportadas: 
* Fila do Service Bus 
* Tópico do Service Bus 
* Hub de eventos 

No exemplo seguinte, adicionamos os dois campos `DeviceId` e `DeviceStatus` os metadados. 
* Consulta:`select *, DeviceId, DeviceStatus from iotHubInput`
* Configuração de saída:`DeviceId,DeviceStatus`

![Colunas de propriedade](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

A imagem que se segue mostra as propriedades da mensagem de saída inspecionadas no EventHub através [do Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Propriedades personalizadas do evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Propriedades do sistema para fila de autocarros de serviço e saídas de tópicos 
Pode anexar colunas de consulta como [propriedades do sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) à fila do autocarro de serviço de saída ou mensagens tópicos. Estas colunas não entram na carga útil, em vez disso, a propriedade correspondente do [sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) BrokeredMessage é povoada com os valores da coluna de consulta.
Estas propriedades do sistema são suportadas - `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc` .
Os valores de cadeia destas colunas são analisados como o tipo de valor contabilístico correspondente do sistema e quaisquer falhas de análise são tratadas como erros de dados.
Este campo é fornecido como um formato de objeto JSON. Detalhes sobre este formato são os seguintes -
* Rodeada por aparelhos {} encaracolados.
* Escrito em pares chave/valor.
* As chaves e os valores devem ser cordas.
* Chave é o nome e valor da propriedade do sistema é o nome da coluna de consulta.
* As chaves e os valores são separados por um cólon.
* Cada par de chaves/valor é separado por uma vírgula.

Isto mostra como usar esta propriedade –

* Consulta:`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Colunas de Propriedade do Sistema:`{ "MessageId": "column1", "PartitionKey": "column2"}`

Isto define as mensagens de fila de `MessageId` autocarros de serviço com `column1` os valores e partitionKey é definido com `column2` os valores de 's'

## <a name="partitioning"></a>Criação de partições

O quadro que se segue resume o suporte à partição e o número de autores de saída para cada tipo de saída:

| Tipo de saída | Suporte de partilha | Chave de partição  | Número de escritores de saída |
| --- | --- | --- | --- |
| Azure Data Lake Store | Sim | Use tokens {date} e {time} no padrão de prefixo do caminho. Escolha o formato de data, como YYYY/MM/DD, DD/MM/YYYY, ou MM-DD-YYYY. HH é usado para o formato de tempo. | Segue a partição de entrada para [consultas totalmente paralizações](stream-analytics-scale-jobs.md). |
| Base de Dados SQL do Azure | Sim, precisa de ativar. | Com base na partição por cláusula na consulta. | Quando a opção de partição herdada estiver ativada, segue a partição de entrada para [consultas totalmente paralitáveis](stream-analytics-scale-jobs.md). Para saber mais sobre como obter um melhor desempenho de produção de escrita quando estiver a carregar dados na Base de Dados Azure SQL, consulte [a saída Azure Stream Analytics para a Base de Dados Azure SQL](stream-analytics-sql-output-perf.md). |
| Armazenamento de Blobs do Azure | Sim | Use tokens {date} e {time} dos campos de eventos no padrão do caminho. Escolha o formato de data, como YYYY/MM/DD, DD/MM/YYYY, ou MM-DD-YYYY. HH é usado para o formato de tempo. A saída do blob pode ser dividida por um único atributo de evento personalizado {fieldname} ou {datetime: \<specifier> }. | Segue a partição de entrada para [consultas totalmente paralizações](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Sim | Sim | Varia dependendo do alinhamento de divisórias.<br /> Quando a chave de partição para a saída do centro de eventos está igualmente alinhada com o passo de consulta a montante (anterior), o número de escritores é o mesmo que o número de divisórias na produção do centro de eventos. Cada escritor usa a [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) para enviar eventos para a partição específica. <br /> Quando a chave de partição para a saída do centro de eventos não está alinhada com o passo de consulta a montante (anterior), o número de escritores é o mesmo que o número de divisórias nesse passo anterior. Cada escritor usa a [aula SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) no **EventHubClient** para enviar eventos para todas as divisórias de saída. |
| Power BI | Não | Nenhuma | Não aplicável. |
| Armazenamento de Tabelas do Azure | Sim | Qualquer coluna de saída.  | Segue a partição de entrada para [consultas totalmente paralelas](stream-analytics-scale-jobs.md). |
| Tópico do Azure Service Bus | Sim | Escolhido automaticamente. O número de divisórias baseia-se no [Serviço de Autocarros SKU e tamanho.](../service-bus-messaging/service-bus-partitioning.md) A chave de partição é um valor inteiro único para cada partição.| O mesmo que o número de divisórias no tópico de saída.  |
| Fila do Azure Service Bus | Sim | Escolhido automaticamente. O número de divisórias baseia-se no [Serviço de Autocarros SKU e tamanho.](../service-bus-messaging/service-bus-partitioning.md) A chave de partição é um valor inteiro único para cada partição.| O mesmo que o número de divisórias na fila de saída. |
| Azure Cosmos DB | Sim | Com base na partição por cláusula na consulta. | Segue a partição de entrada para [consultas totalmente paralelas](stream-analytics-scale-jobs.md). |
| Funções do Azure | Sim | Com base na partição por cláusula na consulta. | Segue a partição de entrada para [consultas totalmente paralelas](stream-analytics-scale-jobs.md). |

O número de escritores de saída também pode ser controlado `INTO <partition count>` usando (ver [INTO)](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)cláusula na sua consulta, o que pode ser útil para alcançar uma topologia de emprego desejada. Se o adaptador de saída não estiver dividido, a falta de dados numa partição de entrada causará um atraso até ao tempo de chegada tardio. Nesses casos, a produção é fundida a um único escritor, o que pode causar estrangulamentos no seu oleoduto. Para saber mais sobre a política de chegada tardia, consulte considerações de [ordem de eventos Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída
O Azure Stream Analytics utiliza lotes de tamanho variável para processar eventos e escrever para saídas. Normalmente, o motor Stream Analytics não escreve uma mensagem de cada vez e utiliza lotes para eficiência. Quando a taxa dos eventos de entrada e saída é elevada, o Stream Analytics utiliza lotes maiores. Quando a taxa de saída é baixa, utiliza lotes menores para manter a latência baixa.

O quadro seguinte explica algumas das considerações relativas ao loteamento de saída:

| Tipo de saída |    Tamanho máximo da mensagem | Otimização do tamanho do lote |
| :--- | :--- | :--- |
| Azure Data Lake Store | Consulte os [limites de armazenamento do Lago de Dados.](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits) | Utilize até 4 MB por operação de escrita. |
| Base de Dados SQL do Azure | Configurável usando a contagem de lotes Max. 10.000 filas máximas e 100 filas mínimas por inserção a granel por predefinição.<br />Consulte [os limites do Azure SQL](../sql-database/sql-database-resource-limits.md). |  Cada lote é inicialmente inserido a granel com a contagem máxima de lote. O lote é dividido ao meio (até à contagem mínima de lotes) com base em erros retripáveis do SQL. |
| Armazenamento de Blobs do Azure | Consulte [os limites de armazenamento do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | O tamanho máximo do bloco blob é de 4 MB.<br />A contagem máxima de bolhas é de 50.000. |
| Azure Event Hubs    | 256 KB ou 1 MB por mensagem. <br />Consulte os [limites do Centro de Eventos](../event-hubs/event-hubs-quotas.md). |    Quando a partição de entrada/saída não está alinhada, cada evento é embalado individualmente `EventData` e enviado num lote até ao tamanho máximo da mensagem. Isto também acontece se forem utilizadas [propriedades de metadados personalizados.](#custom-metadata-properties-for-output) <br /><br />  Quando a partição de entrada/saída está alinhada, vários eventos são embalados numa única `EventData` instância, até ao tamanho máximo da mensagem, e enviados.    |
| Power BI | Consulte os [limites da API de repouso Power BI](https://msdn.microsoft.com/library/dn950053.aspx). |
| Armazenamento de Tabelas do Azure | Consulte [os limites de armazenamento do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | O padrão é de 100 entidades por transação única. Pode configurá-lo a um valor menor, se necessário. |
| Fila do Azure Service Bus    | 256 KB por mensagem para o nível Standard, 1MB para nível Premium.<br /> Consulte [os limites do serviço de autocarros](../service-bus-messaging/service-bus-quotas.md). | Utilize um único evento por mensagem. |
| Tópico do Azure Service Bus | 256 KB por mensagem para o nível Standard, 1MB para nível Premium.<br /> Consulte [os limites do serviço de autocarros](../service-bus-messaging/service-bus-quotas.md). | Utilize um único evento por mensagem. |
| Azure Cosmos DB    | Ver [limites de DB de Azure Cosmos](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | O tamanho do lote e a frequência de escrita são ajustados dinamicamente com base nas respostas DB do Azure Cosmos. <br /> Não existem limitações pré-determinadas da Stream Analytics. |
| Funções do Azure    | | O tamanho do lote predefinido é de 262.144 bytes (256 KB). <br /> A contagem de eventos predefinidos por lote é de 100. <br /> O tamanho do lote é configurável e pode ser aumentado ou diminuído nas [opções de saída](#azure-functions)stream Analytics .

## <a name="next-steps"></a>Próximos passos
> [!div class="nextstepaction"]
> 
> [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
