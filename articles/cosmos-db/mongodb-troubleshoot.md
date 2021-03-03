---
title: Resolução de problemas erros comuns na API da Azure Cosmos DB para a Mongo DB
description: Este doc discute as formas de resolver problemas comuns encontrados na API da Azure Cosmos DB para o MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 13ac90ae70262f2f6781f5f40ec67da4bf74ab68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661278"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Resolução de problemas comuns na API da Azure Cosmos DB para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O seguinte artigo descreve erros e soluções comuns para implementações usando a API API AZure Cosmos DB para a MongoDB.

>[!Note]
> A Azure Cosmos DB não acolhe o motor MongoDB. Fornece uma implementação da versão [4.0](mongodb-feature-support-40.md), [3.6](mongodb-feature-support-36.md), e suporte legado para [a versão 3.2](mongodb-feature-support.md)do protocolo de fio. Portanto, alguns destes erros só são encontrados na API da Azure Cosmos para a MongoDB.

## <a name="common-errors-and-solutions"></a>Erros comuns e soluções

| Código       | Erro                | Descrição  | Solução  |
|------------|----------------------|--------------|-----------|
| 2 | A trajetória de índice correspondente ao item de encomenda especificado é excluída ou a ordem por consulta não tem um índice composto correspondente do qual possa ser servido. | A consulta pede uma ordenação num campo que não está indexado. | Crie um índice correspondente (ou índice composto) para a consulta de ordenação que está a ser tentada. |
| 13 | Não autorizado | O pedido não tem as permissões para ser concluído. | Certifique-se de que define as permissões adequadas para a sua base de dados e recolha.  |
| 16 | Inválido | O pedido especificado tem um comprimento inválido. | Se estiver a utilizar a função de explicação, certifique-se de que fornece apenas uma operação. |
| 26 | NamespaceNotFound | Não é possível encontrar a base de dados ou a coleção que está a ser referenciada na consulta. | Verifique se o nome da base de dados/coleção corresponde exatamente ao nome na consulta.|
| 50 | ExceededTimeLimit | O pedido excedeu o tempo limite de 60 segundos de execução. |  Pode haver muitas causas para este erro. Uma das causas é quando a capacidade das unidades de pedido atualmente alocadas não é suficiente para concluir o pedido. Isto pode ser resolvido ao aumentar as unidades de pedido dessa coleção ou base de dados. Noutros casos, este erro pode ser solucionado através da divisão de um pedido grande em partes menores. Repetir uma operação de escrita que recebeu este erro pode resultar numa escrita duplicada. <br><br>Se estiver a tentar eliminar grandes quantidades de dados sem que isto tenha impacto sobre as RUs: <br>-Considere a utilização de TTL (baseado em timetamp): [Expire dados com API da Azure Cosmos DB para o MongoDB](./mongodb-time-to-live.md) <br>-Utilize o tamanho cursor/lote para executar a eliminação. Pode obter um único documento de cada vez e eliminá-lo através de um ciclo. Esta operação ajudará a eliminar dados lentamente sem afetar a aplicação de produção.|
| 61 | ShardKeyNotFound | O documento no pedido não continha a chave shard da coleção (chave de partição do Azure Cosmos DB). | Verifique se a chave shard da coleção está a ser utilizada no pedido.|
| 66 | ImmutableField | O pedido está a tentar alterar um campo imutável | Os campos de "id" são imutáveis. Certifique-se de que o seu pedido não tenta atualizar este campo. |
| 67 | CannotCreateIndex | Não é possível concluir o pedido para criar um índice. | Podem ser criados até 500 índices de campo único num contentor. Podem ser incluídos até oito campos num índice composto (os índices compostos são suportados na versão 3.6e superior). |
| 115 | CommandNotSupported | O pedido tentado não é suportado. | Deve indicar detalhes adicionais sobre o erro. Se esta funcionalidade for importante para as suas implementações, avise-nos criando um bilhete de apoio no [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) |
| 11000 | DuplicateKey | A chave shard (chave de partição do Azure Cosmos DB) do documento que está a inserir já existe na coleção ou foi violada uma restrição de campo do índice exclusivo. | Utilize a função update() para atualizar um documento existente. Se a restrição de campo do índice exclusivo tiver sido violada, insira ou atualize o documento com um valor de campo que ainda não exista na partição/shard. |
| 16500 | TooManyRequests  | O número total de unidades de pedido consumidas é maior do que a taxa pedido/unidade aprovisionada para a coleção e foi limitada. | Pondere dimensionar o débito atribuído a um contentor ou a um conjunto de contentores do portal do Azure ou pode repetir a operação. Se [ativar o SSR](prevent-rate-limiting-errors.md) (retentação do lado do servidor), o Azure Cosmos DB recauchuta automaticamente os pedidos que falham devido a este erro. |
| 16501 | LimiteMemóriaExcedido | Como um serviço multi-inquilino, a operação ultrapassou a alocação de memória do cliente. | Reduza o âmbito da operação através de critérios de consulta mais restritivos ou contacte o suporte a partir do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nome da fase do pipeline não reconhecido. | O nome da fase no pedido de pipeline de agregação não foi reconhecido. | Verifique se todos os nomes de pipelines de agregação são válidos no pedido. |
| - | Problemas da versão de transmissão do MongoDB | As versões mais antigas dos controladores do MongoDB não conseguem detetar o nome da conta do Azure Cosmos nas cadeias de ligação. | Append *appName=@**contaName** @* no final da sua API de ligação cosmos DB para a cadeia de conexão MongoDB, onde o nome de ***contaName*** é o seu nome de conta Cosmos DB. |
| - | Problemas de rede do cliente do MongoDB (como exceções de socket ou endOfStream)| Falha no pedido da rede. Geralmente causado por uma ligação TCP inativa que o cliente do MongoDB está a tentar utilizar. Os controladores do MongoDB utilizam frequentemente conjuntos de ligações, o que resulta numa ligação aleatória escolhida a partir do conjunto que está a ser utilizado para um pedido. Normalmente, as ligações inativas expiram no Azure Cosmos DB após quatro minutos. | Pode reefaça estes pedidos falhados no seu código de aplicação, altere as definições do seu cliente MongoDB (controlador) para derrubar ligações TCP inativas antes da janela de intervalo de quatro minutos, ou configurar as definições de manutenção do seu SISTEMA para manter as ligações TCP num estado ativo.<br><br>Para evitar mensagens relacionadas com a conectividade, altere a cadeia de ligação de modo a definir maxConnectionIdleTime com 1 a 2 minutos.<br>- Condutor de Mongo: configurar *maxIdleTimeMS=120000* <br>- Node.JS: *configurar tomadaTimeoutMS=120000,* *autoReconnect* = true, *keepAlive* = true, *keepAliveInitialDelay* = 3 minutos

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.