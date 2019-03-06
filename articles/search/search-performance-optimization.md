---
title: Estratégias de implantação e as melhores práticas para otimizar o desempenho - Azure Search
description: Aprenda técnicas e práticas recomendadas para ajustar o desempenho de pesquisa do Azure e configurar o dimensionamento ideal.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 8a07657d04cee57cb69c9f5f7862fed3e7965716
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404472"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Estratégias de implantação e as melhores práticas para otimizar o desempenho no Azure Search

Este artigo descreve as melhores práticas para cenários avançados com requisitos sofisticados para escalabilidade e disponibilidade. 

## <a name="develop-baseline-numbers"></a>Desenvolver os números de linha de base
Ao otimizar o desempenho de pesquisa, deve se concentrar em reduzir o tempo de execução da consulta. Para isso, precisa saber o aspeto de uma carga típica de consulta. As diretrizes seguintes podem ajudá-lo a chegar a números de consulta de linha de base.

1. Escolher uma latência de destino (ou a quantidade máxima de tempo) que uma pesquisa típica do pedido deve demorar para concluir.
2. Criar e testar uma carga de trabalho real em relação a seu serviço de pesquisa com um conjunto de dados realista para medir estas tarifas de latência.
3. Começar com um número reduzido de consultas por segundo (QPS) e a aumentar gradualmente o número executado no teste, até que a latência da consulta passa a ser inferior a latência de destino definidos. Este é um parâmetro de comparação importante para ajudar a planear para dimensionamento à medida que aumenta a aplicação em utilização.
4. Sempre que possível, reutilize ligações HTTP. Se estiver a utilizar o SDK .NET da Azure Search, isso significa que deve reutilizar uma instância ou [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) instância, e se estiver a utilizar a API REST, deve reutilizar um HttpClient único.
5. Variar a substância de pedidos de consulta, para que a pesquisa ocorre através de diferentes partes do seu índice. Variação é importante porque se executar continuamente os mesmos pedidos de pesquisa, a cache de dados começará a fazer do desempenho parecer melhor que ele poderá com uma consulta mais diferentes definido.
6. Variar a estrutura de pedidos de consulta para que diferentes tipos de consultas. Nem todas as consultas de pesquisa executa no mesmo nível. Por exemplo, uma sugestão de pesquisa ou de pesquisa de documento é normalmente mais rápida do que uma consulta com um número significativo de facetas e filtros. Composição de teste deve incluir várias consultas, em aproximadamente os mesmo rácios, como esperaria em produção.  

Ao criar essas cargas de trabalho de teste, existem algumas características do Azure Search para ter em mente:

+ É possível sobrecarregar o seu serviço ao enviar demasiadas consultas de pesquisa de uma só vez. Quando isto acontecer, verá códigos de resposta de HTTP 503. Para evitar um 503 durante o teste, comece com vários intervalos de pedidos de pesquisa para ver as diferenças nas taxas de latência, à medida que adiciona mais pedidos de pesquisa.

+ O Azure Search não executa a indexação de tarefas em segundo plano. Se o seu serviço manipula a consulta e indexação de cargas de trabalho em simultâneo, leve isso em conta introduzindo qualquer um dos trabalhos de indexação para seus testes de consulta ou ao explorar opções para executar tarefas de indexação durante os horários de pico.

> [!NOTE]
> [Teste de carga do Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) é uma excelente maneira de executar o benchmark testa, pois permite-lhe executar pedidos HTTP que seria necessário para executar consultas no Azure Search e permite a paralelização de pedidos.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Pedidos limitados do Microsoft e de dimensionamento para o volume de consulta elevado
Quando está recebendo demasiados pedidos limitados ou excede as taxas de latência de destino de uma carga maior de consulta, pode consultar para diminuir as taxas de latência de uma de duas formas:

