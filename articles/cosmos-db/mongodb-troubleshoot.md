---
title: Resolução de problemas erros comuns na API da Azure Cosmos DB para a Mongo DB
description: Este doc discute as formas de resolver problemas comuns encontrados na API da Azure Cosmos DB para o MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: de39aee73a6f4b422af4524d3302f8858f8b060b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692241"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Resolução de problemas comuns na API da Azure Cosmos DB para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O seguinte artigo descreve erros e soluções comuns para implementações usando a API API AZure Cosmos DB para a MongoDB.

>[!Note]
> A Azure Cosmos DB não acolhe o motor MongoDB. Fornece uma implementação da versão [4.0](mongodb-feature-support-40.md), [3.6](mongodb-feature-support-36.md), e suporte legado para [a versão 3.2](mongodb-feature-support.md)do protocolo de fio. Portanto, alguns destes erros só são encontrados na API da Azure Cosmos para a MongoDB.

## <a name="common-errors-and-solutions"></a>Erros comuns e soluções

| Código       | Erro                | Descrição  | Solução  |
|------------|----------------------|--------------|-----------|
| 2 | BadValue | Uma causa comum é um caminho de índice correspondente à ordem especificada por item ter sido excluído ou a ordem por consulta não ter um índice composto correspondente a partir do qual possa ser servida. A consulta pede uma ordenação num campo que não está indexado. | Crie um índice correspondente (ou índice composto) para a consulta de ordenação que está a ser tentada. |
| 2 | A transação não está ativa | A transação multidocumentos ultrapassou o limite de tempo fixado de 5 segundos. | Repita a transação multidocumentos ou limite o âmbito das operações na transação multidocumentos para a completar no limite de tempo de 5 segundos. |
| 13 | Não autorizado | O pedido não tem as permissões para ser concluído. | Verifique se está a utilizar as chaves corretas.  |
| 26 | NamespaceNotFound | Não é possível encontrar a base de dados ou a coleção que está a ser referenciada na consulta. | Verifique se o nome da base de dados/coleção corresponde exatamente ao nome na consulta.|
| 50 | ExceededTimeLimit | O pedido excedeu o tempo limite de 60 segundos de execução. |  Pode haver muitas causas para este erro. Uma das causas é quando a capacidade das unidades de pedido atualmente alocadas não é suficiente para concluir o pedido. Isto pode ser resolvido ao aumentar as unidades de pedido dessa coleção ou base de dados. Noutros casos, este erro pode ser solucionado através da divisão de um pedido grande em partes menores. Repetir uma operação de escrita que recebeu este erro pode resultar numa escrita duplicada. <br><br>Se estiver a tentar eliminar grandes quantidades de dados sem que isto tenha impacto sobre as RUs: <br>- Considere a utilização de TTL (com base no Carimbo de Data/Hora): [Dados de expiração com a API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md) <br>- Utilize o tamanho do Cursor/Lote para executar a eliminação. Pode obter um único documento de cada vez e eliminá-lo através de um ciclo. Esta operação ajudará a eliminar dados lentamente sem afetar a aplicação de produção.|
| 61 | ShardKeyNotFound | O documento no pedido não continha a chave shard da coleção (chave de partição do Azure Cosmos DB). | Verifique se a chave shard da coleção está a ser utilizada no pedido.|
| 66 | ImmutableField | O pedido está a tentar alterar um campo imutável | Os campos de "_id" são imutáveis. Verifique se o pedido não está a tentar atualizar esse campo ou o campo da chave shard. |
| 67 | CannotCreateIndex | Não é possível concluir o pedido para criar um índice. | Podem ser criados até 500 índices de campo único num contentor. Podem ser incluídos até oito campos num índice composto (os índices compostos são suportados na versão 3.6e superior). |
| 112 | WriteConflict | A transação multidocumentos falhou devido a uma transação multidocumentos em conflito | Repita a transação multidocumentos até ser concluída com êxito. |
| 115 | CommandNotSupported | O pedido tentado não é suportado. | Deve indicar detalhes adicionais sobre o erro. Se esta funcionalidade for importante para as suas implementações, crie um bilhete de apoio no [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e a equipa DB do Azure Cosmos irá entrar em contato consigo. |
| 11000 | DuplicateKey | A chave shard (chave de partição do Azure Cosmos DB) do documento que está a inserir já existe na coleção ou foi violada uma restrição de campo do índice exclusivo. | Utilize a função update() para atualizar um documento existente. Se a restrição de campo do índice exclusivo tiver sido violada, insira ou atualize o documento com um valor de campo que ainda não exista na partição/shard. Outra opção seria utilizar um campo com uma combinação do ID e dos campos da chave shard. |
| 16500 | TooManyRequests  | O número total de unidades de pedido consumidas é maior do que a taxa pedido/unidade aprovisionada para a coleção e foi limitada. | Pondere dimensionar o débito atribuído a um contentor ou a um conjunto de contentores do portal do Azure ou pode repetir a operação. Se ativar o SSR (repetição do lado do servidor), o Azure Cosmos DB repete automaticamente os pedidos que falham devido a este erro. |
| 16501 | LimiteMemóriaExcedido | Como um serviço multi-inquilino, a operação ultrapassou a alocação de memória do cliente. Apenas aplicável à API do Azure Cosmos DB para a versão 3.2 do MongoDB. | Reduza o âmbito da operação através de critérios de consulta mais restritivos ou contacte o suporte a partir do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nome da fase do pipeline não reconhecido. | O nome da fase no pedido de pipeline de agregação não foi reconhecido. | Verifique se todos os nomes de pipelines de agregação são válidos no pedido. |
| - | Problemas da versão de transmissão do MongoDB | As versões mais antigas dos controladores do MongoDB não conseguem detetar o nome da conta do Azure Cosmos nas cadeias de ligação. | Apêndice `appName=@accountName@` no final da sua cadeia de conexão, onde está o nome da sua conta `accountName` DB Azure Cosmos. |
| - | Problemas de rede do cliente do MongoDB (como exceções de socket ou endOfStream)| Falha no pedido da rede. Geralmente causado por uma ligação TCP inativa que o cliente do MongoDB está a tentar utilizar. Os controladores do MongoDB utilizam frequentemente conjuntos de ligações, o que resulta numa ligação aleatória escolhida a partir do conjunto que está a ser utilizado para um pedido. Normalmente, as ligações inativas expiram no Azure Cosmos DB após quatro minutos. | Pode repetir estes pedidos falhados no código da aplicação, alterar as definições do cliente do MongoDB (controlador) para repor as ligações TCP inativas antes da janela de tempo limite de quatro minutos ou configurar as definições `keepalive` do SO para manter as ligações TCP num estado ativo.<br><br>Para evitar mensagens relacionadas com a conectividade, altere a cadeia de ligação de modo a definir `maxConnectionIdleTime` para 1 a 2 minutos.<br>- Controlador do Mongo: configure `maxIdleTimeMS=120000` <br>- Node.JS: configure `socketTimeoutMS=120000`, `autoReconnect` = true, `keepAlive` = true, `keepAliveInitialDelay` = 3 minutos
| - | A Shell do Mongo não está a funcionar no portal do Azure | Quando o utilizador está a tentar abrir uma shell do Mongo, nada acontece e o separador fica em branco.  | Verifique a Firewall. A firewall com a shell do Mongo não é suportada no portal do Azure. <br>- Instale a shell do Mongo no computador local dentro das regras da firewall <br>- Utilize a shell do Mongo legada
| - | Não é possível ligar à cadeia de ligação  | A cadeia de ligação foi alterada ao atualizar da versão 3.2 para a versão 3.6 | Observe que, ao usar a API do Azure Cosmos DB para contas do MongoDB, a versão 3.6 das contas tem o ponto final no formato `*.mongo.cosmos.azure.com`, enquanto a versão 3.2 das contas tem o ponto final no formato `*.documents.azure.com`.  

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.
