---
title: Diagnosticar e resolver problemas ao utilizar o acionador do Azure Cosmos DB nas funções do Azure
description: Problemas comuns e soluções alternativas e passos de diagnóstico, ao utilizar o acionador do Azure Cosmos DB com as funções do Azure
author: ealsur
ms.service: cosmos-db
ms.date: 05/23/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 09ea70ac302806b4cb0e97fde92dda4208e3d659
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734519"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>Diagnosticar e resolver problemas ao utilizar o acionador do Azure Cosmos DB nas funções do Azure

Este artigo aborda problemas comuns e passos de diagnóstico, soluções alternativas ao utilizar o [acionador do Azure Cosmos DB](change-feed-functions.md) com as funções do Azure.

## <a name="dependencies"></a>Dependências

O acionador do Azure Cosmos DB e os enlaces dependem os pacotes de extensão ao longo do tempo de execução de funções do Azure base. Tenha sempre esses pacotes atualizados, como eles podem incluir correções e novas funcionalidades que podem resolver problemas potenciais que podem ser encontrados:

* Para a V2 de funções do Azure, consulte [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Para a V1 de funções do Azure, consulte [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Este artigo irá sempre se referir a V2 de funções do Azure sempre que o tempo de execução é mencionado, a menos que explicitamente especificado.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Consumir o SDK do Azure Cosmos DB de forma independente

É a principal funcionalidade do pacote de extensão fornecer suporte para o acionador do Azure Cosmos DB e enlaces. Ele também inclui a [SDK de .NET do Azure Cosmos DB](sql-api-sdk-dotnet-core.md), que é útil se pretender interagir com o Azure Cosmos DB através de programação sem utilizar o acionador e os enlaces.

Se pretende utilizar o SDK do Azure Cosmos DB, certifique-se de que não adicionar ao seu projeto outra referência de pacote de NuGet. Em vez disso, **permitir que a referência do SDK resolver por meio do pacote de extensão das funções do Azure**. Consumir o SDK do Azure Cosmos DB em separado do acionador e enlaces

Além disso, se estiver a criar sua própria instância do manualmente os [cliente SDK do Azure Cosmos DB](./sql-api-sdk-dotnet-core.md), deve seguir o padrão de ter apenas uma instância do cliente [usando uma abordagem de padrão de Singleton](../azure-functions/manage-connections.md#documentclient-code-example-c) . Este processo irá evitar potenciais problemas de socket em suas operações.

## <a name="common-scenarios-and-workarounds"></a>Cenários e soluções alternativas comuns

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Falha de função do Azure com a recolha de mensagem de erro não existe

Função do Azure falhar com mensagem de erro "de qualquer coleção de origem"coleção-name"(na base de dados"da base de dados-name") ou a coleção de concessão"coleção2-name"(na base de dados"base de dados2-name") não existe. Ambas as coleções têm de existir antes de inicia o serviço de escuta. Para criar automaticamente a coleção de concessão, definir 'CreateLeaseCollectionIfNotExists' como 'true' "

Isso significa que um ou ambos dos contentores do Azure Cosmos necessários para o acionador trabalhar, não existem ou não sejam encontram acessíveis para a função do Azure. **O próprio erro lhe dirá qual banco de dados do Cosmos do Azure e contentores é o acionador à procura de** com base na sua configuração.

1. Verifique se o `ConnectionStringSetting` atributo e que ele **faz referência a uma definição de que existe na sua aplicação de funções do Azure**. O valor neste atributo não deve ser a cadeia de ligação em si, mas o nome da definição de configuração.
2. Certifique-se de que o `databaseName` e `collectionName` existe na sua conta do Cosmos do Azure. Se estiver a utilizar a substituição automática de valor (usando `%settingName%` padrões), certifique-se de que o nome da definição de existe na sua aplicação de funções do Azure.
3. Se não especificar um `LeaseCollectionName/leaseCollectionName`, a predefinição é "concessões". Certifique-se de que existe esse contentor. Opcionalmente, pode definir o `CreateLeaseCollectionIfNotExists` atributo no seu acionador para `true` para criar automaticamente.
4. Verifique se sua [configuração da Firewall da conta do Azure Cosmos](how-to-configure-firewall.md) para ver se não encontra não está a bloquear a função do Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Função do Azure não consegue começar com "coleção de taxa de transferência partilhada deve ter uma chave de partição"

As versões anteriores da extensão do Azure Cosmos DB não oferecia suporte a utilizar um contentor de concessões que foi criado dentro de um [base de dados da taxa de transferência partilhada](./set-throughput.md#set-throughput-on-a-database). Para resolver este problema, atualize o [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) extensão para obter a versão mais recente.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Função do Azure não consegue começar com "a coleção de concessão, se particionada, tem de ter igual a id de chave de partição."

Este erro significa que o seu contentor de concessões atual está particionado, mas não é o caminho da chave de partição `/id`. Para resolver este problema, tem de recriar o contentor de concessões com `/id` como a chave de partição.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Verá um "valor não pode ser nulo. Nome do parâmetro: o "nos seus registos de funções do Azure ao tentar executar o acionador

Este problema surge se estiver a utilizar o portal do Azure e tentar selecionar o **executar** botão na tela quando inspecionar uma função do Azure que utiliza o acionador. O acionador não requer que selecione Run para começar, irá iniciar automaticamente quando a função do Azure é implementada. Se pretender verificar o fluxo de registo da função do Azure no portal do Azure, apenas vá para o seu contentor monitorizado e inserir alguns itens novos, verá automaticamente a execução do acionador.

### <a name="my-changes-take-too-long-be-received"></a>Minha opinião de alterações muito sejam recebidas

Este cenário pode ter várias causas e todos eles devem ser verificados:

1. Sua função do Azure é implementada na mesma região que a sua conta do Cosmos do Azure? Para a latência de rede não ideais, tanto a função do Azure e a sua conta do Cosmos do Azure devem ser colocalizados na mesma região do Azure.
2. As alterações ocorrem no seu contentor do Azure Cosmos contínuo ou esporádico?
Se for a última opção, pode haver algum atraso entre as alterações que está a ser armazenadas e a função do Azure pegou-los. Isto acontece porque internamente, quando o acionador verifica a existência de alterações no seu contentor do Cosmos do Azure e localiza nenhum pendente a ser lido, ele será em modo de suspensão durante um período configurável de tempo (5 segundos, por predefinição) antes de verificar a existência de novas alterações (evitar o alto consumo de RU). Pode configurar este tempo de suspensão por meio do `FeedPollDelay/feedPollDelay` definição [configuração](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) do acionador (o valor deve ser em milissegundos).
3. O contentor do Cosmos do Azure pode ser [taxa limitado](./request-units.md).
4. Pode utilizar o `PreferredLocations` atributo no seu acionador para especificar uma lista separada por vírgulas de regiões do Azure para definir uma ordem preferencial de ligação personalizada.

### <a name="some-changes-are-missing-in-my-trigger"></a>Algumas alterações estão em falta no meu acionador

Se achar que algumas das alterações que ocorreram no seu contentor do Cosmos do Azure não são a ser aplicadas pela função do Azure, há uma etapa de investigação inicial que precisa ser feita.

Quando a sua função do Azure recebe as alterações,, muitas vezes, processa e pode, opcionalmente, enviar o resultado para outro destino. Quando estiver a investigar alterações em falta, certifique-se de que **medida que as alterações que estão a ser recebidas no ponto de ingestão** (quando a função do Azure é iniciado), não no destino.

Se algumas alterações estão em falta no destino, isto pode significar que é algum erro ocorre durante a execução de função do Azure, depois das alterações foram recebidas.

Neste cenário, o melhor curso de ação é adicionar `try/catch blocks` em seu código e dentro os loops que poderão estar a processar as alterações, para detetar qualquer falha para um determinado subconjunto de itens e manipulá-las em conformidade (enviá-los para outro armazenamento para outra análise ou tente novamente). 

> [!NOTE]
> Acionador do Azure Cosmos DB, por predefinição, não repita um lote de alterações se Ocorreu uma exceção não processada durante a execução de seu código. Isso significa que o motivo que as alterações não chegou no destino é porque o que estão a falhar para processá-las.

Se, achar que algumas alterações não foram recebidas em todos os pelo seu acionador, o cenário mais comum é que há **outra execução de função do Azure**. É possível que outra função do Azure implementadas no Azure ou uma função do Azure em execução localmente no computador de um desenvolvedor que tenha **exatamente a mesma configuração** (mesmo monitorizados e contentores da concessão), e esta função do Azure é aquele que rouba um subconjunto das alterações que esperaria que a função do Azure vai processar.

Além disso, o cenário pode ser validado, se sabe quantas instâncias de aplicação de funções do Azure tem em execução. Se inspecionar o contentor de concessões e contar o número de itens de concessão dentro, os valores distintos do `Owner` propriedade nos mesmos, deve ser igual ao número de instâncias da sua aplicação de função. Se existirem mais proprietários que as instâncias da aplicação de funções do Azure conhecidos, significa que estes proprietários extras são um "roubar" as alterações.

Uma maneira fácil para resolver esta situação, é aplicar uma `LeaseCollectionPrefix/leaseCollectionPrefix` à sua função com um valor novo/diferente ou, em alternativa, testar com um novo contentor de concessões.

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Enlace só pode ser feito com IReadOnlyList<Document> ou JArray

Este erro ocorre se o seu projeto de funções do Azure (ou qualquer projeto referenciado) contém uma referência de NuGet manual para o SDK do Azure Cosmos DB com uma versão diferente daquele fornecido pelos [Azure Functions Cosmos DB Extension](./troubleshoot-changefeed-functions.md#dependencies).

Para resolver esta situação, remova a referência de NuGet manual que foi adicionada e deixar que a referência do SDK do Azure Cosmos DB resolver por meio do pacote de extensão do Azure funções Cosmos DB.

## <a name="next-steps"></a>Passos Seguintes

* [Ativar a monitorização para as suas funções do Azure](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK de resolução de problemas](./troubleshoot-dot-net-sdk.md)