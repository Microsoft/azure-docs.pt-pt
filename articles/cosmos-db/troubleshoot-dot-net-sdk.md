---
title: Diagnosticar e resolver problemas ao utilizar o SDK de .NET do Azure Cosmos DB
description: Utilize funcionalidades como registo do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas problemas problemas com a Azure Cosmos DB quando utilizar .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 02/05/2021
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: dce309b955882f6236f285ee6bd20a79201e43fb
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429940"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticar e resolver problemas ao utilizar o SDK de .NET do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
*    [Registar métricas](./monitor-cosmos-db.md) utilizando o portal Azure. As métricas do portal mostram a telemetria DB Azure Cosmos, que é útil para determinar se o problema corresponde à DB Azure Cosmos ou se é do lado do cliente.
*    Faça o registo da [cadeia de diagnósticos](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) no V2 SDK ou nos [diagnósticos](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) em V3 SDK a partir das respostas de operação de ponto.
*    Registar as [Métricas de Consulta SQL](sql-api-query-metrics.md) de todas as respostas de consulta 
*    Siga a configuração para [a sessão SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Veja a secção [questões comuns e soluções alternativas](#common-issues-workarounds) neste artigo.

Verifique a [secção de problemas](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) do GitHub que é monitorizada ativamente. Verifique se algum problema semelhante com uma solução já está arquivado. Se não encontrou uma solução, então arquive um problema do GitHub. Pode abrir um tique de apoio para questões urgentes.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemas comuns e soluções

### <a name="general-suggestions"></a>Sugestões gerais
* Execute a sua aplicação na mesma região Azure que a sua conta DB Azure Cosmos, sempre que possível. 
* Pode encontrar problemas de conectividade/disponibilidade devido à falta de recursos na sua máquina cliente. Recomendamos monitorizar a utilização do CPU nos nós que executam o cliente DB da Azure Cosmos e escalonar se estiverem a funcionar com carga elevada.

### <a name="check-the-portal-metrics"></a>Verifique as métricas do portal
A verificação das [métricas](./monitor-cosmos-db.md) do portal ajudará a determinar se é um problema do lado do cliente ou se há algum problema com o serviço. Por exemplo, se as métricas contiverem uma alta taxa de pedidos limitados de taxa (código de estado HTTP 429), o que significa que o pedido está a ser acelerado, então verifique a [taxa de pedido demasiado grande.](troubleshoot-request-rate-too-large.md) 

## <a name="retry-logic"></a>Lógica de Retíria <a id="retry-logics"></a>
Em qualquer falha de E/S, o SDK do Cosmos DB tentará repetir a operação falhada se for viável realizar a repetição no SDK. Ter uma nova agem para qualquer falha é uma boa prática, mas especificamente lidar/tentar falhar a escrita é uma obrigação. Recomenda-se usar o mais recente SDK, uma vez que a lógica de retíria está continuamente a ser melhorada.

1. As falhas de IO de leitura e consulta serão novamente julgadas pelo SDK sem as deixar em cima do utilizador final.
2. As escritas (Criar, Aumentar, Substituir, Excluir) são "não" idempotentes e, portanto, a SDK nem sempre pode voltar a tentar cegamente as operações de escrita falhadas. É necessário que a lógica de aplicação do utilizador lide com a falha e relemque.
3. [Problemas de tiro sdk disponibilidade](troubleshoot-sdk-availability.md) explica retréis para contas de Cosmos DB multi-região.

## <a name="common-error-status-codes"></a>Códigos comuns de estado de erro <a id="error-codes"></a>

| Código de Estado | Descrição | 
|----------|-------------|
| 400 | Mau pedido (Depende da mensagem de erro)| 
| 401 | [Não autorizado](troubleshoot-unauthorized.md) | 
| 403 | [Proibido](troubleshoot-forbidden.md) |
| 404 | [O recurso não é encontrado](troubleshoot-not-found.md) |
| 408 | [Pedido cronometrado](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | A falha de conflito é quando o ID fornecido para um recurso numa operação de escrita foi tomado por um recurso existente. Utilize outro ID para o recurso para resolver este problema, pois o ID deve ser único dentro de todos os documentos com o mesmo valor chave de partição. |
| 410 | Exceções iram (falha transitória que não deve violar SLA) |
| 412 | Falha de pré-condição é onde a operação especificou um eTag diferente da versão disponível no servidor. É um erro de concordância otimista. Repita o pedido depois de ler a versão mais recente do recurso e atualizar a eTag no pedido.
| 413 | [Entidade de pedido muito grande](concepts-limits.md#per-item-limits) |
| 429 | [Muitos pedidos](troubleshoot-request-rate-too-large.md) |
| 449 | Erro transitório que ocorre apenas em operações de escrita, e é seguro para voltar a tentar |
| 500 | A operação falhou devido a um erro de serviço inesperado. Contacte o suporte. Consulte a apresentação de um problema de [suporte Azure](https://aka.ms/azure-support). |
| 503 | [Serviço indisponível](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Exaustão portuária de Azure SNAT (PAT)

Se a sua aplicação for implementada em [Máquinas Virtuais Azure sem endereço IP público,](../load-balancer/load-balancer-outbound-connections.md)por padrão [as portas Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) estabelecem ligações a qualquer ponto final fora do seu VM. O número de ligações permitidas do VM ao ponto final DB Azure Cosmos é limitado pela [configuração Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Esta situação pode levar a estrangulamentos de ligação, ao encerramento de ligações ou aos intervalos de tempo do pedido acima [mencionados](troubleshoot-dot-net-sdk-request-timeout.md).

 As portas Azure SNAT são utilizadas apenas quando o seu VM tem um endereço IP privado que está a ligar a um endereço IP público. Existem duas soluções alternativas para evitar a limitação do Azure SNAT (desde que já esteja a utilizar uma única instância de cliente em toda a aplicação):

* Adicione o seu ponto final de serviço Azure Cosmos DB à sub-rede da sua rede virtual Azure Virtual Machines. Para mais informações, consulte [os pontos finais do serviço Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quando o ponto final de serviço está ativado, os pedidos deixaram de ser enviados de um IP público para a Azure Cosmos DB. Em vez disso, a rede virtual e a identidade da sub-rede são enviadas. Esta alteração pode resultar em quedas de firewall se apenas os IPs públicos forem permitidos. Se utilizar uma firewall, quando ativar o ponto final de serviço, adicione uma sub-rede à firewall utilizando [ACLs de rede virtual](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Atribua um [IP público ao seu Azure VM](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Latência de alta rede
A alta latência da rede pode ser identificada utilizando a [cadeia de diagnósticos](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) no V2 SDK ou [diagnósticos](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) em V3 SDK.

Se não houver [intervalos de tempo](troubleshoot-dot-net-sdk-request-timeout.md) e os diagnósticos mostrarem pedidos únicos em que a elevada latência é evidente na diferença entre `ResponseTime` `RequestStartTime` e, assim (>300 milissegundos neste exemplo):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Esta latência pode ter múltiplas causas:

* A sua candidatura não está a decorrer na mesma região que a sua conta DB Azure Cosmos.
* A [configuração preferida deLocalização](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) ou [Região de Aplicação](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) está incorreta e está a tentar ligar-se a uma região diferente do local onde a sua aplicação está atualmente a decorrer.
* Pode haver um estrangulamento na interface da Rede por causa do tráfego elevado. Se a aplicação estiver em execução em Azure Virtual Machines, existem possíveis soluções alternativas:
    * Considere utilizar uma [máquina virtual com rede acelerada ativada](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Ativar [a rede acelerada numa máquina virtual existente.](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)
    * Considere usar uma [máquina virtual de extremidade superior.](../virtual-machines/sizes.md)

### <a name="common-query-issues"></a>Questões de consulta comuns

As [métricas de consulta](sql-api-query-metrics.md) ajudarão a determinar onde a consulta está a passar a maior parte do tempo. Pelas métricas de consulta, pode ver quanto está a ser gasto no back-end vs cliente. Saiba mais sobre [o desempenho da consulta de resolução de problemas.](troubleshoot-query-performance.md)

* Se a consulta de back-end voltar rapidamente, e passar um grande tempo no cliente verifique a carga na máquina. É provável que não haja recursos suficientes e o SDK está à espera que os recursos estejam disponíveis para lidar com a resposta.
* Se a consulta de back-end for lenta, tente [otimizar a consulta](troubleshoot-query-performance.md) e olhar para a política de [indexação](index-overview.md) atual

    > [!NOTE]
    > Para um melhor desempenho, recomendamos o processamento do anfitrião windows 64 bits. O SQL SDK inclui uma ServiceInterop.dll nativa para analisar e otimizar consultas localmente. ServiceInterop.dll é suportado apenas na plataforma Windows x64. Para o Linux e outras plataformas não suportadas onde ServiceInterop.dll não esteja disponível, será feita uma chamada adicional de rede para o gateway para obter a consulta otimizada.

Se encontrar o seguinte erro: `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` e estiver a utilizar o Windows, deverá atualizar para a versão mais recente do Windows.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre as diretrizes de desempenho para [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET V2](performance-tips.md)
* Conheça os [SDKs Java baseados no reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list