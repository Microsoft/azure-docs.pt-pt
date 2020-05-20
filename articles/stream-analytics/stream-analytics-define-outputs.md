---
title: Compreender as saídas do Azure Stream Analytics
description: Este artigo descreve as opções de saída de dados disponíveis no Azure Stream Analytics, incluindo o Power BI para resultados de análise.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/8/2020
ms.openlocfilehash: c4790585d089ab287260f74001a8aa3f1cb7e5f7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647496"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Compreender as saídas do Azure Stream Analytics

Este artigo descreve os tipos de saídas disponíveis para um trabalho de Azure Stream Analytics. As saídas permitem-lhe armazenar e guardar os resultados do trabalho do Stream Analytics. Ao utilizar os dados de saída, pode fazer mais análises de negócios e armazenamento de dados dos seus dados.

Quando conceber a sua consulta Stream Analytics, consulte o nome da saída utilizando a [cláusula INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Pode utilizar uma única saída por trabalho, ou várias saídas por trabalho de streaming (se precisar) fornecendo várias cláusulas DE EM na consulta.

Para criar, editar e testar as saídas de trabalho do Stream Analytics, pode utilizar o [portal Azure,](stream-analytics-quick-create-portal.md#configure-job-output) [Azure PowerShell,](stream-analytics-quick-create-powershell.md#configure-output-to-the-job) [.NET API,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet) [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)e [Visual Studio](stream-analytics-quick-create-vs.md).

Alguns tipos de saídas suportam [a partilha](#partitioning). [Os tamanhos do lote](#output-batch-size) de saída variam para otimizar a produção.


## <a name="azure-data-lake-storage-gen-1"></a>Armazenamento de lagos azure data gen 1

Stream Analytics suporta [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). O Azure Data Lake Storage é um repositório de hiperescala em toda a empresa para grandes cargas de trabalho analíticas de dados. Pode utilizar o Data Lake Storage para armazenar dados de qualquer tamanho, tipo e velocidade de ingestão para análiseoperacional e exploratória. O Stream Analytics precisa de ser autorizado a aceder ao Armazenamento de Data Lake.

A produção de armazenamento de lagos azure data da Stream Analytics não está atualmente disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International).

A tabela seguinte lista os nomes de propriedade e as suas descrições para configurar a saída do Data Lake Storage Gen 1.   

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável usado em consultas para direcionar a saída de consulta para data lake store. |
| Subscrição | A subscrição que contém a sua conta de Armazenamento de Lagos De Dados Azure. |
| Nome da conta | O nome da conta data lake store onde está a enviar a sua saída. É-lhe apresentada uma lista de contas da Data Lake Store que estão disponíveis na sua subscrição. |
| Padrão de prefixo do caminho | O caminho de ficheiro que é usado para escrever os seus ficheiros dentro da conta especificada data lake store. Pode especificar uma ou mais instâncias das variáveis {date} e {time} :<br /><ul><li>Exemplo 1: folder1/logs/{date}/{time}</li><li>Exemplo 2: pasta1/logs/{date}</li></ul><br />O carimbo temporal da estrutura de pasta criada segue a UTC e não a hora local.<br /><br />Se o padrão do caminho do ficheiro não contiver um corte de rasto (/), o último padrão no caminho do ficheiro é tratado como um prefixo de nome de ficheiro. <br /><br />Novas ficheiros são criados nestas circunstâncias:<ul><li>Alteração do esquema de saída</li><li>Reinício externo ou interno de um emprego</li></ul> |
| Formato de data | Opcional. Se o token da data for utilizado no caminho de prefixo, pode selecionar o formato de data no qual os seus ficheiros estão organizados. Exemplo: Yyyy/MM/DD |
|Formato de tempo | Opcional. Se o sinal de tempo for utilizado no caminho de prefixo, especifique o formato de tempo em que os seus ficheiros estão organizados. Atualmente, o único valor suportado é o HH. |
| Formato de serialização de eventos | O formato de serialização para dados de saída. JSON, CSV e Avro são apoiados.|
| Encoding | Se estiver a utilizar o formato CSV ou JSON, deve especificar-se uma codificação. O UTF-8 é o único formato de codificação suportado neste momento.|
| Delimitador | Aplicável apenas para a serialização do CSV. O Stream Analytics suporta uma série de delimitadores comuns para serializar dados csv. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical.|
| Formato | Aplicável apenas para a serialização da JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido.  **A matriz** especifica que a saída é formatada como uma variedade de objetos JSON. Esta matriz só é fechada quando o trabalho para ou o Stream Analytics passou para a janela da próxima vez. Em geral, é preferível usar jSON separado de linha, porque não requer qualquer manuseamento especial enquanto o ficheiro de saída ainda está a ser escrito.|
| Modo de autenticação | Pode autorizar o acesso à sua conta de Armazenamento de Data Lake utilizando [identidade gerida](stream-analytics-managed-identities-adls.md) ou ficha do utilizador. Assim que conceder acesso, pode revogar o acesso alterando a palavra-passe da conta de utilizador, apagando a saída de Armazenamento de Data Lake para este trabalho ou apagando o trabalho do Stream Analytics. |

## <a name="sql-database"></a>Base de Dados SQL

Pode utilizar a Base de [Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) como uma saída para dados de natureza relacional ou para aplicações que dependem de conteúdo hospedado numa base de dados relacional. Os trabalhos da Stream Analytics escrevem para uma tabela existente na Base de Dados SQL. O esquema de mesa deve corresponder exatamente aos campos e aos seus tipos na saída do seu trabalho. Também pode especificar [o Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) como uma saída através da opção de saída da Base de Dados SQL. Para aprender sobre formas de melhorar a produção de escrita, consulte o Stream Analytics com a Base de [Dados Azure SQL como](stream-analytics-sql-output-perf.md) artigo de saída.

Também pode utilizar o Caso Gerido pela Base de [Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) como saída. Tem de configurar o ponto final público na Instância gerida pela Base de [Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) e, em seguida, configurar manualmente as seguintes definições no Azure Stream Analytics. A máquina virtual Azure que executa o SQL Server com uma base de dados anexada também é suportada configurando manualmente as definições abaixo.

A tabela seguinte lista os nomes da propriedade e a sua descrição para a criação de uma saída de Base de Dados SQL.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída de consulta para esta base de dados. |
| Base de Dados | O nome da base de dados para onde está a enviar a sua saída. |
| Nome do servidor | O nome do servidor da Base de Dados SQL. Para a Instância Gerida pela Base de Dados Azure SQL, é necessário especificar a porta 3342. Por exemplo, *sampleserver.public.database.windows.net,3342* |
| Nome de utilizador | O nome de utilizador que tem acesso à base de dados. Stream Analytics suporta apenas a autenticação SQL. |
| Palavra-passe | A palavra-passe para ligar à base de dados. |
| Tabela | O nome da mesa onde a saída está escrita. O nome da mesa é sensível ao caso. O esquema desta tabela deve corresponder exatamente ao número de campos e aos seus tipos que a sua saída de trabalho gera. |
|Regime de partição herdado| Uma opção para herdar o esquema de partilha do seu passo de consulta anterior, para permitir topologia totalmente paralela com vários escritores à mesa. Para mais informações, consulte a saída do Azure Stream Analytics para a Base de [Dados Azure SQL](stream-analytics-sql-output-perf.md).|
|Contagem máxima de lote| O limite superior recomendado para o número de registos enviados com cada transação de inserção a granel.|

Existem dois adaptadores que permitem a saída de Azure Stream Analytics para Azure Synapse Analytics (anteriormente SQL Data Warehouse): SQL Database e Azure Synapse. Recomendamos que escolha o adaptador Azure Synapse Analytics em vez do adaptador De base de dados SQL se alguma das seguintes condições se mantiver verdadeira:

* **Entrada**: Se a sua produção esperada agora ou no futuro for superior a 10MB/seg, utilize a opção de saída Azure Synapse para um melhor desempenho.

* **Divisórias de entrada**: Se tiver oito ou mais divisórias de entrada, utilize a opção de saída Azure Synapse para uma melhor escala.

## <a name="azure-synapse-analytics-preview"></a>Análise sinafna Azure (Pré-visualização)

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (anteriormente SQL Data Warehouse) é um serviço de análise ilimitado que reúne armazenamento de dados da empresa e análise de Big Data. 

Os trabalhos da Azure Stream Analytics podem chegar a uma mesa de bilhar SQL no Azure Synapse Analytics e podem processar taxas de produção até 200MB/seg. Isto suporta as necessidades de análise em tempo real mais exigentes e de processamento de dados em vias quentes para cargas de trabalho, tais como relatórios e dashboards.  

A mesa de bilhar SQL deve existir antes de poder adicioná-la como saída ao seu trabalho de Stream Analytics. O esquema da mesa deve coincidir com os campos e os seus tipos na saída do seu trabalho. 

Para utilizar o Azure Synapse como saída, tem de garantir que tem a conta de armazenamento configurada. Navegue para as definições da conta de armazenamento para configurar a conta de armazenamento. Apenas são permitidos os tipos de conta de armazenamento que suportam tabelas: V2 de uso geral e V1 de uso geral. Selecione apenas Standard Tier. O nível premium não é suportado.   

A tabela seguinte lista os nomes da propriedade e as suas descrições para a criação da saída azure Synapse Analytics.

|Nome da propriedade|Descrição|
|-|-|
|Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída de consulta para esta base de dados. |
|Base de Dados |Nome da piscina SQL para onde está a enviar a sua saída. |
|Nome do servidor |Nome do servidor Azure Synapse.  |
|Nome de utilizador |O nome de utilizador que tem acesso à base de dados. Stream Analytics suporta apenas a autenticação SQL. |
|Palavra-passe |A palavra-passe para ligar à base de dados. |
|Tabela  | O nome da mesa onde a saída está escrita. O nome da mesa é sensível ao caso. O esquema desta tabela deve corresponder exatamente ao número de campos e aos seus tipos que a sua saída de trabalho gera.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Armazenamento blob e Lago de Dados Azure Gen2

Data Lake Storage Gen2 faz do Azure Storage a base para a construção de lagos de dados empresariais em Azure. Projetado desde o início para servir vários petabytes de informação, ao mesmo tempo que sustenta centenas de gigabits de entrada, data Lake Storage Gen2 permite-lhe gerir facilmente quantidades massivas de dados. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um espaço hierárquico ao armazenamento blob.

O armazenamento Azure Blob oferece uma solução rentável e escalável para armazenar grandes quantidades de dados não estruturados na nuvem. Para uma introdução no armazenamento blob e sua utilização, consulte [Upload, download e list blobs com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

A tabela seguinte lista os nomes da propriedade e as suas descrições para a criação de uma saída blob ou ADLS Gen2.

| Nome da propriedade       | Descrição                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de saída        | Um nome amigável usado em consultas para direcionar a saída de consulta para este armazenamento de bolhas. |
| Conta de armazenamento     | O nome da conta de armazenamento para onde está a enviar a sua saída.               |
| Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento.                              |
| Recipiente de armazenamento   | Um agrupamento lógico para bolhas armazenadas no serviço Azure Blob. Quando enviar uma bolha para o serviço Blob, deve especificar um recipiente para essa bolha. |
| Padrão do caminho | Opcional. O padrão do caminho de arquivo que é usado para escrever as suas bolhas dentro do recipiente especificado. <br /><br /> No padrão do caminho, pode optar por utilizar uma ou mais instâncias das variáveis data e hora para especificar a frequência que as bolhas estão escritas: <br /> {date}, {time} <br /><br />Pode utilizar divisórias blob personalizadas para especificar um nome personalizado {field} dos dados do seu evento até bolhas de partição. O nome de campo é alfanumérico e pode incluir espaços, hífens e sublinhados. As restrições aos campos personalizados incluem: <ul><li>Os nomes de campo não são sensíveis aos casos. Por exemplo, o serviço não pode diferenciar entre a coluna "ID" e a coluna "ID".</li><li>Campos aninhados não são permitidos. Em vez disso, use um pseudónimo na consulta de trabalho para "achatar" o campo.</li><li>Expressões não podem ser usadas como nome de campo.</li></ul> <br />Esta funcionalidade permite a utilização de configurações personalizadas de especificadores de formato data/hora no caminho. Os formatos de data e hora personalizados devem ser especificados um de cada vez, incluído si a {datetime: \< especificador>} palavra-chave. As inputs admissíveis para \< o especificador> são yyyy, MM, M, dd, d, HH, H, mm, m, ss ou s. A palavra-chave {datetime: \< especificador>} pode ser usada várias vezes no caminho para formar configurações de data/hora personalizadas. <br /><br />Exemplos: <ul><li>Exemplo 1: cluster1/logs/{date}/{time}</li><li>Exemplo 2: cluster1/logs/{date}</li><li>Exemplo 3: cluster1/{client_id}/{date}/{time}</li><li>Exemplo 4: cluster1/{datetime:ss}/{myField} onde está a consulta: SELECT data.myField AS myField FROM Input;</li><li>Exemplo 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />O carimbo temporal da estrutura de pasta criada segue a UTC e não a hora local.<br /><br />O nome de ficheiros utiliza a seguinte convenção: <br /><br />{Padrão de prefixo de caminho}/schemaHashcode_Guid_Number.extensão.<br /><br />Ficheiros de saída de exemplo:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Para obter mais informações sobre esta funcionalidade, consulte a divisão de saída de [blob personalizada do Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato de data | Opcional. Se o token da data for utilizado no caminho de prefixo, pode selecionar o formato de data no qual os seus ficheiros estão organizados. Exemplo: Yyyy/MM/DD |
| Formato de tempo | Opcional. Se o sinal de tempo for utilizado no caminho de prefixo, especifique o formato de tempo em que os seus ficheiros estão organizados. Atualmente, o único valor suportado é o HH. |
| Formato de serialização de eventos | Formato de serialização para dados de saída. JSON, CSV, Avro e Parquet são apoiados. |
|Filas mínimas (apenas Parquet)|O número de filas mínimas por lote. Para o Parquet, cada lote criará um novo ficheiro. O valor padrão atual é de 2.000 linhas e o máximo permitido é de 10.000 linhas.|
|Tempo máximo (apenas Parquet)|O tempo máximo de espera por lote. Após este tempo, o lote será escrito à saída mesmo que o requisito mínimo das linhas não seja cumprido. O valor padrão atual é de 1 minuto e o máximo permitido é de 2 horas. Se a sua saída de bolha tiver frequência de padrão de trajetória, o tempo de espera não pode ser superior ao intervalo de tempo de partição.|
| Encoding    | Se estiver a utilizar o formato CSV ou JSON, deve especificar-se uma codificação. O UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador   | Aplicável apenas para a serialização do CSV. O Stream Analytics suporta uma série de delimitadores comuns para serializar dados csv. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato      | Aplicável apenas para a serialização da JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **A matriz** especifica que a saída é formatada como uma variedade de objetos JSON. Esta matriz só é fechada quando o trabalho para ou o Stream Analytics passou para a janela da próxima vez. Em geral, é preferível usar jSON separado de linha, porque não requer qualquer manuseamento especial enquanto o ficheiro de saída ainda está a ser escrito. |

Quando estiver a usar o armazenamento Blob como saída, um novo ficheiro é criado na bolha nos seguintes casos:

* Se o ficheiro exceder o número máximo de blocos permitidos (atualmente 50.000). Pode atingir o número máximo permitido de blocos sem atingir o tamanho máximo permitido. Por exemplo, se a taxa de saída for elevada, pode ver mais bytes por bloco, e o tamanho do ficheiro é maior. Se a taxa de saída for baixa, cada bloco tem menos dados e o tamanho do ficheiro é menor.
* Se houver uma alteração de esquema na saída, e o formato de saída requer esquema fixo (CSV e Avro).
* Se um trabalho for reiniciado, seja externamente por um utilizador que o pare e o inicie, quer internamente para manutenção do sistema ou recuperação de erros.
* Se a consulta estiver totalmente dividida, e for criado um novo ficheiro para cada partição de saída.
* Se o utilizador apagar um ficheiro ou um recipiente da conta de armazenamento.
* Se a saída for dividida pelo tempo, utilizando o padrão de prefixo do caminho, e uma nova bolha for utilizada quando a consulta se move para a hora seguinte.
* Se a saída for dividida por um campo personalizado, e for criada uma nova bolha por chave de partição se não existir.
* Se a saída for dividida por um campo personalizado onde a chave de partilha da cardinalidade excede 8.000, e uma nova bolha é criada por chave de partição.

## <a name="event-hubs"></a>Hubs de Eventos

O serviço [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) é um evento de subscrição de editora altamente escalável ingestor. Pode recolher milhões de eventos por segundo. Uma utilização de um centro de eventos como saída é quando a saída de um trabalho de Stream Analytics se torna a entrada de outro trabalho de streaming. Para obter informações sobre o tamanho máximo da mensagem e otimização do tamanho do lote, consulte a secção de tamanho do lote de [saída.](#output-batch-size)

Precisa de alguns parâmetros para configurar os fluxos de dados dos centros de eventos como uma saída.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável usado em consultas para direcionar a saída de consulta para este centro de eventos. |
| Espaço de nomes do hub de eventos | Um recipiente para um conjunto de entidades de mensagens. Quando criou um novo centro de eventos, também criou um espaço de nome sem nome para eventos. |
| Nome do hub de eventos | O nome da saída do seu centro de eventos. |
| Nome da política do centro de eventos | A política de acesso partilhado, que pode criar no separador **Configure** do centro de eventos. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
| Chave política do centro de eventos | A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nome do centro do evento. |
| Coluna de chaves de partição | Opcional. Uma coluna que contém a chave de partição para a saída do centro de eventos. |
| Formato de serialização de eventos | O formato de serialização para dados de saída. JSON, CSV e Avro são apoiados. |
| Encoding | Para CSV e JSON, o UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador | Aplicável apenas para a serialização do CSV. O Stream Analytics suporta uma série de delimitadores comuns para serializar dados em formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato | Aplicável apenas para a serialização da JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **A matriz** especifica que a saída é formatada como uma variedade de objetos JSON.  |
| Colunas de propriedades | Opcional. Colunas separadas da vírvia que precisam de ser anexadas como propriedades do utilizador da mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção [Propriedades de metadados personalizados para saída](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

Pode utilizar [o Power BI](https://powerbi.microsoft.com/) como uma saída para um trabalho de Stream Analytics para proporcionar uma rica experiência de visualização dos resultados da análise. Você pode usar esta capacidade para dashboards operacionais, geração de relatórios e relatórios orientados por métricas.

A produção de Power BI da Stream Analytics não está atualmente disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International).

A tabela seguinte lista os nomes de propriedade e as suas descrições para configurar a sua saída power BI.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Forneça um nome amigável que é usado em consultas para direcionar a saída de consulta para esta saída power BI. |
| Espaço de trabalho em grupo |Para permitir a partilha de dados com outros utilizadores do Power BI, pode selecionar grupos dentro da sua conta Power BI ou escolher **o My Workspace** se não quiser escrever para um grupo. A atualização de um grupo existente requer a renovação da autenticação Power BI. |
| Nome do conjunto de dados |Forneça um nome de conjunto de dados que deseja que a saída power BI utilize. |
| Nome da tabela |Forneça um nome de tabela sob o conjunto de dados da saída Power BI. Atualmente, a produção de Power BI a partir de trabalhos stream analytics pode ter apenas uma tabela num conjunto de dados. |
| Autorizar a ligação | Tem de autorizar com o Power BI configurar as definições de saída. Assim que conceder este acesso ao seu painel power BI, pode revogar o acesso alterando a palavra-passe da conta de utilizador, apagando a saída de trabalho ou apagando o trabalho do Stream Analytics. | 

Para uma passagem pela configuração de uma saída e painel de instrumentos do Power BI, consulte o tutorial [Azure Stream Analytics e Power BI.](stream-analytics-power-bi-dashboard.md)

> [!NOTE]
> Não crie explicitamente o conjunto de dados e a tabela no painel power bi. O conjunto de dados e a tabela são automaticamente povoados quando o trabalho é iniciado e o trabalho começa a bombear a saída para o Power BI. Se a consulta de trabalho não gerar resultados, o conjunto de dados e a tabela não são criados. Se o Power BI já tinha um conjunto de dados e uma tabela com o mesmo nome que o fornecido neste trabalho de Stream Analytics, os dados existentes são substituídos.
>

### <a name="create-a-schema"></a>Criar um esquema
O Azure Stream Analytics cria um conjunto de dados power BI e um esquema de mesa para o utilizador se eles já não existirem. Em todos os outros casos, a tabela é atualizada com novos valores. Atualmente, apenas uma tabela pode existir dentro de um conjunto de dados. 

O Power BI utiliza a política de retenção do primeiro in, first-out (FIFO). Os dados serão recolhidos numa tabela até atingir 200.000 linhas.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converter um tipo de dados do Stream Analytics para O Power BI
O Azure Stream Analytics atualiza o modelo de dados dinamicamente no prazo de execução se o esquema de saída mudar. Alterações no nome da coluna, alterações do tipo da coluna e a adição ou remoção das colunas são todas rastreadas.

Esta tabela cobre as conversões do tipo de dados de [dados do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) para os tipos de Modelo de Dados de Entidades Power BI [(EDM),](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)se não existir um conjunto de dados e uma tabela power BI.

Da Stream Analytics | Para Power BI
-----|-----
bigint | Int64
nvarchar (max) | String
datetime | Datetime
float | Double
Conjunto de registos | Tipo de corda, valor constante "IRecord" ou "IArray"

### <a name="update-the-schema"></a>Atualizar o esquema
O Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Mais tarde, se necessário, o esquema do modelo de dados é atualizado para acomodar eventos que podem não se encaixar no esquema original.

Evite a consulta para evitar uma atualização dinâmica do `SELECT *` esquema entre linhas. Além das potenciais implicações de desempenho, pode resultar na incerteza do tempo devida para os resultados. Selecione os campos exatos que precisam de ser mostrados no painel power bi. Além disso, os valores dos dados devem estar em conformidade com o tipo de dados escolhido.


Anterior/corrente | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datetime | String | String |  Datetime | String

## <a name="table-storage"></a>Table Storage

[O armazenamento da Mesa Azure](../storage/common/storage-introduction.md) oferece armazenamento altamente disponível e massivamente escalável, de modo a que uma aplicação possa automaticamente escalar para satisfazer a procura do utilizador. O armazenamento de mesa é a loja de teclado/atributo da Microsoft, que pode utilizar para dados estruturados com menos restrições no esquema. O armazenamento da mesa azure pode ser usado para armazenar dados para persistência e recuperação eficiente.

A tabela seguinte lista os nomes da propriedade e as suas descrições para a criação de uma saída de tabela.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para este armazenamento de mesa. |
| Conta de armazenamento |O nome da conta de armazenamento para onde está a enviar a sua saída. |
| Chave da conta de armazenamento |A chave de acesso associada à conta de armazenamento. |
| Nome da tabela |O nome da mesa. A mesa é criada se não existir. |
| Chave de partição |O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador único para a partição dentro de uma tabela que forma a primeira parte da chave primária de uma entidade. É um valor de cadeia que pode ter até 1 KB de tamanho. |
| Chave de linha |O nome da coluna de saída que contém a chave da linha. A chave da linha é um identificador único para uma entidade dentro de uma partição. Forma a segunda parte da chave principal de uma entidade. A chave da linha é um valor de cadeia que pode ter até 1 KB de tamanho. |
| Tamanho do lote |O número de registos para uma operação de lote. O incumprimento (100) é suficiente para a maioria dos postos de trabalho. Consulte a especificação de [funcionamento](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) do lote de mesa para obter mais detalhes sobre a modificação desta definição. |

## <a name="service-bus-queues"></a>Filas do Service Bus

[As filas de ônibus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) de serviço oferecem uma entrega de mensagens FIFO a um ou mais consumidores concorrentes. Normalmente, as mensagens são recebidas e processadas pelos recetores na ordem temporal em que foram adicionadas à fila. Cada mensagem é recebida e processada por apenas uma mensagem consumidora.

Em [cocompatibilidade nível 1.2,](stream-analytics-compatibility-level.md)o Azure Stream Analytics utiliza o protocolo avançado de mensagens [de fila de mensagens (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) para escrever para service bus queues e tópicos. A AMQP permite-lhe construir aplicações híbridas e de plataforma cruzada utilizando um protocolo padrão aberto.

A tabela seguinte lista os nomes da propriedade e as suas descrições para criar uma saída de fila.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para esta fila de ônibus de serviço. |
| Espaço de nome de ônibus de serviço |Um recipiente para um conjunto de entidades de mensagens. |
| Nome da fila |O nome da fila do Autocarro de Serviço. |
| Nome da política da fila |Ao criar uma fila, também pode criar políticas de acesso partilhado no separador **Configure** da fila. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
| Chave política de fila |A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nome do Ônibus de serviço. |
| Formato de serialização de eventos |O formato de serialização para dados de saída. JSON, CSV e Avro são apoiados. |
| Encoding |Para CSV e JSON, o UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador |Aplicável apenas para a serialização do CSV. O Stream Analytics suporta uma série de delimitadores comuns para serializar dados em formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato |Aplicável apenas para o tipo JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **A matriz** especifica que a saída é formatada como uma variedade de objetos JSON. |
| Colunas de propriedades | Opcional. Colunas separadas da vírvia que precisam de ser anexadas como propriedades do utilizador da mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção [Propriedades de metadados personalizados para saída](#custom-metadata-properties-for-output). |
| Colunas de propriedade do sistema | Opcional. Par de valor chave de Propriedades do Sistema e nomes de colunas correspondentes que precisam de ser anexados à mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção Propriedades do Sistema para a fila de ônibus de [serviço e saídas tópicos](#system-properties-for-service-bus-queue-and-topic-outputs)  |

O número de divisórias [baseia-se no SKU e tamanho do ônibus de serviço.](../service-bus-messaging/service-bus-partitioning.md) A chave de partição é um valor inteiro único único para cada partição.

## <a name="service-bus-topics"></a>Tópicos de Service Bus
As filas de ônibus de serviço fornecem um método de comunicação de um para um, do remetente para o recetor. [Os tópicos](https://msdn.microsoft.com/library/azure/hh367516.aspx) de ônibus de serviço fornecem uma forma de comunicação de um a muitos.

A tabela seguinte lista os nomes da propriedade e as suas descrições para a criação de uma saída de tópico de ônibus de serviço.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para este tópico de Ônibus de serviço. |
| Espaço de nome de ônibus de serviço |Um recipiente para um conjunto de entidades de mensagens. Quando criou um novo centro de eventos, também criou um espaço de nome service Bus. |
| Nome tópico |Os tópicos são entidades de mensagens, semelhantes a centros de eventos e filas. São projetados para recolher fluxos de eventos de dispositivos e serviços. Quando um tópico é criado, também é dado um nome específico. As mensagens enviadas para um tópico não estão disponíveis a menos que seja criada uma subscrição, por isso certifique-se de que há uma ou mais subscrições sob o tema. |
| Nome da política do tópico |Ao criar um tópico de Ônibus de serviço, também pode criar políticas de acesso partilhado no separador **Configurado** do tópico. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
| Chave da política do tópico |A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nome do Ônibus de serviço. |
| Formato de serialização de eventos |O formato de serialização para dados de saída. JSON, CSV e Avro são apoiados. |
| Encoding |Se estiver a utilizar o formato CSV ou JSON, deve especificar-se uma codificação. O UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador |Aplicável apenas para a serialização do CSV. O Stream Analytics suporta uma série de delimitadores comuns para serializar dados em formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Colunas de propriedades | Opcional. Colunas separadas da vírvia que precisam de ser anexadas como propriedades do utilizador da mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção [Propriedades de metadados personalizados para saída](#custom-metadata-properties-for-output). |
| Colunas de propriedade do sistema | Opcional. Par de valor chave de Propriedades do Sistema e nomes de colunas correspondentes que precisam de ser anexados à mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção Propriedades do Sistema para a fila de ônibus de [serviço e saídas tópicos](#system-properties-for-service-bus-queue-and-topic-outputs) |

O número de divisórias [baseia-se no SKU e tamanho do ônibus de serviço.](../service-bus-messaging/service-bus-partitioning.md) A chave divisória é um valor inteiro único único para cada partição.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[O Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um serviço de base de dados distribuído globalmente que oferece uma escala elástica ilimitada em todo o mundo, consulta rica e indexação automática sobre modelos de dados schema-agnósticos. Para saber sobre as opções de contentores Azure Cosmos DB para stream analytics, consulte o [Stream Analytics com o Azure Cosmos DB como](stream-analytics-documentdb-output.md) artigo de saída.

A produção da Azure Cosmos DB da Stream Analytics não está atualmente disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International).

> [!Note]
> Neste momento, o Azure Stream Analytics apenas suporta a ligação ao Azure Cosmos DB utilizando a API SQL.
> Outras APIs DB da Azure Cosmos ainda não são apoiadas. Se apontar o Azure Stream Analytics para as contas DoB Do MB Azure criadas com outras APIs, os dados podem não estar devidamente armazenados.

A tabela seguinte descreve as propriedades para criar uma saída De Db Azure Cosmos.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um pseudónimo para remeter esta saída na sua consulta stream analytics. |
| Sink | Azure Cosmos DB. |
| Opção de Importar | Escolha **selecione Cosmos DB a partir da sua subscrição** ou **forneça as definições de Cosmos DB manualmente**.
| ID da Conta | O nome ou ponto final URI da conta Azure Cosmos DB. |
| Chave de conta | A chave de acesso partilhado para a conta Azure Cosmos DB. |
| Base de Dados | O nome da base de dados Azure Cosmos DB. |
| Nome do contentor | O nome do recipiente a utilizar, que deve existir em Cosmos DB. Exemplo:  <br /><ul><li> _MyContainer_: Deve existir um recipiente chamado "MyContainer".</li>|
| Id documento |Opcional. O nome do campo em eventos de saída que é usado para especificar a chave primária em que as operações de inserção ou atualização são baseadas.

## <a name="azure-functions"></a>Funções do Azure
O Azure Functions é um serviço de computação sem servidor especado que pode utilizar para executar código a pedido sem ter de fornecer ou gerir explicitamente a infraestrutura. Permite-lhe implementar o código que é desencadeado por eventos que ocorrem em serviços Azure ou parceiros. Esta capacidade das Funções Azure para responder aos gatilhos torna-a uma saída natural para o Azure Stream Analytics. Este adaptador de saída permite que os utilizadores liguem o Stream Analytics às Funções Azure e executem um script ou peça de código em resposta a uma variedade de eventos.

A produção da Azure Functions do Stream Analytics não está atualmente disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International).

O Azure Stream Analytics invoca funções Azure através de gatilhos HTTP. O adaptador de saída funções Azure está disponível com as seguintes propriedades configuráveis:

| Nome da propriedade | Descrição |
| --- | --- |
| Function app |O nome da sua aplicação Funções Azure. |
| Função |O nome da função na sua aplicação Funções Azure. |
| Chave |Se pretender utilizar uma Função Azure a partir de outra subscrição, pode fazê-lo fornecendo a chave para aceder à sua função. |
| Tamanho máximo do lote |Uma propriedade que permite definir o tamanho máximo para cada lote de saída que é enviado para a sua função Azure. A unidade de entrada está em bytes. Por predefinição, este valor é de 262.144 bytes (256 KB). |
| Contagem máxima de lote  |Uma propriedade que permite especificar o número máximo de eventos em cada lote que é enviado para funções Azure. O valor predefinido é 100. |

O Azure Stream Analytics espera o status HTTP 200 da aplicação Functions para lotes que foram processados com sucesso.

Quando o Azure Stream Analytics recebe uma exceção 413 ("http Request Entity Too Large") de uma função Azure, reduz o tamanho dos lotes que envia para as Funções Azure. No seu código de função Azure, utilize esta exceção para se certificar de que o Azure Stream Analytics não envia lotes de grandes dimensões. Além disso, certifique-se de que a contagem máxima de lote e os valores de tamanho utilizados na função são consistentes com os valores introduzidos no portal Stream Analytics.

> [!NOTE]
> Durante a ligação de teste, o Stream Analytics envia um lote vazio às Funções Azure para testar se a ligação entre os dois funciona. Certifique-se de que a sua aplicação Functions lida com pedidos de lote vazios para garantir que a ligação de teste passa.

Além disso, numa situação em que não há evento sacar numa janela temporal, não é gerada nenhuma saída. Como resultado, a função **computeResult** não é chamada. Este comportamento é consistente com as funções agregadas incorporadas.

## <a name="custom-metadata-properties-for-output"></a>Propriedades personalizadas de metadados para saída 

Pode anexar colunas de consulta como propriedades do utilizador às suas mensagens de saída. Estas colunas não entram na carga. As propriedades estão presentes sob a forma de um dicionário na mensagem de saída. *A chave* é o nome e *valor* da coluna é o valor da coluna no dicionário de propriedades. Todos os tipos de dados do Stream Analytics são suportados, exceto o Record e o Array.  

Saídas suportadas: 
* Fila do Service Bus 
* Tópico do Service Bus 
* Hub de eventos 

No exemplo seguinte, adicionamos os dois campos `DeviceId` e `DeviceStatus` os metadados. 
* Consulta:`select *, DeviceId, DeviceStatus from iotHubInput`
* Configuração de saída:`DeviceId,DeviceStatus`

![Colunas de propriedades](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

A imagem que se segue mostra as propriedades da mensagem de saída inspecionadas no EventHub através do [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Propriedades personalizadas do evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Propriedades do sistema para filas de ônibus de serviço e saídas tópicos 
Pode anexar colunas de consulta como propriedades do [sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) à sua fila de autocarros de serviço de saída ou mensagens tópicos. Estas colunas não entram na carga útil, em vez disso, a propriedade correspondente do [sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) BrokeredMessage é povoada com os valores da coluna de consulta.
Estas propriedades do sistema são suportadas - `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc` .
Os valores de cadeia destas colunas são analisados como o tipo de valor de propriedade do sistema correspondente e quaisquer falhas de análise são tratadas como erros de dados.
Este campo é fornecido como um formato de objeto JSON. Os detalhes sobre este formato são os seguintes -
* Rodeado por aparelhos {} encaracolados.
* Escrito em pares chave/valor.
* Chaves e valores devem ser cordas.
* A chave é o nome e valor do sistema é o nome da coluna de consulta.
* Chaves e valores são separados por um cólon.
* Cada par chave/valor é separado por uma vírem.

Isto mostra como usar esta propriedade –

* Consulta:`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Colunas de propriedade do sistema:`{ "MessageId": "column1", "PartitionKey": "column2"}`

Isto define as mensagens de fila de `MessageId` autocarros de serviço com `column1` valores e partitionKey é definido com `column2` os valores .

## <a name="partitioning"></a>Criação de partições

A tabela seguinte resume o suporte de partição e o número de autores de saída para cada tipo de saída:

| Tipo de saída | Apoio à partilha | Chave de partição  | Número de escritores de saída |
| --- | --- | --- | --- |
| Azure Data Lake Store | Sim | Utilize {date} e {time} tokens no padrão de prefixo do caminho. Escolha o formato de data, tais como YYYY/MM/DD, DD/MM/YYYY ou MM-DD-YYYY. HH é usado para o formato de tempo. | Segue a divisão de entrada para [consultas totalmente paralelas.](stream-analytics-scale-jobs.md) |
| Base de Dados SQL do Azure | Sim, precisa de ser ativado. | Com base na cláusula PARTITION BY na consulta. | Quando a opção herdar a partilha estiver ativada, segue a divisão de entrada para [consultas totalmente paralelas](stream-analytics-scale-jobs.md). Para saber mais sobre como obter um melhor desempenho de produção de escrita quando estiver a carregar dados para a Base de Dados Azure SQL, consulte a saída do Azure Stream Analytics para a Base de [Dados Azure SQL](stream-analytics-sql-output-perf.md). |
| Armazenamento de Blobs do Azure | Sim | Utilize fichas {date} e {time} dos campos de eventos no padrão do caminho. Escolha o formato de data, tais como YYYY/MM/DD, DD/MM/YYYY ou MM-DD-YYYY. HH é usado para o formato de tempo. A saída blob pode ser dividida por um único atributo de evento personalizado {fieldname} ou {datetime: \< especificador>}. | Segue a divisão de entrada para [consultas totalmente paralelas.](stream-analytics-scale-jobs.md) |
| Azure Event Hubs | Sim | Sim | Varia dependendo do alinhamento da divisória.<br /> Quando a chave de partição para a saída do hub de eventos está igualmente alinhada com o passo de consulta a montante (anterior), o número de escritores é o mesmo que o número de divisórias na saída do centro de eventos. Cada escritor usa a [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) para enviar eventos para a partição específica. <br /> Quando a chave de partição para a saída do centro de eventos não está alinhada com o passo de consulta a montante (anterior), o número de escritores é o mesmo que o número de divisórias nesse passo anterior. Cada escritor usa a [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) no **EventHubClient** para enviar eventos para todas as divisórias de saída. |
| Power BI | Não | Nenhum | Não aplicável. |
| Armazenamento de Tabelas do Azure | Sim | Qualquer coluna de saída.  | Segue a divisão de entrada para [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |
| Tópico do Azure Service Bus | Sim | Escolhido automaticamente. O número de divisórias baseia-se no SKU e tamanho do [ônibus de serviço.](../service-bus-messaging/service-bus-partitioning.md) A chave divisória é um valor inteiro único único para cada partição.| O mesmo que o número de divisórias no tópico de saída.  |
| Fila do Azure Service Bus | Sim | Escolhido automaticamente. O número de divisórias baseia-se no SKU e tamanho do [ônibus de serviço.](../service-bus-messaging/service-bus-partitioning.md) A chave divisória é um valor inteiro único único para cada partição.| O mesmo que o número de divisórias na fila de saída. |
| Azure Cosmos DB | Sim | Com base na cláusula PARTITION BY na consulta. | Segue a divisão de entrada para [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |
| Funções do Azure | Sim | Com base na cláusula PARTITION BY na consulta. | Segue a divisão de entrada para [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |

O número de autores de saída também pode ser controlado usando `INTO <partition count>` a cláusula (ver [INTO)](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)na sua consulta, o que pode ser útil para alcançar uma topologia de emprego desejada. Se o adaptador de saída não for dividido, a falta de dados numa divisória de entrada causará um atraso até ao período de tempo de chegada tardio. Nesses casos, a saída é fundida a um único escritor, o que pode causar estrangulamentos no seu oleoduto. Para saber mais sobre a política de chegada tardia, consulte as considerações de ordem de [eventos do Azure Stream Analytics.](stream-analytics-out-of-order-and-late-events.md)

## <a name="output-batch-size"></a>Tamanho do lote de saída
O Azure Stream Analytics utiliza lotes de tamanho variável para processar eventos e escrever para saídas. Normalmente, o motor Stream Analytics não escreve uma mensagem de cada vez e utiliza lotes para eficiência. Quando a taxa dos eventos de entrada e saída é elevada, o Stream Analytics utiliza lotes maiores. Quando a taxa de saída é baixa, utiliza lotes menores para manter a latência baixa.

O quadro seguinte explica algumas das considerações relativas ao loteamento de saída:

| Tipo de saída |    Tamanho da mensagem má | Otimização do tamanho do lote |
| :--- | :--- | :--- |
| Azure Data Lake Store | Consulte [os limites](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits)de armazenamento do Lago de Dados . | Utilize até 4 MB por operação de escrita. |
| Base de Dados SQL do Azure | Configurável utilizando a contagem de lotes Max. 10.000 filas máximas e 100 linhas mínimas por inserção a granel por defeito por defeito.<br />Consulte [os limites Azure SQL](../sql-database/sql-database-resource-limits.md). |  Cada lote é inicialmente inserido a granel com a contagem máxima de lote. O lote é dividido ao meio (até a contagem mínima de lote) com base em erros retáveis da SQL. |
| Armazenamento de Blobs do Azure | Consulte os limites de [armazenamento azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | O tamanho máximo do bloco de bolhas é de 4 MB.<br />A contagem máxima de blob bock é de 50.000. |
| Azure Event Hubs    | 256 KB ou 1 MB por mensagem. <br />Ver limites de Centros de [Eventos](../event-hubs/event-hubs-quotas.md). |    Quando a partilha de entrada/saída não está alinhada, cada evento é embalado individualmente `EventData` e enviado num lote de até ao tamanho máximo da mensagem. Isto também acontece se forem utilizadas [propriedades de metadados personalizados.](#custom-metadata-properties-for-output) <br /><br />  Quando a divisão de entrada/saída está alinhada, vários eventos são embalados numa única `EventData` instância, até ao tamanho máximo da mensagem, e enviados.    |
| Power BI | Ver [limites de API power BI Rest](https://msdn.microsoft.com/library/dn950053.aspx). |
| Armazenamento de Tabelas do Azure | Consulte os limites de [armazenamento azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | O predefinido é de 100 entidades por transação única. Pode configurá-lo a um valor menor, se necessário. |
| Fila do Azure Service Bus    | 256 KB por mensagem para o nível Standard, 1MB para o nível Premium.<br /> Ver limites de [ônibus de serviço](../service-bus-messaging/service-bus-quotas.md). | Use um único evento por mensagem. |
| Tópico do Azure Service Bus | 256 KB por mensagem para o nível Standard, 1MB para o nível Premium.<br /> Ver limites de [ônibus de serviço](../service-bus-messaging/service-bus-quotas.md). | Use um único evento por mensagem. |
| Azure Cosmos DB    | Ver [limites de DB do Azure Cosmos.](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits) | O tamanho do lote e a frequência de escrita são ajustados dinamicamente com base nas respostas do Azure Cosmos DB. <br /> Não existem limitações pré-determinadas do Stream Analytics. |
| Funções do Azure    | | O tamanho do lote padrão é de 262.144 bytes (256 KB). <br /> A contagem de eventos padrão por lote é de 100. <br /> O tamanho do lote é configurável e pode ser aumentado ou diminuído nas [opções](#azure-functions)de saída do Stream Analytics .

## <a name="next-steps"></a>Passos seguintes
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
