---
title: Compreender as saídas do Azure Stream Analytics
description: Este artigo descreve as opções de saída de dados disponíveis no Azure Stream Analytics, incluindo o Power BI para os resultados da análise.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/21/2018
ms.custom: seodec18
ms.openlocfilehash: 0a3fd2cc66a066d2790d2e12822e3246dc3db382
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898878"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Compreender as saídas do Azure Stream Analytics
Este artigo descreve os diferentes tipos de saídas disponíveis para uma tarefa do Azure Stream Analytics. Saídas permitem-lhe armazenar e guardar os resultados da tarefa do Stream Analytics. Pode fazer com os dados de saída, ainda mais análises de negócio e o armazenamento de dados dos seus dados.

Ao conceber a sua consulta do Stream Analytics, consulte o nome da saída usando o [cláusula INTO](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Pode usar uma única saída por tarefa ou várias saídas por transmissão em fluxo de trabalho se for necessário, fornecendo várias cláusulas INTO na consulta.

Para criar, editar e testar a tarefa do Stream Analytics produz, pode utilizar o [portal do Azure](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), e [Visual Studio](stream-analytics-quick-create-vs.md).

Algum suporte de tipos de saídas [particionamento](#partitioning), e [tamanhos de lote de saída](#output-batch-size) variar para otimizar o débito.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics suporta [do Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). O Azure Data Lake Store é um repositório de hiper escala a nível da empresa para cargas de trabalho de análise de macrodados. Data Lake Store permite-lhe armazenar dados de qualquer tamanho, tipo e velocidade de ingestão para análises exploratórias e operacionais. Stream Analytics tem de estar autorizado a aceder a Store de Lake de dados.

Saída de Azure Data Lake Store do Stream Analytics não está atualmente disponível nas regiões do Azure Alemanha (T-Systems International) e Azure China (21Vianet).

### <a name="authorize-an-azure-data-lake-store-account"></a>Autorizar uma conta do Azure Data Lake Store

1. Quando o armazenamento do Data Lake está selecionado como uma saída no portal do Azure, lhe for pedido para autorizar uma ligação para um Lake Store existente do dados.

   ![Autorizar a ligação para o Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Se já tiver acesso ao Data Lake Store, selecione **autorizar agora** e uma página será exibida indicando **redirecionar para autorização**. Após a autorização obtiver êxito, é apresentada a página que lhe permite configurar a saída do Data Lake Store.

3. Assim que tiver a conta do Data Lake Store autenticada, pode configurar as propriedades para a saída do Data Lake Store. A tabela a seguir é a lista de nomes de propriedade e a respetiva descrição para configurar a saída do Data Lake Store.

   ![Definir o Data Lake Store como saída de Stream Analytics](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para esta Store de Lake de dados. |
| Nome da conta | O nome da conta de armazenamento do Data Lake onde estão a enviar a saída. É apresentada uma lista pendente de contas do Data Lake Store que estão disponíveis na sua subscrição. |
| Padrão do prefixo do caminho | O caminho de ficheiro utilizado para escrever ficheiros dentro da conta especificada do Data Lake Store. Pode especificar uma ou mais instâncias de {date} e {time} variáveis.<br /><ul><li>Exemplo 1: pasta1/registos / {data} / {time}</li><li>Exemplo 2: pasta1/registos / {data}</li></ul><br />O carimbo de hora da estrutura da pasta criada segue UTC e não a hora local.<br /><br />Se o padrão de caminho de ficheiro não contém à direita "/", o último padrão no caminho de ficheiro é tratado como um prefixo de nome de ficheiro. <br /><br />Nessas circunstâncias, são criados novos ficheiros:<ul><li>Alteração no esquema de saída</li><li>Externo ou interno o reinício de uma tarefa.</li></ul> |
| Formato de data | Opcional. Se o token de data é utilizado no caminho de prefixo, pode selecionar o formato de data em que os ficheiros estão organizados. Exemplo: DD/MM/AAAA |
|Formato de hora | Opcional. Se o token de tempo é utilizado no caminho de prefixo, especifique o formato de hora em que os ficheiros estão organizados. Atualmente, o único valor suportado é HH. |
| Formato de serialização de eventos | Formato de serialização para dados de saída. JSON, CSV e Avro são suportados.|
| Codificação | Se utilizar o formato JSON ou CSV, uma codificação tem de ser especificada. UTF-8 é o único formato de codificação suportado neste momento.|
| Delimitador | Só é aplicável para a serialização de CSV. Stream Analytics suporta um número de delimitadores comuns para serializar os dados do CSV. Valores suportados são vírgula, ponto e vírgula, espaço, separador e, barra vertical.|
| Formato | Só é aplicável para a serialização do JSON. Separado por linhas Especifica que o resultado é formatado fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que o resultado é formatado como uma matriz de objetos JSON. Essa matriz só é fechada quando as paradas de tarefa ou o Stream Analytics tem movido para a próxima janela de tempo. Em geral, é preferível usar linha separados JSON, uma vez que ele não requer tratamento especial, enquanto o ficheiro de saída ainda está a ser escrito.|

### <a name="renew-data-lake-store-authorization"></a>Renovar a autorização do Data Lake Store
Tem de autenticar a sua conta do Data Lake Store, se a palavra-passe tiver sido alterado, uma vez que a tarefa foi criada ou pela última vez autenticada. Se não autenticar, o seu trabalho não produz resultados de saída e mostra um erro que indica a necessidade de reautorização nos registos de operação. Atualmente, existe uma limitação em que o token de autenticação tem de ser atualizados manualmente todos os 90 dias para todas as tarefas com a saída do Data Lake Store. No entanto, pode superar essa limitação ao [autenticação com o managed identidades (pré-visualização)](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls).

Para renovar a autorização, **parar** seu trabalho > vá para a saída do Data Lake Store > Clique a **renovar a autorização** associar e, por um breve período a página será exibida indicando **redirecionar para autorização...** . A página fecha automaticamente e se tiver êxito, indica **autorização foi renovada com êxito**. Precisa, em seguida, clique em **salvar** na parte inferior da página e pode continuar, mediante a reiniciar a tarefa da **data/hora da última parado** para evitar a perda de dados.

![Renovar a autorização do Data Lake Store na saída](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>SQL Database
[Base de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) pode ser utilizado como uma saída de dados que é de natureza relacionais ou de aplicativos que dependem de conteúdo a ser alojado numa base de dados relacional. Tarefas do Stream Analytics gravar numa tabela existente numa base de dados SQL do Azure. O esquema da tabela deve corresponder exatamente os campos e seus tipos que está a ser o resultado da sua tarefa. Uma [do Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) também podem ser especificadas como uma saída via também a opção de saída de base de dados SQL. Para saber mais sobre formas de melhorar o débito de escrita, consulte a [Stream Analytics com o Azure SQL DB como saída](stream-analytics-sql-output-perf.md) artigo. A tabela abaixo lista os nomes de propriedade e a respetiva descrição para a criação de uma base de dados SQL de saída.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para esta base de dados. |
| Base de Dados | O nome da base de dados em que estão a enviar a saída. |
| Nome do servidor | O nome do servidor de base de dados SQL. |
| Nome de utilizador | O nome de utilizador, que tem acesso de gravação no banco de dados. Stream Analytics só suporta a autenticação do SQL. |
| Palavra-passe | A palavra-passe para ligar à base de dados. |
| Tabela | O nome da tabela em que o resultado é escrito. O nome da tabela diferencia maiúsculas de minúsculas e o esquema desta tabela deve corresponder exatamente ao número de campos e seus tipos gerados pelo seu resultado da tarefa. |
|Herdam o esquema de partição| Isto permite-lhe herdam o esquema de particionamento de seu passo de consulta anterior para ativar a topologia totalmente paralela com vários gravadores à tabela. Para obter mais informações, consulte [saída de Azure Stream Analytics para a base de dados do Azure SQL](stream-analytics-sql-output-perf.md).|
|Corresponde à contagem de batch| O número de limite superior recomendado de registos enviados com cada em massa inserir a transação.|

> [!NOTE]
> Atualmente, a oferta de base de dados do Azure SQL é suportada para uma saída da tarefa do Stream Analytics. No entanto, não é suportada uma Máquina Virtual com o SQL Server com uma base de dados anexado. Trata-se sujeita a alterações em versões futuras.
>

## <a name="blob-storage"></a>Armazenamento de blobs
Armazenamento de BLOBs oferece uma solução económica e dimensionável para armazenar grandes quantidades de dados não estruturados na cloud. Para obter uma introdução no armazenamento de Blobs do Azure e a sua utilização, consulte a documentação em [como utilizar Blobs](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

A tabela abaixo lista os nomes de propriedade e a respetiva descrição para a criação de uma saída de Blobs.

| Nome da propriedade       | Descrição                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de Saída        | Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este armazenamento de Blobs. |
| Conta de Armazenamento     | O nome da conta de armazenamento em que estão a enviar a saída.               |
| Chave da Conta de Armazenamento | A chave secreta associada à conta de armazenamento.                              |
| Contentor de armazenamento   | Os contentores oferecem um agrupamento lógico para blobs armazenados no serviço de Blobs do Microsoft Azure. Ao carregar um blob para o serviço de BLOBs, tem de especificar um contentor para esse blob. |
| Padrão do Caminho | Opcional. O padrão de caminho de ficheiro utilizado para escrever os blobs no contentor especificado. <br /><br /> O padrão de caminho, pode optar por utilizar uma ou mais instâncias das variáveis do tempo de data para especificar a frequência com que os blobs são escritos: <br /> {date}, {time} <br /><br />Pode utilizar a criação de partições de blob personalizado para especificar um nome personalizado {campo} a partir dos seus dados de eventos para blobs de partição. O nome do campo seja alfanumérico e pode incluir espaços, hífenes e carateres de sublinhado. Restrições em campos personalizados incluem o seguinte: <ul><li>Caso a distinção entre e minúsculas (não consegue diferenciar entre a coluna "ID" e coluna "id")</li><li>Campos aninhados não são permitidos (em vez disso, utilize um alias a consulta da tarefa para "aplainar" o campo)</li><li>As expressões não podem ser utilizadas como um nome de campo.</li></ul> <br /><br /> Esta funcionalidade permite a utilização de configurações de especificador de formato de data/hora personalizada no caminho. Personalizado data e hora formatos tem de ser especificado um de cada vez, entre o {datetime:\<especificador >} palavra-chave. Entradas permitidas \<especificador > são AAAA, MM, M, dd, d, HH, H, mm, m, ss ou s. O {datetime:\<especificador >} palavra-chave pode ser utilizada várias vezes no caminho para formar a configurações de data/hora personalizada. <br /><br />Exemplos: <ul><li>Exemplo 1: cluster1/registos / {data} / {time}</li><li>Exemplo 2: cluster1/registos / {data}</li><li>Exemplo 3: cluster1 / {client_id} / {data} / {time}</li><li>Exemplo 4: cluster1 / {datetime:ss} / {myField} em que a consulta é: SELECT data.myField AS myField FROM Input;</li><li>Exemplo 5: cluster1/ano = {datetime:yyyy} / mês = {datetime:MM} / dia = {datetime:dd}</ul><br /><br />O carimbo de hora da estrutura da pasta criada segue UTC e não a hora local.<br /><br />Nomes de arquivos segue a seguinte convenção: <br /><br />{Caminho Pattern}/schemaHashcode_Guid_Number.extension de prefixo<br /><br />Ficheiros de saída de exemplo:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br /><br /> Para obter mais informações sobre esta funcionalidade, visite [criação de partições de saída de Blobs do Azure Stream Analytics personalizado](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato de data | Opcional. Se o token de data é utilizado no caminho de prefixo, pode selecionar o formato de data em que os ficheiros estão organizados. Exemplo: DD/MM/AAAA |
| Formato de hora | Opcional. Se o token de tempo é utilizado no caminho de prefixo, especifique o formato de hora em que os ficheiros estão organizados. Atualmente, o único valor suportado é HH. |
| Formato de serialização de eventos | Formato de serialização para dados de saída. JSON, CSV e Avro são suportados. |
| Codificação    | Se utilizar o formato JSON ou CSV, uma codificação tem de ser especificada. UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador   | Só é aplicável para a serialização de CSV. Stream Analytics suporta um número de delimitadores comuns para serializar os dados do CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato      | Só é aplicável para a serialização do JSON. Separado por linhas Especifica que o resultado é formatado fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que o resultado é formatado como uma matriz de objetos JSON. Essa matriz só é fechada quando as paradas de tarefa ou o Stream Analytics tem movido para a próxima janela de tempo. Em geral, é preferível usar linha separados JSON, uma vez que ele não requer tratamento especial, enquanto o ficheiro de saída ainda está a ser escrito. |

Quando utiliza o armazenamento de BLOBs como resultado, é criado um novo ficheiro no blob nos seguintes casos:

* Se o ficheiro excede o número máximo de blocos permitidos (atualmente 50.000). O número máximo permitido de blocos pode ser atingido sem atingir o tamanho do blob permitido máximo. Por exemplo, se a taxa de saída é alta, pode ver mais bytes por bloco e o tamanho de ficheiro é maior. Se a taxa de saída é baixa, cada bloco tem menos dados e o tamanho de ficheiro é menor.
* Se houver uma alteração de esquema na saída, e o formato de saída requer o esquema fixo (CSV e Avro).
* Se uma tarefa for reiniciada, externamente por um utilizador pará-la e iniciá-lo, ou internamente para recuperação de manutenção ou de erro do sistema.
* Se a consulta estiver particionada completamente, o novo ficheiro é criado para cada partição de saída.
* Se um ficheiro ou de um contentor da conta de armazenamento é eliminado pelo utilizador.
* Se o resultado é particionado usando o padrão de prefixo do caminho de tempo, um blob novo é utilizado quando a consulta é movido para a hora seguinte.
* Se a saída é particionada por um campo personalizado, um blob novo é criado por chave de partição se não existir.
* Se a saída é particionada por um campo personalizado onde a cardinalidade de chave de partição excede 8000, um novo blob pode ser criado por chave de partição.

## <a name="event-hub"></a>Hub de Eventos
O [os Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) serviço é altamente escaláveis de publicação-subscrição ingestor de eventos. Esta possa recolher a milhões de eventos por segundo. Um uso de um Hub de eventos como saída é quando o resultado de uma tarefa do Stream Analytics torna-se a entrada de outra tarefa de transmissão em fluxo.

Existem alguns parâmetros que são necessários para configurar fluxos de dados do Hub de eventos como uma saída.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este Hub de eventos. |
| Espaço de nomes do hub de eventos |Um espaço de nomes do Hub de eventos é um contentor para um conjunto de entidades de mensagens. Quando tiver criado um novo Hub de eventos, também criou um espaço de nomes do Hub de eventos. |
| O nome do hub de eventos | O nome do seu Hub de eventos de saída. |
| Nome da política do Hub de Eventos | A política de acesso partilhado, que pode ser criada no separador Configurar do Hub de eventos. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. |
| Chave da política do Hub de Eventos | A chave de acesso partilhado utilizada para autenticar o acesso ao espaço de nomes do Hub de eventos. |
| Coluna de chave de partição [opcional] | Esta coluna contém a chave de partição para a saída do Hub de eventos. |
| Formato de serialização de eventos | Formato de serialização para dados de saída. JSON, CSV e Avro são suportados. |
| Codificação | Para CSV e JSON, o UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador | Só é aplicável para a serialização de CSV. O Stream Analytics suporta um número de delimitadores comuns para serializar dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato | Só é aplicável para a serialização do JSON. Separado por linhas Especifica que o resultado é formatado fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que o resultado é formatado como uma matriz de objetos JSON. Essa matriz só é fechada quando as paradas de tarefa ou o Stream Analytics tem movido para a próxima janela de tempo. Em geral, é preferível usar linha separados JSON, uma vez que ele não requer tratamento especial, enquanto o ficheiro de saída ainda está a ser escrito. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) pode ser utilizado como uma saída de uma tarefa do Stream Analytics para fornecer uma experiência Rica visualização dos resultados da análise. Esta capacidade pode ser utilizada para dashboards operacionais, geração de relatórios e controlado por relatórios de métrica.

Saída do Power BI do Stream Analytics não está atualmente disponível nas regiões do Azure Alemanha (T-Systems International) e Azure China (21Vianet).

### <a name="authorize-a-power-bi-account"></a>Autorizar uma conta do Power BI
1. Quando o Power BI está selecionada como uma saída no portal do Azure, são-lhe pedido para autorizar um utilizador do Power BI existente ou criar uma nova conta do Power BI.
   
   ![Autorizar o utilizador do Power BI para configurar a saída](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Crie uma nova conta se não, mas tiver um, em seguida, clique em autorizar agora. É apresentada a página seguinte:
   
   ![Autenticação no Power BI da conta do Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. Neste passo, forneça a conta escolar ou profissional para autorizar a saída do Power BI. Se não já têm sessão iniciada Power BI, selecione inscrever-se agora. A conta escolar ou profissional que utiliza para o Power BI pode ser diferente da conta de subscrição do Azure, que tem sessão iniciada em com.

### <a name="configure-the-power-bi-output-properties"></a>Configurar as propriedades de saída do Power BI
Assim que tiver a conta do Power BI autenticada, pode configurar as propriedades para a saída do Power BI. A tabela a seguir é a lista de nomes de propriedade e a respetiva descrição para configurar a saída do Power BI.

| Nome da propriedade | descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este resultado do Power BI. |
| Área de trabalho de Grupo |Para ativar a partilha de dados com outros utilizadores do Power BI pode selecionar grupos dentro de sua conta do Power BI ou escolha "A minha área de trabalho" se não pretender escrever a um grupo. A atualizar um grupo existente requer a autenticação do Power BI a renovar. |
| Nome do conjunto de dados |Forneça um nome de conjunto de dados que ele foi criado para a saída do Power BI a utilizar |
| Nome da tabela |Forneça um nome de tabela em que o conjunto de dados de saída do Power BI. Atualmente, a saída do Power BI a partir de tarefas do Stream Analytics só pode ter uma tabela num conjunto de dados |

Para um passo a passo de configuração de um resultado do Power BI e o dashboard, consulte a [do Azure Stream Analytics e o Power BI](stream-analytics-power-bi-dashboard.md) artigo.

> [!NOTE]
> Não crie explicitamente o conjunto de dados e a tabela no dashboard do Power BI. O conjunto de dados e a tabela é preenchido automaticamente quando a tarefa é iniciada e a tarefa é iniciada a saída de bombagem para o Power BI. Tenha em atenção que se a consulta da tarefa não gera nenhum resultado, o conjunto de dados, e a tabela não é criada. Tenha em atenção que, se o Power BI já tinha um conjunto de dados e uma tabela com o mesmo nome que o que é fornecido com esta tarefa de Stream Analytics, os dados existentes são substituídos.
>

### <a name="schema-creation"></a>Criação de esquemas
O Azure Stream Analytics cria um conjunto de dados do Power BI e a tabela em nome do utilizador, se ainda não existir. Todos os outros casos, a tabela é atualizada com novos valores. Atualmente, existe uma limitação que apenas uma tabela pode existir dentro de um conjunto de dados. O Power BI utiliza a política de retenção FIFO. Quando ativada, irão recolher dados numa tabela do até atingir 200 000 linhas.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Conversão de tipo de dados do Stream Analytics para Power BI
O Azure Stream Analytics atualiza o modelo de dados dinamicamente no tempo de execução, se alterar o esquema de saída. Todas as alterações de nome de coluna, alterações de tipo de coluna e a adição ou remoção de colunas são controlados.

Esta tabela abrange as conversões de tipo de dados do [tipos de dados do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835065.aspx) para o Power BIs [tipos de modelo de dados de entidade (EDM)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) se não existir um conjunto de dados do POWER BI e uma tabela.

Do Stream Analytics | Para o Power BI
-----|-----
bigint | Int64
nvarchar (Max) | Cadeia
datetime | Datetime
float | Valor de duplo
Matriz de registo | Cadeia de caracteres de tipo, o valor constante "IRecord" ou "IArray"

### <a name="schema-update"></a>Atualização do esquema
Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Mais tarde, se necessário, o esquema do modelo de dados é atualizado para acomodar os eventos de entrada não podem se encaixa o esquema original.

O `SELECT *` consulta deve ser evitada para impedir que a atualização do esquema dinâmico em linhas. Para além das implicações de desempenho potencial, também pode resultar num incerteza de tempo decorrido para os resultados. Os campos exatos que precisam para ser mostrada no dashboard do Power BI devem ser selecionados. Além disso, os valores de dados devem estar em conformidade com o tipo de dados escolhido.


Anterior/atual | Int64 | Cadeia | Datetime | Valor de duplo
-----------------|-------|--------|----------|-------
Int64 | Int64 | Cadeia | Cadeia | Valor de duplo
Valor de duplo | Valor de duplo | Cadeia | Cadeia | Valor de duplo
Cadeia | Cadeia | Cadeia | Cadeia | Cadeia 
Datetime | Cadeia | Cadeia |  Datetime | Cadeia


### <a name="renew-power-bi-authorization"></a>Renovar a autorização do Power BI
Se a palavra-passe de conta do Power BI for alterado depois da tarefa de Stream Analytics foi criada ou pela última vez autenticada, terá de autenticar o Stream Analytics. Se a multi-factor Authentication (MFA) é configurada no seu inquilino do Azure Active Directory (AAD), terá também de renovar a autorização do Power BI duas em duas semanas. Um sintoma de que este problema é sem saída de tarefa e um "erro do utilizador de autenticar" nos registos de operação:

  ![Erro de utilizador de autenticação do Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Para resolver este problema, pare o seu trabalho em execução e vá para a saída do Power BI. Selecione o **renovar a autorização** ligação e reinicie a tarefa da **data/hora da última parado** para evitar a perda de dados.

  ![Renovar a autorização do Power BI para saída](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Armazenamento de Tabelas
[Armazenamento de tabelas do Azure](../storage/common/storage-introduction.md) oferece armazenamento de elevada disponibilidade e escalável em massa, para que um aplicativo pode dimensionar automaticamente para satisfazer o pedido de utilizador. O Table storage é o arquivo chaves/atributos NoSQL da Microsoft, qual deles pode tirar partido para dados estruturados com menos restrições no esquema. Armazenamento de tabelas do Azure pode ser utilizado para armazenar dados de persistência e obtenção de eficiente.

A tabela abaixo lista os nomes de propriedade e a respetiva descrição para a criação de uma saída de tabela.

| Nome da propriedade | descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este armazenamento de tabela. |
| Conta de armazenamento |O nome da conta de armazenamento em que estão a enviar a saída. |
| Chave da conta de armazenamento |A chave de acesso associada com a conta de armazenamento. |
| Nome da Tabela |O nome da tabela. A tabela é criada, se não existir. |
| Chave de partição |O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador exclusivo para a partição dentro de uma determinada tabela que forma a primeira parte da chave primária de uma entidade. É um valor de cadeia de caracteres que pode ser até 1 KB de tamanho. |
| Chave da fila |O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo de uma entidade dentro de uma determinada partição. Isso constitui a segunda parte da chave primária de uma entidade. A chave de linha é um valor de cadeia de caracteres que pode ser até 1 KB de tamanho. |
| Tamanho do batch |O número de registos para uma operação em lote. A predefinição (100) é suficiente para a maioria das tarefas. Consulte a [especificação de operação em lote tabela](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obter mais detalhes sobre como modificar esta definição. |

## <a name="service-bus-queues"></a>Filas de Service Bus
[Filas do Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) oferecem um primeiro a entrar, entrega de mensagens First Out (FIFO) para um ou mais consumidores concorrentes. Normalmente, as mensagens devem ser recebidas e processadas pelos recetores na ordem temporal em que foram adicionadas à fila, e cada mensagem é recebida e processada por apenas um consumidor de mensagens.

A tabela abaixo lista os nomes de propriedade e a respetiva descrição para a criação de uma saída da fila.

| Nome da propriedade | descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para esta fila do Service Bus. |
| Espaço de nomes do Service Bus |Um espaço de nomes do Service Bus é um contentor para um conjunto de entidades de mensagens. |
| Nome da fila |O nome da fila do Service Bus. |
| Nome da política da fila |Quando cria uma fila, também pode criar políticas de acesso partilhado no separador Configurar da fila. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. |
| Chave da política da fila |A chave de acesso partilhado utilizada para autenticar o acesso ao espaço de nomes do Service Bus |
| Formato de serialização de eventos |Formato de serialização para dados de saída. JSON, CSV e Avro são suportados. |
| Codificação |Para CSV e JSON, o UTF-8 é o único formato de codificação suportado neste momento |
| Delimitador |Só é aplicável para a serialização de CSV. O Stream Analytics suporta um número de delimitadores comuns para serializar dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato |Só é aplicável para o tipo JSON. Separado por linhas Especifica que o resultado é formatado fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que o resultado é formatado como uma matriz de objetos JSON. |

É o número de partições [com base no SKU de barramento de serviço e no tamanho](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="service-bus-topics"></a>Tópicos de Service Bus
Apesar de filas do Service Bus fornecem um método de comunicação de um para um de remetente para o receptor, [tópicos do Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornecem uma forma de um-para-muitos de comunicação.

A tabela abaixo lista os nomes de propriedade e a respetiva descrição para a criação de uma saída de tabela.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este tópico do Service Bus. |
| Espaço de nomes do Service Bus |Um espaço de nomes do Service Bus é um contentor para um conjunto de entidades de mensagens. Quando tiver criado um novo Hub de eventos, também criou um espaço de nomes do Service Bus |
| Nome do tópico |Tópicos são entidades de mensagens, semelhantes a filas e hubs de eventos. Foram concebidos para recolher fluxos de eventos a partir de um número de diferentes dispositivos e serviços. Quando é criado um tópico, também é fornecido um nome específico. As mensagens enviadas para um tópico não está disponível, a menos que é criada uma subscrição, então verifique se existem uma ou mais subscrições sob o tópico |
| Nome da política do tópico |Quando cria um tópico, também pode criar políticas de acesso partilhado no separador Configurar do tópico. Cada política de acesso partilhado tem o nome, as permissões que definir e chaves de acesso |
| Chave da política do tópico |A chave de acesso partilhado utilizada para autenticar o acesso ao espaço de nomes do Service Bus |
| Formato de serialização de eventos |Formato de serialização para dados de saída. JSON, CSV e Avro são suportados. |
| Codificação |Se utilizar o formato JSON ou CSV, uma codificação tem de ser especificada. UTF-8 é o único formato de codificação suportado neste momento |
| Delimitador |Só é aplicável para a serialização de CSV. O Stream Analytics suporta um número de delimitadores comuns para serializar dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |

É o número de partições [com base no SKU de barramento de serviço e no tamanho](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[O Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é uma base de dados com múltiplos modelo distribuída globalmente, serviço de dimensionamento elástico ilimitado em todo o mundo, a consulta avançada e indexação automática através de modelos de dados sem esquema, oferece baixa latência garantida e líderes do setor SLAs abrangentes. Para saber mais sobre as opções de coleção do Cosmos DB para o Stream Analytics, consulte a [Stream Analytics com o Cosmos DB como saída](stream-analytics-documentdb-output.md) artigo.

Saída do Azure Cosmos DB do Stream Analytics não está atualmente disponível nas regiões do Azure Alemanha (T-Systems International) e Azure China (21Vianet).

> [!Note]
> Neste momento, o Azure Stream Analytics suporta apenas a ligação ao cosmos DB com **API do SQL**.
> Outras APIs do Azure Cosmos DB ainda não são suportados. Se ponto Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, os dados poderão não ser corretamente armazenados.

A tabela seguinte descreve as propriedades para a criação de uma saída do Azure Cosmos DB.

| Nome da propriedade | descrição |
| --- | --- |
| Alias de saída | Um alias para fazer referência esta saída na sua consulta do Stream Analytics. |
| Sink | BD do Cosmos |
| Opção de Importar | Escolher entre "Selecione o Cosmos DB da sua subscrição", ou a "definições de forneça Cosmos DB manualmente".
| ID de conta | O nome ou o ponto final do URI de conta do Cosmos DB. |
| Chave de conta | A chave de acesso partilhado para a conta do Cosmos DB. |
| Base de Dados | O nome de base de dados do Cosmos DB. |
| Padrão do nome de coleção | O nome da coleção ou seus padrão para as coleções a ser utilizado. <br />O formato de nome de coleção pode ser construído de acordo com o token {partition} opcional, onde as partições começam do 0. Dois exemplos:  <br />1. _MyCollection_ – uma coleção com o nome "MyCollection" tem de existir.  <br />2. _MyCollection {partition}_ – com base na coluna de particionamento. <br />As coleções de coluna de criação de partições tem de existir – "MyCollection0", "MyCollection1", "MyCollection2" e assim por diante. |
| Chave de partição | Opcional. Isto só é necessário se estiver a utilizar um token {partition} no seu padrão de nome de coleção.<br /> A chave de partição é o nome do campo em eventos de saída utilizado para especificar a chave de partições da saída nas coleções.<br /> Para saída única coleção, pode ser utilizada qualquer coluna de saída arbitrário. Por exemplo, PartitionId. |
| ID do documento |Opcional. O nome do campo em eventos de saída utilizado para especificar a chave primária no qual insert ou update baseiam-se operações.

## <a name="azure-functions"></a>Funções do Azure
As Funções do Azure são um serviço de computação sem servidor lhe que permite executar código a pedido sem ter de aprovisionar ou gerir explicitamente uma infraestrutura. Permite-lhe implementar código acionado por eventos que ocorrem no Azure ou serviços de terceiros. Esta capacidade das funções do Azure para responder a acionadores torna-as numa saída natural para um Azure Stream Analytics. Este adaptador de saída permite aos utilizadores ligar as funções do Azure Stream Analytics e executar um script ou fragmento de código em resposta a uma variedade de eventos.

Saída de funções do Azure do Stream Analytics não está atualmente disponível nas regiões do Azure Alemanha (T-Systems International) e Azure China (21Vianet).

O Azure Stream Analytics invoca funções do Azure através de acionadores HTTP. O novo adaptador de saída da função do Azure está disponível com as seguintes propriedades configuráveis:

| Nome da propriedade | descrição |
| --- | --- |
| Function app |Nome da sua aplicação de funções do Azure |
| Função |Nome da função na sua aplicação de funções do Azure |
| Chave |Se pretender utilizar uma função do Azure a partir de outra subscrição, pode fazê-ao fornecer a chave para aceder à sua função |
| Tamanho máximo de lote |Esta propriedade pode ser utilizada para definir o tamanho máximo para cada lote de saída que é enviado para a sua função do Azure. É a unidade de entrada em bytes. Por predefinição, este valor é 262.144 bytes (256 KB) |
| Contagem máxima de lotes  |Como o nome indica, esta propriedade permite-lhe especificar o número máximo de eventos em cada lote que é enviado para as funções do Azure. O valor de contagem de lote máximo predefinido é 100 |

Quando o Azure Stream Analytics recebe 413 exceção de (protocolo http pedir entidade demasiado grande) da função do Azure, ela reduz o tamanho dos lotes que envia para as funções do Azure. No código da função do Azure, utilize esta exceção para se certificar de que o Azure Stream Analytics não envia lotes demasiado grandes. Além disso, certifique-se de que os valores de contagem e tamanho de lote máximo utilizados na função são consistentes com os valores introduzidos no portal do Stream Analytics.

Além disso, numa situação onde não existe nenhum evento de destino numa janela de tempo, nenhuma saída é gerada. Como resultado, a função de computeResult não é chamada. Este comportamento é consistente com as funções de agregação em janela internas.

## <a name="partitioning"></a>Criação de partições

A tabela seguinte resume o suporte de partição e o número de gravadores de saída para cada tipo de saída:

| Tipo de saída | Suporte de criação de partições | Chave de partição  | Vários autores de saída |
| --- | --- | --- | --- |
| Azure Data Lake Store | Sim | Utilize {date} e tokens de {time} no padrão do prefixo do caminho. Escolha o formato de data, como aaaa/MM/DD, aaaa/MM/AAAA, DD-MM-AAAA. HH é utilizada para o formato de hora. | Segue-se a criação de partições entrada para [consultas totalmente ponto pode ser paralelizadas](stream-analytics-scale-jobs.md). |
| Base de Dados SQL do Azure | Sim | Com base na cláusula PARTITION BY na consulta | Segue-se a criação de partições entrada para [consultas totalmente ponto pode ser paralelizadas](stream-analytics-scale-jobs.md). Para saber mais sobre a obtenção da, melhor escrever o desempenho de taxa de transferência quando está carregando dados na base de dados do SQL Azure, visite [saída de Azure Stream Analytics para a base de dados do Azure SQL](stream-analytics-sql-output-perf.md). |
| Armazenamento de Blobs do Azure | Sim | Utilize {date} e tokens de {time} a partir de seus campos de evento no padrão de caminho. Escolha o formato de data, como aaaa/MM/DD, aaaa/MM/AAAA, DD-MM-AAAA. HH é utilizada para o formato de hora. Saída de BLOBs pode ser particionada por um atributo único evento personalizado {fieldname} ou {datetime:\<especificador >}. | Segue-se a criação de partições entrada para [consultas totalmente ponto pode ser paralelizadas](stream-analytics-scale-jobs.md). |
| Hub de Eventos do Azure | Sim | Sim | Varia consoante o alinhamento da partição.<br /> Quando a saída de chave de partição igualmente está alinhada com a montante () consulta passo anterior, o número de gravadores de Hub de eventos é o mesmo número de saída de partições do Hub de eventos. Cada writer usa do EventHub [EventHubSender classe](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) para enviar eventos para a partição específica. <br /> Quando o resultado da chave de partição não está alinhada com a montante () consulta passo anterior, o número de gravadores de Hub de eventos é igual ao número de partições nesse passo anterior. Cada writer usa EventHubClient [SendBatchAsync classe](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) para enviar eventos para todas as partições de saída. |
| Power BI | Não | Nenhuma | Não aplicável. |
| Armazenamento de Tabelas do Azure | Sim | Qualquer coluna de saída.  | Segue-se a criação de partições entrada para [totalmente em paralelo consultas](stream-analytics-scale-jobs.md). |
| Tópico de barramento de serviço do Azure | Sim | Automaticamente escolhido. O número de partições se baseia a [SKU de barramento de serviço e o tamanho](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições do tópico de saída.  |
| Fila de barramento de serviço do Azure | Sim | Automaticamente escolhido. O número de partições se baseia a [SKU de barramento de serviço e o tamanho](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições na fila de saída. |
| Azure Cosmos DB | Sim | Utilize o token {partition} no padrão de nome de coleção. valor de {partition} baseia-se a cláusula PARTITION BY na consulta. | Segue-se a criação de partições entrada para [totalmente em paralelo consultas](stream-analytics-scale-jobs.md). |
| Funções do Azure | Não | Nenhuma | Não aplicável. |

Se o adaptador de saída não for particionado, falta de dados de uma partição de entrada fará com que um atraso até ao montante de chegada tardia do tempo.  Nesses casos, a saída é intercalada com um único gravador que poderá provocar afunilamentos no seu pipeline. Para saber mais sobre a política de chegada tardia, visite [considerações de ordem de eventos do Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída
O Azure Stream Analytics utiliza lotes de tamanho variável, para processar eventos e escrita para saídas. Normalmente, o mecanismo de Stream Analytics não escreve uma mensagem por vez e utiliza lotes para uma eficiência. Quando a entrada e a taxa de eventos de saída é alta, ele usa lotes maiores. Quando a taxa de saída é baixa, ele usa lotes mais pequenos para manter a latência baixa.

A tabela seguinte explica algumas das considerações para a criação de batches de saída:

| Tipo de saída | Tamanho máximo de mensagem | Otimização de tamanho de lote |
| :--- | :--- | :--- |
| Azure Data Lake Store | Consulte [limites de armazenamento do Data Lake](../azure-subscription-service-limits.md#data-lake-store-limits) | Até 4 MB por operação de escrita |
| Base de Dados SQL do Azure | Inserir 10.000 máx. de linhas por em massa única<br />Mínimo de 100 linhas por inserção em massa única <br />Consulte também [limita de SQL do Azure](../sql-database/sql-database-resource-limits.md) |  Todos os lotes é inicialmente em massa inserida com tamanho máximo do batch e podem dividir a batch em metade, (até o tamanho de lote de Mín), com base em caso de erro não permanente de SQL. |
| Armazenamento de Blobs do Azure | Consulte [limites de armazenamento do Azure](../azure-subscription-service-limits.md#storage-limits) | Tamanho máximo de bloco de Blob é de 4 MB<br />Contagem máxima de bock de Blob é 50000 |
| Hub de Eventos do Azure   | 256 KB por mensagem <br />Consulte também [limita os Hubs de eventos](../event-hubs/event-hubs-quotas.md) |   Quando a criação de partições de entrada saída não alinha, cada evento é fornecido individualmente num EventData e enviado num lote de até ao tamanho máximo de mensagem (1 MB para Premium SKU). <br /><br />  Quando a criação de partições de entrada-saída está alinhada, vários eventos são incluídos numa única EventData até o tamanho de mensagem máximo e enviados.  |
| Power BI | Consulte [limita a API de Rest do Power BI](https://msdn.microsoft.com/library/dn950053.aspx) |
| Armazenamento de Tabelas do Azure | Consulte [limites de armazenamento do Azure](../azure-subscription-service-limits.md#storage-limits) | Padrão é 100 entidades por transação única e pode ser configurado para um valor menor, conforme necessário. |
| Fila do Service Bus do Azure   | 256 KB por mensagem<br /> Consulte também [limita do Service Bus](../service-bus-messaging/service-bus-quotas.md) | Evento único por mensagem |
| Tópico de barramento de serviço do Azure | 256 KB por mensagem<br /> Consulte também [limita do Service Bus](../service-bus-messaging/service-bus-quotas.md) | Evento único por mensagem |
| Azure Cosmos DB   | Consulte [limites do Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits) | Batch de tamanho e frequência é ajustadas dinamicamente com base CosmosDB as respostas de escrita. <br /> Não existem limitações predeterminadas do Stream Analytics. |
| Funções do Azure   | | Tamanho de lote de predefinido é 262.144 bytes (256 KB). <br /> Contagem de eventos padrão por lote é 100. <br /> O tamanho do lote é configurável e pode aumentar ou diminuir o Stream Analytics [opções de saída](#azure-functions).

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
