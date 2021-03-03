---
title: Dimensionamento para desempenho
titleSuffix: Azure Cognitive Search
description: Aprenda técnicas e boas práticas para afinar o desempenho da Pesquisa Cognitiva Azure e configurar a escala ideal.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.custom: references_regions
ms.openlocfilehash: 60371888dbc4f0cbc33f1ad1b2a685dbb071c01a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670715"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Escala para desempenho na Pesquisa Cognitiva Azure

Este artigo descreve as melhores práticas para cenários avançados com requisitos sofisticados de escalabilidade e disponibilidade.

## <a name="start-with-baseline-numbers"></a>Comece com os números de base

Antes de realizar um esforço de implantação maior, certifique-se de saber como é uma carga de consulta típica. As seguintes diretrizes podem ajudá-lo a chegar aos números de consulta de base.

1. Escolha uma latência do alvo (ou quantidade máxima de tempo) que um pedido típico de pesquisa deve levar para completar.

1. Crie e teste uma carga de trabalho real contra o seu serviço de pesquisa com um conjunto de dados realista para medir estas taxas de latência.

1. Comece com um número reduzido de consultas por segundo (QPS) e, em seguida, aumente gradualmente o número executado no teste até que a latência da consulta caia abaixo do alvo predefinido. Esta é uma referência importante para ajudá-lo a planear a escala à medida que a sua aplicação cresce no uso.

1. Sempre que possível, reutilizar as ligações HTTP. Se estiver a utilizar a Azure Cognitive Search .NET SDK, isto significa que deve reutilizar uma instância ou [instância searchClient,](/dotnet/api/azure.search.documents.searchclient) e se estiver a utilizar a API REST, deverá reutilizar um único HttpClient.

1. Varie a substância dos pedidos de consulta para que a pesquisa ocorra em diferentes partes do seu índice. A variação é importante porque se executar continuamente os mesmos pedidos de pesquisa, o caching de dados começará a fazer o desempenho parecer melhor do que poderia com um conjunto de consultas mais díspares.

1. Varie a estrutura dos pedidos de consulta para que obtenha diferentes tipos de consultas. Nem todas as consultas de pesquisa sãodas no mesmo nível. Por exemplo, uma sugestão de pesquisa ou pesquisa de documentos é tipicamente mais rápida do que uma consulta com um número significativo de facetas e filtros. A composição do teste deve incluir várias consultas, aproximadamente nas mesmas relações que seria de esperar na produção.  

Ao criar estas cargas de trabalho de teste, existem algumas características da Pesquisa Cognitiva Azure para ter em mente:

+ É possível sobrecarregar o seu serviço empurrando demasiadas consultas de pesquisa de cada vez. Quando isto acontecer, verá os códigos de resposta HTTP 503. Para evitar um 503 durante os testes, comece com várias gamas de pedidos de pesquisa para ver as diferenças nas taxas de latência à medida que adiciona mais pedidos de pesquisa.

+ A Azure Cognitive Search não executa tarefas de indexação em segundo plano. Se o seu serviço tratar de consultas e indexação de cargas de trabalho simultaneamente, tenha isso em conta, introduzindo empregos de indexação nos seus testes de consulta, ou explorando opções para executar trabalhos de indexação durante as horas de ponta.

