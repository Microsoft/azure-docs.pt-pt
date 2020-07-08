---
title: Diagnosticar e resolver problemas ao utilizar o SDK de .NET do Azure Cosmos DB
description: Utilize funcionalidades como registo do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas problemas problemas com a Azure Cosmos DB quando utilizar .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/16/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0eb5d9cd86be05e5ad69bc9543231987e3c1dd2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85799270"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticar e resolver problemas ao utilizar o SDK de .NET do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [SDK v4 de Java](troubleshoot-java-sdk-v4-sql.md)
> * [SDK v2 Java assíncrono](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Este artigo abrange questões comuns, soluções alternativas, passos de diagnóstico e ferramentas quando utiliza as contas [API .NET SDK](sql-api-sdk-dotnet.md) com Azure Cosmos DB SQL API.
O .NET SDK fornece representação lógica do lado do cliente para aceder à AZure Cosmos DB SQL API. Este artigo descreve as ferramentas e abordagens para o ajudar se encontrar problemas.

## <a name="checklist-for-troubleshooting-issues"></a>Lista de verificação para problemas de resolução de problemas
Considere a seguinte lista de verificação antes de transferir a sua aplicação para a produção. A utilização da lista de verificação evitará várias questões comuns que possa ver. Também pode diagnosticar rapidamente quando ocorre um problema:

*    Utilize o [SDK](sql-api-sdk-dotnet-standard.md)mais recente. Os SDKs de pré-visualização não devem ser utilizados para a produção. Isto evitará que se acertem questões conhecidas que já estão corrigidas.
*    Reveja as [dicas de desempenho](performance-tips.md)e siga as práticas sugeridas. Isto ajudará a prevenir a escala, a latência e outros problemas de desempenho.
*    Ative o registo SDK para ajudá-lo a resolver um problema. Ativar a exploração pode afetar o desempenho, por isso o melhor é permitir apenas problemas de resolução de problemas. Pode ativar os seguintes registos:
*    [Registar métricas](monitor-accounts.md) utilizando o portal Azure. As métricas do portal mostram a telemetria DB Azure Cosmos, que é útil para determinar se o problema corresponde à DB Azure Cosmos ou se é do lado do cliente.
*    Faça o registo da [cadeia de diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) no V2 SDK ou nos [diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) em V3 SDK a partir das respostas de operação de ponto.
*    Registar as [Métricas de Consulta SQL](sql-api-query-metrics.md) de todas as respostas de consulta 
*    Siga a configuração para [a sessão SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Veja a secção [questões comuns e soluções alternativas](#common-issues-workarounds) neste artigo.

Verifique a [secção de problemas](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) do GitHub que é monitorizada ativamente. Verifique se algum problema semelhante com uma solução já está arquivado. Se não encontrou uma solução, então arquive um problema do GitHub. Pode abrir um tique de apoio para questões urgentes.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemas comuns e soluções

### <a name="general-suggestions"></a>Sugestões gerais
* Execute a sua aplicação na mesma região Azure que a sua conta DB Azure Cosmos, sempre que possível. 
* Pode encontrar problemas de conectividade/disponibilidade devido à falta de recursos na sua máquina cliente. Recomendamos monitorizar a utilização do CPU nos nós que executam o cliente DB da Azure Cosmos e escalonar se estiverem a funcionar com carga elevada.

### <a name="check-the-portal-metrics"></a>Verifique as métricas do portal
A verificação das [métricas](monitor-accounts.md) do portal ajudará a determinar se é um problema do lado do cliente ou se há algum problema com o serviço. Por exemplo, se as métricas contiverem uma alta taxa de pedidos limitados de taxa (código de estado HTTP 429), o que significa que o pedido está a ser acelerado, então verifique a [taxa de pedido demasiado grande.] 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Pedidos de intervalos
RequestTimeout geralmente acontece quando se utiliza Direct/TCP, mas pode acontecer no modo Gateway. Estes erros são as causas conhecidas comuns e sugestões sobre como corrigir o problema.

* A utilização do CPU é elevada, o que irá causar tempo limite de latência e/ou pedido. O cliente pode escalar a máquina hospedeira para lhe dar mais recursos, ou a carga pode ser distribuída por mais máquinas.
* A disponibilidade da tomada /porta pode ser baixa. Ao correr em Azure, os clientes que usam o .NET SDK podem atingir a exaustão da porta Azure SNAT (PAT). Para reduzir a possibilidade de atingir este problema, utilize a versão mais recente 2.x ou 3.x do .NET SDK. Este é um exemplo do porquê de ser recomendado executar sempre a versão SDK mais recente.
* A criação de vários casos de DocumentClient pode levar a problemas de contenção de ligação e de tempo limite. Siga as [dicas de desempenho](performance-tips.md)e use uma única instância DocumentClient em todo um processo.
* Os utilizadores por vezes vêem tempo limites elevados de latência ou pedido porque as suas coleções são disponibilizadas insuficientemente, os pedidos de aceleração de back-end e as retrações do cliente internamente. Verifique as [métricas](monitor-accounts.md)do portal.
* AZure Cosmos DB distribui a produção global a provisionada uniformemente através de divisórias físicas. Verifique as métricas do portal para ver se a carga de trabalho encontra uma chave de [partição](partition-data.md)quente . Isto fará com que a produção agregada consumida (RU/s) pareça estar ao abrigo das RUs previstas, mas uma única produção de partição consumida (RU/s) excederá a produção prevista. 
* Adicionalmente, o 2.0 SDK adiciona semântica de canal às ligações diretas/TCP. Uma ligação TCP é utilizada para vários pedidos ao mesmo tempo. Isto pode levar a duas questões em casos específicos:
    * Um alto grau de concordância pode levar à contenção no canal.
    * Grandes pedidos ou respostas podem levar a bloqueios de linha no canal e exacerbar a contenção, mesmo com um grau de concordância relativamente baixo.
    * Se o caso se enquadrar em qualquer uma destas duas categorias (ou se se suspeitar de uma elevada utilização da CPU), estas são possíveis atenuações:
        * Tente escalar a aplicação para cima/para fora.
        * Além disso, os registos SDK podem ser capturados através [do Trace Listener](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) para obter mais detalhes.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Latência de alta rede
A alta latência da rede pode ser identificada utilizando a [cadeia de diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) no V2 SDK ou [diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) em V3 SDK.

Se não houver [intervalos de tempo](#request-timeouts) e os diagnósticos mostrarem pedidos únicos em que a elevada latência é evidente na diferença entre `ResponseTime` `RequestStartTime` e, assim (>300 milissegundos neste exemplo):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Esta latência pode ter múltiplas causas:

* A sua candidatura não está a decorrer na mesma região que a sua conta DB Azure Cosmos.
* A [configuração preferida deLocalização](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) ou [Região de Aplicação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) está incorreta e está a tentar ligar-se a uma região diferente do local onde a sua aplicação está atualmente a decorrer.
* Pode haver um estrangulamento na interface da Rede por causa do tráfego elevado. Se a aplicação estiver em execução em Azure Virtual Machines, existem possíveis soluções alternativas:
    * Considere utilizar uma [máquina virtual com rede acelerada ativada](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Ativar [a rede acelerada numa máquina virtual existente.](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)
    * Considere usar uma [máquina virtual de extremidade superior.](../virtual-machines/windows/sizes.md)

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Exaustão portuária de Azure SNAT (PAT)

Se a sua aplicação for implementada em [Máquinas Virtuais Azure sem endereço IP público,](../load-balancer/load-balancer-outbound-connections.md)por padrão [as portas Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) estabelecem ligações a qualquer ponto final fora do seu VM. O número de ligações permitidas do VM ao ponto final DB Azure Cosmos é limitado pela [configuração Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Esta situação pode levar a estrangulamentos de ligação, ao encerramento de ligações ou aos intervalos de tempo do pedido acima [mencionados](#request-timeouts).

 As portas Azure SNAT são utilizadas apenas quando o seu VM tem um endereço IP privado que está a ligar a um endereço IP público. Existem duas soluções alternativas para evitar a limitação do Azure SNAT (desde que já esteja a utilizar uma única instância de cliente em toda a aplicação):

* Adicione o seu ponto final de serviço Azure Cosmos DB à sub-rede da sua rede virtual Azure Virtual Machines. Para mais informações, consulte [os pontos finais do serviço Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quando o ponto final de serviço está ativado, os pedidos deixaram de ser enviados de um IP público para a Azure Cosmos DB. Em vez disso, a rede virtual e a identidade da sub-rede são enviadas. Esta alteração pode resultar em quedas de firewall se apenas os IPs públicos forem permitidos. Se utilizar uma firewall, quando ativar o ponto final de serviço, adicione uma sub-rede à firewall utilizando [ACLs de rede virtual](../virtual-network/virtual-networks-acl.md).
* Atribua um [IP público ao seu Azure VM](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="http-proxy"></a>Procuração HTTP
Se utilizar um representante HTTP, certifique-se de que consegue suportar o número de ligações configuradas no SDK `ConnectionPolicy` .
Caso contrário, terá problemas de ligação.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Taxa de pedido demasiado grande
A «taxa de pedido demasiado grande» ou o código de erro 429 indica que os seus pedidos estão a ser estrangulados, uma vez que a produção consumida (RU/s) excedeu a [produção prevista](set-throughput.md). O SDK redorá automaticamente os pedidos com base na política de [relemisão](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)especificada. Se você obtém este fracasso com frequência, considere aumentar a produção na coleção. Verifique as [métricas do portal](use-metrics.md) para ver se está a obter 429 erros. Reveja a sua [chave de partição](partitioning-overview.md#choose-partitionkey) para garantir que resulta numa distribuição uniforme do armazenamento e do volume de pedidos. 

### <a name="slow-query-performance"></a>Desempenho de consulta lenta
As [métricas de consulta](sql-api-query-metrics.md) ajudarão a determinar onde a consulta está a passar a maior parte do tempo. Pelas métricas de consulta, pode ver quanto está a ser gasto no back-end vs cliente.
* Se a consulta de back-end voltar rapidamente, e passar um grande tempo no cliente verifique a carga na máquina. É provável que não haja recursos suficientes e o SDK está à espera que os recursos estejam disponíveis para lidar com a resposta.
* Se a consulta de back-end for lenta tente [otimizar a consulta](optimize-cost-queries.md) e olhar para a política de [indexação](index-overview.md) atual 

### <a name="http-401-the-mac-signature-found-in-the-http-request-is-not-the-same-as-the-computed-signature"></a>HTTP 401: A assinatura MAC encontrada no pedido HTTP não é a mesma que a assinatura computada
Se recebeu a seguinte mensagem de erro 401: “A assinatura MAC encontrada no pedido HTTP não é igual à assinatura calculada.” pode ser causado pelos seguintes cenários.

1. A chave foi rodada e não seguiu as [melhores práticas](secure-access-to-data.md#key-rotation). Normalmente, é este o caso. A rotação da chave da conta Cosmos DB pode demorar entre alguns segundos e vários dias, consoante o tamanho da conta Cosmos DB.
   1. A assinatura MAC 401 é vista pouco depois de uma rotação da chave e acaba por parar sem alterações. 
1. A chave está incorretamente configurada na aplicação, pelo que não corresponde à conta.
   1. O problema da assinatura MAC 401 será consistente e acontecerá para todas as chamadas
1. A aplicação está a usar as [chaves apenas de leitura](secure-access-to-data.md#master-keys) para operações de escrita.
   1. O problema da assinatura MAC 401 só acontecerá quando a aplicação estiver a realizar pedidos de escrita, mas os pedidos de leitura serão bem-sucedidos.
1. Existe uma condição race com a criação do contentor. Uma instância da aplicação está a tentar aceder ao contentor antes de a criação do mesmo estar concluída. No cenário mais comum, tal acontece quando o contentor é eliminado e recriado com o mesmo nome enquanto a aplicação está em execução. O SDK tentará utilizar o novo contentor, mas a criação do contentor ainda está em curso, pelo que não tem as chaves.
   1. O problema da assinatura MAC 401 é encontrado pouco depois da criação de um contentor e só ocorre até que a criação do contentor esteja concluída.
 
 ### <a name="http-error-400-the-size-of-the-request-headers-is-too-long"></a>HTTP Erro 400. O tamanho dos cabeçalhos de pedido é muito longo.
 O tamanho do cabeçalho cresceu para grande e excede o tamanho máximo permitido. É sempre recomendado usar o mais recente SDK. Certifique-se de que utiliza pelo menos a versão [3.x](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md) ou [2.x](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md), que adiciona o tamanho do cabeçalho a rastrear a mensagem de exceção.

Causas:
 1. O símbolo da sessão cresceu muito. O token da sessão aumenta à medida que o número de divisórias aumenta no contentor.
 2. O símbolo de continuação cresceu para grande. Diferentes consultas terão diferentes tamanhos de símbolo de continuação.
 3. É causado por uma combinação do símbolo da sessão e da continuação do símbolo.

Solução:
   1. Siga as [dicas de desempenho](performance-tips.md) e converta a aplicação no modo de ligação Direct + TCP. Direct + TCP não tem a restrição de tamanho do cabeçalho como HTTP faz o que evita este problema.
   2. Se o sinal da sessão for a causa, então uma mitigação temporária é reiniciar a aplicação. Reiniciar a instância de aplicação irá redefinir o token da sessão. Se as exceções pararem após o reinício, então confirma que o sinal da sessão é a causa. Acabará por voltar ao tamanho que irá causar a exceção.
   3. Se a aplicação não puder ser convertida para Direct + TCP e o token da sessão for a causa, então a mitigação pode ser feita alterando o [nível de consistência](consistency-levels.md)do cliente . O token da sessão é usado apenas para a consistência da sessão, que é o padrão para Cosmos DB. Qualquer outro nível de consistência não utilizará o token da sessão. 
   4. Se a aplicação não puder ser convertida para Direct + TCP e o token de continuação for a causa, tente definir a opção ResponseContinuationTokenLimitInKb. A opção pode ser encontrada nas Opções de Feed para v2 ou nas Opções QueryRequest em v3.

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Taxa de pedido demasiado grande]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
