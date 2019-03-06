---
title: Diagnosticar e resolver problemas ao utilizar o SDK de .NET do Azure Cosmos DB
description: Utilizar funcionalidades como o registo do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas do Azure Cosmos DB ao utilizar o SDK do .NET.
author: j82w
ms.service: cosmos-db
ms.date: 01/19/2019
ms.author: jawilley
ms.devlang: c#
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7f969ab6059140ec32c9c5bf5045c546602a3c15
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411516"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticar e resolver problemas ao utilizar o SDK de .NET do Azure Cosmos DB
Este artigo aborda problemas comuns, soluções alternativas, passos de diagnóstico e ferramentas, ao utilizar o [SDK de .NET](sql-api-sdk-dotnet.md) com contas de API de SQL do Azure Cosmos DB.
O SDK de .NET fornece a representação lógica do lado do cliente para acessar a API de SQL do Azure Cosmos DB. Este artigo descreve ferramentas e abordagens para ajudá-lo caso se depare com quaisquer problemas.

## <a name="checklist-for-troubleshooting-issues"></a>Lista de verificação para resolução de problemas:
Considere a seguinte lista de verificação antes de mudar a sua aplicação para produção. Usando a lista de verificação, irá impedir que vários problemas comuns que poderá ver. Pode também rapidamente diagnosticar quando ocorre um problema:

