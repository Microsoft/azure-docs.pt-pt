---
title: Diagnosticar e solucionar problemas ao usar o SDK do .NET Azure Cosmos DB
description: Use recursos como registro em log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas de Azure Cosmos DB ao usar o SDK do .NET.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 51b37c43b94ad59090f32af0d57bbefaa57f30fa
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932562"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticar e solucionar problemas ao usar o SDK do .NET Azure Cosmos DB
Este artigo aborda problemas comuns, soluções alternativas, etapas de diagnóstico e ferramentas quando você usa o [SDK do .net](sql-api-sdk-dotnet.md) com Azure Cosmos DB contas da API do SQL.
O SDK do .NET fornece a representação lógica do lado do cliente para acessar a API do SQL do Azure Cosmos DB. Este artigo descreve as ferramentas e abordagens para ajudá-lo se você encontrar problemas.

## <a name="checklist-for-troubleshooting-issues"></a>Lista de verificação para solucionar problemas:
Considere a seguinte lista de verificação antes de mover seu aplicativo para produção. O uso da lista de verificação impedirá vários problemas comuns que você possa ver. Você também pode diagnosticar rapidamente quando ocorrer um problema:

*   Use o [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)mais recente. Os SDKs de visualização não devem ser usados para produção. Isso evitará o pressionamento de problemas conhecidos que já foram corrigidos.
*   Examine as [dicas de desempenho](performance-tips.md)e siga as práticas sugeridas. Isso ajudará a evitar o dimensionamento, latência e outros problemas de desempenho.
*   Habilite o log do SDK para ajudá-lo a solucionar um problema. Habilitar o registro em log pode afetar o desempenho para que seja melhor habilitá-lo somente ao solucionar problemas. Você pode habilitar os seguintes logs:
    *   [Métricas de log](monitor-accounts.md) usando o portal do Azure. As métricas do portal mostram a telemetria do Azure Cosmos DB, o que é útil para determinar se o problema corresponde a Azure Cosmos DB ou se ele é do lado do cliente.
    *   Registre a [cadeia de caracteres de diagnóstico](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) das respostas de operação de ponto.
    *   Registrar as [métricas de consulta SQL](sql-api-query-metrics.md) de todas as respostas de consulta 
    *   Siga a configuração para o [log do SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Veja a seção [problemas comuns e soluções alternativas](#common-issues-workarounds) neste artigo.

Verifique a [seção de problemas do GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) monitorado ativamente. Verifique se algum problema semelhante com uma solução alternativa já foi arquivado. Se você não encontrar uma solução, então, execute um problema do GitHub. Você pode abrir um tique de suporte para problemas urgentes.


## <a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="general-suggestions"></a>Sugestões gerais
* Execute seu aplicativo na mesma região do Azure que sua conta de Azure Cosmos DB, sempre que possível. 
* Você pode encontrar problemas de conectividade/disponibilidade devido à falta de recursos no computador cliente. É recomendável monitorar a utilização da CPU em nós que executam o cliente Azure Cosmos DB e escalar verticalmente se eles estiverem sendo executados com alta carga.

### <a name="check-the-portal-metrics"></a>Verificar as métricas do portal
Verificar as [métricas do portal](monitor-accounts.md) ajudará a determinar se é uma questão do lado do cliente ou se há um problema com o serviço. Por exemplo, se as métricas contiverem uma alta taxa de solicitações limitadas por taxa (código de status HTTP 429), o que significa que a solicitação está sendo limitada e, em seguida, verifique a seção [taxa de solicitação muito grande] . 

### <a name="request-timeouts"></a>Tempos limite de solicitações
RequestTimeout geralmente ocorre ao usar Direct/TCP, mas pode acontecer no modo de gateway. Essas são as causas conhecidas comuns e sugestões sobre como corrigir o problema.

* A utilização da CPU é alta, o que causará latência e/ou tempos limite de solicitação. O cliente pode escalar verticalmente o computador host para fornecer mais recursos ou a carga pode ser distribuída entre mais computadores.
* A disponibilidade de soquete/porta pode estar baixa. Ao executar no Azure, os clientes que usam o SDK do .NET podem atingir o esgotamento de porta de SNAT do Azure (PAT). Para reduzir a chance de atingir esse problema, use a versão 2. x ou 3. x mais recente do SDK do .NET. Este é um exemplo de por que é recomendável sempre executar a versão mais recente do SDK.
* A criação de várias instâncias de DocumentClient pode levar à contenção de conexão e a problemas de tempo limite. Siga as [dicas de desempenho](performance-tips.md)e use uma única instância de DocumentClient em todo o processo.
* Os usuários às vezes veem latência elevada ou tempos limite de solicitação porque suas coleções são provisionadas insuficientemente, as solicitações de restrição de back-end e o cliente tenta novamente internamente sem identificando-lo ao chamador. Verifique as [métricas do portal](monitor-accounts.md).
* Azure Cosmos DB distribui a taxa de transferência provisionada de maneira uniforme entre as partições físicas. Verifique as métricas do portal para ver se a carga de trabalho está encontrando uma [chave de partição](partition-data.md)ativa. Isso fará com que a taxa de transferência consumida de agregação (RU/s) pareça estar sob o RUs provisionado, mas uma taxa de transferência consumida de partição única (RU/s) excederá a taxa de transferência provisionada. 
* Além disso, o SDK 2,0 adiciona a semântica de canal a conexões diretas/TCP. Uma conexão TCP é usada para várias solicitações ao mesmo tempo. Isso pode levar a dois problemas em casos específicos:
    * Um alto grau de simultaneidade pode levar à contenção no canal.
    * Solicitações ou respostas grandes podem levar ao bloqueio de cabeçalho de linha no canal e exacerbar a contenção, mesmo com um grau relativamente baixo de simultaneidade.
    * Se o caso cair em qualquer uma dessas duas categorias (ou se a alta utilização da CPU for suspeita), essas são possíveis mitigações:
        * Tente dimensionar o aplicativo para cima/para fora.
        * Além disso, os logs do SDK podem ser capturados por meio do [ouvinte de rastreamento](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) para obter mais detalhes.

### <a name="connection-throttling"></a>Limitação de conexão
A limitação de conexão pode ocorrer devido a um limite de conexão em um computador host. Anterior a 2,0, os clientes em execução no Azure podem atingir o [Esgotamento de porta de SNAT do Azure (PAT)].

### <a name="snat"></a>Esgotamento de porta de SNAT do Azure (PAT)

Se seu aplicativo for implantado em máquinas virtuais do Azure sem um endereço IP público, por padrão, [as portas SNAT do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) estabelecerão conexões com qualquer ponto de extremidade fora de sua VM. O número de conexões permitidas da VM para o ponto de extremidade Azure Cosmos DB é limitado pela [configuração de SNAT do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 As portas SNAT do Azure são usadas somente quando sua VM tem um endereço IP privado e um processo da VM tenta se conectar a um endereço IP público. Há duas soluções alternativas para evitar a limitação de SNAT do Azure:

* Adicione seu ponto de extremidade de serviço do Azure Cosmos DB à sub-rede de sua rede virtual de máquinas virtuais do Azure. Para obter mais informações, consulte [pontos de extremidade de serviço de rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Quando o ponto de extremidade de serviço é habilitado, as solicitações não são mais enviadas de um IP público para Azure Cosmos DB. Em vez disso, a rede virtual e a identidade de sub-rede são enviadas. Essa alteração poderá resultar em descartes de firewall se apenas IPs públicos forem permitidos. Se você usar um firewall, ao habilitar o ponto de extremidade de serviço, adicione uma sub-rede ao firewall usando [ACLs de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Atribua um IP público à sua VM do Azure.

### <a name="http-proxy"></a>Proxy HTTP
Se você usar um proxy HTTP, verifique se ele pode dar suporte ao número de conexões configuradas `ConnectionPolicy`no SDK.
Caso contrário, você enfrenta problemas de conexão.

### Taxa de solicitação muito grande<a name="request-rate-too-large"></a>
' Taxa de solicitação muito grande ' ou código de erro 429 indica que suas solicitações estão sendo limitadas, pois a taxa de transferência consumida (RU/s) excedeu a taxa de transferência provisionada. O SDK tentará automaticamente as solicitações com base na [política de repetição](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)especificada. Se você receber essa falha com frequência, considere aumentar a taxa de transferência na coleção. Verifique as [métricas do portal](use-metrics.md) para ver se você está recebendo erros 429. Examine sua [chave de partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) para garantir que ela resulte em uma distribuição uniforme do armazenamento e do volume de solicitação. 

### <a name="slow-query-performance"></a>Desempenho de consulta lento
As [métricas de consulta](sql-api-query-metrics.md) ajudarão a determinar onde a consulta está sendo gasta na maior parte do tempo. A partir das métricas de consulta, você pode ver quanto ele está sendo gasto no back-end em vez do cliente.
* Se a consulta de back-end retornar rapidamente e passar um tempo grande no cliente, verifique a carga no computador. É provável que não haja recursos suficientes e o SDK esteja aguardando que os recursos estejam disponíveis para lidar com a resposta.
* Se a consulta de back-end estiver lenta, tente [otimizar a consulta](optimize-cost-queries.md) e examinar a [política de indexação](index-overview.md) atual 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Taxa de solicitação muito grande]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Esgotamento de porta de SNAT do Azure (PAT)]: #snat
[Production check list]: #production-check-list


