---
title: Otimização de implantação e desempenho
titleSuffix: Azure Cognitive Search
description: Aprenda técnicas e práticas recomendadas para ajustar o desempenho de Pesquisa Cognitiva do Azure e configurar a escala ideal.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4ff6779843546bd41423c140ef40de6a7e24b026
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113260"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-cognitive-search"></a>Estratégias de implantação e práticas recomendadas para otimizar o desempenho no Azure Pesquisa Cognitiva

Este artigo descreve as práticas recomendadas para cenários avançados com requisitos sofisticados de escalabilidade e disponibilidade. 

## <a name="develop-baseline-numbers"></a>Desenvolver números de linha de base
Ao otimizar o desempenho da pesquisa, você deve se concentrar em reduzir o tempo de execução da consulta. Para fazer isso, você precisa saber a aparência de um carregamento de consulta típico. As diretrizes a seguir podem ajudá-lo a chegar em números de consulta de linha de base.

1. Escolha uma latência de destino (ou a quantidade máxima de tempo) que uma solicitação de pesquisa típica deve levar para ser concluída.
2. Crie e teste uma carga de trabalho real em relação ao seu serviço de pesquisa com um conjunto de espaço real para medir essas taxas de latência.
3. Comece com um número baixo de consultas por segundo (QPS) e aumente gradualmente o número executado no teste até que a latência de consulta fique abaixo da latência de destino definida. Esse é um parâmetro de comparação importante para ajudá-lo a planejar a escala à medida que seu aplicativo cresce de acordo com o uso.
4. Sempre que possível, reutilize conexões HTTP. Se você estiver usando o SDK do .NET Pesquisa Cognitiva do Azure, isso significa que você deve reutilizar uma instância do ou [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) e, se estiver usando a API REST, deverá reutilizar um único HttpClient.
5. Varie a substância de solicitações de consulta para que a pesquisa ocorra em diferentes partes do índice. A variação é importante porque, se você executar continuamente as mesmas solicitações de pesquisa, o cache de dados começará a fazer com que o desempenho seja melhor do que pode com um conjunto de consultas mais distintas.
6. Varie a estrutura das solicitações de consulta para que você obtenha diferentes tipos de consultas. Nem todas as consultas de pesquisa são executadas no mesmo nível. Por exemplo, uma pesquisa de documento ou sugestão de pesquisa é normalmente mais rápida do que uma consulta com um número significativo de facetas e filtros. A composição de teste deve incluir várias consultas, em aproximadamente as mesmas proporções que você esperaria na produção.  

Ao criar essas cargas de trabalho de teste, há algumas características do Azure Pesquisa Cognitiva para ter em mente:

+ É possível sobrecarregar seu serviço enviando por push muitas consultas de pesquisa de uma só vez. Quando isso acontecer, você verá códigos de resposta HTTP 503. Para evitar um 503 durante o teste, comece com vários intervalos de solicitações de pesquisa para ver as diferenças nas taxas de latência à medida que você adiciona mais solicitações de pesquisa.

+ O Azure Pesquisa Cognitiva não executa tarefas de indexação em segundo plano. Se o seu serviço tratar as cargas de trabalho de consulta e indexação simultaneamente, leve isso em conta introduzindo trabalhos de indexação em seus testes de consulta ou explorando as opções para executar trabalhos de indexação fora do horário de pico.

> [!NOTE]
> O [teste de carga do Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) é uma maneira realmente boa de executar testes de benchmark, pois ele permite que você execute solicitações HTTP, pois você precisaria para executar consultas no Azure pesquisa cognitiva e permite a paralelização de solicitações.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Dimensionamento para alto volume de consulta e solicitações limitadas
Quando você estiver recebendo muitas solicitações limitadas ou exceder suas taxas de latência de destino de uma carga de consulta maior, você pode procurar diminuir as taxas de latência de uma das duas maneiras:

