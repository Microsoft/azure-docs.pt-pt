---
title: Resolução de problemas erros comuns na API da Azure Cosmos DB para a Mongo DB
description: Este doc discute as formas de resolver problemas comuns encontrados na API da Azure Cosmos DB para o MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 06a06d275ba6f5ded475ffd693ee61e7a72b9516
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127707"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Resolução de problemas comuns na API da Azure Cosmos DB para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O seguinte artigo descreve erros e soluções comuns para implementações usando a API API AZure Cosmos DB para a MongoDB.

>[!Note]
> A Azure Cosmos DB não acolhe o motor MongoDB. Fornece uma implementação do protocolo de arame MongoDB. Portanto, alguns destes erros só são encontrados na API da Azure Cosmos para a MongoDB. 

## <a name="common-errors-and-solutions"></a>Erros comuns e soluções

| Código       | Erro                | Descrição  | Solução  |
|------------|----------------------|--------------|-----------|
| 2 | A trajetória de índice correspondente ao item de encomenda especificado é excluída ou a ordem por consulta não tem um índice composto correspondente do qual possa ser servido. | A consulta pede uma ordenação num campo que não está indexado. | Crie um índice de correspondência (ou índice composto) para a consulta de tipo que está a ser tentada. |
| 13 | Não autorizado | O pedido carece de permissões para ser concluído. | Certifique-se de que define as permissões adequadas para a sua base de dados e recolha.  |
| 16 | Inválido | O pedido especificado tem um comprimento inválido. | Se estiver a utilizar a função de explicação, certifique-se de que fornece apenas uma operação. |
| 26 | Espaço nomeNotFound | A base de dados ou a recolha referenciada na consulta não podem ser encontradas. | Certifique-se de que o nome da sua base de dados/recolha corresponde precisamente ao nome na sua consulta.|
| 50 | UltrapassadoTimeLimit | O pedido excedeu o tempo limite de 60 segundos de execução. |  Pode haver muitas causas para este erro. Uma das causas é quando a capacidade das unidades de pedido atualmente atribuídas não é suficiente para completar o pedido. Isto pode ser resolvido ao aumentar as unidades de pedido dessa coleção ou base de dados. Noutros casos, este erro pode ser trabalhado ao dividir um grande pedido em outros mais pequenos. Re-julgar uma operação de escrita que tenha recebido este erro pode resultar numa escrita duplicada.|
| 61 | ShardKeyNotFound | O documento no seu pedido não continha a chave de fragmentos da coleção (chave de partição Azure Cosmos DB). | Certifique-se de que a chave de fragmentos da coleção está a ser utilizada no pedido.|
| 66 | Campo Imutável | O pedido está a tentar mudar um campo imutável | Os campos de "id" são imutáveis. Certifique-se de que o seu pedido não tenta atualizar este campo. |
| 67 | Não é tão bem | O pedido de criação de um índice não pode ser concluído. | Até 500 índices de campo único podem ser criados num recipiente. Até oito campos podem ser incluídos num índice composto (os índices compostos são suportados na versão 3.6+). |
| 115 | ComandoNotSupiado | O pedido tentado não é apoiado. | Devem ser fornecidos detalhes adicionais no erro. Se esta funcionalidade for importante para as suas implementações, avise-nos criando um bilhete de apoio no [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) |
| 11000 | Chave duplicada | A chave de fragmentos (chave de partição DB Azure Cosmos) do documento que está a inserir já existe na coleção ou foi violada uma restrição de campo de índice única. | Utilize a função atualização para atualizar um documento existente. Se a restrição de campo de índice único tiver sido violada, insira ou atualize o documento com um valor de campo que ainda não existe no fragmento/partição. |
| 16500 | TooManyRequests  | O número total de unidades de pedido consumidas é maior do que a taxa pedido/unidade aprovisionada para a coleção e foi limitada. | Pondere dimensionar o débito atribuído a um contentor ou a um conjunto de contentores do portal do Azure ou pode repetir a operação. Se ativar o SSR (retentação do lado do servidor), o Azure Cosmos DB recauchuta automaticamente os pedidos que falham devido a este erro. |
| 16501 | LimiteMemóriaExcedido | Como serviço multi-inquilino, a operação passou por cima do loteamento de memória do cliente. | Reduzir o âmbito de funcionamento através de critérios de consulta mais restritivos ou suporte de contacto do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nome artístico do oleoduto não reconhecido. | O nome artístico no seu pedido de agregação não foi reconhecido. | Certifique-se de que todos os nomes do gasoduto de agregação são válidos no seu pedido. |
| - | Problemas da versão de transmissão do MongoDB | As versões mais antigas dos condutores do MongoDB são incapazes de detetar o nome da conta Azure Cosmos nas cordas de ligação. | Append *appName=@**contaName** @* no final da sua API de ligação cosmos DB para a cadeia de conexão MongoDB, onde o nome de ***contaName*** é o seu nome de conta Cosmos DB. |
| - | Problemas de rede de clientes mongoDB (tais como exceções à tomada ou ao fim doOfStream)| O pedido de rede falhou. Isto é frequentemente causado por uma ligação TCP inativa que o cliente MongoDB está a tentar usar. Os controladores mongoDB usam frequentemente o pooling de conexão, o que resulta numa ligação aleatória escolhida da piscina sendo usada para um pedido. As ligações inativas normalmente no fim do Azure Cosmos DB terminam após quatro minutos. | Pode reefaça estes pedidos falhados no seu código de aplicação, altere as definições do seu cliente MongoDB (controlador) para derrubar ligações TCP inativas antes da janela de intervalo de quatro minutos, ou configurar as definições de manutenção do seu SISTEMA para manter as ligações TCP num estado ativo. |

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.
