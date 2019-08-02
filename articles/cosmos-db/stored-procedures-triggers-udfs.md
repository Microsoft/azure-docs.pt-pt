---
title: Trabalhando com procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB
description: Este artigo apresenta os conceitos como procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 1c2bf53a610c566ac58df588f6d96389f2206563
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717539"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Procedimentos armazenados, gatilhos e funções definidas pelo usuário

O Azure Cosmos DB fornece execução transacional e de JavaScript integrada à linguagem. Ao usar a API do SQL no Azure Cosmos DB, você pode gravar **procedimentos armazenados**, **gatilhos**e **UDFs (funções definidas pelo usuário)** na linguagem JavaScript. Você pode escrever sua lógica em JavaScript que é executada dentro do mecanismo de banco de dados. Você pode criar e executar gatilhos, procedimentos armazenados e UDFs usando [portal do Azure](https://portal.azure.com/), a [API de consulta integrada da linguagem JavaScript no Azure Cosmos DB](javascript-query-api.md) ou os [SDKs do cliente Cosmos DB API do SQL](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Benefícios do uso da programação no lado do servidor

Escrever procedimentos armazenados, gatilhos e UDFs (funções definidas pelo usuário) em JavaScript permite que você crie aplicativos avançados e eles têm as seguintes vantagens:

* **Lógica de procedimento:** JavaScript como uma linguagem de programação de alto nível que fornece uma interface rica e familiar para expressar a lógica de negócios. Você pode executar uma sequência de operações complexas nos dados.

* **Transações atômicas:** Azure Cosmos DB garante que as operações de banco de dados executadas em um único procedimento armazenado ou um gatilho sejam atômicas. Essa funcionalidade atômica permite que um aplicativo Combine operações relacionadas em um único lote, para que todas as operações tenham sucesso ou nenhuma delas tenha sucesso.

* **Desempenho** Os dados JSON são mapeados intrinsecamente para o sistema de tipos de linguagem JavaScript. Esse mapeamento permite uma série de otimizações como a materialização lenta de documentos JSON no pool de buffers e sua disponibilização sob demanda para o código de execução. Há outros benefícios de desempenho associados à lógica de negócios de envio para o banco de dados, que inclui:

   * *Envio em lote* Você pode agrupar operações como inserções e enviá-las em massa. Os custos de latência de tráfego de rede e a sobrecarga de armazenamento para criar transações separadas são reduzidos significativamente.

   * *Pré-compilação:* Procedimentos armazenados, gatilhos e UDFs são implicitamente compilados para o formato de código de bytes para evitar o custo de compilação no momento de cada invocação de script. Devido à pré-compilação, a invocação de procedimentos armazenados é rápida e tem um espaço insuficiente.

   * *Sequenciamento* Às vezes, as operações precisam de um mecanismo de disparo que possa executar uma ou mais atualizações para os dados. Além da atomicidade, também há benefícios de desempenho ao executar no lado do servidor.

* **Encapsulamento** Os procedimentos armazenados podem ser usados para agrupar lógica em um único local. O encapsulamento adiciona uma camada de abstração sobre os dados, o que permite que você evolua seus aplicativos independentemente dos dados. Essa camada de abstração é útil quando os dados são sem esquema e você não precisa gerenciar a adição de lógica adicional diretamente em seu aplicativo. A abstração permite manter os dados seguros simplificando o acesso dos scripts.

> [!TIP]
> Os procedimentos armazenados são mais adequados para operações que são de gravação pesada e exigem uma transação em um valor de chave de partição. Ao decidir se deseja usar procedimentos armazenados, otimize o encapsulamento da quantidade máxima de gravações possível. Em termos gerais, os procedimentos armazenados não são os meios mais eficientes para fazer grandes quantidades de operações de leitura ou consulta; portanto, o uso de procedimentos armazenados para enviar grandes quantidades de leituras em lotes para retornar ao cliente não produzirá o benefício desejado. Para obter o melhor desempenho, essas operações de leitura pesada devem ser feitas no lado do cliente, usando o SDK do cosmos. 

## <a name="transactions"></a>Transações

Transação numa base de dados típica pode ser definida como uma seqüência de operações executadas como uma unidade lógica única de trabalho. Cada transação fornece **garantias de propriedade Acid**. ACID é um acrônimo bem conhecido que significa: **Tomicity,** **C**onsistency, **I**solation e **D**urability. 

* A atomicidade garante que todas as operações realizadas dentro de uma transação sejam tratadas como uma única unidade e que todas elas sejam confirmadas ou nenhuma delas. 

* A consistência garante que os dados estejam sempre em um estado válido entre as transações. 

* O isolamento garante que duas transações não interfiram umas com as outras – muitos sistemas comerciais fornecem vários níveis de isolamento que podem ser usados com base nas necessidades do aplicativo. 

* A durabilidade garante que qualquer alteração confirmada em um banco de dados sempre estará presente.

Em Azure Cosmos DB, o tempo de execução do JavaScript é hospedado dentro do mecanismo de banco de dados. Portanto, as solicitações feitas dentro dos procedimentos armazenados e dos gatilhos são executadas no mesmo escopo da sessão de banco de dados. Esse recurso permite que Azure Cosmos DB garanta propriedades ACID para todas as operações que fazem parte de um procedimento armazenado ou um gatilho. Para obter exemplos, consulte [o artigo como implementar transações](how-to-write-stored-procedures-triggers-udfs.md#transactions) .

### <a name="scope-of-a-transaction"></a>Escopo de uma transação

Se um procedimento armazenado estiver associado a um contêiner Cosmos do Azure, o procedimento armazenado será executado no escopo da transação de uma chave de partição lógica. Cada execução de procedimento armazenado deve incluir um valor de chave de partição lógica que corresponda ao escopo da transação. Para obter mais informações, consulte o artigo [Azure Cosmos DB particionamento](partition-data.md) .

### <a name="commit-and-rollback"></a>Confirmação e reversão

As transações são integradas nativamente ao modelo de programação Azure Cosmos DB JavaScript. Em uma função JavaScript, todas as operações são automaticamente encapsuladas em uma única transação. Se a lógica JavaScript em um procedimento armazenado for concluída sem nenhuma exceção, todas as operações dentro da transação serão confirmadas no banco de dados. Instruções como `BEGIN TRANSACTION` e `COMMIT TRANSACTION` (familiares para bancos de dados relacionais) são implícitas em Azure Cosmos DB. Se houver alguma exceção do script, o tempo de execução do Azure Cosmos DB JavaScript reverterá toda a transação. Dessa forma, gerar uma exceção é efetivamente equivalente a um `ROLLBACK TRANSACTION` Azure Cosmos DB.

### <a name="data-consistency"></a>Consistência dos dados

Os procedimentos armazenados e os gatilhos são sempre executados na réplica primária de um contêiner Cosmos do Azure. Esse recurso garante que as leituras de procedimentos armazenados ofereçam [consistência forte](consistency-levels-tradeoffs.md). As consultas que usam funções definidas pelo usuário podem ser executadas no primário ou em qualquer réplica secundária. Os procedimentos armazenados e os gatilhos destinam-se a dar suporte a gravações transacionais – enquanto a lógica somente leitura é melhor implementada como lógica do lado do aplicativo e consultas que usam os [Azure Cosmos DB SDKs da API do SQL](sql-api-dotnet-samples.md), o ajudará a saturar a taxa de transferência do banco 

## <a name="bounded-execution"></a>Execução estagnação

Todas as operações de Azure Cosmos DB devem ser concluídas dentro da duração do tempo limite especificado. Essa restrição se aplica a funções JavaScript – procedimentos armazenados, gatilhos e funções definidas pelo usuário. Se uma operação não for concluída dentro desse limite de tempo, a transação será revertida.

Você pode garantir que suas funções JavaScript sejam concluídas dentro do limite de tempo ou implementar um modelo baseado em continuação para executar lote/retomar a execução. Para simplificar o desenvolvimento de procedimentos armazenados e gatilhos para lidar com limites de tempo, todas as funções no contêiner Cosmos do Azure (por exemplo, criar, ler, atualizar e excluir itens) retornam um valor booliano que representa se essa operação irá concluí. Se esse valor for false, será uma indicação de que o procedimento deve encapsular a execução porque o script está consumindo mais tempo ou taxa de transferência provisionada do que o valor configurado. Operações em fila antes da primeira operação de arquivo não aceite são garantidas para concluir o procedimento armazenado é concluído no tempo e não colocar em fila mais pedidos. Portanto, as operações devem ser enfileiradas uma de cada vez usando a Convenção de retorno de chamada do JavaScript para gerenciar o fluxo de controle do script. Como os scripts são executados em um ambiente do lado do servidor, eles são estritamente governados. Os scripts que violam os limites de execução repetidamente podem ser marcados como inativos e não podem ser executados e devem ser recriados para honrar os limites de execução.

As funções de JavaScript também estão sujeitas à [capacidade de taxa de transferência](request-units.md)provisionada. As funções de JavaScript podem acabar usando um grande número de unidades de solicitação dentro de um curto período de tempo e podem ser limitadas por taxa se o limite da capacidade de taxa de transferência provisionada for atingido. É importante observar que os scripts consomem taxa de transferência adicional além da taxa de transferência gasto na execução de operações de banco de dados, embora essas operações de banco de dados sejam um pouco menos caras do que a execução das mesmas operações do cliente.

## <a name="triggers"></a>Ativadores

O Azure Cosmos DB dá suporte a dois tipos de gatilhos:

### <a name="pre-triggers"></a>Pré-gatilhos

Azure Cosmos DB fornece gatilhos que podem ser invocados executando uma operação em um item de Azure Cosmos DB. Por exemplo, você pode especificar um pré-gatilho ao criar um item. Nesse caso, o pré-gatilho será executado antes de o item ser criado. Pré-acionadores não podem ter parâmetros de entrada. Se necessário, o objeto de solicitação pode ser usado para atualizar o corpo do documento da solicitação original. Quando os acionadores são registrados, os utilizadores podem especificar as operações que pode ser executado com. Se um gatilho tiver sido criado `TriggerOperation.Create`com, isso significa que usar o gatilho em uma operação de substituição não será permitido. Para obter exemplos, consulte [o artigo como escrever gatilhos](how-to-write-stored-procedures-triggers-udfs.md#triggers) .

### <a name="post-triggers"></a>Pós-gatilhos

Semelhantes aos pré-gatilhos, pós-gatilhos, também são associados a uma operação em um item de Azure Cosmos DB e não exigem nenhum parâmetro de entrada. Eles são executados *após* a conclusão da operação e têm acesso à mensagem de resposta que é enviada ao cliente. Para obter exemplos, consulte [o artigo como escrever gatilhos](how-to-write-stored-procedures-triggers-udfs.md#triggers) .

> [!NOTE]
> Os gatilhos registrados não são executados automaticamente quando suas operações correspondentes (criar/excluir/substituir/atualizar) acontecem. Eles precisam ser chamados explicitamente ao executar essas operações. Para saber mais, consulte [o artigo como executar gatilhos](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) .

## <a id="udfs"></a>Funções definidas pelo utilizador

As UDFs (funções definidas pelo usuário) são usadas para estender a sintaxe da linguagem de consulta da API do SQL e implementar a lógica de negócios personalizada facilmente. Eles só podem ser chamados em consultas. As UDFs não têm acesso ao objeto de contexto e devem ser usadas como computação somente JavaScript. Portanto, as UDFs podem ser executadas em réplicas secundárias. Para obter exemplos, consulte [o artigo como gravar funções definidas pelo usuário](how-to-write-stored-procedures-triggers-udfs.md#udfs) .

## <a id="jsqueryapi"></a>API de consulta integrada à linguagem JavaScript

Além de emitir consultas usando a sintaxe de consulta da API do SQL, o [SDK do lado do servidor](https://azure.github.io/azure-cosmosdb-js-server) permite que você execute consultas usando uma interface JavaScript sem qualquer conhecimento do SQL. A API de consulta JavaScript permite criar consultas programaticamente passando funções de predicado para sequência de chamadas de função. As consultas são analisadas pelo tempo de execução do JavaScript e são executadas com eficiência no Azure Cosmos DB. Para saber mais sobre o suporte à API de consulta JavaScript, consulte o artigo sobre [como trabalhar com a API de consulta integrada à linguagem JavaScript](javascript-query-api.md) . Para obter exemplos, consulte [o artigo como escrever procedimentos armazenados e gatilhos usando a API de consulta JavaScript](how-to-write-javascript-query-api.md) .

## <a name="next-steps"></a>Passos Seguintes

Saiba como escrever e usar procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB com os seguintes artigos:

* [Como escrever procedimentos armazenados, gatilhos e funções definidas pelo usuário](how-to-write-stored-procedures-triggers-udfs.md)

* [Como usar procedimentos armazenados, gatilhos e funções definidas pelo usuário](how-to-use-stored-procedures-triggers-udfs.md)

* [Trabalhando com API de consulta integrada à linguagem JavaScript](javascript-query-api.md)