*   Utilizar a versão mais recente [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). SDKs de pré-visualização não devem ser utilizadas para produção. Isso impedirá a sofrer problemas conhecidos que já foram corrigidos.
*   Reveja os [sugestões de desempenho](performance-tips.md)e siga as práticas sugeridas. Isto ajudará a evitar o dimensionamento, a latência e outros problemas de desempenho.
*   Ative o registo de SDK para o ajudar a solucionar um problema. Ativar o registo pode afetar o desempenho, então é melhor para ativá-la apenas quando a resolução de problemas. Pode ativar os seguintes registos:
    *   [Métricas de registo](monitor-accounts.md) com o portal do Azure. Métricas do portal mostram a telemetria do Azure Cosmos DB, que é útil para determinar se o problema corresponde ao Azure Cosmos DB ou se for do lado do cliente.
    *   Registo a [cadeia de caracteres de diagnóstico](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) das respostas de operação de ponto.
    *   Registo a [métricas de consulta de SQL](sql-api-query-metrics.md) de todas as respostas de consulta 
    *   Siga o programa de configuração para [registo de SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Dê uma olhada a [problemas comuns e soluções alternativas](#common-issues-workarounds) secção deste artigo.

Verifique os [secção de problemas do GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) que está a ser monitorizado ativamente. Verifique se qualquer problema semelhante com uma solução alternativa é já o arquivou. Se não encontrar uma solução, em seguida, envie um problema no GitHub. Pode abrir uma escala de suporte das questões urgentes.


## <a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="general-suggestions"></a>Sugestões gerais
* Execute a aplicação na mesma região do Azure como a sua conta do Azure Cosmos DB, sempre que possível. 
* Pode encontrar problemas de conectividade/disponibilidade devido à falta de recursos no seu computador cliente. Recomendamos que monitorize a utilização da CPU em nós a executar o cliente do Azure Cosmos DB e dimensionamento de segurança/out se estarem a ser executados em cargas elevadas.

### <a name="check-the-portal-metrics"></a>Verificar as métricas do portal
A verificar a [métricas do portal](monitor-accounts.md) irá ajudar a determinar se é um problema de lado do cliente ou se existir um problema com o serviço. Por exemplo se as métricas de contenham uma alta taxa de pedidos de taxa limitado (código de estado HTTP 429) que significa que o pedido está a obter limitado, em seguida, verifica o [taxa de pedido demasiado grande] secção. 

### <a name="request-timeouts"></a>Tempos limite de pedidos
RequestTimeout acontece normalmente quando utilizar Direct/TCP, mas pode acontecer no modo de Gateway. Estas são as causas conhecidas comuns e sugestões sobre como resolver o problema.

* Utilização da CPU é alta, que irá causar uma latência e/ou tempos limite do pedido. O cliente pode aumentar verticalmente a máquina de anfitrião para atribuir-lhe mais recursos, ou a carga pode ser distribuída por mais máquinas.
* Socket / disponibilidade de porta pode ser baixa. Ao utilizar os SDKs do .NET lançados antes da versão 2.0, os clientes em execução no Azure conseguiram acertar a [Exaustão de porta de SNAT (PAT) do Azure]. Este exemplo de por que é recomendado que sempre executar a versão mais recente do SDK.
* Criar várias instâncias de DocumentClient pode levar à contenção de ligação e problemas de tempo limite. Siga os [sugestões de desempenho](performance-tips.md)e o uso de uma única instância do DocumentClient em todo o processo.
* Os utilizadores veem, por vezes, tempos limite elevado de latência ou pedido porque suas coleções aprovisionadas isolá, o back-end limita os pedidos e o cliente tenta novamente internamente sem analisar isso para o chamador. Verifique os [métricas do portal](monitor-accounts.md).
* O Azure Cosmos DB distribui o débito aprovisionado total uniformemente por partições físicas. Verificar as métricas do portal para ver se a carga de trabalho está a experienciar um acesso frequente [chave de partição](partition-data.md). Isso fará com que o débito consumido agregado (RU/s) a aparecer como estando sob os RUs aprovisionadas, mas um débito de partição única consumido (RU/s) excederá o débito aprovisionado. 
* Além disso, o SDK 2.0 adiciona semântica de canal para ligações de direct/TCP. Uma ligação de TCP é utilizada para várias solicitações ao mesmo tempo. Isso pode levar a problemas de dois em casos específicos:
    * Um alto grau de simultaneidade pode levar à contenção no canal.
    * Respostas ou das solicitações grandes podem levar a cabeça de linha de bloqueio no canal e exacerbam a contenção, mesmo com um nível relativamente baixo de simultaneidade.
    * Se o caso se em qualquer uma destas duas categorias (ou se suspeita de alta utilização da CPU), são possíveis mitigações:
        * Tente dimensionar a aplicação de segurança/out.
        * Além disso, os registos do SDK podem ser capturados através de [ouvinte de rastreamento](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) para obter mais detalhes.

### <a name="connection-throttling"></a>Limitação de conexão
Limitação de conexão pode acontecer devido a um limite de ligação numa máquina host. Anteriormente a 2.0, os clientes em execução no Azure conseguiram acertar a [Exaustão de porta de SNAT (PAT) do Azure].

### <a name="snat"></a>Exaustão de porta de SNAT (PAT) do Azure

Se a aplicação é implementada em máquinas de virtuais do Azure sem um endereço IP público, por predefinição [portas de SNAT de Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) estabelecer ligações para qualquer ponto de extremidade fora da sua VM. O número de ligações permitido da VM para o ponto de final do Azure Cosmos DB é limitado pelos [configuração do Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Portas SNAT do Azure são utilizadas apenas quando a VM tem um endereço IP privado e um processo da VM tenta estabelecer ligação com um endereço IP público. Existem duas soluções alternativas para evitar a limitação de SNAT do Azure:

* Adicione o ponto de final de serviço do Azure Cosmos DB para a sub-rede da sua rede virtual de máquinas virtuais do Azure. Para obter mais informações, consulte [pontos finais de serviço de rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Quando o ponto final de serviço está ativado, os pedidos não são mais enviados de um IP público para o Azure Cosmos DB. Em vez disso, a rede virtual e a identidade do sub-rede são enviados. Esta alteração poderá resultar em quedas de firewall se só são permitidos IPs públicos. Se utilizar uma firewall, quando ativar o ponto final de serviço, adicionar uma sub-rede para o firewall usando [ACLs de rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Atribua um IP público à VM do Azure.

### <a name="http-proxy"></a>Proxy HTTP
Se utilizar um proxy de HTTP, certificar-se de que pode suportar o número de ligações configuradas no SDK `ConnectionPolicy`.
Caso contrário, o que enfrenta problemas de ligação.

### Taxa de pedido demasiado grande<a name="request-rate-too-large"></a>
'Taxa demasiado grande do pedido' ou o código de erro 429 indica que os pedidos estão a ser limitados, uma vez que o débito consumido (RU/s) excedeu o débito aprovisionado. O SDK será automaticamente repetida de pedidos com base no especificado [política de repetição](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Se obtiver, muitas vezes, esta falha, considere aumentar o débito na coleção. Verifique os [métricas do portal](use-metrics.md) para ver se estão a receber erros de 429. Rever seu [chave de partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) para se certificar de que resulta numa distribuição uniforme de volume de armazenamento e a pedido. 

### <a name="slow-query-performance"></a>Desempenho de consulta lenta
O [consultar métricas](sql-api-query-metrics.md) irá ajudar a determinar onde a consulta passa a maior parte do tempo. As métricas de consulta, pode ver quanto é que está a ser gasto em vs os back-end, o cliente.
* Se a consulta do back-end devolve rapidamente e passa um tempo de grandes dimensões no cliente Verifique a carga na máquina. É provável que não há suficiente recursos e o SDK está a aguardar recursos estejam disponíveis para manipular a resposta.
* Se a consulta do back-end estiver lenta tente [otimizar a consulta](optimize-cost-queries.md) e olhar para o atual [política de indexação](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Taxa de pedido demasiado grande]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Exaustão de porta de SNAT (PAT) do Azure]: #snat
[Production check list]: #production-check-list


