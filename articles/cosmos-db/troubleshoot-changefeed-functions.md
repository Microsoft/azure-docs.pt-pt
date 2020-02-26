---
title: Problemas de resolução de problemas ao usar funções azure disparam para Cosmos DB
description: Questões comuns, salções e passos de diagnóstico, ao utilizar o gatilho funções Azure para Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 07/17/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f382406d164aa7378631753c2cfc85bc69003a4f
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605082"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnosticar e resolver problemas ao usar funções azure gatilho para Cosmos DB

Este artigo abrange questões comuns, salões e passos de diagnóstico, quando utiliza o [gatilho das Funções Azure para cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Dependências

O gatilho das Funções Azure e as ligações para cosmos DB dependem dos pacotes de extensão sobre o tempo de funcionamento das Funções Azure base. Mantenha sempre estes pacotes atualizados, pois podem incluir correções e novas funcionalidades que possam resolver eventuais problemas que possa encontrar:

* Para funções Azure V2, consulte [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Para funções Azure V1, consulte [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Este artigo irá sempre referir-se às Funções Azure V2 sempre que o tempo de funcionamento for mencionado, salvo especificação explícita.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Consumir o Azure Cosmos DB SDK de forma independente

A funcionalidade chave do pacote de extensão é fornecer suporte para o gatilho das Funções Azure e encadernações para cosmos DB. Também inclui o [Azure Cosmos DB .NET SDK,](sql-api-sdk-dotnet-core.md)que é útil se quiser interagir com o Azure Cosmos DB programáticamente sem usar o gatilho e as encadernações.

Se quiser utilizar o Azure Cosmos DB SDK, certifique-se de que não adiciona ao seu projeto outra referência ao pacote NuGet. Em vez disso, **deixe a referência SDK resolver através do pacote de extensão das funções Azure**. Consumir o Azure Cosmos DB SDK separadamente do gatilho e encadernações

Além disso, se estiver a criar manualmente a sua própria instância do [cliente Azure Cosmos DB SDK,](./sql-api-sdk-dotnet-core.md)deve seguir o padrão de ter apenas uma instância do cliente [usando uma abordagem](../azure-functions/manage-connections.md#documentclient-code-example-c)de padrão Singleton . Este processo evitará os potenciais problemas de tomada nas suas operações.

## <a name="common-scenarios-and-workarounds"></a>Cenários e suposições comuns

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Função Azure falha com recolha de mensagens de erro não existe

A Função Azure falha com a mensagem de erro "Ou a 'coleção de códigos' (na base de dados 'nome') ou a coleção de aluguer 'collection2-name' (na base de dados 'database2-name') não existe. Ambas as coleções devem existir antes do ouvinte começar. Para criar automaticamente a coleção de arrendamento, detete 'CreateLeaseCollectionIfNotExists' para 'true'"

Isto significa que um ou ambos os contentores Azure Cosmos necessários para o gatilho funcionar não existem ou não estão acessíveis à Função Azure. **O erro em si dirá qual base de dados e contentores da Azure Cosmos é o gatilho que procura** com base na sua configuração.

1. Verifique o atributo `ConnectionStringSetting` e que **se refere a uma definição que existe na sua App de Funções Azure**. O valor deste atributo não deve ser a própria Linha de Ligação, mas sim o nome da Definição de Configuração.
2. Verifique se existem `databaseName` e `collectionName` na sua conta Azure Cosmos. Se estiver a utilizar uma substituição automática de valor (utilizando padrões `%settingName%`), certifique-se de que o nome da definição existe na sua App de Funções Azure.
3. Se não especificar um `LeaseCollectionName/leaseCollectionName`, o padrão é "arrendamentos". Verifique se existe tal contentor. Opcionalmente, pode definir o atributo `CreateLeaseCollectionIfNotExists` no seu Gatilho para `true` para o criar automaticamente.
4. Verifique a configuração firewall da sua [conta Azure Cosmos](how-to-configure-firewall.md) para ver se não está a bloquear a Função Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Função Azure não começa com "A recolha de passes partilhados deve ter uma chave de partição"

As versões anteriores da Extensão DB do Azure Cosmos não suportam a utilização de um contentor de arrendamento criado dentro de uma base de dados de [entrada partilhada.](./set-throughput.md#set-throughput-on-a-database) Para resolver este problema, atualize a extensão [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) para obter a versão mais recente.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>A Função Azure não começa com "A coleção de arrendamento, se dividida, deve ter a chave de partição igual à identificação."

Este erro significa que o seu atual recipiente de locação está dividido, mas o caminho-chave da divisória não é `/id`. Para resolver este problema, é necessário recriar o contentor de arrendamento com `/id` como chave de partição.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Vê-se um "Valor não pode ser nulo. Nome do parâmetro: o" nos registos das suas Funções Azure quando tenta executar o gatilho

Este problema aparece se estiver a utilizar o portal Azure e tentar selecionar o botão **Executar** no ecrã ao inspecionar uma Função Azure que utiliza o gatilho. O gatilho não requer que selecione Executar para iniciar, iniciando-se automaticamente quando a Função Azure estiver implantada. Se pretender verificar o fluxo de registo da Função Azure no portal Azure, basta ir ao seu recipiente monitorizado e inserir alguns itens novos, verá automaticamente a execução do Gatilho.

### <a name="my-changes-take-too-long-to-be-received"></a>As minhas mudanças demoram muito tempo a ser recebidas.

Este cenário pode ter múltiplas causas e todas elas devem ser verificadas:

1. A Função do Azure está implementada na mesma região que a sua conta do Azure Cosmos? Para obter uma latência de rede ideal, tanto a Função do Azure como a sua conta do Azure Cosmos devem estar colocadas na mesma região do Azure.
2. As alterações que ocorrem no contentor do Azure Cosmos são contínuas ou esporádicas?
Se for a última, poderá existir algum atraso entre as alterações a serem armazenadas e a Função do Azure que as seleciona. Tal ocorre porque, internamente, quando o acionador verifica a existência de alterações no contentor do Azure Cosmos e não encontra nenhuma pendente para ser lida, este entra no modo de suspensão durante um período de tempo configurável (5 segundos, por predefinição) antes de verificar a existência de novas alterações (para evitar um consumo elevado de RUs). Pode configurar este tempo de suspensão através da definição `FeedPollDelay/feedPollDelay` na [configuração](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) do acionador (espera-se que o valor esteja em milissegundos).
3. O seu recipiente Azure Cosmos pode ser [limitado pela taxa.](./request-units.md)
4. Pode utilizar o atributo `PreferredLocations` no seu gatilho para especificar uma lista separada de vírinas das regiões azure para definir uma ordem de ligação preferencial personalizada.

### <a name="some-changes-are-missing-in-my-trigger"></a>Faltam algumas mudanças no meu Gatilho.

Se descobrir que algumas das mudanças que ocorreram no seu contentor Azure Cosmos não estão a ser captadas pela Função Azure, há um passo inicial de investigação que tem de ser realizado.

Quando a sua Função Azure recebe as alterações, muitas vezes processa-as, e pode, opcionalmente, enviar o resultado para outro destino. Quando estiver a investigar alterações em falta, certifique-se de **que mede as alterações que estão** a ser recebidas no ponto de ingestão (quando a Função Azure começa), e não no destino.

Se faltarem algumas alterações no destino, isso pode significar que se trata de algum erro durante a execução da Função Azure após a sua recebida das alterações.

Neste cenário, o melhor curso de ação é adicionar `try/catch` blocos no seu código e dentro dos circuitos que possam estar a processar as alterações, detetar qualquer falha para um determinado subconjunto de itens e manuseá-los em conformidade (enviá-los para outro armazenamento para análise ou nova tentativa). 

> [!NOTE]
> O gatilho das Funções Azure para cosmos DB, por padrão, não voltará a tentar um lote de alterações se houver uma exceção não manipulada durante a execução do seu código. Isto significa que a razão pela qual as alterações não chegaram ao destino é porque não as está a processar.

Se descobrir que algumas alterações não foram recebidas pelo seu gatilho, o cenário mais comum é que há **outra Função Azure em execução**. Pode ser outra Função Azure implantada em Azure ou uma Função Azure a funcionar localmente na máquina de um desenvolvedor que tem **exatamente a mesma configuração** (mesmos recipientes monitorizados e de aluguer), e esta Função Azure está a roubar um subconjunto das alterações que seria de esperar que a sua Função Azure processasse.

Além disso, o cenário pode ser validado, se souber quantas instâncias da App de Função Azure tem em execução. Se inspecionar o seu contentor de arrendamento e contar o número de artigos de locação no seu interior, os valores distintos do `Owner` propriedade neles devem ser iguais ao número de instâncias da sua App de Funções. Se houver mais Proprietários do que as instâncias conhecidas da Aplicação de Funções do Azure, tal significa que estes proprietários adicionais serão os responsáveis pelo “roubo” das alterações.

Uma maneira fácil de contornar esta situação é aplicar um `LeaseCollectionPrefix/leaseCollectionPrefix` à sua Função com um valor novo/diferente ou, em alternativa, testar com um novo recipiente de arrendamento.

### <a name="need-to-restart-and-re-process-all-the-items-in-my-container-from-the-beginning"></a>Precisa reiniciar e reprocessar todos os itens no meu recipiente desde o início 
Para reprocessar todos os itens num recipiente desde o início:
1. Pare a sua função Azure se estiver em funcionamento. 
1. Eliminar os documentos na coleção de arrendamento (ou excluir e recriar a coleção de arrendamento para que esteja vazia)
1. Detete o atributo [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger na sua função para verdadeiro. 
1. Reiniciar a função Azure. Agora vai ler e processar todas as mudanças desde o início. 

Definição [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) to true will tell the Azure function to start reading changes from the beginning of the history of the collection in the current time. Isto só funciona quando não existem arrendamentos já criados (isto é, documentos na coleção de arrendamentos). A definição deste imóvel de forma verdadeira quando existem arrendamentos já criados não tem qualquer efeito; neste cenário, quando uma função é interrompida e reiniciada, começará a ler a partir do último posto de controlo, conforme definido na coleção de arrendamentos. Para reprocessar desde o início, siga os passos acima 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>A ligação só pode ser feita com iReadOnlyList\<Document> ou JArray

Este erro ocorre se o seu projeto Funções Azure (ou qualquer projeto referenciado) contiver uma referência manual nuGet ao Azure Cosmos DB SDK com uma versão diferente da fornecida pela [extensão db funções Azure Cosmos.](./troubleshoot-changefeed-functions.md#dependencies)

Para contornar esta situação, remova a referência manual NuGet que foi adicionada e deixe a referência Azure Cosmos DB SDK resolver através do pacote de extensão de dbs DB funções Azure Funs.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Alterar o intervalo de votação da Função Azure para as alterações de deteção

Como explicado anteriormente para [as minhas alterações demoram demasiado tempo a ser recebida,](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)a função Azure dormirá durante um período de tempo configurável (5 segundos, por padrão) antes de verificar se há novas alterações (para evitar um elevado consumo de RU). Pode configurar este tempo de suspensão através da definição `FeedPollDelay/feedPollDelay` na [configuração](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) do acionador (espera-se que o valor esteja em milissegundos).

## <a name="next-steps"></a>Passos seguintes

* [Ativar a monitorização das suas Funções Azure](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK Resolução de problemas](./troubleshoot-dot-net-sdk.md)
