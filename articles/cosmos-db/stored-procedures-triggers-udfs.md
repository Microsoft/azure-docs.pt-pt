---
title: Trabalhar com procedimentos armazenados, gatilhos e UDFs em Azure Cosmos DB
description: Este artigo introduz os conceitos como procedimentos armazenados, gatilhos e funções definidas pelo utilizador no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 706f52a6cda2bbcb0e5ca1cfe9372600fa6709d0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246529"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Procedimentos armazenados, gatilhos e funções definidas pelo utilizador

O Azure Cosmos DB fornece uma execução transacional de linguagem integrada de JavaScript. Ao utilizar a API SQL em Azure Cosmos DB, pode escrever **procedimentos armazenados,** **gatilhos**e **funções definidas pelo utilizador (UDFs)** na língua JavaScript. Pode escrever a lógica em JavaScript, executada dentro do motor de base de dados. Pode criar e executar gatilhos, procedimentos armazenados e UDFs utilizando o [portal Azure,](https://portal.azure.com/)a Consulta Integrada de Consulta integrada em [Língua JavaScript em Azure Cosmos DB](javascript-query-api.md) ou o [SDKs do cliente Cosmos DB SQL API.](how-to-use-stored-procedures-triggers-udfs.md)

## <a name="benefits-of-using-server-side-programming"></a>Benefícios da utilização da programação do lado do servidor

A escrita de procedimentos, gatilhos e funções definidas pelo utilizador (UDFs) no JavaScript permite-lhe construir aplicações ricas e têm as seguintes vantagens:

* **Lógica processual:** JavaScript como uma linguagem de programação de alto nível que fornece interface rica e familiar para expressar a lógica do negócio. Pode realizar uma sequência de operações complexas nos dados.

* **Transações atómicas:** A Azure Cosmos DB garante que as operações de base de dados que são realizadas dentro de um único procedimento armazenado ou um gatilho são atómicos. Esta funcionalidade atómica permite que uma aplicação combine operações relacionadas num único lote, de modo a que todas as operações tenham sucesso ou nenhuma delas tenha sucesso.

* **Desempenho:** Os dados jSON são intrinsecamente mapeados para o sistema de tipo javaScript. Este mapeamento permite uma série de otimizações como a materialização preguiçosa de documentos JSON no pool tampão e disponibilizá-los a pedido do código de execução. Existem outros benefícios de desempenho associados à lógica do negócio de envio para a base de dados, que inclui:

   * *Lotação:* Pode agrupar operações como inserções e submetê-las a granel. Os custos de latência do tráfego da rede e a sobrecarga da loja para criar transações separadas são significativamente reduzidos.

   * *Pré-compilação:* Os procedimentos armazenados, os gatilhos e os UDFs são implicitamente pré-compilados para o formato de código byte, a fim de evitar o custo de compilação no momento da invocação de cada script. Devido à pré-compilação, a invocação dos procedimentos armazenados é rápida e tem uma pegada baixa.

   * *Sequenciação:* Por vezes, as operações precisam de um mecanismo de desencadeamento que possa realizar uma ou adicional atualização aos dados. Além da Atomicity, existem também benefícios de desempenho na execução do lado do servidor.

* **Encapsulação:** Os procedimentos armazenados podem ser usados para agrupar a lógica num só local. A encapsulação adiciona uma camada de abstração em cima dos dados, o que lhe permite evoluir as suas aplicações independentemente dos dados. Esta camada de abstração é útil quando os dados são sem esquemae não é preciso gerir a adição de lógica adicional diretamente na sua aplicação. A abstração permite manter os dados seguros, racionalizando o acesso dos scripts.

> [!TIP]
> Os procedimentos armazenados são mais adequados para operações que são pesadas e requerem uma transação através de um valor-chave de partição. Ao decidir se utiliza os procedimentos armazenados, otimize em torno de encapsular a quantidade máxima de escritapossível. De um modo geral, os procedimentos armazenados não são os meios mais eficientes para fazer um grande número de operações de leitura ou consulta, pelo que a utilização de procedimentos armazenados para lotear um grande número de leituras para devolver ao cliente não dará o benefício desejado. Para melhor desempenho, estas operações pesadas de leitura devem ser feitas no lado do cliente, utilizando o Cosmos SDK. 

## <a name="transactions"></a>Transações

Transação numa base de dados típica pode ser definida como uma seqüência de operações executadas como uma unidade lógica única de trabalho. Cada transação fornece **garantias de propriedade acid.** ACID é um acrónimo bem conhecido que significa: **Uma**tomicidade, onsistency **C,** **solção,** e urabilidade **D.** 

* A atomicidade garante que todas as operações efetuadas dentro de uma transação são tratadas como uma única unidade, e ou todas estão comprometidas ou nenhuma delas. 

* A consistência assegura-se de que os dados estão sempre num estado válido através de transações. 

* O isolamento garante que nenhuma transação interfere entre si – muitos sistemas comerciais fornecem múltiplos níveis de isolamento que podem ser usados com base nas necessidades da aplicação. 

* A durabilidade garante que qualquer alteração que seja cometida numa base de dados estará sempre presente.

Em Azure Cosmos DB, o tempo de execução do JavaScript está hospedado dentro do motor de base de dados. Assim, os pedidos feitos dentro dos procedimentos armazenados e os gatilhos executam no mesmo âmbito que a sessão da base de dados. Esta funcionalidade permite à Azure Cosmos DB garantir propriedades acidas para todas as operações que façam parte de um procedimento armazenado ou de um gatilho. Por exemplo, veja como implementar artigo [de transações.](how-to-write-stored-procedures-triggers-udfs.md#transactions)

### <a name="scope-of-a-transaction"></a>Âmbito de uma transação

Se um procedimento armazenado estiver associado a um recipiente Azure Cosmos, então o procedimento armazenado é executado no âmbito de transação de uma chave de partição lógica. Cada execução de procedimento armazenado deve incluir um valor-chave de partição lógica que corresponda ao âmbito da transação. Para mais informações, consulte o artigo de [partição da Azure Cosmos DB.](partition-data.md)

### <a name="commit-and-rollback"></a>Confirmação e reversão

As transações estão integradas de forma nativa no modelo de programação Azure Cosmos DB JavaScript. Dentro de uma função JavaScript, todas as operações são automaticamente embrulhadas numa única transação. Se a lógica JavaScript num procedimento armazenado estiver concluída sem exceções, todas as operações dentro da transação estão comprometidas com a base de dados. Declarações como `BEGIN TRANSACTION` e `COMMIT TRANSACTION` (familiares a bases de dados relacionais) estão implícitas no Azure Cosmos DB. Se houver alguma exceção do script, o tempo de execução do Azure Cosmos DB JavaScript reverterá toda a transação. Como tal, lançar uma exceção é efetivamente equivalente a uma `ROLLBACK TRANSACTION` em Azure Cosmos DB.

### <a name="data-consistency"></a>Consistência dos dados

Os procedimentos e gatilhos armazenados são sempre executados na réplica primária de um contentor Azure Cosmos. Esta funcionalidade garante que as leituras dos procedimentos armazenados oferecem [uma forte consistência.](consistency-levels-tradeoffs.md) As consultas utilizando funções definidas pelo utilizador podem ser executadas na réplica primária ou secundária. Os procedimentos e gatilhos armazenados destinam-se a apoiar as escritas transacionais – entretanto, a lógica apenas de leitura é melhor implementada como lógica do lado da aplicação e consultas utilizando os [SDKs API Do MS BDM Azure Cosmos](sql-api-dotnet-samples.md), irá ajudá-lo a saturar a entrada da base de dados. 

## <a name="bounded-execution"></a>Execução estagnação

Todas as operações da Azure Cosmos DB devem ser concluídas dentro da duração prevista do tempo limite. Esta restrição aplica-se às funções JavaScript - procedimentos armazenados, gatilhos e funções definidas pelo utilizador. Se uma operação não estiver concluída dentro desse prazo, a transação é rerolada.

Pode garantir que as funções JavaScript terminem dentro do prazo ou implementem um modelo baseado na continuação para executar o lote/retomar. A fim de simplificar o desenvolvimento de procedimentos e gatilhos armazenados para cumprir os prazos, todas as funções sob o recipiente Azure Cosmos (por exemplo, criar, ler, atualizar e eliminar itens) devolvem um valor booleano que representa se essa operação irá completo. Se este valor for falso, é uma indicação de que o procedimento deve terminar a execução porque o script está a consumir mais tempo ou a provisão do que o valor configurado. Operações em fila antes da primeira operação de arquivo não aceite são garantidas para concluir o procedimento armazenado é concluído no tempo e não colocar em fila mais pedidos. Assim, as operações devem ser em fila uma de cada vez, utilizando a convenção de callback do JavaScript para gerir o fluxo de controlo do script. Como os scripts são executados num ambiente do lado do servidor, são estritamente governados. Scripts que violam repetidamente os limites de execução podem ser marcados inativos e não podem ser executados, e devem ser recriados para honrar os limites de execução.

As funções JavaScript também estão sujeitas à [capacidade de entrada prevista](request-units.md). As funções JavaScript podem potencialmente acabar por utilizar um grande número de unidades de pedido num curto espaço de tempo e podem ser limitadas se o limite de capacidade de entrada previsto for atingido. É importante notar que os scripts consomem um adicional de entrada para além da entrada gasta a executar operações de base de dados, embora estas operações de base de dados sejam ligeiramente menos caras do que executar as mesmas operações do cliente.

## <a name="triggers"></a>Acionadores

O Azure Cosmos DB suporta dois tipos de acionadores:

### <a name="pre-triggers"></a>Pré-gatilhos

O Azure Cosmos DB fornece acionadores que podem ser invocados ao executar uma operação num item do Azure Cosmos. Por exemplo, pode especificar um pré-acionador quando cria um item. Neste caso, o pré-acionador será executado antes da criação do item. Os pré-acionadores não podem ter parâmetros de entrada. Se necessário, pode ser utilizado o objeto de pedido para atualizar o corpo do documento no pedido original. Quando os acionadores são registados, os utilizadores podem especificar as operações com as quais estes podem ser executados. Se um gatilho foi criado com `TriggerOperation.Create`, isto significa que não será permitido utilizar o gatilho numa operação de substituição. Por exemplo, ver [Como escrever desencadeia](how-to-write-stored-procedures-triggers-udfs.md#triggers) artigo.

### <a name="post-triggers"></a>Pós-gatilhos

Semelhantes aos pré-gatilhos, os pós-gatilhos, também estão associados a uma operação num item Azure Cosmos e não requerem parâmetros de entrada. Funcionam *depois* de concluída a operação e têm acesso à mensagem de resposta que é enviada ao cliente. Por exemplo, ver [Como escrever desencadeia](how-to-write-stored-procedures-triggers-udfs.md#triggers) artigo.

> [!NOTE]
> Os gatilhos registados não funcionam automaticamente quando as suas operações correspondentes (criar/eliminar / substituir/atualizar) acontecem. Têm de ser chamados quando estas operações estiverem em execução. Para saber mais, veja como executar o artigo de [gatilhos.](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)

## <a id="udfs"></a>Funções definidas pelo utilizador

As funções definidas pelo utilizador (UDFs) são usadas para estender a sintaxe de linguagem de consulta SQL API e implementar facilmente a lógica de negócio personalizada. Só podem ser chamados dentro de consultas. Os UDFs não têm acesso ao objeto de contexto e destinam-se a ser usados apenas como computação JavaScript. Portanto, os UDFs podem ser executados em réplicas secundárias. Por exemplo, consulte como escrever o artigo de [funções definidas pelo utilizador.](how-to-write-stored-procedures-triggers-udfs.md#udfs)

## <a id="jsqueryapi"></a>API de consulta integrada em linguagem JavaScript

Além de emitir consultas utilizando sintaxe de consulta SQL API, o [SDK do lado do servidor permite-lhe](https://azure.github.io/azure-cosmosdb-js-server) realizar consultas utilizando uma interface JavaScript sem qualquer conhecimento do SQL. A Consulta JavaScript API permite-lhe construir consultas programáticas, passando funções predicadas em sequência de chamadas de função. As consultas são analisadas pelo tempo de execução do JavaScript e são executadas eficientemente dentro do Azure Cosmos DB. Para saber mais sobre o suporte da API de consulta JavaScript, consulte Trabalhar com o artigo da API integrado em [linguagem JavaScript.](javascript-query-api.md) Por exemplo, consulte Como escrever procedimentos e gatilhos armazenados usando o artigo [da Javascript Query API.](how-to-write-javascript-query-api.md)

## <a name="next-steps"></a>Passos seguintes

Saiba escrever e utilizar procedimentos, gatilhos e funções definidas pelo utilizador no Azure Cosmos DB com os seguintes artigos:

* [Como escrever procedimentos, gatilhos e funções definidas pelo utilizador](how-to-write-stored-procedures-triggers-udfs.md)

* [Como utilizar procedimentos, gatilhos e funções definidas pelo utilizador](how-to-use-stored-procedures-triggers-udfs.md)

* [Trabalhar com a API integrada da linguagem JavaScript](javascript-query-api.md)
