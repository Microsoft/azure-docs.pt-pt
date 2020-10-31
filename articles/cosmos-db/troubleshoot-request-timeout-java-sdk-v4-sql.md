---
title: Resolução de problemas Azure Cosmos DB HTTP 408 ou solicitar problemas de timeout com o Java v4 SDK
description: Saiba como diagnosticar e corrigir Java SDK solicitar exceções de tempo limite com o Java v4 SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 45452109582be40f007ae57a00c2a151f216bdb8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103166"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Diagnosticar e resolver problemas Azure Cosmos DB Java v4 SDK solicitar exceções no tempo limite
O erro HTTP 408 ocorre se o SDK não conseguir completar o pedido antes do prazo de tempo.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para solicitar exceções no prazo de tempo.

### <a name="high-cpu-utilization"></a>Alta utilização do CPU
Alta utilização do CPU é o caso mais comum. Para uma latência ótima, o uso do CPU deve ser de aproximadamente 40%. Utilize 10 segundos como intervalo para monitorizar a utilização máxima (não média) do CPU. Os picos de CPU são mais comuns com consultas de divisórias cruzadas onde pode fazer múltiplas ligações para uma única consulta.

#### <a name="solution"></a>Solução:
A aplicação do cliente que utiliza o SDK deve ser dimensionada ou para fora.

### <a name="connection-throttling"></a>Estrangulamento de conexão
O estrangulamento da ligação pode ocorrer devido a um limite de ligação numa máquina hospedeira ou à exaustão da porta Azure SNAT (PAT).

### <a name="connection-limit-on-a-host-machine"></a>Limite de ligação numa máquina hospedeira
Alguns sistemas Linux, como o Red Hat, têm um limite superior no número total de ficheiros abertos. As tomadas no Linux são implementadas como ficheiros, pelo que este número também limita o número total de ligações. Execute o seguinte comando.

```bash
ulimit -a
```

#### <a name="solution"></a>Solução:
O número de ficheiros abertos no máximo permitidos, que são identificados como "nofile", tem de ser de pelo menos 10.000 ou mais. Para mais informações, consulte as dicas de [desempenho](performance-tips-java-sdk-v4-sql.md)V4 da Azure Cosmos DB Java SDK v4 .

### <a name="socket-or-port-availability-might-be-low"></a>A disponibilidade da tomada ou da porta pode ser baixa
Ao correr em Azure, os clientes que usam o Java SDK podem atingir a exaustão da porta Azure SNAT (PAT).

#### <a name="solution-1"></a>Solução 1:
Se estiver a correr em VMs Azure, siga o guia de [exaustão da porta SNAT](troubleshoot-java-sdk-v4-sql.md#snat).

#### <a name="solution-2"></a>Solução 2:
Se estiver a executar o Azure App Service, siga o [guia de resolução de erros de ligação](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) e [utilize diagnósticos do Serviço de Aplicações](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Solução 3:
Se estiver a executar funções Azure, verifique se está a seguir a recomendação da [Azure Functions](../azure-functions/manage-connections.md#static-clients) de manter clientes singleton ou estáticos para todos os serviços envolvidos (incluindo Azure Cosmos DB). Verifique os [limites](../azure-functions/functions-scale.md#service-limits) de serviço com base no tipo e tamanho do alojamento da App de Função.

#### <a name="solution-4"></a>Solução 4:
Se utilizar um representante HTTP, certifique-se de que consegue suportar o número de ligações configuradas no SDK `GatewayConnectionConfig` . Caso contrário, enfrentará problemas de ligação.

### <a name="create-multiple-client-instances"></a>Criar múltiplas instâncias de clientes
A criação de múltiplas instâncias de clientes pode levar a problemas de contenção de ligação e de tempo limite.

#### <a name="solution-1"></a>Solução 1:
Siga as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md#sdk-usage)e use um único exemplo cosmosclient em toda uma aplicação.

#### <a name="solution-2"></a>Solução 2:
Se a Singleton CosmosClient não for possível ter numa aplicação, recomendamos a utilização da partilha de ligações em vários Clientes Cosmos através desta API `connectionSharingAcrossClientsEnabled(true)` em CosmosClient. Quando tem múltiplas instâncias do Cosmos Client no mesmo JVM interagindo em várias contas cosmos, permitindo isso permite a partilha de ligação em modo direto, se possível, entre instâncias do Cliente Cosmos. Por favor, note que ao definir esta opção, a configuração de ligação (por exemplo, config de tempo de tomada, config de tempo de inatividade) do primeiro cliente instantâneo será usada para todas as outras instâncias do cliente.

### <a name="hot-partition-key"></a>Chave de partição quente
AZure Cosmos DB distribui a produção global a provisionada uniformemente através de divisórias físicas. Quando há uma partição quente, uma ou mais teclas de partição lógica numa partição física estão a consumir todas as Unidades de Pedido de Partição Física por segundo (RU/s). Ao mesmo tempo, o RU/S em outras divisórias físicas não está a ser utilizado. Como sintoma, o total de RU/s consumidos será inferior ao TOTAL previsto ru/s na base de dados ou contentor, mas ainda verá estrangulamento (429s) nos pedidos contra a chave de partição lógica quente. Utilize a [métrica de consumo de RU normalizada](monitor-normalized-request-units.md) para ver se a carga de trabalho encontra uma divisória quente. 

#### <a name="solution"></a>Solução:
Escolha uma boa chave de partição que distribui uniformemente o volume e armazenamento do pedido. Saiba como mudar a [sua tecla de partição.](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)

### <a name="high-degree-of-concurrency"></a>Elevado grau de concordância
A aplicação está a fazer um alto nível de concordância, o que pode levar a uma contenda no canal.

#### <a name="solution"></a>Solução:
A aplicação do cliente que utiliza o SDK deve ser dimensionada ou para fora.

### <a name="large-requests-or-responses"></a>Grandes pedidos ou respostas
Grandes pedidos ou respostas podem levar a bloqueios de linha no canal e exacerbar a contenção, mesmo com um grau de concordância relativamente baixo.

#### <a name="solution"></a>Solução:
A aplicação do cliente que utiliza o SDK deve ser dimensionada ou para fora.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>A taxa de falha está dentro do Azure Cosmos DB SLA
A aplicação deve ser capaz de lidar com falhas transitórias e tentar novamente quando necessário. Quaisquer 408 exceções não são novamente julgadas porque na criação de caminhos é impossível saber se o serviço criou o item ou não. Enviar o mesmo item novamente para criar irá causar uma exceção de conflito. A lógica de negócio das aplicações dos utilizadores pode ter uma lógica personalizada para lidar com conflitos, o que quebraria a ambiguidade de um item existente versus conflito de uma criação de novamente.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>Taxa de falha viola o Azure Cosmos DB SLA
Contacte [o Suporte Azure](https://aka.ms/azure-support).

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-java-sdk-v4-sql.md) quando utilizar o Azure Cosmos DB Java v4 SDK.
* Saiba mais sobre as diretrizes de desempenho para [Java v4](performance-tips-java-sdk-v4-sql.md).