1. **Aumente as réplicas:**  Uma réplica é como uma cópia dos seus dados, permitindo que o Azure Search carregar pedidos de balanceamento em relação as várias cópias.  Tudo o balanceamento de carga e a replicação de dados em réplicas é gerida pelo Azure Search e é possível alterar o número de réplicas alocado para o seu serviço em qualquer altura.  Pode alocar até 12 réplicas num serviço de pesquisa padrão e 3 réplicas num serviço de pesquisa básica. As réplicas podem ser ajustadas a partir da [portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).
2. **Aumente o escalão de pesquisa:**  O Azure Search é uma [número de camadas](https://azure.microsoft.com/pricing/details/search/) e cada uma dessas camadas oferece diferentes níveis de desempenho.  Em alguns casos, pode ter muitas consultas que a camada a que se estiver a utilizar não é possível fornecer as taxas de tão baixa latência, mesmo quando as réplicas são alcance o limite máximo. Neste caso, poderá querer considerar a tirar partido de um dos escalões de pesquisa superior, como a camada de Azure Search S3 que é bem adequado para cenários com grande número de documentos e cargas de trabalho de consulta extremamente alta.

## <a name="scaling-for-slow-individual-queries"></a>Dimensionamento para consultas individuais lentas
Outro motivo para as taxas de alta latência é uma única consulta demorar demasiado tempo a concluir. Neste caso, a adicionar réplicas não irá ajudar. Duas opções opções possíveis que podem ajudar a incluem o seguinte:

1. **Aumentar as partições** uma partição é um mecanismo para dividir os dados entre recursos extras. Adicionar uma segunda partição divide os dados em duas, uma terceira partição divide-o em três e assim por diante. Um efeito de lado positivo é que as consultas mais lentas, às vezes, realizam mais rapidamente devido a computação paralela. Podemos ter apontado paralelização em consultas de baixa seletividade, como consultas que correspondem a muitos documentos ou facetas fornecendo contagens num grande número de documentos. Uma vez que o cálculo significativo é necessária para classificar a relevância dos documentos, ou contar o número de documentos, adicionar partições Extras ajuda a consultas de conclusão das análises.  
   
   Pode haver um máximo de 12 partições no serviço de pesquisa padrão e 1 partição no serviço de pesquisa básica.  As partições podem ser ajustadas a partir da [portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).

2. **Campos de cardinalidade elevada de limite:** Um campo de cardinalidade elevada consiste num facetável ou o campo filtrável que tem um número significativo de valores exclusivos e, consequentemente, consome recursos significativos ao computar os resultados. Por exemplo, definir um campo de ID do produto ou a descrição como facetável/filtrável contabilizaria como cardinalidade elevada porque a maioria dos valores do documento para documento é exclusiva. Sempre que possível, limite o número de campos de cardinalidade elevada.

3. **Aumente o escalão de pesquisa:**  Mover até um escalão mais elevado do Azure Search pode ser outra maneira de melhorar o desempenho das consultas lentas. Cada escalão superior mais depressa fornece CPUs e mais memória, que tem um impacto positivo no desempenho de consulta.

## <a name="scaling-for-availability"></a>Dimensionamento para disponibilidade
Réplicas não só ajudam a reduzir a latência da consulta, mas também podem permitir para elevada disponibilidade. Com uma única réplica, deve esperar periódico período de indisponibilidade devido a reinícios de servidor após as atualizações de software ou para outros eventos de manutenção que irão ocorrer.  Como resultado, é importante considerar se o seu aplicativo requer alta disponibilidade das pesquisas (consultas), bem como de escritas (indexação eventos). O Azure Search oferece opções de SLA em todas as ofertas de pesquisa paga com os seguintes atributos:

* 2 réplicas para elevada disponibilidade de só de leitura cargas de trabalho (consultas)
* 3 ou mais réplicas para elevada disponibilidade de cargas de trabalho de leitura / escrita (consultas e indexação)

Para obter mais detalhes sobre isso, visite o [contrato de nível de serviço do Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Uma vez que as réplicas são cópias dos seus dados, ter várias réplicas permite a pesquisa do Azure para machine reinícios e manutenção com base numa réplica que permite a execução da consulta continuar com outras réplicas. Por outro lado, se utilizar réplicas de distância, irá incorrer degradação do desempenho de consulta, partindo do princípio de que essas réplicas foram um recurso subutilizados.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Dimensionamento para cargas de trabalho distribuída geograficamente e a redundância geográfica
Para cargas de trabalho distribuída geograficamente, os utilizadores que estão localizados longe de ser o Datacenter que aloja o Azure Search terão as taxas de latência superior. Uma atenuação é aprovisionar vários serviços de pesquisa nas regiões com mais de perto proximidade aos utilizadores. O Azure Search atualmente não fornecem um método automatizado de georreplicação índices da Azure Search em várias regiões, mas existem algumas técnicas que podem ser utilizadas que podem tornar esse processo simples de implementar e gerir. Elas são descritas nas próximas seções.

O objetivo de um conjunto distribuída geograficamente dos serviços de pesquisa é ter dois ou mais índices disponíveis em duas ou mais regiões em que um utilizador é encaminhado para o serviço de Azure Search que fornece a latência mais baixa, como mostrado neste exemplo:

   ![Cross-separador de serviços por região][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Mantendo os dados sincronizados em vários serviços do Azure Search
Existem duas opções para manter os seus serviços de pesquisa distribuída em sincronia que são compostas por meio da [indexador de pesquisa do Azure](search-indexer-overview.md) ou da API Push (também conhecido como o [API REST da Azure Search](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Utilizar indexadores para atualizar conteúdo em vários serviços

Se já estiver a utilizar o indexador num serviço, pode configurar um indexador segundo num segundo serviço para utilizar o mesmo objeto de origem de dados, extrair dados a partir da mesma localização. Cada serviço em cada região tem seu próprio indexador e um índice de destino (corpo não é compartilhado, que significa que os dados são duplicados), mas cada indexador faz referência a mesma origem de dados.

Aqui está um visual de alto nível de como ficaria nessa arquitetura.

   ![Origem de dados única com o indexador distribuído e combinações de serviço][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Utilizar REST APIs para enviar atualizações de conteúdo em vários serviços
Se estiver a utilizar a API de REST do Azure Search para [push o conteúdo no seu índice da Azure Search](https://docs.microsoft.com/rest/api/searchservice/update-index), pode manter os vários serviços de pesquisa em sincronia ao enviar alterações para todos os serviços de pesquisa, sempre que é necessária uma atualização. Em seu código, certifique-se lidar com casos em que uma atualização do serviço de pesquisa de uma falha, mas falha por outros serviços de pesquisa.

## <a name="leverage-azure-traffic-manager"></a>Tirar partido do Gestor de tráfego do Azure
[O Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) permite-lhe para encaminhar os pedidos para vários sites localizados geograficamente que, em seguida, são apoiados por vários serviços de pesquisa do Azure. Uma vantagem do Gestor de tráfego é que podem testar o Azure Search para garantir que está disponível e encaminhar os utilizadores para serviços de pesquisa alternativo em caso de período de indisponibilidade. Além disso, se o encaminhamento de pedidos de pesquisa por meio de Web Sites do Azure, Gestor de tráfego do Azure permite que carregar casos de saldo em que o Web site está ativo, mas não a Azure Search. Eis um exemplo da aparência da arquitetura que tira partido do Gestor de tráfego.

   ![Cross-separador de serviços por região, com o Gestor de tráfego central][3]

## <a name="monitor-performance"></a>Monitorizar desempenho
O Azure Search oferece a capacidade de analisar e monitorizar o desempenho do seu serviço através de [a análise de tráfego de pesquisa](search-traffic-analytics.md). Quando ativar esta funcionalidade e adicionar instrumentação à sua aplicação de cliente, opcionalmente, pode iniciar as operações de pesquisa individuais, bem como métricas agregadas para uma conta de armazenamento do Azure que, em seguida, pode ser processada para análise ou visualizada no Power BI. Capturas de métricas desta forma fornecem estatísticas de desempenho, tais como o número médio de consultas ou tempos de resposta da consulta. Além disso, o registo de operação permite-lhe explorar detalhes de operações de pesquisa específico.

Análise de tráfego é útil para entender as taxas de latência desse ponto de vista do Azure Search. Uma vez que as métricas de desempenho de consulta com sessão iniciadas baseiam-se o tempo que uma consulta leva para ser totalmente processados na Azure Search (desde o momento em que for solicitada quando ele é enviado), é possível usá-lo para determinar se são de problemas de latência do lado do serviço de Azure Search ou detalhes IDE do serviço, como from a latência de rede.  

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre os limites de serviços e escalões de preços para cada um, veja [limites de serviço do Azure Search](search-limits-quotas-capacity.md).

Visite [planeamento de capacidade](search-capacity-planning.md) para saber mais sobre as combinações de partição e réplica.

Para obter mais aprofundamento sobre o desempenho e para ver algumas demonstrações de como implementar as otimizações discutidas neste artigo, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