> [!Tip]
> Pode simular uma carga de consulta realista utilizando ferramentas de teste de carga. Experimente [o teste de carga com a Azure DevOps](/azure/devops/test/load-test/get-started-simple-cloud-load-test) ou utilize uma [destas alternativas](/azure/devops/test/load-test/overview#alternatives).

## <a name="scale-for-high-query-volume"></a>Escala para volume de consulta elevado

Um serviço é sobrecarregado quando as consultas demoram muito tempo ou quando o serviço começa a deixar cair pedidos. Se isto acontecer, pode resolver o problema de duas formas:

+ **Adicionar réplicas**  

  Cada réplica é uma cópia dos seus dados, permitindo ao serviço carregar pedidos de saldo contra várias cópias.  Todo o equilíbrio de carga e replicação de dados é gerido pela Azure Cognitive Search e pode alterar o número de réplicas atribuídas ao seu serviço a qualquer momento. Pode alocar até 12 réplicas num serviço de pesquisa Standard e 3 réplicas num serviço de pesquisa Básico. As réplicas podem ser ajustadas a partir do [portal Azure](search-create-service-portal.md) ou [powerShell](search-manage-powershell.md).

+ **Criar um novo serviço num nível mais alto**  

  A Azure Cognitive Search vem em [vários níveis](https://azure.microsoft.com/pricing/details/search/) e cada um oferece diferentes níveis de desempenho. Em alguns casos, pode ter tantas consultas que o nível em que está não pode fornecer uma reviravolta suficiente, mesmo quando as réplicas são esgotadas. Neste caso, considere passar para um nível de desempenho mais elevado, como o nível Standard S3, projetado para cenários com um grande número de documentos e cargas de trabalho de consulta extremamente elevadas.

## <a name="scale-for-slow-individual-queries"></a>Escala para consultas individuais lentas

Outra razão para taxas elevadas de latência é uma consulta única que demora demasiado tempo a ser completada. Neste caso, adicionar réplicas não vai ajudar. Duas opções possíveis que podem ajudar a incluir o seguinte:

+ **Aumentar as divisórias**

  Uma partição divide dados através de recursos de computação extra. Duas divisórias dividem dados ao meio, uma terceira divisória divide-o em terços, e assim por diante. Um efeito colateral positivo é que as consultas mais lentas às vezes funcionam mais rapidamente devido à computação paralela. Temos notado a paralelização em consultas de baixa seletividade, tais como consultas que combinam com muitos documentos, ou facetas que fornecem contagens sobre um grande número de documentos. Uma vez que o cálculo significativo é necessário para marcar a relevância dos documentos, ou para contar o número de documentos, adicionar divisórias extras ajuda as consultas a completar mais rapidamente.  
   
  Pode haver um máximo de 12 divisórias no serviço de pesquisa Standard e 1 partição no serviço de pesquisa Basic. As divisórias podem ser ajustadas a partir do [portal Azure](search-create-service-portal.md) ou [powerShell](search-manage-powershell.md).

+ **Limite campos de alta cardinalidade**

  Um campo de alta cardinalidade é composto por um campo facetável ou filtrado que tem um número significativo de valores únicos e, como resultado, consome recursos significativos ao calcular resultados. Por exemplo, definir um campo de identificação ou descrição do produto como facetable/filtrado contaria como cardeal elevado, porque a maioria dos valores de documento para documento são únicos. Sempre que possível, limite o número de campos de alto escalão.

+ **Aumentar o nível de pesquisa**  

  Subir para um nível de Pesquisa Cognitiva Azure mais elevado pode ser outra forma de melhorar o desempenho de consultas lentas. Cada nível mais alto fornece CPUs mais rápidos e mais memória, ambos com um impacto positivo no desempenho da consulta.

## <a name="scale-for-availability"></a>Escala para disponibilidade

As réplicas não só ajudam a reduzir a latência da consulta, como também podem permitir uma elevada disponibilidade. Com uma única réplica, deverá esperar tempo de inatividade periódico devido a reboots de servidores após atualizações de software ou para outros eventos de manutenção que ocorram. Como resultado, é importante considerar se a sua aplicação requer alta disponibilidade de pesquisas (consultas) bem como de escritos (eventos de indexação). A Azure Cognitive Search oferece opções de SLA em todas as ofertas de pesquisa pagas com os seguintes atributos:

+ Duas réplicas para alta disponibilidade de cargas de trabalho apenas de leitura (consultas)

+ Três ou mais réplicas para uma elevada disponibilidade de cargas de trabalho de leitura-escrita (consultas e indexação)

Para mais detalhes sobre este problema, visite o Acordo de [Nível de Serviço de Pesquisa Cognitiva Azure.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)

Uma vez que as réplicas são cópias dos seus dados, ter múltiplas réplicas permite que a Azure Cognitive Search faça reboots e manutenção de máquinas contra uma réplica, enquanto a execução de consultas continua em outras réplicas. Por outro lado, se retirar as réplicas, incorrerá na degradação do desempenho da consulta, assumindo que as réplicas eram um recurso subutilado.

<a name="availability-zones"></a>

### <a name="availability-zones"></a>Zonas de Disponibilidade

[As Zonas de Disponibilidade](../availability-zones/az-overview.md) dividem os centros de dados de uma região em grupos de localização física distintos para fornecer alta disponibilidade, dentro da mesma região. Para pesquisa cognitiva, réplicas individuais são as unidades para atribuição de zona. Um serviço de pesquisa funciona dentro de uma região; suas réplicas funcionam em diferentes zonas.

Pode utilizar Zonas de Disponibilidade com Pesquisa Cognitiva Azure adicionando duas ou mais réplicas ao seu serviço de pesquisa. Cada réplica será colocada numa zona de disponibilidade diferente dentro da região. Se tiver mais réplicas do que Zonas de Disponibilidade, as réplicas serão distribuídas pelas Zonas de Disponibilidade da forma mais homogénea possível.

A Azure Cognitive Search suporta atualmente Zonas de Disponibilidade para serviços de pesquisa standard ou de pesquisa superior que foram criados numa das seguintes regiões:

+ Austrália Oriental (criada a 30 de janeiro de 2021 ou mais tarde)
+ Canadá Central (criado a 30 de janeiro de 2021 ou mais tarde)
+ Central DOS EUA (criado a 4 de dezembro de 2020 ou mais tarde)
+ Leste dos EUA (criado a 27 de janeiro de 2021 ou mais tarde)
+ East US 2 (criado a 30 de janeiro de 2021 ou mais tarde)
+ France Central (criada a 23 de outubro de 2020 ou mais tarde)
+ Japão Oriental (criado a 30 de janeiro de 2021 ou mais tarde)
+ Norte da Europa (criado em 28 de janeiro de 2021 ou mais tarde)
+ Sudeste Asiático (criado em 31 de janeiro de 2021 ou mais tarde)
+ Reino Unido Sul (criado a 30 de janeiro de 2021 ou mais tarde)
+ Europa Ocidental (criada em 29 de janeiro de 2021 ou mais tarde)
+ West US 2 (criado a 30 de janeiro de 2021 ou mais tarde)

As Zonas de Disponibilidade não afetam o Acordo de [Nível de Serviço de Pesquisa Cognitiva Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Ainda precisa de 3 ou mais réplicas para consulta de alta disponibilidade.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Escala para cargas de trabalho geo-distribuídas e geo-redundância

Para cargas de trabalho geo-distribuídas, os utilizadores que estão localizados longe do centro de dados do anfitrião terão taxas de latência mais elevadas. Uma das atenuações é a prestação de múltiplos serviços de pesquisa em regiões com maior proximidade com estes utilizadores.

A Azure Cognitive Search não fornece atualmente um método automatizado de geo-replicação de índices de Pesquisa Cognitiva Azure em todas as regiões, mas existem algumas técnicas que podem ser usadas que podem tornar este processo simples de implementar e gerir. Estes estão delineados nas próximas secções.

O objetivo de um conjunto geo-distribuído de serviços de pesquisa é ter dois ou mais índices disponíveis em duas ou mais regiões, onde um utilizador é encaminhado para o serviço de Pesquisa Cognitiva Azure que fornece a latência mais baixa como visto neste exemplo:

   ![Separador transversal de serviços por região][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Mantenha os dados sincronizados em vários serviços

Existem duas opções para manter os seus serviços de pesquisa distribuídos em sincronização, que consistem em utilizar o Índice de [Pesquisa Cognitiva Azure](search-indexer-overview.md) ou a API push (também referido como [API de Pesquisa Cognitiva Azure).](/rest/api/searchservice/)  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Utilize indexantes para atualizar conteúdos em vários serviços

Se já estiver a utilizar indexante num serviço, pode configurar um segundo indexante num segundo serviço para utilizar o mesmo objeto de origem de dados, retirando dados do mesmo local. Cada serviço em cada região tem o seu próprio indexante e um índice-alvo (o seu índice de pesquisa não é partilhado, o que significa que os dados são duplicados), mas cada indexante refere a mesma fonte de dados.

Aqui está um visual de alto nível de como seria a arquitetura.

   ![Fonte única de dados com indexação distribuída e combinações de serviços][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Use APIs REST para empurrar atualizações de conteúdo em vários serviços

Se estiver a utilizar a Azure Cognitive Search REST API para [empurrar conteúdos no seu índice de Pesquisa Cognitiva Azure,](/rest/api/searchservice/update-index)pode manter os seus vários serviços de pesquisa sincronizados, empurrando alterações em todos os serviços de pesquisa sempre que for necessária uma atualização. No seu código, certifique-se de lidar com casos em que uma atualização de um serviço de pesquisa falha, mas que sucede a outros serviços de pesquisa.

## <a name="leverage-azure-traffic-manager"></a>Alavancagem Azure Traffic Manager

[O Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) permite-lhe encaminhar pedidos para vários sites geo-localizados que são depois apoiados por múltiplos serviços de pesquisa. Uma vantagem do Gestor de Tráfego é que pode sondar a Azure Cognitive Search para garantir que está disponível e encaminhar os utilizadores para serviços de pesquisa alternativos em caso de inatividade. Além disso, se estiver a encaminhar pedidos de pesquisa através dos Web Sites Azure, o Azure Traffic Manager permite-lhe carregar casos de equilíbrio em que o Website está em cima, mas não a Azure Cognitive Search. Aqui está um exemplo do que a arquitetura que alavanca o Gestor de Tráfego.

   ![Separador transversal de serviços por região, com central de Tráfego Manager][3]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os limites de preços e serviços para cada um, consulte [os limites de Serviço.](search-limits-quotas-capacity.md) Consulte [o Plano de Capacidade](search-capacity-planning.md) para saber mais sobre as combinações de divisórias e réplicas.

Para uma discussão sobre desempenho e demonstrações das técnicas discutidas neste artigo, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png