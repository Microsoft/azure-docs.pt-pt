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
ms.openlocfilehash: 52bbb52b13a3606e3ddc8deca2da8505233c9352
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062011"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Saída do Azure Stream Analytics ao Azure Cosmos DB  
Stream Analytics pode visar [do Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para saída JSON, permitindo que consultas de arquivamento e baixa latência de dados em dados não estruturados de JSON. Este documento abrange algumas das melhores práticas para implementar esta configuração.

Para aqueles que não estão familiarizados com o Cosmos DB, dê uma olhada [percurso de aprendizagem do Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para começar a utilizar. 

> [!Note]
> Neste momento, o Azure Stream Analytics suporta apenas a ligação para utilizar o Azure Cosmos DB **API do SQL**.
> Outras APIs do Azure Cosmos DB ainda não são suportados. Se ponto Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, os dados poderão não ser corretamente armazenados. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Noções básicas do Cosmos DB como um destino de saída
A saída de Azure Cosmos DB no Stream Analytics permite gravar seus resultados de processamento de fluxo como saída JSON em seus contêineres de Cosmos DB. Stream Analytics não cria contêineres em seu banco de dados, em vez disso, exige que você os crie antecipadamente. Isso é para que os custos de cobrança de contêineres de Cosmos DB sejam controlados por você e para que você possa ajustar o desempenho, a consistência e a capacidade de seus contêineres diretamente usando as [APIs de Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Você deve adicionar 0.0.0.0 à lista de IPs permitidos de seu firewall Azure Cosmos DB.

Algumas das opções de contêiner de Cosmos DB são detalhadas abaixo.

## <a name="tune-consistency-availability-and-latency"></a>Ajustar a consistência, disponibilidade e latência
Para corresponder aos requisitos do aplicativo, Azure Cosmos DB permite ajustar o banco de dados e os contêineres e fazer compensações entre consistência, disponibilidade, latência e taxa de transferência. Dependendo do que níveis de consistência de leitura suas necessidades de cenário em relação a ler e escrever a latência, que pode escolher um nível de consistência na sua conta de base de dados. A taxa de transferência pode ser melhorada com a expansão de RUs (unidades de solicitação) no contêiner. Também por padrão, Azure Cosmos DB habilita a indexação síncrona em cada operação CRUD para seu contêiner. Essa é outra opção útil para controlar o desempenho de leitura/escrita no Azure Cosmos DB. Para obter mais informações, reveja os [alterar os níveis de consistência da base de dados e consulta](../cosmos-db/consistency-levels.md) artigo.

## <a name="upserts-from-stream-analytics"></a>Upserts do Stream Analytics
Stream Analytics integração com o Azure Cosmos DB permite inserir ou atualizar registros em seu contêiner com base em uma determinada coluna de ID de documento. Isso também é referido como um *Upsert*.

Stream Analytics utiliza uma abordagem de upsert otimista, onde as atualizações apenas são efetuadas quando insert falha com um conflito de ID do documento. Com o nível de compatibilidade 1,0, essa atualização é executada como um PATCH, de modo que permite atualizações parciais no documento, ou seja, a adição de novas propriedades ou a substituição de uma propriedade existente é executada de forma incremental. No entanto, as alterações nos valores das propriedades de matriz no seu resultado de documento JSON da matriz todo getting substituído, ou seja, a matriz não está intercalado. Com o 1,2, o comportamento Upsert é modificado para inserir ou substituir o documento. Isso é descrito mais detalhadamente na seção nível de compatibilidade 1,2 abaixo.

Se o documento JSON de entrada tem um campo de ID existente, que o campo é automaticamente utilizado como a coluna de ID do documento no Cosmos DB e quaisquer gravações subseqüentes são tratadas como tal, o que leva a uma das situações:
- IDs exclusivos levam para inserir
- IDs duplicados e de "ID do documento' definido como"ID"leva a upsert
- duplicar IDs e ID do documento não conjunto leva a erro, após o primeiro documento

Se quiser salvar <i>todos os</i> documentos, incluindo as que têm um ID duplicado, mudar o nome do campo de ID na sua consulta (com a palavra-chave AS) e permitir que o Cosmos DB, criar o campo de ID ou substitua o ID de valor de outra coluna (usando a palavra-chave AS ou pelo Utilize a definição de "ID do documento").

## <a name="data-partitioning-in-cosmos-db"></a>Criação de partições de dados no Cosmos DB
Azure Cosmos DB [](../cosmos-db/partition-data.md) contêineres ilimitados são a abordagem recomendada para particionar seus dados, já que Azure Cosmos DB dimensiona automaticamente as partições com base em sua carga de trabalho. Ao gravar em contêineres ilimitados, Stream Analytics usa como muitos gravadores paralelos como a etapa de consulta anterior ou o esquema de particionamento de entrada.
> [!NOTE]
> Neste momento, Azure Stream Analytics dá suporte apenas a contêineres ilimitados com chaves de partição no nível superior. Por exemplo, `/region` é suportada. Aninhados chaves de partição (por exemplo, `/region/name`) não são suportadas. 

Dependendo de sua escolha de chave de partição, você poderá receber este _aviso_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

É importante escolher uma propriedade de chave de partição que tenha um número de valores distintos e permitir que você distribua sua carga de trabalho uniformemente entre esses valores. Como um artefato natural de particionamento, as solicitações que envolvem a mesma chave de partição são limitadas pela taxa de transferência máxima de uma única partição. Além disso, o tamanho do armazenamento para documentos pertencentes à mesma chave de partição é limitado a 10 GB. Uma chave de partição ideal é aquela que aparece com frequência como um filtro em suas consultas e tem cardinalidade suficiente para garantir que sua solução seja escalonável.

Uma chave de partição também é o limite para transações nos gatilhos e procedimentos armazenados do DocumentDB. Você deve escolher a chave de partição para que os documentos que ocorrem em transações compartilhem o mesmo valor de chave de partição. O artigo [particionamento no cosmos DB](../cosmos-db/partitioning-overview.md) fornece mais detalhes sobre como escolher uma chave de partição.

Para contêineres de Azure Cosmos DB fixos, Stream Analytics não permite escalar verticalmente ou horizontalmente quando estiverem cheios. Eles têm um limite superior de 10 GB e 10 000 de RU/s débito.  Para migrar os dados a partir de um contentor fixo para um contentor ilimitado (por exemplo, um com uma chave de partição e, pelo menos, 1.000 RU/s), tem de utilizar o [ferramenta de migração de dados](../cosmos-db/import-data.md) ou o [biblioteca de feed de alterações](../cosmos-db/change-feed.md).

A capacidade de gravar em vários contêineres fixos está sendo preterida e não é recomendável para escalar horizontalmente seu trabalho de Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Taxa de transferência aprimorada com nível de compatibilidade 1,2
Com o nível de compatibilidade 1,2, o Stream Analytics dá suporte à integração nativa para gravação em massa em Cosmos DB. Isso permite a gravação eficaz para Cosmos DB com a maximização da taxa de transferência e o tratamento eficiente de solicitações de limitação. O mecanismo de escrita aprimorado está disponível em um novo nível de compatibilidade devido a uma diferença de comportamento de Upsert.  Antes de 1,2, o comportamento do Upsert é inserir ou mesclar o documento. Com o 1,2, o comportamento upserts é modificado para inserir ou substituir o documento.

Antes de 1,2, o usa um procedimento armazenado personalizado para Upsert em massa documentos por chave de partição em Cosmos DB, em que um lote é gravado como uma transação. Mesmo quando um único registro atinge um erro transitório (limitação), todo o lote deve ser repetido. Isso tornou os cenários com uma limitação ainda razoável relativamente mais lenta. A comparação a seguir mostra como esses trabalhos se comportariam com o 1,2.

O exemplo a seguir mostra dois trabalhos de Stream Analytics idênticos lendo da mesma entrada do hub de eventos. Ambos os trabalhos de Stream Analytics são [totalmente particionados](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) com uma consulta de passagem e gravam em contêineres CosmosDB idênticos. As métricas à esquerda são do trabalho configurado com o nível de compatibilidade 1,0 e aquelas à direita são configuradas com 1,2. A chave de partição de um contêiner de Cosmos DB é um GUID exclusivo proveniente do evento de entrada.

![comparação de métricas do Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

A taxa de eventos de entrada no Hub de eventos é 2x maior que os contêineres de Cosmos DB (20 mil RUs) são configurados para entrada, portanto, a limitação é esperada em Cosmos DB. No entanto, o trabalho com 1,2, está escrevendo consistentemente em uma taxa de transferência mais alta (eventos de saída/minuto) e com uma menor utilização média de SU%. Em seu ambiente, essa diferença dependerá de mais alguns fatores, como opções de formato de evento, tamanho de evento/mensagem de entrada, chaves de partição, consulta etc.

![comparação de métricas do cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Com o 1,2, o Stream Analytics é mais inteligente na utilização de 100% da taxa de transferência disponível em Cosmos DB com poucos reenvios de limitação/limitação de taxa. Isso fornece uma experiência melhor para outras cargas de trabalho, como consultas em execução no contêiner ao mesmo tempo. Caso você precise experimentar como o ASA é dimensionado com Cosmos DB como um coletor de 1K para 10 mil mensagens/segundo, aqui está um [projeto de exemplos do Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) que permite fazer isso.
Observe que Cosmos DB taxa de transferência de saída é idêntica com 1,0 e 1,1. Como 1,2 não é o padrão no momento, você pode [definir o nível de compatibilidade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) para um trabalho de Stream Analytics usando o portal ou usando a chamada de [API REST de trabalho Create](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). É *altamente recomendável* usar o nível de compatibilidade 1,2 no ASA com Cosmos DB.



## <a name="cosmos-db-settings-for-json-output"></a>Definições de cosmos DB para a saída JSON

Criar o Cosmos DB como uma saída no Stream Analytics gera uma linha de comandos para obter informações, como mostrado abaixo. Esta seção fornece uma explicação sobre a definição de propriedades.

![ecrã de saída do documentdb stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Descrição|
|-------------   | -------------|
|Alias de saída    | Um alias para fazer referência a essa saída em sua consulta ASA.|
|Subscription    | Escolha a assinatura do Azure.|
|ID da Conta      | O nome ou URI do ponto de extremidade da conta de Azure Cosmos DB.|
|Chave de conta     | A chave de acesso compartilhado para a conta de Azure Cosmos DB.|
|Base de Dados        | O nome do banco de dados Azure Cosmos DB.|
|Nome do contentor | O nome do contêiner a ser usado. `MyContainer`é um exemplo de entrada válida: um contêiner `MyContainer` chamado deve existir.  |
|ID do documento     | Opcional. O nome da coluna em eventos de saída utilizado como a chave exclusiva no qual inserção ou atualização de operações devem basear-se. Se deixado em branco, serão possível inserir todos os eventos, sem qualquer opção de atualização.|

## <a name="error-handling-and-retries"></a>Tratamento de erros e novas tentativas

No caso de uma falha transitória, a indisponibilidade ou a limitação do serviço ao enviar eventos para Cosmos DB, Stream Analytics repete indefinidamente para concluir a operação com êxito. No entanto, existem algumas falhas para as quais não são executadas tentativas, que são as seguintes:

- Não autorizado (código de erro http 401)
- Não encontrado (código de erro http 404)
- Proibido (código de erro HTTP 403)
- BadRequest (código de erro http 400)
