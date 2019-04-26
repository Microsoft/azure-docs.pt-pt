---
title: Saída do Azure Stream Analytics para o Cosmos DB
description: Este artigo descreve como utilizar o Azure Stream Analytics para guardar a saída para o Azure Cosmos DB para a saída JSON, para o arquivo de dados e consultas de baixa latência nos dados não estruturados de JSON.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: 734cf09869e5a2df5f9a505a3cb8ccc7bc2338d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402310"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Saída do Azure Stream Analytics ao Azure Cosmos DB  
Stream Analytics pode visar [do Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para saída JSON, permitindo que consultas de arquivamento e baixa latência de dados em dados não estruturados de JSON. Este documento abrange algumas das melhores práticas para implementar esta configuração.

Para aqueles que não estão familiarizados com o Cosmos DB, dê uma olhada [percurso de aprendizagem do Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para começar a utilizar. 

> [!Note]
> Neste momento, o Azure Stream Analytics suporta apenas a ligação para utilizar o Azure Cosmos DB **API do SQL**.
> Outras APIs do Azure Cosmos DB ainda não são suportados. Se ponto Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, os dados poderão não ser corretamente armazenados. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Noções básicas do Cosmos DB como um destino de saída
A saída do Azure Cosmos DB no Stream Analytics permite escrever a sua transmissão em fluxo processando os resultados como saída JSON em suas coleções de Cosmos DB. Stream Analytics não criar coleções na base de dados, em vez disso, que seja necessário criá-los de antemão. Isto é, para que os custos de faturas de coleções do Cosmos DB são controlados por si e para que pode ajustar o desempenho, a consistência e a capacidade de seus conjuntos de diretamente com o [Cosmos DB APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Tem de adicionar 0.0.0.0 à lista de IPs permitidos de firewall do Azure Cosmos DB.

Algumas das opções de coleção do Cosmos DB são detalhadas abaixo.

## <a name="tune-consistency-availability-and-latency"></a>Ajustar a consistência, disponibilidade e latência
Para corresponder aos requisitos de aplicação, o Azure Cosmos DB permite-lhe ajustar a base de dados e coleções e fazer compensações entre consistência, disponibilidade e latência. Dependendo do que níveis de consistência de leitura suas necessidades de cenário em relação a ler e escrever a latência, que pode escolher um nível de consistência na sua conta de base de dados. Também por predefinição, a Azure Cosmos DB permite a indexação síncrona de cada operação CRUD à sua coleção. Essa é outra opção útil para controlar o desempenho de leitura/escrita no Azure Cosmos DB. Para obter mais informações, reveja os [alterar os níveis de consistência da base de dados e consulta](../cosmos-db/consistency-levels.md) artigo.

## <a name="upserts-from-stream-analytics"></a>Upserts do Stream Analytics
Integração do Stream Analytics com o Azure Cosmos DB permite-lhe inserir ou atualizar registos na sua coleção com base numa determinada coluna de ID do documento. Isso também é referido como um *Upsert*.

Stream Analytics utiliza uma abordagem de upsert otimista, onde as atualizações apenas são efetuadas quando insert falha com um conflito de ID do documento. Com o 1.0 de nível de compatibilidade, esta atualização é executada como um PATCH, para que ele permite que as atualizações parciais para o documento, ou seja, a adição de novas propriedades ou substituir que uma propriedade existente é realizada de forma incremental. No entanto, as alterações nos valores das propriedades de matriz no seu resultado de documento JSON da matriz todo getting substituído, ou seja, a matriz não está intercalado. Com 1.2, o comportamento de upsert é modificado para inserir ou substituir o documento. Isso é descrito mais detalhadamente na secção 1.2 de nível de compatibilidade abaixo.

Se o documento JSON de entrada tem um campo de ID existente, que o campo é automaticamente utilizado como a coluna de ID do documento no Cosmos DB e quaisquer gravações subseqüentes são tratadas como tal, o que leva a uma das situações:
- IDs exclusivos levam para inserir
- IDs duplicados e de "ID do documento' definido como"ID"leva a upsert
- duplicar IDs e ID do documento não conjunto leva a erro, após o primeiro documento

Se quiser salvar <i>todos os</i> documentos, incluindo as que têm um ID duplicado, mudar o nome do campo de ID na sua consulta (com a palavra-chave AS) e permitir que o Cosmos DB, criar o campo de ID ou substitua o ID de valor de outra coluna (usando a palavra-chave AS ou pelo Utilize a definição de "ID do documento").

## <a name="data-partitioning-in-cosmos-db"></a>Criação de partições de dados no Cosmos DB
O Azure Cosmos DB [ilimitado](../cosmos-db/partition-data.md) contentores são a abordagem recomendada para a partição dos dados, como o Azure Cosmos DB automaticamente dimensiona-se partições com base na carga de trabalho. Ao escrever os contentores ilimitados, o Stream Analytics utiliza os escritores paralelas como anterior consulta passo ou entrada de esquema de partições.
> [!Note]
> Neste momento, o Azure Stream Analytics suporta apenas coleções ilimitadas com chaves de partição no nível superior. Por exemplo, `/region` é suportada. Aninhados chaves de partição (por exemplo, `/region/name`) não são suportadas. 

Para coleções do Azure Cosmos DB fixas, o Stream Analytics não permite nenhuma forma de aumentar vertical ou horizontalmente, uma vez que estão cheias. Eles têm um limite superior de 10 GB e 10 000 de RU/s débito.  Para migrar os dados a partir de um contentor fixo para um contentor ilimitado (por exemplo, um com uma chave de partição e, pelo menos, 1.000 RU/s), tem de utilizar o [ferramenta de migração de dados](../cosmos-db/import-data.md) ou o [biblioteca de feed de alterações](../cosmos-db/change-feed.md).

Gravar em vários contentores fixos está a ser preterida e não é a abordagem recomendada para aumentar horizontalmente a tarefa de Stream Analytics. O artigo [criação de partições e dimensionamento no Cosmos DB](../cosmos-db/sql-api-partition-data.md) fornecem mais detalhes.

## <a name="improved-throughput-with-compatibility-level-12"></a>Melhor débito com 1.2 de nível de compatibilidade
Com o nível de compatibilidade 1.2, o Stream Analytics suporta a integração nativa em massa escrever para o Cosmos DB. Isto permite que escrever com eficiência no Cosmos DB com a maximizar o débito e eficiente de pedidos de limitação do identificador. O mecanismo de escrita melhorado está disponível num novo nível de compatibilidade devido a uma diferença de comportamento de upsert.  Antes de 1.2, o comportamento de upsert é inserir ou intercalar o documento. Com 1.2, o comportamento de upserts é modificado para inserir ou substituir o documento. 

Antes de 1.2, usa um procedimento armazenado personalizado para documentos de upsert em massa por chave de partição no Cosmos DB, onde um lote é escrito como uma transação. Mesmo quando um único registo atinge um erro transitório (limitação), o lote inteiro deve ser repetido. Isso tornou cenários com ainda razoável a limitação relativamente mais lentamente. Comparação a seguir mostra como essas tarefas seriam se comportar com 1.2.

Abaixo configuração mostra duas tarefas idênticas de Stream Analytics ler a partir da mesma entrada (hub de eventos). Ambas as tarefas do Stream Analytics estão [totalmente particionados](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) com uma consulta pass-through e a gravação idênticas coleções do cosmos DB. Métricas do lado esquerdo é da tarefa configurada com o nível de compatibilidade 1.0 e aqueles à direita está configurado com 1.2. Chave de partição de coleções do cosmos DB é um guid exclusivo, proveniente do evento de entrada.

![comparação de métricas do Stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Taxa de receção de eventos no Hub de eventos é 2 vezes maior do que as coleções do Cosmos DB (20 mil RUs) estão configuradas para ingestão, portanto, a limitação é esperada no Cosmos DB. No entanto, a tarefa com 1.2, consistentemente escreve a um débito mais elevado (eventos/minuto de saída) e com uma utilização mais baixa de % SU média. No seu ambiente, essa diferença dependerá poucos mais fatores, tais como a opção de formato de evento, o tamanho de entrada/mensagem de evento, as chaves de partição, consulta etc.

![comparação de métricas do cosmos db](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Com 1.2, o Stream Analytics é mais inteligente na utilização de 100% da taxa de transferência disponível no Cosmos DB com muito poucas resubmissions de limitação de velocidade/limitação. Isso possibilita uma melhor experiência para outras cargas de trabalho, como consultas em execução na coleção ao mesmo tempo. Caso seja necessário experimentar como ASA aumenta horizontalmente com o Cosmos DB como um sink para 1 mil a 10 mil mensagens por segundo, aqui está uma [projeto de exemplos do azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) que permite-lhe fazê-lo.
Tenha em atenção que o débito de saída do Cosmos DB é idêntico com 1.0 e 1.1. Uma vez que 1.2 não atualmente é o padrão, pode [defina o nível de compatibilidade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) para uma tarefa do Stream Analytics através do portal ou utilizando o [chamada de REST API de tarefa de criar](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Ele possui *vivamente recomendado* utilizar 1.2 de nível de compatibilidade no ASA com o Cosmos DB. 



## <a name="cosmos-db-settings-for-json-output"></a>Definições de cosmos DB para a saída JSON

Criar o Cosmos DB como uma saída no Stream Analytics gera uma linha de comandos para obter informações, como mostrado abaixo. Esta seção fornece uma explicação sobre a definição de propriedades.

![ecrã de saída do documentdb stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Descrição|
|-------------   | -------------|
|Alias de saída    | Um alias para fazer referência esta saída na sua consulta do ASA.|
|Subscrição    | Escolha a subscrição do Azure.|
|ID da Conta      | O nome ou o ponto final do URI de conta do Azure Cosmos DB.|
|Chave de conta     | A chave de acesso partilhado para a conta do Azure Cosmos DB.|
|Base de Dados        | O nome de base de dados do Azure Cosmos DB.|
|Padrão do nome de coleção | O nome da coleção para a coleção a ser utilizado. `MyCollection` é uma entrada válida de exemplo - uma coleção com o nome `MyCollection` tem de existir.  |
|ID do documento     | Opcional. O nome da coluna em eventos de saída utilizado como a chave exclusiva no qual inserção ou atualização de operações devem basear-se. Se deixado em branco, serão possível inserir todos os eventos, sem qualquer opção de atualização.|
