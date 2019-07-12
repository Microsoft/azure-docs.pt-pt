---
title: Compreender as saídas do Azure Stream Analytics
description: Este artigo descreve as opções de saída de dados disponíveis no Azure Stream Analytics, incluindo o Power BI para os resultados da análise.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: ef2a55b377c2ca48b9417310926a014a82f679d7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621888"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Compreender as saídas do Azure Stream Analytics

Este artigo descreve os tipos de saídas disponíveis para uma tarefa do Azure Stream Analytics. Saídas permitem-lhe armazenar e guardar os resultados da tarefa do Stream Analytics. Ao utilizar os dados de saída, pode fazer ainda mais análises de negócio e o armazenamento de dados dos seus dados.

Ao conceber a sua consulta do Stream Analytics, consulte o nome da saída, utilizando o [cláusula INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Pode usar uma única saída por tarefa ou várias saídas por transmissão em fluxo de trabalho (se necessário), fornecendo várias cláusulas INTO na consulta.

Para criar, editar e testar a tarefa do Stream Analytics produz, pode utilizar o [portal do Azure](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), e [Visual Studio](stream-analytics-quick-create-vs.md).

Algum suporte de tipos de saídas [particionamento](#partitioning). [Tamanhos de lote de saída](#output-batch-size) variar para otimizar o débito.


## <a name="azure-data-lake-storage-gen-1"></a>Armazenamento do Azure Data Lake Ger 1

Stream Analytics suporta [do Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Armazenamento do Azure Data Lake é um repositório de hiperescala para cargas de trabalho de análise de macrodados em toda a empresa. Pode utilizar o armazenamento do Data Lake para armazenar dados de qualquer tamanho, tipo e velocidade de ingestão para análises exploratórias e operacionais. Stream Analytics tem de estar autorizado a aceder ao armazenamento do Data Lake.

Saída de armazenamento do Azure Data Lake do Stream Analytics não está atualmente disponível nas regiões do Azure Alemanha (T-Systems International) e Azure China 21Vianet.

A tabela seguinte lista os nomes das propriedades e suas descrições para configurar a saída de armazenamento Gen 1 do Data Lake.   

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para o Data Lake Store. |
| Subscription | A subscrição que contém a sua conta de armazenamento do Azure Data Lake. |
| Nome da conta | O nome da conta do Data Lake Store, onde está a enviar a saída. É apresentada uma lista pendente de contas do Data Lake Store que estão disponíveis na sua subscrição. |
| Padrão do prefixo do caminho | O caminho do ficheiro que é utilizado para escrever ficheiros na conta do Data Lake Store especificado. Pode especificar uma ou mais instâncias de {date} e {time} variáveis:<br /><ul><li>Exemplo 1: pasta1/registos / {data} / {time}</li><li>Exemplo 2: pasta1/registos / {data}</li></ul><br />O carimbo de data / hora da estrutura de pasta criada segue UTC e não a hora local.<br /><br />Se o padrão de caminho de ficheiro não contém uma barra (/), o último padrão no caminho de ficheiro é tratado como um prefixo de nome de ficheiro. <br /><br />Nessas circunstâncias, são criados novos ficheiros:<ul><li>Alteração no esquema de saída</li><li>Reinício externo ou interno de uma tarefa</li></ul> |
| Formato de data | Opcional. Se o token de data é utilizado no caminho de prefixo, pode selecionar o formato de data em que os ficheiros estão organizados. Exemplo: DD/MM/AAAA |
|Formato de hora | Opcional. Se o token de tempo é utilizado no caminho de prefixo, especifique o formato de hora em que os ficheiros estão organizados. Atualmente, o único valor suportado é HH. |
| Formato de serialização de eventos | O formato de serialização para dados de saída. JSON, CSV e Avro são suportados.|
| Codificação | Se estiver a utilizar o formato JSON ou CSV, uma codificação tem de ser especificada. UTF-8 é o único formato de codificação suportado neste momento.|
| Delimitador | Aplicável apenas para a serialização de CSV. Stream Analytics suporta um número de delimitadores comuns para serializar os dados do CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical.|
| Formato | Aplicável apenas para a serialização do JSON. **Separado por linhas** Especifica que o resultado é formatado fazendo com que cada objeto JSON separado por uma nova linha. **Matriz** Especifica que o resultado é formatado como uma matriz de objetos JSON. Essa matriz só é fechada quando as paradas de tarefa ou o Stream Analytics tem movido para a próxima janela de tempo. Em geral, é preferível utilizar o JSON de linha separados, pois ele não requer tratamento especial, enquanto o ficheiro de saída ainda está a ser escrito.|
| Modo de autenticação | Pode autorizar o acesso à sua conta de armazenamento do Data Lake com [identidade gerido](stream-analytics-managed-identities-adls.md) ou token de utilizador. Depois de conceder acesso, pode revogar o acesso ao alterar a palavra-passe da conta de utilizador, a eliminar a saída de armazenamento do Data Lake para esta tarefa ou a eliminação da tarefa do Stream Analytics. |

## <a name="sql-database"></a>SQL Database

Pode usar [base de dados do Azure SQL](https://azure.microsoft.com/services/sql-database/) como uma saída de dados que é de natureza relacionais ou de aplicativos que dependem de conteúdo a ser alojado numa base de dados relacional. Tarefas do Stream Analytics gravar numa tabela existente na base de dados SQL. O esquema da tabela deve corresponder exatamente os campos e seus tipos de dados de saída da tarefa. Também pode especificar [do Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) como uma saída através da base de dados SQL a opção de saída. Para saber mais sobre formas de melhorar o débito de escrita, consulte a [Stream Analytics com o Azure SQL Database como saída](stream-analytics-sql-output-perf.md) artigo.

A tabela seguinte lista os nomes de propriedade e a respetiva descrição para a criação de uma base de dados SQL de saída.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para esta base de dados. |
| Base de Dados | O nome da base de dados em que está a enviar a saída. |
| Nome do servidor | O nome do servidor de base de dados SQL. |
| Nome de utilizador | O nome de utilizador que tenha acesso de escrita para a base de dados. Stream Analytics suporta apenas a autenticação do SQL. |
| Palavra-passe | A palavra-passe para ligar à base de dados. |
| Tabela | O nome da tabela em que o resultado é escrito. O nome da tabela diferencia maiúsculas de minúsculas. O esquema desta tabela deve corresponder exatamente ao número de campos e seus tipos que gera a saída de tarefa. |
|Herdam o esquema de partição| Uma opção para herdar o esquema de particionamento de seu passo de consulta anterior, para ativar a topologia totalmente paralela com vários gravadores à tabela. Para obter mais informações, consulte [saída de Azure Stream Analytics para a base de dados do Azure SQL](stream-analytics-sql-output-perf.md).|
|Contagem máxima de lotes| O limite superior recomendado, o número de registos enviados com cada em massa de inserir a transação.|

> [!NOTE]
> O Azure SQL Database oferta é suportada para uma saída da tarefa de Stream Analytics, mas a máquina virtual do Azure com o SQL Server com uma base de dados anexados ou numa instância gerida do SQL Azure ainda não é suportado. Trata-se sujeita a alterações em versões futuras.

## <a name="blob-storage"></a>Armazenamento de blobs

Armazenamento de Blobs do Azure oferece uma solução económica e dimensionável para armazenar grandes quantidades de dados não estruturados na cloud. Para obter uma introdução no armazenamento de BLOBs e a sua utilização, consulte [carregar, transferir e listar os blobs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

A tabela seguinte lista os nomes de propriedade e suas descrições para a criação de uma saída de Blobs.

| Nome da propriedade       | Descrição                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de saída        | Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este armazenamento de Blobs. |
| Conta de armazenamento     | O nome da conta de armazenamento em que está a enviar a saída.               |
| Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento.                              |
| Contentor de armazenamento   | Um agrupamento lógico para blobs armazenados no serviço de Blobs do Azure. Ao carregar um blob para o serviço de BLOBs, tem de especificar um contentor para esse blob. |
| Padrão do caminho | Opcional. O padrão do caminho de ficheiro que é utilizado para escrever os blobs no contentor especificado. <br /><br /> O padrão de caminho, pode optar por utilizar uma ou mais instâncias das variáveis de data e hora para especificar a frequência com que os blobs são escritos: <br /> {date}, {time} <br /><br />Pode utilizar a criação de partições de blob personalizado para especificar um nome personalizado {campo} a partir dos seus dados de eventos para blobs de partição. O nome do campo seja alfanumérico e pode incluir espaços, hífenes e carateres de sublinhado. Restrições em campos personalizados incluem o seguinte: <ul><li>Os nomes de campos não são maiúsculas e minúsculas. Por exemplo, o serviço não consegue diferenciar entre a coluna "ID" e coluna "id".</li><li>Campos aninhados não são permitidos. Em vez disso, utilize um alias a consulta da tarefa para o campo "aplainar".</li><li>As expressões não podem ser utilizadas como um nome de campo.</li></ul> <br />Esta funcionalidade permite a utilização de configurações de especificador de formato de data/hora personalizada no caminho. Personalizado data e hora formatos tem de ser especificado um de cada vez, entre o {datetime:\<especificador >} palavra-chave. Entradas permitidas para \<especificador > são AAAA, MM, M, dd, d, HH, H, mm, m, ss ou s. O {datetime:\<especificador >} palavra-chave pode ser utilizada várias vezes no caminho para formar a configurações de data/hora personalizada. <br /><br />Exemplos: <ul><li>Exemplo 1: cluster1/registos / {data} / {time}</li><li>Exemplo 2: cluster1/registos / {data}</li><li>Exemplo 3: cluster1 / {client_id} / {data} / {time}</li><li>Exemplo 4: cluster1 / {datetime:ss} / {myField} em que a consulta é: SELECT data.myField AS myField FROM Input;</li><li>Exemplo 5: cluster1/ano = {datetime:yyyy} / mês = {datetime:MM} / dia = {datetime:dd}</ul><br />O carimbo de data / hora da estrutura de pasta criada segue UTC e não a hora local.<br /><br />Nomes de arquivos utiliza a seguinte convenção: <br /><br />{Caminho Pattern}/schemaHashcode_Guid_Number.extension de prefixo<br /><br />Ficheiros de saída de exemplo:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Para obter mais informações sobre esta funcionalidade, consulte [criação de partições de saída de Blobs do Azure Stream Analytics personalizado](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato de data | Opcional. Se o token de data é utilizado no caminho de prefixo, pode selecionar o formato de data em que os ficheiros estão organizados. Exemplo: DD/MM/AAAA |
| Formato de hora | Opcional. Se o token de tempo é utilizado no caminho de prefixo, especifique o formato de hora em que os ficheiros estão organizados. Atualmente, o único valor suportado é HH. |
| Formato de serialização de eventos | Formato de serialização para dados de saída. JSON, CSV e Avro são suportados. |
| Codificação    | Se estiver a utilizar o formato JSON ou CSV, uma codificação tem de ser especificada. UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador   | Aplicável apenas para a serialização de CSV. Stream Analytics suporta um número de delimitadores comuns para serializar os dados do CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato      | Aplicável apenas para a serialização do JSON. **Separado por linhas** Especifica que o resultado é formatado fazendo com que cada objeto JSON separado por uma nova linha. **Matriz** Especifica que o resultado é formatado como uma matriz de objetos JSON. Essa matriz só é fechada quando as paradas de tarefa ou o Stream Analytics tem movido para a próxima janela de tempo. Em geral, é preferível utilizar o JSON de linha separados, pois ele não requer tratamento especial, enquanto o ficheiro de saída ainda está a ser escrito. |

Quando estiver a utilizar o armazenamento de BLOBs como resultado, é criado um novo ficheiro no blob nos seguintes casos:

* Se o ficheiro excede o número máximo de blocos permitidos (atualmente 50.000). Atinja o número máximo permitido de blocos sem atingir o tamanho do blob permitido máximo. Por exemplo, se a taxa de saída é alta, pode ver mais bytes por bloco e o tamanho de ficheiro é maior. Se a taxa de saída é baixa, cada bloco tem menos dados e o tamanho de ficheiro é menor.
* Se houver uma alteração de esquema na saída, e o formato de saída requer o esquema fixo (CSV e Avro).
* Se uma tarefa for reiniciada, externamente por um utilizador pará-la e iniciá-lo, ou internamente para recuperação de manutenção ou de erro do sistema.
* Se a consulta estiver particionada totalmente e um novo ficheiro é criado para cada partição de saída.
* Se o utilizador elimina um ficheiro ou de um contentor da conta de armazenamento.
* Se o resultado é particionado usando o padrão de prefixo do caminho de tempo e um novo blob é utilizado quando a consulta é movido para a hora seguinte.
* Se a saída particionada um campo personalizado e um blob novo é criado por if de chave de partição não existe.
* Se a saída é particionada por um campo personalizado em que a partição chave cardinalidade excede 8.000 e um blob novo é criado por chave de partição.

## <a name="event-hubs"></a>Hubs de Eventos

O [os Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) serviço é altamente escaláveis de publicação-subscrição ingestor de eventos. Esta possa recolher a milhões de eventos por segundo. Um uso de um hub de eventos como saída é quando o resultado de uma tarefa do Stream Analytics torna-se a entrada de outra tarefa de transmissão em fluxo.

Precisa de alguns parâmetros para configurar fluxos de dados dos hubs de eventos como uma saída.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este hub de eventos. |
| Espaço de nomes do hub de eventos | Um contentor para um conjunto de entidades de mensagens. Quando tiver criado um novo hub de eventos, também criou um espaço de nomes do hub de eventos. |
| Nome do hub de eventos | O nome da sua saída do hub de eventos. |
| Nome de política do hub de eventos | A política de acesso partilhado, o que pode criar no hub de eventos **configurar** separador. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. |
| Chave de política do hub de eventos | A chave de acesso partilhado que é utilizada para autenticar o acesso para o espaço de nomes do hub de eventos. |
| Coluna de chave de partição | Opcional. Uma coluna que contém a chave de partição para a saída do hub de eventos. |
| Formato de serialização de eventos | O formato de serialização para dados de saída. JSON, CSV e Avro são suportados. |
| Codificação | Para CSV e JSON, o UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador | Aplicável apenas para a serialização de CSV. O Stream Analytics suporta um número de delimitadores comuns para serializar dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato | Aplicável apenas para a serialização do JSON. **Separado por linhas** Especifica que o resultado é formatado fazendo com que cada objeto JSON separado por uma nova linha. **Matriz** Especifica que o resultado é formatado como uma matriz de objetos JSON. Essa matriz só é fechada quando as paradas de tarefa ou o Stream Analytics tem movido para a próxima janela de tempo. Em geral, é preferível utilizar o JSON de linha separados, pois ele não requer tratamento especial, enquanto o ficheiro de saída ainda está a ser escrito. |
| Colunas de propriedade | Opcional. Colunas separados por vírgulas que precisem de ser anexados como propriedades de utilizador de mensagem de saída, em vez do payload. Obter mais informações sobre esta funcionalidade estão na seção [propriedades de metadados personalizadas para saída](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

Pode usar [Power BI](https://powerbi.microsoft.com/) como uma saída de uma tarefa do Stream Analytics para fornecer uma experiência Rica visualização dos resultados da análise. Pode utilizar esta capacidade para dashboards operacionais, geração de relatórios e controlado por métrica de relatório.

Saída do Power BI do Stream Analytics não está atualmente disponível nas regiões do Azure Alemanha (T-Systems International) e Azure China 21Vianet.

A tabela seguinte lista os nomes das propriedades e suas descrições para configurar a saída do Power BI.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Forneça um nome amigável que é utilizado em consultas para direcionar o resultado da consulta para este resultado do Power BI. |
| Área de trabalho de Grupo |Para ativar a partilha de dados com outros utilizadores do Power BI, pode selecionar grupos dentro de sua conta do Power BI ou escolher **minha área de trabalho** se não quiser escrever para um grupo. A atualizar um grupo existente requer a autenticação do Power BI a renovar. |
| Nome do conjunto de dados |Forneça um nome de conjunto de dados que pretende que a saída do Power BI a utilizar. |
| Nome da tabela |Forneça um nome de tabela em que o conjunto de dados de saída do Power BI. Atualmente, a saída do Power BI a partir de tarefas do Stream Analytics pode ter apenas uma tabela num conjunto de dados. |
| Autorizar a ligação | Terá de autorizar com o Power BI para configurar as definições de saída. Depois de conceder esta saída acesso ao seu dashboard do Power BI, pode revogar o acesso ao alterar a palavra-passe da conta de utilizador, a eliminar o resultado da tarefa ou a eliminação da tarefa do Stream Analytics. | 

Para obter instruções de configuração de um resultado do Power BI e o dashboard, consulte a [do Azure Stream Analytics e o Power BI](stream-analytics-power-bi-dashboard.md) tutorial.

> [!NOTE]
> Explicitamente não crie o conjunto de dados e a tabela no dashboard do Power BI. O conjunto de dados e a tabela são preenchidas automaticamente quando a tarefa é iniciada e a tarefa é iniciada a saída de bombagem para o Power BI. Se a consulta da tarefa não gera nenhum resultado, o conjunto de dados e a tabela não são criados. Se o Power BI já tinha um conjunto de dados e uma tabela com o mesmo nome daquele que foi fornecido nesta tarefa de Stream Analytics, os dados existentes são substituídos.
>

### <a name="create-a-schema"></a>Criar um esquema
O Azure Stream Analytics cria um esquema de conjunto de dados e de tabela do Power BI para o utilizador se ainda não existam. Todos os outros casos, a tabela é atualizada com novos valores. Atualmente, pode existir apenas uma tabela dentro de um conjunto de dados. 

O Power BI utiliza a política de retenção first in, First Out (FIFO). Recolha dados numa tabela de até atingir 200 000 linhas.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converter um tipo de dados do Stream Analytics para o Power BI
O Azure Stream Analytics atualiza o modelo de dados dinamicamente no tempo de execução, se alterar o esquema de saída. Todas as alterações de nome de coluna, alterações de tipo de coluna e a adição ou remoção de colunas são controlados.

Esta tabela abrange as conversões de tipo de dados do [tipos de dados do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) para o Power BI [tipos de modelo de dados de entidade (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), se um conjunto de dados do Power BI e a tabela não existem.

Do Stream Analytics | Para o Power BI
-----|-----
bigint | Int64
nvarchar (Max) | Cadeia
datetime | Datetime
float | Valor de duplo
Matriz de registo | Cadeia de caracteres de tipo, o valor constante "IRecord" ou "IArray"

### <a name="update-the-schema"></a>Atualizar o esquema
Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Mais tarde, se necessário, o esquema do modelo de dados é atualizado para acomodar os eventos de entrada que podem não caber no esquema original.

Evitar o `SELECT *` consulta para impedir que a atualização de esquema dinâmico em linhas. Para além das implicações de desempenho potencial, poderá resultar em incerteza de tempo decorrido para os resultados. Selecione os campos exatos que precisam para ser mostrada no dashboard do Power BI. Além disso, os valores de dados devem estar em conformidade com o tipo de dados escolhido.


Anterior/atual | Int64 | Cadeia | Datetime | Valor de duplo
-----------------|-------|--------|----------|-------
Int64 | Int64 | Cadeia | Cadeia | Valor de duplo
Valor de duplo | Valor de duplo | Cadeia | Cadeia | Valor de duplo
Cadeia | String | String | String | Cadeia 
Datetime | Cadeia | Cadeia |  Datetime | Cadeia

## <a name="table-storage"></a>Table Storage

[Armazenamento de tabelas do Azure](../storage/common/storage-introduction.md) oferece armazenamento de elevada disponibilidade e escalável em massa, para que um aplicativo pode dimensionar automaticamente para satisfazer o pedido de utilizador. O Table storage é o arquivo de chaves/atributos NoSQL da Microsoft, que pode ser usado para dados estruturados com menos restrições no esquema. Armazenamento de tabelas do Azure pode ser utilizado para armazenar dados de persistência e obtenção de eficiente.

A tabela seguinte lista os nomes de propriedade e suas descrições para a criação de uma saída de tabela.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este armazenamento de tabela. |
| Conta de armazenamento |O nome da conta de armazenamento em que está a enviar a saída. |
| Chave da conta de armazenamento |A chave de acesso associada com a conta de armazenamento. |
| Nome da tabela |O nome da tabela. A tabela é criada, se não existir. |
| Chave de partição |O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador exclusivo para a partição dentro de uma tabela que forma a primeira parte da chave primária de uma entidade. É um valor de cadeia de caracteres que pode ser até 1 KB de tamanho. |
| Chave da fila |O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo de uma entidade dentro de uma partição. Isso constitui a segunda parte da chave primária de uma entidade. A chave de linha é um valor de cadeia de caracteres que pode ser até 1 KB de tamanho. |
| Tamanho do batch |O número de registos para uma operação em lote. A predefinição (100) é suficiente para a maioria das tarefas. Consulte a [especificação de operação em lote tabela](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) para obter mais detalhes sobre como modificar esta definição. |

## <a name="service-bus-queues"></a>Filas do Service Bus

[Filas do Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) oferecem uma entrega de mensagens FIFO para um ou mais consumidores concorrentes. Normalmente, as mensagens são recebidas e processadas pelos recetores na ordem temporal em que foram adicionados à fila. Cada mensagem é recebida e processada por apenas um consumidor de mensagens.

A tabela seguinte lista os nomes de propriedade e suas descrições para a criação de uma saída da fila.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para esta fila do Service Bus. |
| Espaço de nomes do Service Bus |Um contentor para um conjunto de entidades de mensagens. |
| Nome da fila |O nome da fila do Service Bus. |
| Nome da política da fila |Quando cria uma fila, também pode criar políticas de acesso partilhado da fila **configurar** separador. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. |
| Chave da política da fila |A chave de acesso partilhado que é utilizada para autenticar o acesso ao espaço de nomes do Service Bus. |
| Formato de serialização de eventos |O formato de serialização para dados de saída. JSON, CSV e Avro são suportados. |
| Codificação |Para CSV e JSON, o UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador |Aplicável apenas para a serialização de CSV. O Stream Analytics suporta um número de delimitadores comuns para serializar dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato |Aplicável apenas para o tipo JSON. **Separado por linhas** Especifica que o resultado é formatado fazendo com que cada objeto JSON separado por uma nova linha. **Matriz** Especifica que o resultado é formatado como uma matriz de objetos JSON. |
| Colunas de propriedade | Opcional. Colunas separados por vírgulas que precisem de ser anexados como propriedades de utilizador de mensagem de saída, em vez do payload. Obter mais informações sobre esta funcionalidade estão na seção [propriedades de metadados personalizadas para saída](#custom-metadata-properties-for-output). |

É o número de partições [com base no SKU de barramento de serviço e no tamanho](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="service-bus-topics"></a>Tópicos de Service Bus
Filas do Service Bus fornecem um método de comunicação unidirecional de remetente para o recetor. [Tópicos do Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornecem uma forma de um-para-muitos de comunicação.

A tabela seguinte lista os nomes de propriedade e suas descrições para a criação de uma saída de tópico do Service Bus.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este tópico do Service Bus. |
| Espaço de nomes do Service Bus |Um contentor para um conjunto de entidades de mensagens. Quando tiver criado um novo hub de eventos, também criou um espaço de nomes do Service Bus. |
| Nome do tópico |Tópicos são entidades de mensagens, semelhantes a filas e hubs de eventos. Foram concebidos para recolher fluxos de eventos a partir de dispositivos e serviços. Quando é criado um tópico, também forneceu um nome específico. As mensagens enviadas para um tópico não estão disponíveis, a menos que é criada uma subscrição, então verifique se existe uma ou mais subscrições sob o tópico. |
| Nome da política do tópico |Quando cria um tópico do Service Bus, também pode criar políticas de acesso partilhado sobre o assunto **configurar** separador. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. |
| Chave da política do tópico |A chave de acesso partilhado que é utilizada para autenticar o acesso ao espaço de nomes do Service Bus. |
| Formato de serialização de eventos |O formato de serialização para dados de saída. JSON, CSV e Avro são suportados. |
| Codificação |Se estiver a utilizar o formato JSON ou CSV, uma codificação tem de ser especificada. UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador |Aplicável apenas para a serialização de CSV. O Stream Analytics suporta um número de delimitadores comuns para serializar dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Colunas de propriedade | Opcional. Colunas separados por vírgulas que precisem de ser anexados como propriedades de utilizador de mensagem de saída, em vez do payload. Obter mais informações sobre esta funcionalidade estão na seção [propriedades de metadados personalizadas para saída](#custom-metadata-properties-for-output). |

É o número de partições [com base no SKU de barramento de serviço e no tamanho](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[O Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um serviço de base de dados distribuído globalmente que oferece dimensionamento elástico ilimitado em todo o mundo, a consulta avançada e indexação automática através de modelos de dados sem esquema. Para saber mais sobre as opções de contentor do Azure Cosmos DB para o Stream Analytics, consulte a [Stream Analytics com o Azure Cosmos DB como saída](stream-analytics-documentdb-output.md) artigo.

Saída do Azure Cosmos DB do Stream Analytics não está atualmente disponível nas regiões do Azure Alemanha (T-Systems International) e Azure China 21Vianet.

> [!Note]
> Neste momento, o Azure Stream Analytics só suporta a ligação ao Azure Cosmos DB com a API de SQL.
> Outras APIs do Azure Cosmos DB ainda não são suportados. Se ponto Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, os dados poderão não ser corretamente armazenados.

A tabela seguinte descreve as propriedades para a criação de uma saída do Azure Cosmos DB.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um alias para fazer referência esta saída na sua consulta do Stream Analytics. |
| Sink | Azure Cosmos DB. |
| Opção de Importar | Escolhem **selecione o Cosmos DB da sua subscrição** ou **definições de fornecer o Cosmos DB manualmente**.
| ID da Conta | O nome ou o ponto final do URI de conta do Azure Cosmos DB. |
| Chave de conta | A chave de acesso partilhado para a conta do Azure Cosmos DB. |
| Base de Dados | O nome de base de dados do Azure Cosmos DB. |
| Nome do contentor | O nome do contentor a ser usado, que tem de existir no Cosmos DB. Exemplo:  <br /><ul><li> _MyContainer_: Tem de existir um contentor com o nome "MyContainer".</li>|
| ID do documento |Opcional. O nome do campo em eventos de saída que é utilizado para especificar a chave primária no qual insert ou update baseiam-se operações.

## <a name="azure-functions"></a>Funções do Azure
As funções do Azure é um serviço de computação sem servidor que pode utilizar para executar código a pedido sem ter de aprovisionar ou gerir a infraestrutura explicitamente. Permite-lhe implementar código acionado por eventos que ocorrem nos serviços do Azure ou o parceiro. Esta capacidade das funções do Azure para responder a acionadores torna-as numa saída natural para o Azure Stream Analytics. Este adaptador de saída permite aos utilizadores ligar as funções do Azure Stream Analytics e executar um script ou fragmento de código em resposta a uma variedade de eventos.

Saída de funções do Azure do Stream Analytics não está atualmente disponível nas regiões do Azure Alemanha (T-Systems International) e Azure China 21Vianet.

O Azure Stream Analytics invoca funções do Azure através de acionadores HTTP. O adaptador de saída das funções do Azure está disponível com as seguintes propriedades configuráveis:

| Nome da propriedade | Descrição |
| --- | --- |
| Function app |O nome da sua aplicação de funções do Azure. |
| Função |O nome da função na sua aplicação de funções do Azure. |
| Chave |Se pretender utilizar uma função do Azure a partir de outra subscrição, pode fazê-lo ao fornecer a chave para aceder à sua função. |
| Tamanho máximo de lote |Uma propriedade que lhe permite definir o tamanho máximo para cada lote de saída que é enviado para a sua função do Azure. É a unidade de entrada em bytes. Por predefinição, este valor é 262.144 bytes (256 KB). |
| Contagem máxima de lotes  |Uma propriedade que permite especificar o número máximo de eventos em cada lote que é enviado para as funções do Azure. O valor predefinido é 100. |

Quando o Azure Stream Analytics recebe um 413 ("http do pedido entidade demasiado grande") exceção através de uma função do Azure, reduz o tamanho dos lotes que envia para as funções do Azure. No código da função do Azure, utilize esta exceção para se certificar de que o Azure Stream Analytics não envia lotes demasiado grandes. Além disso, certifique-se de que os valores de contagem e tamanho de lote máximo utilizados na função são consistentes com os valores introduzidos no portal do Stream Analytics.

Além disso, numa situação onde não existe nenhum evento de destino numa janela de tempo, nenhuma saída é gerada. Como resultado, o **computeResult** não se chama a função. Este comportamento é consistente com as funções de agregação em janela internas.

## <a name="custom-metadata-properties-for-output"></a>Propriedades de metadados personalizados de saída 

Pode anexar colunas de consulta como propriedades de utilizador às suas mensagens de saída. Estas colunas não passam no payload. As propriedades estão presentes na forma de um dicionário na mensagem de saída. *Chave* é o nome da coluna e *valor* é o valor da coluna no dicionário de propriedades. Todos os tipos de dados do Stream Analytics são suportados, exceto o registo e de matriz.  

Saídas suportadas: 
* Fila do Service Bus 
* Tópico do Service Bus 
* Hub de eventos 

No exemplo a seguir, adicionamos os dois campos `DeviceId` e `DeviceStatus` aos metadados. 
* Consulta: `select *, DeviceId, DeviceStatus from iotHubInput`
* Configuração de saída: `DeviceId,DeviceStatus`

![Colunas de propriedade](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

A captura de ecrã a seguir mostra as propriedades da mensagem inspecioná-los no EventHub por meio de saída [Explorador do Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

![Propriedades personalizadas do evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Criação de partições

A tabela seguinte resume o suporte de partição e o número de gravadores de saída para cada tipo de saída:

| Tipo de saída | Suporte de criação de partições | Chave de partição  | Vários autores de saída |
| --- | --- | --- | --- |
| Azure Data Lake Store | Sim | Utilize {date} e tokens de {time} no padrão do prefixo do caminho. Escolha o formato de data, como MM-DD-YYYY, DD/MM/AAAA ou AAAA/MM/DD. HH é utilizada para o formato de hora. | Segue-se a criação de partições entrada para [consultas totalmente ponto pode ser paralelizadas](stream-analytics-scale-jobs.md). |
| Base de Dados SQL do Azure | Sim, precisa de ativada. | Com base na cláusula PARTITION BY na consulta. | Quando a opção de herdar de criação de partições estiver ativada, segue-se a criação de partições entrada para [consultas totalmente ponto pode ser paralelizadas](stream-analytics-scale-jobs.md). Para saber mais sobre a obtenção da, melhor escrever o desempenho de taxa de transferência quando está carregando dados na base de dados do Azure SQL, veja [saída de Azure Stream Analytics para a base de dados do Azure SQL](stream-analytics-sql-output-perf.md). |
| Armazenamento de Blobs do Azure | Sim | Utilize {date} e tokens de {time} a partir de seus campos de evento no padrão de caminho. Escolha o formato de data, como MM-DD-YYYY, DD/MM/AAAA ou AAAA/MM/DD. HH é utilizada para o formato de hora. Saída de BLOBs pode ser particionada por um atributo único evento personalizado {fieldname} ou {datetime:\<especificador >}. | Segue-se a criação de partições entrada para [consultas totalmente ponto pode ser paralelizadas](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Sim | Sim | Varia consoante o alinhamento da partição.<br /> Quando a chave de partição para a saída do hub de eventos é igualmente alinhada com o passo de consulta (anterior) a montante, o número de gravadores é igual ao número de partições na saída do hub de eventos. Cada writer usa a [EventHubSender classe](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) para enviar eventos para a partição específica. <br /> Quando a chave de partição para a saída do hub de eventos não está alinhada com o passo de consulta (anterior) a montante, o número de gravadores é igual ao número de partições nesse passo anterior. Cada writer usa a [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) na **EventHubClient** para enviar eventos para todas as partições de saída. |
| Power BI | Não | Nenhuma | Não aplicável. |
| Armazenamento de Tabelas do Azure | Sim | Qualquer coluna de saída.  | Segue-se a criação de partições entrada para [totalmente em paralelo consultas](stream-analytics-scale-jobs.md). |
| Tópico de barramento de serviço do Azure | Sim | Automaticamente escolhido. O número de partições se baseia a [SKU de barramento de serviço e o tamanho](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições do tópico de saída.  |
| Fila do Service Bus do Azure | Sim | Automaticamente escolhido. O número de partições se baseia a [SKU de barramento de serviço e o tamanho](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições na fila de saída. |
| Azure Cosmos DB | Sim | Com base na cláusula PARTITION BY na consulta. | Segue-se a criação de partições entrada para [totalmente em paralelo consultas](stream-analytics-scale-jobs.md). |
| Funções do Azure | Não | Nenhuma | Não aplicável. |

O número de gravadores de saída também pode ser controlado através de `INTO <partition count>` (consulte [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) cláusula FROM na consulta, o que pode ser útil na obtenção de uma topologia da tarefa pretendida. Se o adaptador de saída não for particionado, falta de dados de uma partição de entrada fará com que um atraso até ao montante de chegada tardia do tempo. Nesses casos, a saída é intercalada com um único gravador, o que poderá provocar afunilamentos no seu pipeline. Para saber mais sobre a política de chegada final, veja [considerações de ordem de eventos do Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída
O Azure Stream Analytics utiliza lotes de tamanho variável, para processar eventos e escrita para saídas. Normalmente, o mecanismo de Stream Analytics não gravar uma mensagem por vez e utiliza lotes para uma eficiência. Quando a taxa de eventos recebidos e enviados é elevada, o Stream Analytics utiliza lotes maiores. Quando a taxa de saída é baixa, ele usa lotes mais pequenos para manter a latência baixa.

A tabela seguinte explica algumas das considerações sobre a criação de batches de saída:

| Tipo de saída | Tamanho máximo de mensagem | Otimização de tamanho de lote |
| :--- | :--- | :--- |
| Azure Data Lake Store | Ver [limites de armazenamento do Data Lake](../azure-subscription-service-limits.md#data-lake-store-limits). | Utilize até 4 MB por operação de escrita. |
| Base de Dados SQL do Azure | Configurável com a contagem máxima de lotes. Inserir 10.000 máximas e 100 mínimas de linhas por em massa única por predefinição.<br />Ver [limita de SQL do Azure](../sql-database/sql-database-resource-limits.md). |  Todos os lotes é inicialmente em massa inserida com contagem de lote máximo. Batch é dividido em metade (até que a contagem mínima de lote) com base em caso de erro não permanente de SQL. |
| Armazenamento de Blobs do Azure | Ver [limites de armazenamento do Azure](../azure-subscription-service-limits.md#storage-limits). | O tamanho do bloco de blob máximo é de 4 MB.<br />A contagem de bock de blob máximo é de 50 000. |
| Azure Event Hubs  | 256 KB ou 1 MB por mensagem. <br />Ver [limita os Hubs de eventos](../event-hubs/event-hubs-quotas.md). |  Quando não está alinhada a criação de partições de entrada/saída, cada evento é fornecido em individualmente `EventData` e enviadas num lote de até ao tamanho máximo de mensagens. Isso também acontece se [propriedades de metadados personalizados](#custom-metadata-properties-for-output) são utilizados. <br /><br />  Quando a criação de partições de entrada/saída está alinhada, vários eventos são incluídos numa única `EventData` de instância, até ao tamanho máximo de mensagens e enviados. |
| Power BI | Ver [limita a API de Rest do Power BI](https://msdn.microsoft.com/library/dn950053.aspx). |
| Armazenamento de Tabelas do Azure | Ver [limites de armazenamento do Azure](../azure-subscription-service-limits.md#storage-limits). | A predefinição é 100 entidades por transação única. Pode configurá-la para um valor menor, conforme necessário. |
| Fila do Service Bus do Azure   | 256 KB por mensagem para o escalão Standard, 1MB para o escalão Premium.<br /> Ver [do Service Bus limita](../service-bus-messaging/service-bus-quotas.md). | Utilize um único evento por mensagem. |
| Tópico de barramento de serviço do Azure | 256 KB por mensagem para o escalão Standard, 1MB para o escalão Premium.<br /> Ver [do Service Bus limita](../service-bus-messaging/service-bus-quotas.md). | Utilize um único evento por mensagem. |
| Azure Cosmos DB   | Ver [do Azure Cosmos DB limita](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Tamanho do lote e escrever a frequência são ajustadas dinamicamente com base nas respostas do Azure Cosmos DB. <br /> Não existem não existem limitações predeterminadas do Stream Analytics. |
| Funções do Azure   | | O tamanho de lote de predefinido é 262.144 bytes (256 KB). <br /> A contagem de eventos padrão por lote é 100. <br /> O tamanho do lote é configurável e pode aumentar ou diminuir o Stream Analytics [opções de saída](#azure-functions).

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> 
> [Quickstart: Criar uma tarefa de Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
