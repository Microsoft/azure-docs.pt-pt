---
title: Trabalhar com procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB
description: Este artigo apresenta os conceitos, como procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d1960fbc9fc9e8c1d672b66d3cf1f41399842059
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083203"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Procedimentos armazenados, acionadores e funções definidas pelo utilizador

O Azure Cosmos DB oferece a execução transacional, integrada à linguagem do JavaScript. Ao utilizar a API de SQL no Azure Cosmos DB, pode escrever **procedimentos armazenados**, **acionadores**, e **funções definidas pelo utilizador (UDFs)** na linguagem JavaScript. Pode escrever a lógica em JavaScript executado dentro do motor de base de dados. Pode criar e executar UDFs, procedimentos armazenados e acionadores usando [portal do Azure](https://portal.azure.com/), o [linguagem JavaScript integrada a API de consulta no Azure Cosmos DB](javascript-query-api.md) ou a [cliente Cosmos DB SQL API SDKs](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Vantagens da utilização de programação do lado do servidor

Escrever procedimentos armazenados, acionadores e funções definidas pelo utilizador (UDFs) em JavaScript permite-lhe criar aplicações avançadas e têm as seguintes vantagens:

* **Lógica de procedimento:** JavaScript como uma linguagem de programação de alto nível que oferece uma interface avançada e familiar para lógica de negócios express. Pode executar uma seqüência de operações complexas nos dados.

* **Transações atômicas:** O Azure Cosmos DB garante que as operações de base de dados que são executadas dentro de um procedimento armazenado único ou um acionador são atómicas. Esta funcionalidade atômica permite que um aplicativo combinar operações relacionadas num único lote, para que todas as operações com êxito ou nenhum deles seja bem-sucedida.

* **Desempenho:** Os dados JSON intrinsecamente são mapeados para o sistema de tipo de linguagem do JavaScript. Permite que este mapeamento para diversas otimizações, como lenta materialização de documentos JSON no pool de buffer e tornando-os disponíveis a pedido para o código em execução. Existem outras vantagens de desempenho associadas com envio de lógica de negócios para a base de dados, que inclui:

   * *Processamento em lote:* Pode agrupar operações como inserções e submetê-las em massa. Os custos de latência de tráfego de rede e a sobrecarga de armazenamento para criar transações separadas diminui significativamente.

   * *Pré-compilação:* Procedimentos armazenados, acionadores e UDFs são implicitamente compiladas previamente para o formato de código de byte para evitar o custo de compilação no momento da cada invocação do script. Devido a pré-compilação, a invocação de procedimentos armazenados é rápida e tem uma capacidade de baixa.

   * *Sequenciamento:* Às vezes, operações precisam de um mecanismo acionadora que pode realizar uma ou mais atualizações dos dados. Além de atomicidade, também existem benefícios de desempenho durante a execução no lado do servidor.

* **Encapsulamento:** Procedimentos armazenados podem ser utilizados para agrupar lógica num único local. Encapsulamento adiciona uma camada de abstração sobre os dados, que permite-lhe evoluir as suas aplicações de forma independente a partir dos dados. Essa camada de abstração é útil quando os dados são o esquema e não precisa de gerir a adicionar a lógica adicional diretamente na sua aplicação. A abstração permite que seu mantê-lo que seguros ao facilitar o acesso a partir dos scripts.

> [!TIP]
> Procedimentos armazenados são mais adequados para operações que são pesadas de escrita. Ao decidir onde utilizar procedimentos armazenados, Otimize as encapsulando a quantidade máxima de escritas possível. Em termos gerais, procedimentos armazenados não são o meio mais eficaz para fazer um grande número de operações de leitura para que o uso de procedimentos armazenados para um grande número de batch de leituras para retornar para o cliente não irá produzir o benefício pretendido.

## <a name="transactions"></a>Transações

Transação numa base de dados típica pode ser definida como uma seqüência de operações executadas como uma unidade lógica única de trabalho. Cada transação fornece **garantias ACID propriedade**. ACID é um acrônimo bem conhecido, que significa: **R**tomicity, **C**onsistency, **eu**solation, e **1!d**urability. 

* Atomicidade garante que todas as operações realizadas dentro de uma transação são tratadas como uma única unidade e qualquer um dos todos eles são confirmadas ou nenhum deles estão. 

* Consistência certifica-se de que os dados estão sempre num estado válido em transações. 

* Isolamento garante que nenhuma dois transação interfere uns aos outros – muitos sistemas comerciais fornecem vários níveis de isolamento que podem ser utilizados com base nas necessidades da aplicação. 

* Durabilidade garante que qualquer alteração que está comprometida num banco de dados estará sempre presente.

No Azure Cosmos DB, o tempo de execução do JavaScript é hospedado dentro do motor de base de dados. Por conseguinte, solicitações feitas aos procedimentos armazenados e os disparadores são executadas no mesmo escopo como a sessão de banco de dados. Esta funcionalidade permite que o Azure Cosmos DB garantir a propriedades ACID para todas as operações que fazem parte de um procedimento armazenado ou um acionador. Para obter exemplos, consulte [como implementar transações](how-to-write-stored-procedures-triggers-udfs.md#transactions) artigo.

### <a name="scope-of-a-transaction"></a>Âmbito de uma transação

Se um procedimento armazenado é associado um contentor do Cosmos do Azure, em seguida, o procedimento armazenado é executado no âmbito da transação de uma chave de partição lógica. Cada execução do procedimento armazenado tem de incluir um valor de chave de partição lógica que corresponda ao escopo da transação. Para obter mais informações, consulte [criação de partições do Azure Cosmos DB](partition-data.md) artigo.

### <a name="commit-and-rollback"></a>Confirmação e reversão

Transações são integradas de forma nativa para o modelo de programação JavaScript do Azure Cosmos DB. Dentro de uma função de JavaScript, a todas as operações são automaticamente encapsuladas numa única transação. Se a lógica de JavaScript num procedimento armazenado for concluído sem quaisquer exceções, todas as operações dentro da transação são consolidadas na base de dados. Gostam de instruções `BEGIN TRANSACTION` e `COMMIT TRANSACTION` (familiares relacional para bases de dados) estão implícitas no Azure Cosmos DB. Se existirem quaisquer exceções de scripts, o tempo de execução do JavaScript do Azure Cosmos DB irá reverter toda a transação. Como tal, lançar uma exceção é efetivamente equivalente a um `ROLLBACK TRANSACTION` no Azure Cosmos DB.

### <a name="data-consistency"></a>Consistência dos dados

Procedimentos armazenados e acionadores são sempre executados na réplica primária de um contentor do Cosmos do Azure. Esta funcionalidade garante que lê a partir de oferta de procedimentos armazenados [consistência forte](consistency-levels-tradeoffs.md). Consultas com funções definidas pelo utilizador podem ser executadas no principal ou de qualquer réplica secundária. Procedimentos armazenados e acionadores destinam-se para suportar escritas transacionais – enquanto isso só de leitura lógica melhor é implementada como a lógica do lado do aplicativo e consulta a utilizar o [SDKs de API de SQL do Azure Cosmos DB](sql-api-dotnet-samples.md), irá ajudá-lo a saturar a débito de base de dados. 

## <a name="bounded-execution"></a>Execução estagnação

Todas as operações do Azure Cosmos DB tem de concluir dentro da duração de tempo limite especificado. Esta restrição aplica-se às funções do JavaScript - os procedimentos armazenados, acionadores e funções definidas pelo utilizador. Se uma operação não for concluída dentro do limite de tempo, a transação é revertida.

Pode optar por garantir que as suas funções de JavaScript concluir dentro do tempo limite ou implementam um modelo com base em continuação a execução de batch/retomar. Para simplificar o desenvolvimento de procedimentos armazenados e acionadores para lidar com limites de tempo, todas as funções no contentor do Cosmos do Azure (por exemplo, criar, ler, atualizar e eliminar itens) retornar um valor booleano que representa se essa operação será Conclua. Se este valor for FALSO, é uma indicação de que o procedimento tem de encapsular a execução de uma vez que o script está a consumir mais tempo ou de débito que o valor configurado. Operações em fila antes da primeira operação de arquivo não aceite são garantidas para concluir o procedimento armazenado é concluído no tempo e não colocar em fila mais pedidos. Assim, as operações devem ser colocado em fila um de cada vez ao utilizar a Convenção de chamada de retorno do JavaScript para gerir o fluxo de controlo do script. Uma vez que scripts são executados num ambiente de servidor, estritamente são regidos. Scripts que violam repetidamente os limites de execução podem ser marcados como inativos e não podem ser executados, e deve ser recriados que respeite os limites de execução.

As funções JavaScript também estão sujeitos aos [aprovisionado a capacidade de débito](request-units.md). Funções do JavaScript poderiam acabar usando um grande número de unidades de pedido dentro de pouco tempo e podem ser limitado taxa se for atingido o limite de capacidade de débito aprovisionado. É importante observar que os scripts consumam débito adicionais além das operações gasto de débito da base de dados em execução, embora essas operações de base de dados são um pouco mais baratos do que executar as mesmas operações do cliente.

## <a name="triggers"></a>Acionadores

Esta secção descreve os dois tipos de acionadores:

### <a name="pre-triggers"></a>Pré-acionadores

O Azure Cosmos DB fornece acionadores que podem ser invocados por efetuar uma operação num item do Azure Cosmos DB. Por exemplo, pode especificar um pré-acionador de quando cria um item. Neste caso, o pré-acionador de será executado antes do item é criado. Pré-acionadores não podem ter parâmetros de entrada. Se necessário, o objeto de solicitação pode servir para atualizar o corpo do documento de solicitação original. Quando os acionadores são registrados, os utilizadores podem especificar as operações que pode ser executado com. Se um acionador foi criado com `TriggerOperation.Create`, isso significa que com o acionador numa operação de substituição não será permitido. Para obter exemplos, consulte [como escrever acionadores](how-to-write-stored-procedures-triggers-udfs.md#triggers) artigo.

### <a name="post-triggers"></a>Pós-acionadores

Semelhante a pré-acionadores, acionadores posterior, também estão associadas uma operação num item do Azure Cosmos DB e não exigem parâmetros de entrada. São executados *depois de* a operação foi concluída e ter acesso à mensagem de resposta que é enviado ao cliente. Para obter exemplos, consulte [como escrever acionadores](how-to-write-stored-procedures-triggers-udfs.md#triggers) artigo.

## <a id="udfs"></a>Funções definidas pelo utilizador

Funções definidas pelo utilizador (UDFs) são utilizadas para estender a sintaxe de linguagem de consulta de API de SQL e implemente facilmente a lógica de negócio personalizada. Eles podem ser chamados apenas dentro de consultas. UDFs não têm acesso ao objeto de contexto e se destinam a ser utilizado como a computação apenas JavaScript. Por conseguinte, UDFs podem ser executadas em réplicas secundárias. Para obter exemplos, consulte [como escrever funções definidas pelo utilizador](how-to-write-stored-procedures-triggers-udfs.md#udfs) artigo.

## <a id="jsqueryapi"></a>Consulta integrada à linguagem JavaScript API

Para além de emitir consultas usando sintaxe de consulta da API de SQL, o [SDK do lado do servidor](https://azure.github.io/azure-cosmosdb-js-server) permite-lhe executar consultas através de uma interface de JavaScript sem qualquer conhecimento de SQL. A consulta de JavaScript API permite-lhe criar programaticamente consultas ao transmitir as funções de predicado em seqüência de chamadas de função. Consultas são analisadas pelo tempo de execução de JavaScript e são executadas com eficiência no Azure Cosmos DB. Para saber mais sobre o suporte de consulta API de JavaScript, veja [trabalhar com a linguagem JavaScript integrada a API de consulta](javascript-query-api.md) artigo. Para obter exemplos, consulte [como escrever procedimentos armazenados e acionadores com a API de consulta Javascript](how-to-write-javascript-query-api.md) artigo.

## <a name="next-steps"></a>Passos Seguintes

Saiba como criar e usar procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB com os seguintes artigos:

* [Como escrever procedimentos armazenados, acionadores e funções definidas pelo utilizador](how-to-write-stored-procedures-triggers-udfs.md)

* [Como utilizar os procedimentos armazenados, acionadores e funções definidas pelo utilizador](how-to-use-stored-procedures-triggers-udfs.md)

* [Trabalhar com a linguagem JavaScript integrada a API de consulta](javascript-query-api.md)
