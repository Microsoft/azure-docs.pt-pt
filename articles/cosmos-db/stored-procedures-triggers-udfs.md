---
title: Trabalhar com procedimentos armazenados, gatilhos e UDFs em Azure Cosmos DB
description: Este artigo introduz conceitos como procedimentos armazenados, gatilhos e funções definidas pelo utilizador em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: ad9e6b99b396465c2cff95bd6ab340ef9d668085
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575962"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Procedimentos armazenados, gatilhos e funções definidas pelo utilizador
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O Azure Cosmos DB fornece uma execução transacional de linguagem integrada de JavaScript. Ao utilizar o API SQL em Azure Cosmos DB, pode escrever **procedimentos armazenados,** **gatilhos** e **funções definidas pelo utilizador (UDFs)** na língua JavaScript. Pode escrever a lógica em JavaScript, executada dentro do motor de base de dados. Pode criar e executar gatilhos, procedimentos armazenados e UDFs utilizando o [portal Azure,](https://portal.azure.com/)a [API integrada de linguagem JavaScript em Azure Cosmos DB](javascript-query-api.md) ou os [SDKs clientes API do Cosmos DB SQL](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Benefícios da utilização da programação do lado do servidor

A escrita de procedimentos armazenados, gatilhos e funções definidas pelo utilizador (UDFs) no JavaScript permite-lhe construir aplicações ricas e têm as seguintes vantagens:

* **Lógica processual:** JavaScript como uma linguagem de programação de alto nível que fornece interface rica e familiar para expressar lógica de negócio. Pode executar uma sequência de operações complexas nos dados.

* **Transações atómicas:** As operações de base de dados DB da Azure Cosmos que são realizadas dentro de um único procedimento armazenado ou um gatilho são atómicas. Esta funcionalidade atómica permite que uma aplicação combine operações relacionadas num único lote, de modo que todas as operações tenham sucesso ou nenhuma delas tenha sucesso.

* **Desempenho:** Os dados JSON são intrinsecamente mapeados para o sistema de tipo de linguagem JavaScript. Este mapeamento permite uma série de otimizações como a materialização preguiçosa de documentos JSON na piscina tampão e torná-los disponíveis a pedido do código de execução. Existem outros benefícios de desempenho associados à lógica de negócio de envio para a base de dados, que inclui:

   * *Loteamento:* Pode agrupar operações como inserções e submetê-las a granel. Os custos de latência do tráfego de rede e as despesas gerais da loja para criar transações separadas são significativamente reduzidos.

   * *Pré-compilação:* Os procedimentos armazenados, os gatilhos e os UDFs são implicitamente pré-compilados para o formato de código byte, a fim de evitar o custo de compilação no momento de cada invocação do script. Devido à pré-compilação, a invocação dos procedimentos armazenados é rápida e tem uma pegada baixa.

   * *Sequenciação:* Por vezes, as operações precisam de um mecanismo de desencadeamento que possa realizar uma ou atualizações adicionais aos dados. Além da Atomicidade, existem também benefícios de desempenho ao executar no lado do servidor.

* **Encapsulamento:** Os procedimentos armazenados podem ser usados para agrupar a lógica num só local. A encapsulação adiciona uma camada de abstração em cima dos dados, o que lhe permite evoluir as suas aplicações independentemente dos dados. Esta camada de abstração é útil quando os dados são sem esquema e você não tem que gerir adicionar lógica adicional diretamente na sua aplicação. A abstração permite manter os dados seguros, racionalizando o acesso a partir dos scripts.

> [!TIP]
> Os procedimentos armazenados são mais adequados para operações que são pesadas e requerem uma transação através de um valor chave de partição. Ao decidir se deve utilizar os procedimentos armazenados, otimize em torno de encapsular a quantidade máxima de escritas possível. De um modo geral, os procedimentos armazenados não são os meios mais eficientes para fazer um grande número de operações de leitura ou consulta, pelo que a utilização de procedimentos armazenados para emar o lote de um grande número de leituras para regressar ao cliente não produzirá o benefício pretendido. Para melhor desempenho, estas operações de leitura pesadas devem ser feitas do lado do cliente, utilizando o Cosmos SDK. 

## <a name="transactions"></a>Transações

A transação numa base de dados típica pode ser definida como uma sequência de operações realizadas como uma única unidade lógica de trabalho. Cada transação fornece **garantias de propriedade ACID.** ACID é um acrónimo bem conhecido que significa: **uma** tomica, onsistency **C,** **i** solação, e **urability D.** 

* A atomicidade garante que todas as operações realizadas dentro de uma transação são tratadas como uma única unidade, e ou todas são comprometidas ou nenhuma delas são. 

* A consistência garante que os dados estão sempre num estado válido em transações. 

* O isolamento garante que não há duas transações que interfiram entre si – muitos sistemas comerciais fornecem múltiplos níveis de isolamento que podem ser utilizados com base nas necessidades da aplicação. 

* A durabilidade garante que qualquer alteração que seja cometida numa base de dados estará sempre presente.

Em Azure Cosmos DB, o tempo de execução javaScript é hospedado dentro do motor de base de dados. Assim, os pedidos feitos dentro dos procedimentos armazenados e os gatilhos executam no mesmo âmbito que a sessão de base de dados. Esta funcionalidade permite à Azure Cosmos DB garantir propriedades ACID para todas as operações que fazem parte de um procedimento armazenado ou de um gatilho. Por exemplo, veja como implementar artigo [de transações.](how-to-write-stored-procedures-triggers-udfs.md#transactions)

### <a name="scope-of-a-transaction"></a>Âmbito de uma transação

Os procedimentos armazenados estão associados a um contentor Azure Cosmos e a execução do procedimento armazenado é traçada para uma chave de partição lógica. Os procedimentos armazenados devem incluir um valor-chave de partição lógica durante a execução que define a partição lógica para o âmbito da transação. Para mais informações, consulte o artigo [de partição da Azure Cosmos DB.](partitioning-overview.md)

### <a name="commit-and-rollback"></a>Comprometer e reverter

As transações são integradas de forma nativa no modelo de programação Azure Cosmos DB JavaScript. Dentro de uma função JavaScript, todas as operações são automaticamente embrulhadas numa única transação. Se a lógica JavaScript num procedimento armazenado completar sem quaisquer exceções, todas as operações dentro da transação estão comprometidas com a base de dados. Declarações como `BEGIN TRANSACTION` e `COMMIT TRANSACTION` (familiares a bases de dados relacionais) estão implícitas na Azure Cosmos DB. Se houver exceções ao script, o tempo de execução do Azure Cosmos DB JavaScript reverterá toda a transação. Como tal, lançar uma exceção é efetivamente equivalente a um `ROLLBACK TRANSACTION` em Azure Cosmos DB.

### <a name="data-consistency"></a>Consistência de dados

Os procedimentos e gatilhos armazenados são sempre executados na réplica primária de um contentor Azure Cosmos. Esta funcionalidade garante que as leituras dos procedimentos armazenados oferecem [uma forte consistência.](./consistency-levels.md) As consultas que utilizam funções definidas pelo utilizador podem ser executadas na réplica primária ou secundária. Os procedimentos e gatilhos armazenados destinam-se a suportar escritas transacionais – entretanto, a lógica apenas de leitura é melhor implementada como lógica do lado da aplicação e consultas utilizando os [SDKs API AZure Cosmos DB SQL](sql-api-dotnet-samples.md), irão ajudá-lo a saturar o rendimento da base de dados. 

> [!TIP]
> As consultas executadas dentro de um procedimento ou gatilho armazenados podem não ver alterações em itens efecionados pela mesma transação de script. Esta declaração aplica-se tanto a consultas SQL como, tais `getContent().getCollection.queryDocuments()` como, assim como consultas linguísticas integradas, tais `getContext().getCollection().filter()` como.

## <a name="bounded-execution"></a>Execução vinculada

Todas as operações DB da Azure Cosmos devem ser concluídas dentro da duração do tempo limite especificado. Os procedimentos armazenados têm um prazo de 5 segundos. Esta restrição aplica-se às funções JavaScript - procedimentos, gatilhos e funções definidas pelo utilizador. Se uma operação não estiver concluída dentro desse prazo, a transação é revoada.

Pode garantir que as suas funções JavaScript terminam dentro do prazo ou implementar um modelo baseado na continuação para a execução em lote/currículo. Para simplificar o desenvolvimento de procedimentos armazenados e gatilhos para lidar com os prazos, todas as funções sob o contentor Azure Cosmos (por exemplo, criar, ler, atualizar e eliminar itens) devolvem um valor boolean que representa se essa operação irá completar. Se este valor for falso, é uma indicação de que o procedimento deve encerrar a execução porque o script está a consumir mais tempo ou produção prevista do que o valor configurado. As operações em fila antes da primeira operação de loja não aceite são garantidas para serem concluídas se o procedimento armazenado completar a tempo e não fizer mais pedidos. Assim, as operações devem ser em fila uma de cada vez, utilizando a convenção de retorno do JavaScript para gerir o fluxo de controlo do script. Como os scripts são executados num ambiente do lado do servidor, são estritamente governados. Scripts que violam repetidamente os limites da execução podem ser marcados inativos e não podem ser executados, e devem ser recriados para honrar os limites da execução.

As funções JavaScript também estão sujeitas à [capacidade de produção aprovisionada.](request-units.md) As funções JavaScript podem potencialmente acabar por utilizar um grande número de unidades de pedido num curto espaço de tempo e podem ser limitadas à taxa se o limite de capacidade de produção forvisionado for atingido. É importante notar que os scripts consomem produção adicional para além do resultado gasto a executar operações de base de dados, embora estas operações de base de dados sejam ligeiramente menos dispendiosas do que executar as mesmas operações do cliente.

## <a name="triggers"></a>Acionadores

O Azure Cosmos DB suporta dois tipos de acionadores:

### <a name="pre-triggers"></a>Pré-acionadores

O Azure Cosmos DB fornece acionadores que podem ser invocados ao executar uma operação num item do Azure Cosmos. Por exemplo, pode especificar um pré-acionador quando cria um item. Neste caso, o pré-acionador será executado antes da criação do item. Os pré-acionadores não podem ter parâmetros de entrada. Se necessário, pode ser utilizado o objeto de pedido para atualizar o corpo do documento no pedido original. Quando os acionadores são registados, os utilizadores podem especificar as operações com as quais estes podem ser executados. Se um acionador tiver sido criado com `TriggerOperation.Create`, significa que não será permitido utilizar o acionador numa operação de substituição. Por exemplo, veja Como escrever artigo [de triggers.](how-to-write-stored-procedures-triggers-udfs.md#triggers)

### <a name="post-triggers"></a>Pós-acionadores

Semelhantes aos pré-gatilhos, pós-gatilhos, também estão associados a uma operação num item Azure Cosmos e não requerem parâmetros de entrada. Funcionam *depois de* concluída a operação e têm acesso à mensagem de resposta que é enviada ao cliente. Por exemplo, veja Como escrever artigo [de triggers.](how-to-write-stored-procedures-triggers-udfs.md#triggers)

> [!NOTE]
> Os gatilhos registados não são executados automaticamente quando as suas operações correspondentes (criar/ eliminar/substituir/atualizar) acontecem. Têm de ser chamados quando estas operações estiverem em execução. Para saber mais, veja como executar o artigo [triggers.](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)

## <a name="user-defined-functions"></a><a id="udfs"></a>Funções definidas pelo utilizador

[As funções definidas pelo utilizador](sql-query-udfs.md) (UDFs) são usadas para alargar a sintaxe de linguagem de consulta SQL EPI e implementar facilmente a lógica de negócios personalizada. Só podem ser chamados dentro de consultas. Os UDFs não têm acesso ao objeto de contexto e destinam-se a ser usados apenas como JavaScript. Portanto, os UDFs podem ser executados em réplicas secundárias. Por exemplo, consulte Como escrever artigo [de funções definido pelo utilizador.](how-to-write-stored-procedures-triggers-udfs.md#udfs)

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>API de consulta integrada em linguagem JavaScript

Além de emitir consultas utilizando a sintaxe de consulta API SQL, o [SDK do lado do servidor permite-lhe](https://azure.github.io/azure-cosmosdb-js-server) realizar consultas utilizando uma interface JavaScript sem qualquer conhecimento de SQL. A consulta JavaScript API permite-lhe criar consultas programáticas, passando funções predicados em sequência de chamadas de função. As consultas são analisadas pelo tempo de execução javaScript e são executadas eficientemente dentro do Azure Cosmos DB. Para saber mais sobre o suporte da API de consulta JavaScript, consulte trabalhar com o artigo de consulta integrada de [linguagem JavaScript.](javascript-query-api.md) Por exemplo, consulte [Como escrever procedimentos armazenados e gatilhos utilizando artigos javascript API.](how-to-write-javascript-query-api.md)

## <a name="next-steps"></a>Passos seguintes

Saiba como escrever e utilizar procedimentos armazenados, gatilhos e funções definidas pelo utilizador em Azure Cosmos DB com os seguintes artigos:

* [Como escrever procedimentos armazenados, gatilhos e funções definidas pelo utilizador](how-to-write-stored-procedures-triggers-udfs.md)

* [Como utilizar procedimentos armazenados, gatilhos e funções definidas pelo utilizador](how-to-use-stored-procedures-triggers-udfs.md)

* [Trabalhar com a API integrada de linguagem JavaScript](javascript-query-api.md)