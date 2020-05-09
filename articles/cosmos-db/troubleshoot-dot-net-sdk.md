---
title: Diagnosticar e resolver problemas ao utilizar o SDK de .NET do Azure Cosmos DB
description: Utilize funcionalidades como a exploração madeireira do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas com problemas do Azure Cosmos DB ao utilizar .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/06/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e389df7cfe0e228030d2d0f730fc5e671ad4c052
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927637"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticar e resolver problemas ao utilizar o SDK de .NET do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [SDK v2 Java assíncrono](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Este artigo abrange questões comuns, salções, passos de diagnóstico e ferramentas quando utiliza o [.NET SDK](sql-api-sdk-dotnet.md) com contas API Azure Cosmos DB SQL.
O .NET SDK fornece representação lógica do lado do cliente para aceder à API Azure Cosmos DB SQL. Este artigo descreve as ferramentas e abordagens para o ajudar se encontrar problemas.

## <a name="checklist-for-troubleshooting-issues"></a>Lista de verificação para problemas de resolução de problemas
Considere a seguinte lista de verificação antes de mudar a sua candidatura para a produção. A utilização da lista de verificação evitará várias questões comuns que poderá ver. Também pode diagnosticar rapidamente quando ocorre um problema:

*    Use o mais recente [SDK](sql-api-sdk-dotnet-standard.md). Os SDKs de pré-visualização não devem ser utilizados para a produção. Isto evitará que se abaterá questões conhecidas que já estão corrigidas.
*    Reveja as [dicas de desempenho](performance-tips.md)e siga as práticas sugeridas. Isto ajudará a prevenir a escala, a latência e outros problemas de desempenho.
*    Ative a exploração madeireira SDK para ajudá-lo a resolver um problema. Permitir a exploração madeireira pode afetar o desempenho, pelo que é melhor permitir apenas quando problemas de resolução de problemas. Pode ativar os seguintes registos:
    *    [Registar métricas](monitor-accounts.md) utilizando o portal Azure. As métricas do portal mostram a telemetria Azure Cosmos DB, que é útil para determinar se o problema corresponde ao Azure Cosmos DB ou se é do lado do cliente.
    *    Inicie o registo da cadeia de [diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) no V2 SDK ou [diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) em V3 SDK a partir das respostas de operação de ponto.
    *    Registar as Métricas de [Consulta SQL](sql-api-query-metrics.md) de todas as respostas de consulta 
    *    Siga a configuração para [a exploração madeireira SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Veja a secção de [questões comuns e de suposições](#common-issues-workarounds) neste artigo.

Verifique a secção de problemas do [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) que está monitorizada ativamente. Verifique se algum problema semelhante com uma suverjás já está arquivado. Se não encontrou uma solução, então arquiva um problema gitHub. Pode abrir um carraça de apoio para questões urgentes.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemas comuns e soluções

### <a name="general-suggestions"></a>Sugestões gerais
* Execute a sua aplicação na mesma região do Azure que a sua conta Azure Cosmos DB, sempre que possível. 
* Pode encontrar problemas de conectividade/disponibilidade devido à falta de recursos na sua máquina cliente. Recomendamos monitorizar a sua utilização de CPU em nós que executam o cliente Da BD Azure Cosmos, e escalar/sair se estiverem a funcionar a alta carga.

### <a name="check-the-portal-metrics"></a>Verifique as métricas do portal
Verificar as [métricas](monitor-accounts.md) do portal ajudará a determinar se é um problema do lado do cliente ou se há algum problema com o serviço. Por exemplo, se as métricas contiverem uma elevada taxa de pedidos limitados de taxas (código de estado HTTP 429), o que significa que o pedido está a ser acelerado, verifique a taxa de [pedido demasiado grande.] 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Pedidos de intervalos
RequestTimeout geralmente acontece quando se utiliza Direct/TCP, mas pode acontecer no modo Gateway. Estes erros são as causas conhecidas comuns, e sugestões sobre como corrigir o problema.

* A utilização do CPU é elevada, o que causará tempo de latência e/ou pedido. O cliente pode escalar a máquina hospedeira para lhe dar mais recursos, ou a carga pode ser distribuída por mais máquinas.
* A disponibilidade da tomada / porta pode ser baixa. Ao correr em Azure, os clientes que usam o .NET SDK podem atingir a exaustão da porta Azure SNAT (PAT). Para reduzir a probabilidade de atingir este problema, utilize a versão mais recente 2.x ou 3.x do .NET SDK. Este é um exemplo do porquê de ser recomendado executar sempre a versão Mais recente do SDK.
* A criação de várias instâncias do DocumentClient pode levar a problemas de contenção de ligação e de tempo. Siga as [dicas de desempenho](performance-tips.md)e use uma única instância do DocumentClient em todo um processo.
* Por vezes, os utilizadores vêem tempos elevados de latência ou pedidos de tempo porque as suas coleções são aprovisionadas de forma insuficiente, os pedidos de aceleração de back-end e o cliente retenta internamente. Verifique as métricas do [portal.](monitor-accounts.md)
* A Azure Cosmos DB distribui o contributo global aprovisionado uniformemente através de divisórias físicas. Verifique as métricas do portal para ver se a carga de trabalho encontra uma chave de [partição](partition-data.md)quente . Isto fará com que o rendimento agregado consumido (RU/s) pareça estar sob as RUs provisionadas, mas uma única partilha consumida (RU/s) excederá a produção prevista. 
* Adicionalmente, o 2.0 SDK adiciona semântica de canal às ligações diretas/TCP. Uma ligação TCP é utilizada para múltiplos pedidos ao mesmo tempo. Isto pode levar a duas questões em casos específicos:
    * Um alto grau de conmoeda pode levar à discórdia no canal.
    * Grandes pedidos ou respostas podem levar a bloqueios de cabeça de linha no canal e exacerbar a contenção, mesmo com um grau de conmoeda relativamente baixo.
    * Se o caso se insere em qualquer uma destas duas categorias (ou se suspeitar de uma utilização elevada da CPU), estas são possíveis atenuações:
        * Tente escalar a aplicação para cima/para fora.
        * Além disso, os registos SDK podem ser capturados através do [Trace Listener](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) para obter mais detalhes.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Latência de alta rede
A latência de alta rede pode ser identificada utilizando a cadeia de [diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) no V2 SDK ou [diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) em V3 SDK.

Se não houver [tempo limites](#request-timeouts) e os diagnósticos mostrarem pedidos únicos em `ResponseTime` `RequestStartTime`que a elevada latência é evidente na diferença entre e, como tal (>300 milissegundos neste exemplo):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Esta latência pode ter múltiplas causas:

* A sua candidatura não está a funcionar na mesma região que a sua conta Azure Cosmos DB.
* A configuração [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) ou [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) está incorreta e está a tentar ligar-se a uma região diferente da qual a sua aplicação está atualmente a funcionar.
* Pode haver um estrangulamento na interface da Rede por causa do tráfego elevado. Se a aplicação estiver em execução em Máquinas Virtuais Azure, existem possíveis soluções alternativas:
    * Considere utilizar uma [máquina virtual com rede acelerada ativada](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Ativar [a rede acelerada numa máquina virtual existente.](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)
    * Considere usar uma [máquina virtual de ponta superior.](../virtual-machines/windows/sizes.md)

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Exaustão da porta Azure SNAT (PAT)

Se a sua aplicação for implantada em [Máquinas Virtuais Azure sem um endereço IP público,](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)por padrão as [portas Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) estabelecem ligações a qualquer ponto final fora do seu VM. O número de ligações permitidas desde o VM até ao ponto final do Azure Cosmos DB é limitado pela [configuração Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Esta situação pode levar a estrangulamentos de ligação, encerramento de ligação ou os intervalos de pedidos acima [mencionados.](#request-timeouts)

 As portas Azure SNAT só são utilizadas quando o seu VM tem um endereço IP privado está ligado a um endereço IP público. Existem duas sobras para evitar a limitação do Azure SNAT (desde que já esteja a utilizar uma única instância de cliente em toda a aplicação):

* Adicione o seu ponto final de serviço Azure Cosmos DB à subnet da sua rede virtual De Máquinas Virtuais Azure. Para mais informações, consulte [os pontos finais do serviço da Rede Virtual Azure](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quando o ponto final do serviço está ativado, os pedidos deixaram de ser enviados de um IP público para o Azure Cosmos DB. Em vez disso, a rede virtual e a identidade da sub-rede são enviadas. Esta alteração pode resultar em quedas de firewall se apenas forem permitidos IPs públicos. Se utilizar uma firewall, quando ativa o ponto final do serviço, adicione uma sub-rede à firewall utilizando [ACLs](../virtual-network/virtual-networks-acl.md)de rede virtuais .
* Atribua um [IP público ao seu Azure VM](../load-balancer/load-balancer-outbound-connections.md#assignilpip).

### <a name="http-proxy"></a>Procuração HTTP
Se utilizar um representante http, certifique-se de que pode suportar o `ConnectionPolicy`número de ligações configuradas no SDK .
Caso contrário, enfrentas problemas de ligação.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Taxa de pedido muito grande
A "taxa de pedido demasiado grande" ou o código de erro 429 indica que os seus pedidos estão a ser acelerados, porque a entrada consumida (RU/s) excedeu a [entrada prevista](set-throughput.md). O SDK irá automaticamente rejulgar os pedidos com base na política de [retry](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)especificada . Se tiver esta falha com frequência, considere aumentar a entrada na coleção. Verifique as [métricas do portal](use-metrics.md) para ver se está a ter 429 erros. Reveja a [sua chave de partição](partitioning-overview.md#choose-partitionkey) para garantir que resulta numa distribuição uniforme do armazenamento e do volume de pedidos. 

### <a name="slow-query-performance"></a>Desempenho de consulta lenta
As [métricas](sql-api-query-metrics.md) de consulta ajudarão a determinar onde a consulta está passando a maior parte do tempo. Pelas métricas de consulta, você pode ver quanto está sendo gasto no back-end vs o cliente.
* Se a consulta de back-end voltar rapidamente, e passar muito tempo no cliente, verifique a carga na máquina. É provável que não haja recursos suficientes e o SDK está à espera que os recursos estejam disponíveis para lidar com a resposta.
* Se a consulta de back-end for lenta tente [otimizar a consulta](optimize-cost-queries.md) e olhar para a política de [indexação](index-overview.md) atual 

### <a name="http-401-the-mac-signature-found-in-the-http-request-is-not-the-same-as-the-computed-signature"></a>HTTP 401: A assinatura MAC encontrada no pedido HTTP não é a mesma que a assinatura computada
Se recebeu a seguinte mensagem de erro 401: "A assinatura MAC encontrada no pedido HTTP não é a mesma que a assinatura computada." pode ser causado pelos seguintes cenários.

1. A chave foi girada e não seguiu as [melhores práticas.](secure-access-to-data.md#key-rotation) Normalmente é o caso. A rotação da chave da conta Cosmos DB pode demorar entre alguns segundos e possivelmente dias, dependendo do tamanho da conta Cosmos DB.
   1. A assinatura 401 MAC é vista pouco depois de uma rotação da chave e acaba por parar sem alterações. 
2. A chave está mal configurada na aplicação para que a chave não corresponda à conta.
   1. 401 edição de assinatura MAC será consistente e acontece para todas as chamadas
3. Há uma condição de raça com criação de contentores. Uma instância de aplicação está a tentar aceder ao contentor antes da criação do contentor estar concluída. O cenário mais comum para isso se a aplicação estiver em execução, e o recipiente for eliminado e recriado com o mesmo nome enquanto a aplicação estiver em execução. O SDK tentará utilizar o novo contentor, mas a criação do contentor ainda está em curso, pelo que não tem as chaves.
   1. A emissão de assinatura mac 401 é vista pouco depois da criação de um contentor, e só ocorre até que a criação do recipiente esteja concluída.
 
 ### <a name="http-error-400-the-size-of-the-request-headers-is-too-long"></a>Erro HTTP 400. O tamanho dos cabeçalhos de pedido é muito longo.
 O tamanho do cabeçalho cresceu para grande e está excedendo o tamanho máximo permitido. É sempre recomendado usar o último SDK. Certifique-se de que utiliza pelo menos a versão [3.x](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md) ou [2.x,](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)que adiciona o tamanho do cabeçalho a rastrear a mensagem de exceção.

Causas:
 1. O símbolo da sessão cresceu muito grande. O símbolo da sessão cresce à medida que o número de divisórias aumenta no recipiente.
 2. O símbolo de continuação cresceu para grande. Diferentes consultas terão diferentes tamanhos de token de continuação.
 3. É causado por uma combinação do símbolo da sessão e do símbolo de continuação.

Solução:
   1. Siga as [dicas de desempenho](performance-tips.md) e converta a aplicação para o modo de ligação Direct + TCP. Direct + TCP não tem a restrição de tamanho do cabeçalho como o HTTP faz o que evita este problema.
   2. Se o símbolo da sessão for a causa, então uma mitigação temporária é para reiniciar a aplicação. Reiniciar a instância de aplicação reiniciará o token da sessão. Se as exceções pararem após o reinício, confirma-se que a ficha da sessão é a causa. Acabará por voltar ao tamanho que irá causar a exceção.
   3. Se a aplicação não puder ser convertida para Direct + TCP e a ficha da sessão for a causa, então a mitigação pode ser feita alterando o [nível](consistency-levels.md)de consistência do cliente . O token da sessão é usado apenas para a consistência da sessão, que é o padrão para Cosmos DB. Qualquer outro nível de consistência não utilizará o símbolo da sessão. 
   4. Se a aplicação não puder ser convertida para Direct + TCP e o token de continuação for a causa, tente definir a opção ResponseContinuationTokenLimitKb. A opção pode ser encontrada nas Opções de Feed para v2 ou nas Opções De ConsultaRequest em v3.

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Taxa de pedido muito grande]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
