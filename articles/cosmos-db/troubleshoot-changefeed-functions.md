---
title: Problemas de resolução de problemas ao utilizar o gatilho de funções Azure para o Cosmos DB
description: Questões comuns, soluções alternativas e passos de diagnóstico, ao utilizar o gatilho das Funções Azure para o Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79365513"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnosticar e resolver problemas ao utilizar o gatilho de funções Azure para o Cosmos DB

Este artigo abrange questões comuns, soluções alternativas e passos de diagnóstico, quando utiliza o [gatilho de Funções Azure para Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Dependências

O gatilho e as ligações do Azure Functions para a Cosmos DB dependem dos pacotes de extensão sobre o tempo de funcionamento das funções Azure base. Mantenha sempre estes pacotes atualizados, pois podem incluir correções e novas funcionalidades que possam resolver quaisquer problemas potenciais que possa encontrar:

* Para as funções Azure V2, consulte [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Para as funções Azure V1, consulte [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Este artigo refere-se sempre às Funções Azure V2 sempre que o tempo de execução for mencionado, salvo especificação explícita.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Consumir o Azure Cosmos DB SDK de forma independente

A principal funcionalidade do pacote de extensão é fornecer suporte para o gatilho e encadernações de Funções Azure para Cosmos DB. Também inclui o [Azure Cosmos DB .NET SDK,](sql-api-sdk-dotnet-core.md)que é útil se quiser interagir programáticamente com a Azure Cosmos DB sem utilizar o gatilho e as ligações.

Se quiser utilizar o Azure Cosmos DB SDK, certifique-se de que não adiciona ao seu projeto outra referência de pacote NuGet. Em vez disso, **deixe a referência SDK resolver através do pacote de extensão das funções Azure.** Consumir o Azure Cosmos DB SDK separadamente do gatilho e das ligações

Além disso, se estiver a criar manualmente o seu próprio exemplo do [cliente Azure Cosmos DB SDK,](./sql-api-sdk-dotnet-core.md)deve seguir o padrão de ter apenas uma instância do cliente [usando uma abordagem de padrão Singleton.](../azure-functions/manage-connections.md#documentclient-code-example-c) Este processo evitará os potenciais problemas de tomada nas suas operações.

## <a name="common-scenarios-and-workarounds"></a>Cenários e soluções alternativas comuns

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Função Azure falha com a recolha de mensagens de erro não existe

A função Azure falha com a mensagem de erro "Não existe a recolha de fontes 'nome de recolha' (na base de dados 'nome da base de dados') ou a coleção de locação 'coleção2-name' (na base de dados 'nome2') não existe. Ambas as coleções devem existir antes do ouvinte começar. Para criar automaticamente a coleção de arrendamento, decreta 'CreateLeaseCollectionIfNotExists' para 'true'"

Isto significa que um ou ambos os recipientes Azure Cosmos necessários para o gatilho funcionar não existem ou não estão alcançáveis à Função Azure. **O erro em si irá dizer-lhe qual a base de dados e o contentor Azure Cosmos é o gatilho que procura** com base na sua configuração.

1. Verifique o `ConnectionStringSetting` atributo e **refere-se a uma definição que existe na sua App de Função Azure**. O valor deste atributo não deve ser a própria Cadeia de Ligação, mas o nome da Definição de Configuração.
2. Verifique se `databaseName` o e existe na sua conta `collectionName` Azure Cosmos. Se estiver a utilizar a substituição automática de valor (utilizando `%settingName%` padrões), certifique-se de que o nome da definição existe na sua App de Função Azure.
3. Se não especificar `LeaseCollectionName/leaseCollectionName` um, o padrão é "arrendamentos". Verifique se tal recipiente existe. Opcionalmente, pode definir o `CreateLeaseCollectionIfNotExists` atributo no seu Gatilho para o criar `true` automaticamente.
4. Verifique a configuração firewall da sua [conta Azure Cosmos](how-to-configure-firewall.md) para ver se não está a bloquear a Função Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure Function não começa com "Coleção de produção partilhada deve ter uma chave de partição"

As versões anteriores da Extensão DB do Azure Cosmos não suportavam a utilização de um recipiente de arrendamento que foi criado dentro de uma [base de dados de produção partilhada.](./set-throughput.md#set-throughput-on-a-database) Para resolver este problema, atualize a extensão [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) para obter a versão mais recente.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>A função Azure não começa com "PartitionKey deve ser fornecida para esta operação."

Este erro significa que está atualmente a utilizar uma coleção de locação dividida com uma antiga [dependência de extensão.](#dependencies) Atualização para a versão mais recente disponível. Se estiver atualmente a executar o Azure Functions V1, terá de atualizar para Azure Functions V2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>A Função Azure não começa com "A coleção de arrendamento, se dividida, deve ter a chave de partição igual à id."

Este erro significa que o seu recipiente de locação atual está dividido, mas o caminho chave da partição não é `/id` . Para resolver este problema, você precisa recriar o recipiente de arrendamento com `/id` como a chave de partição.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Vê-se um "Valor não pode ser nulo. Nome do parâmetro: o" nos registos das funções Azure quando tenta executar o gatilho

Este problema aparece se estiver a utilizar o portal Azure e tentar selecionar o botão **Executar** no ecrã ao inspecionar uma Função Azure que utiliza o gatilho. O gatilho não requer que selecione Executar para iniciar, iniciar-se-á automaticamente quando a Função Azure estiver implantada. Se pretender verificar o fluxo de registo da Função Azure no portal Azure, basta ir ao seu recipiente monitorizado e inserir alguns novos itens, verá automaticamente a execução do Gatilho.

### <a name="my-changes-take-too-long-to-be-received"></a>As minhas mudanças demoram muito tempo a ser recebidas.

Este cenário pode ter múltiplas causas e todas elas devem ser verificadas:

1. A Função do Azure está implementada na mesma região que a sua conta do Azure Cosmos? Para obter uma latência de rede ideal, tanto a Função do Azure como a sua conta do Azure Cosmos devem estar colocadas na mesma região do Azure.
2. As alterações que ocorrem no contentor do Azure Cosmos são contínuas ou esporádicas?
Se for a última, poderá existir algum atraso entre as alterações a serem armazenadas e a Função do Azure que as seleciona. Tal ocorre porque, internamente, quando o acionador verifica a existência de alterações no contentor do Azure Cosmos e não encontra nenhuma pendente para ser lida, este entra no modo de suspensão durante um período de tempo configurável (5 segundos, por predefinição) antes de verificar a existência de novas alterações (para evitar um consumo elevado de RUs). Pode configurar este tempo de suspensão através da definição `FeedPollDelay/feedPollDelay` na [configuração](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) do acionador (espera-se que o valor esteja em milissegundos).
3. O seu contentor Azure Cosmos pode ser [limitado.](./request-units.md)
4. Pode utilizar o `PreferredLocations` atributo no seu gatilho para especificar uma lista separada por vírgulas das regiões de Azure para definir uma ordem de ligação preferida personalizada.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Algumas mudanças são repetidas no meu Gatilho

O conceito de "mudança" é uma operação num documento. Os cenários mais comuns em que os eventos para o mesmo documento são recebidos são:
* A conta está a usar consistência eventual. Enquanto consome o feed de mudança num nível de consistência eventual, pode haver eventos duplicados entre as operações subsequentes de leitura do feed de alteração (o último evento de uma operação de leitura aparece como o primeiro da seguinte).
* O documento está a ser atualizado. O Feed de Alteração pode conter múltiplas operações para os mesmos documentos, se esse documento estiver a receber atualizações, pode recolher vários eventos (um para cada atualização). Uma forma fácil de distinguir entre diferentes operações para o mesmo documento é rastrear a `_lsn` [propriedade para cada mudança.](change-feed.md#change-feed-and-_etag-_lsn-or-_ts) Se não corresponderem, estas são alterações diferentes sobre o mesmo documento.
* Se estiver a identificar documentos apenas `id` por, lembre-se que o identificador único para um documento é a `id` chave e a sua chave de partição (pode haver dois documentos com a `id` mesma, mas diferente tecla de partição).

### <a name="some-changes-are-missing-in-my-trigger"></a>Faltam algumas mudanças no meu Gatilho.

Se descobrir que algumas das alterações que aconteceram no seu contentor Azure Cosmos não estão a ser captadas pela Função Azure, há um primeiro passo de investigação que precisa de ser realizado.

Quando a sua Função Azure recebe as alterações, muitas vezes processa-as e pode, opcionalmente, enviar o resultado para outro destino. Quando estiver a investigar alterações em falta, certifique-se **de que mede quais as alterações que estão a ser recebidas no ponto de ingestão** (quando a Função Azure começa), não no destino.

Se faltarem algumas alterações no destino, isto pode significar que se trata de algum erro ocorrido durante a execução da Função Azure após a recebidação das alterações.

Neste cenário, o melhor caminho a seguir é adicionar `try/catch` blocos no seu código e dentro dos ciclos que podem estar a processar as alterações, detetar qualquer falha num determinado subconjunto de itens e manuseá-los em conformidade (enviá-los para outro armazenamento para posterior análise ou nova análise). 

> [!NOTE]
> Por predefinição, o acionador das Funções do Azure do Cosmos DB não repetirá nenhum lote de alterações se existir alguma exceção não processada durante a execução do código. Isto significa que a razão pela qual as alterações não chegaram ao destino é porque não está a conseguir processá-las.

Se descobrir que algumas alterações não foram recebidas pelo seu gatilho, o cenário mais comum é que existe **outra Função Azure em funcionamento**. Pode ser outra Função Azure implantada em Azure ou uma Função Azure que funciona localmente numa máquina de um desenvolvedor que tem **exatamente a mesma configuração** (os mesmos recipientes monitorizados e de locação), e esta Função Azure está a roubar um subconjunto das alterações que esperaria que a sua Função Azure processasse.

Além disso, o cenário pode ser validado, se souber quantas instâncias da App Azure Function tem em execução. Se inspecionar o seu recipiente de locação e contar o número de itens de locação no seu interior, os valores distintos do `Owner` imóvel neles devem ser iguais ao número de instâncias da sua App de Função. Se há mais proprietários do que os casos conhecidos da App Azure Function, significa que estes proprietários extra são os que "roubam" as alterações.

Uma maneira fácil de contornar esta situação, é aplicar um `LeaseCollectionPrefix/leaseCollectionPrefix` na sua Função com um valor novo/diferente ou, em alternativa, testar com um novo recipiente de arrendamento.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Precisa reiniciar e reprocessar todos os itens no meu recipiente desde o início 
Para reprocessar todos os itens num recipiente desde o início:
1. Pare a função Azure se estiver em funcionamento. 
1. Eliminar os documentos da coleção de arrendamento (ou apagar e recriar a coleção de arrendamento para que esteja vazia)
1. Descreva o atributo [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger na sua função para verdadeiro. 
1. Reinicie a função Azure. Agora vai ler e processar todas as mudanças desde o início. 

Definição [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) to true will tell the Azure function to start reading changes from the beginning of the history of the collection in instead of the current time. Isto só funciona quando não há arrendamentos já criados (ou seja, documentos na coleção de arrendamentos). Definir este imóvel como verdadeiro quando há arrendamentos já criados não tem efeito; neste cenário, quando uma função é interrompida e reiniciada, começará a ler a partir do último ponto de verificação, conforme definido na coleção de arrendamentos. Para reprocessar desde o início, siga os passos acima 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>A ligação só pode ser feita com iReadOnlyList \<Document> ou JArray

Este erro ocorre se o seu projeto Azure Functions (ou qualquer projeto referenciado) contiver uma referência manual do NuGet ao Azure Cosmos DB SDK com uma versão diferente da fornecida pela [Extensão DB do Cosmos functions Azure Functions](./troubleshoot-changefeed-functions.md#dependencies).

Para contornar esta situação, remova a referência manual do NuGet que foi adicionada e deixe que a referência Azure Cosmos DB SDK resolva através do pacote de extensão Azure Functions Cosmos DB.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Alterar o intervalo de votação da Função Azure para as alterações de deteção

Como explicado anteriormente para [as minhas alterações demoram demasiado tempo a ser recebidas, a](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)função Azure dormirá durante um período de tempo configurável (5 segundos, por padrão) antes de verificar novas alterações (para evitar um consumo elevado de RU). Pode configurar este tempo de suspensão através da definição `FeedPollDelay/feedPollDelay` na [configuração](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) do acionador (espera-se que o valor esteja em milissegundos).

## <a name="next-steps"></a>Passos seguintes

* [Ativar a monitorização das suas Funções Azure](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK Resolução de problemas](./troubleshoot-dot-net-sdk.md)