1. **Aumentar réplicas:**  Uma réplica é como uma cópia dos seus dados, permitindo que Pesquisa Cognitiva do Azure carreguem solicitações de balanceamento em relação a várias cópias.  Todos os balanceamentos de carga e replicação de dados entre réplicas são gerenciados pelo Azure Pesquisa Cognitiva e você pode alterar o número de réplicas alocadas para o serviço a qualquer momento.  Você pode alocar até 12 réplicas em um serviço de pesquisa padrão e 3 réplicas em um serviço de pesquisa básico. As réplicas podem ser ajustadas do [portal do Azure](search-create-service-portal.md) ou do [PowerShell](search-manage-powershell.md).
2. **Aumentar a camada de pesquisa:**  O Pesquisa Cognitiva do Azure vem em [várias camadas](https://azure.microsoft.com/pricing/details/search/) e cada uma dessas camadas oferece níveis diferentes de desempenho.  Em alguns casos, você pode ter tantas consultas que a camada em que você está em não pode fornecer taxas de latência suficientemente baixas, mesmo quando as réplicas são maximizadas. Nesse caso, convém considerar o aproveitamento de uma das camadas de pesquisa mais altas, como a camada do Azure Pesquisa Cognitiva S3, que é adequada para cenários com um grande número de documentos e cargas de trabalho de consulta extremamente altas.

## <a name="scaling-for-slow-individual-queries"></a>Dimensionamento para consultas individuais lentas
Outro motivo para taxas de alta latência é uma única consulta demorando muito para ser concluída. Nesse caso, a adição de réplicas não ajudará. Duas opções possíveis que podem ajudar incluem o seguinte:

1. **Aumentar partições** Uma partição é um mecanismo para dividir seus dados em recursos extras. A adição de uma segunda partição divide os dados em dois, uma terceira partição o divide em três e assim por diante. Um efeito colateral positivo é que as consultas mais lentas às vezes são executadas mais rapidamente devido à computação paralela. Observamos a paralelização em consultas de baixa seletividade, como consultas que correspondem a muitos documentos ou facetas que fornecem contagens em um grande número de documentos. Como uma computação significativa é necessária para pontuar a relevância dos documentos ou para contar os números de documentos, adicionar partições extras ajuda as consultas a serem concluídas com mais rapidez.  
   
   Pode haver no máximo 12 partições no serviço de pesquisa padrão e uma partição no serviço de pesquisa básico.  As partições podem ser ajustadas do [portal do Azure](search-create-service-portal.md) ou do [PowerShell](search-manage-powershell.md).

2. **Limitar campos de cardinalidade alta:** Um campo de alta cardinalidade consiste em um campo de facetable ou filtrável que tem um número significativo de valores exclusivos e, como resultado, consome recursos significativos ao calcular os resultados. Por exemplo, definir uma ID do produto ou um campo de descrição como facetable/filtrável conta como alta cardinalidade porque a maioria dos valores de documento para documento são exclusivos. Sempre que possível, limite o número de campos de cardinalidade alta.

3. **Aumentar a camada de pesquisa:**  Mover para uma camada mais alta de Pesquisa Cognitiva do Azure pode ser outra maneira de melhorar o desempenho de consultas lentas. Cada camada mais alta fornece CPUs mais rápidas e mais memória, as quais têm um impacto positivo no desempenho da consulta.

## <a name="scaling-for-availability"></a>Dimensionamento para disponibilidade
As réplicas não apenas ajudam a reduzir a latência de consulta, mas também podem permitir alta disponibilidade. Com uma única réplica, você deve esperar tempo de inatividade periódico devido à reinicialização do servidor após as atualizações de software ou para outros eventos de manutenção que ocorrerão.  Como resultado, é importante considerar se seu aplicativo requer alta disponibilidade de pesquisas (consultas), bem como gravações (eventos de indexação). O Azure Pesquisa Cognitiva oferece opções de SLA em todas as ofertas de pesquisa pagas com os seguintes atributos:

* 2 réplicas para alta disponibilidade de cargas de trabalho somente leitura (consultas)
* 3 ou mais réplicas para alta disponibilidade de cargas de trabalho de leitura/gravação (consultas e indexação)

Para obter mais detalhes sobre isso, visite o [contrato de nível de serviço de pesquisa cognitiva do Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Como as réplicas são cópias de seus dados, ter várias réplicas permite que o Azure Pesquisa Cognitiva faça reinicializações e manutenção do computador em uma réplica, permitindo que a execução da consulta continue em outras réplicas. Por outro lado, se você tirar as réplicas de longe, incorrerá em degradação do desempenho da consulta, supondo que essas réplicas fossem um recurso subutilizado.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Dimensionamento para cargas de trabalho distribuídas geograficamente e redundância geográfica
Para cargas de trabalho distribuídas geograficamente, os usuários que estão localizados longe da data center hospedando o Pesquisa Cognitiva do Azure terão taxas de latência mais altas. Uma mitigação é provisionar vários serviços de pesquisa em regiões com mais proximidade com esses usuários. Atualmente, o Azure Pesquisa Cognitiva não fornece um método automatizado de replicação geográfica de índices de Pesquisa Cognitiva do Azure entre regiões, mas há algumas técnicas que podem ser usadas para tornar esse processo simples de implementar e gerenciar. Elas são descritas nas próximas seções.

O objetivo de um conjunto de serviços de pesquisa distribuído geograficamente é ter dois ou mais índices disponíveis em duas ou mais regiões em que um usuário é encaminhado para o serviço de Pesquisa Cognitiva do Azure que fornece a menor latência, como visto neste exemplo:

   ![Guia cruzada de serviços por região][1]

### <a name="keeping-data-in-sync-across-multiple-azure-cognitive-search-services"></a>Manter os dados sincronizados entre vários serviços de Pesquisa Cognitiva do Azure
Há duas opções para manter seus serviços de pesquisa distribuídos em sincronia, que consistem em usar o [indexador pesquisa cognitiva do Azure](search-indexer-overview.md) ou a API de envio (também conhecida como [API REST do Azure pesquisa cognitiva](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Usar indexadores para atualizar o conteúdo em vários serviços

Se você já estiver usando o indexador em um serviço, poderá configurar um segundo indexador em um segundo serviço para usar o mesmo objeto de fonte de dados, obtendo dados do mesmo local. Cada serviço em cada região tem seu próprio indexador e um índice de destino (o índice de pesquisa não é compartilhado, o que significa que os dados estão duplicados), mas cada indexador faz referência à mesma fonte de dados.

Aqui está um Visual de alto nível da aparência dessa arquitetura.

   ![Fonte de dados única com indexador distribuído e combinações de serviço][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Usar APIs REST para enviar atualizações de conteúdo por push em vários serviços
Se você estiver usando a API REST do Azure Pesquisa Cognitiva para [enviar conteúdo por push em seu índice de pesquisa cognitiva do Azure](https://docs.microsoft.com/rest/api/searchservice/update-index), você poderá manter seus vários serviços de pesquisa sincronizados enviando alterações a todos os serviços de pesquisa sempre que uma atualização for necessária. Em seu código, certifique-se de lidar com casos em que uma atualização para um serviço de pesquisa falha, mas é bem-sucedida para outros serviços de pesquisa.

## <a name="leverage-azure-traffic-manager"></a>Aproveite o Gerenciador de tráfego do Azure
O [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) permite que você encaminhe solicitações para vários sites geograficamente localizados que são então apoiados por vários serviços de pesquisa. Uma vantagem do Gerenciador de tráfego é que ele pode investigar o Pesquisa Cognitiva do Azure para garantir que ele esteja disponível e rotear os usuários para serviços de pesquisa alternativos em caso de tempo de inatividade. Além disso, se você estiver roteando solicitações de pesquisa por meio de sites do Azure, o Gerenciador de tráfego do Azure permite balancear a carga de casos em que o site está ativo, mas não o Azure Pesquisa Cognitiva. Aqui está um exemplo de qual a arquitetura que aproveita o Gerenciador de tráfego.

   ![Entre guias de serviços por região, com o Gerenciador de tráfego central][3]

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os tipos de preço e limites de serviços para cada um, confira [limites de serviço no Azure pesquisa cognitiva](search-limits-quotas-capacity.md).

Visite [planejamento de capacidade](search-capacity-planning.md) para saber mais sobre combinações de partição e réplica.

Para obter mais informações sobre o desempenho e ver algumas demonstrações de como implementar as otimizações discutidas neste artigo, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
