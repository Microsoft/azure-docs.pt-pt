---
title: Escala para desempenho
titleSuffix: Azure Cognitive Search
description: Aprenda técnicas e boas práticas para afinar o desempenho da Pesquisa Cognitiva Azure e configurar a escala ideal.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212375"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Escala para desempenho na Pesquisa Cognitiva Azure

Este artigo descreve as melhores práticas para cenários avançados com requisitos sofisticados de escalabilidade e disponibilidade.

## <a name="start-with-baseline-numbers"></a>Comece com números de base

Antes de realizar um esforço de implantação maior, certifique-se de saber como é uma carga típica de consulta. As seguintes diretrizes podem ajudá-lo a chegar aos números de consulta de base.

1. Escolha uma latência-alvo (ou quantidade máxima de tempo) que um pedido típico de pesquisa deve levar a concluir.

1. Crie e teste uma carga de trabalho real contra o seu serviço de pesquisa com um conjunto de dados realista para medir estas taxas de latência.

1. Comece com um número reduzido de consultas por segundo (QPS) e, em seguida, aumente gradualmente o número executado no teste até que a latência da consulta caia abaixo do alvo predefinido. Esta é uma referência importante para ajudá-lo a planear a escala à medida que a sua aplicação cresce em uso.

1. Sempre que possível, reutilizar as ligações HTTP. Se estiver a utilizar o SDK de Pesquisa Cognitiva Azure.NET, isto significa que deve reutilizar uma instância ou instância [do SearchIndexClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) e se estiver a utilizar a API REST, deve reutilizar um único HttpClient.

1. Varie a substância dos pedidos de consulta para que a pesquisa ocorra em diferentes partes do seu índice. A variação é importante porque se executar continuamente os mesmos pedidos de pesquisa, o cache de dados começará a fazer o desempenho parecer melhor do que poderia com um conjunto de consulta mais díspare.

1. Varie a estrutura dos pedidos de consulta para que obtenha diferentes tipos de consultas. Nem todas as consultas de pesquisa realizam no mesmo nível. Por exemplo, uma sugestão de pesquisa de documentos ou de pesquisa é tipicamente mais rápida do que uma consulta com um número significativo de facetas e filtros. A composição do teste deve incluir várias consultas, aproximadamente nas mesmas proporções que seria de esperar na produção.  

Ao criar estas cargas de trabalho de teste, existem algumas características da Pesquisa Cognitiva Azure a ter em mente:

+ É possível sobrecarregar o seu serviço empurrando demasiadas consultas de pesquisa de uma só vez. Quando isto acontecer, verá códigos de resposta HTTP 503. Para evitar um 503 durante os testes, comece com várias gamas de pedidos de pesquisa para ver as diferenças nas taxas de latência à medida que adiciona mais pedidos de pesquisa.

+ A Pesquisa Cognitiva Azure não executa tarefas de indexação em segundo plano. Se o seu serviço lidar com consultas e indexar as cargas de trabalho em simultâneo, tenha isso em conta, introduzindo empregos indexantes nos seus testes de consulta, ou explorando opções para executar trabalhos de indexação durante as horas de ponta.

> [!Tip]
> Pode simular uma carga de consulta realista utilizando ferramentas de teste de carga. Experimente [os testes de carga com o Azure DevOps](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) ou utilize uma [destas alternativas](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives).

## <a name="scale-for-high-query-volume"></a>Escala para volume de consulta elevado

Um serviço é sobrecarregado quando as consultas demoram muito tempo ou quando o serviço começa a deixar cair pedidos. Se isso acontecer, pode resolver o problema de duas formas:

+ **Adicionar réplicas**  

  Cada réplica é uma cópia dos seus dados, permitindo ao serviço carregar pedidos de saldo contra várias cópias.  Todo o equilíbrio de carga e replicação de dados é gerido pela Azure Cognitive Search e pode alterar o número de réplicas atribuídas ao seu serviço a qualquer momento. Pode alocar até 12 réplicas num serviço de pesquisa Standard e 3 réplicas num serviço de pesquisa Basic. As réplicas podem ser ajustadas quer a partir do [portal Azure](search-create-service-portal.md) quer [da PowerShell](search-manage-powershell.md).

+ **Criar um novo serviço a um nível superior**  

  A Pesquisa Cognitiva Azure vem em [vários níveis](https://azure.microsoft.com/pricing/details/search/) e cada um oferece diferentes níveis de desempenho. Em alguns casos, você pode ter tantas consultas que o nível em que você está não pode fornecer reviravolta suficiente, mesmo quando as réplicas estão no limite. Neste caso, considere passar para um nível de desempenho mais elevado, como o nível Standard S3, projetado para cenários com um grande número de documentos e cargas de trabalho de consulta extremamente elevadas.

## <a name="scale-for-slow-individual-queries"></a>Escala para consultas individuais lentas

Outra razão para altas taxas de latência é uma única consulta que demora muito tempo a ser completada. Neste caso, adicionar réplicas não vai ajudar. Duas opções possíveis que podem ajudar incluem as seguintes:

+ **Aumentar divisórias**

  Uma partição divide dados através de recursos de computação extra. Duas divisórias dividem dados ao meio, uma terceira divisória divide-os em terços, e assim por diante. Um efeito colateral positivo é que as consultas mais lentas às vezes funcionam mais rapidamente devido à computação paralela. Temos notado paralelização em consultas de baixa seletividade, tais como consultas que correspondem a muitos documentos, ou facetas que fornecem contagens sobre um grande número de documentos. Uma vez que é necessário calcular significativamente a relevância dos documentos, ou contar os números de documentos, adicionar divisórias extra ajuda as consultas a completar-se mais rapidamente.  
   
  Pode haver um máximo de 12 divisórias no serviço de pesquisa Standard e 1 partição no serviço de pesquisa Basic. As divisórias podem ser ajustadas quer a partir do [portal Azure](search-create-service-portal.md) quer [da PowerShell](search-manage-powershell.md).

+ **Limite campos de cardinalidade**

  Um campo de alta cardinalidade consiste num campo facetável ou filtravel que tem um número significativo de valores únicos e, como resultado, consome recursos significativos quando a computação resulta. Por exemplo, definir um campo de identificação ou descrição do produto como facetable/filterable contaria como cardeal elevado porque a maioria dos valores de documento para documento são únicos. Sempre que possível, limite o número de campos de alto cardeal.

+ **Aumentar o Nível de Pesquisa**  

  Subir para um nível de pesquisa cognitiva azure mais alto pode ser outra forma de melhorar o desempenho de consultas lentas. Cada nível superior proporciona CPUs mais rápido e mais memória, ambos com um impacto positivo no desempenho da consulta.

## <a name="scale-for-availability"></a>Escala para disponibilidade

As réplicas não só ajudam a reduzir a latência de consultas, como também podem permitir uma alta disponibilidade. Com uma única réplica, deve esperar tempo de paragem periódica devido a reboots de servidorapós atualizações de software ou para outros eventos de manutenção que ocorram. Como resultado, é importante considerar se a sua aplicação requer uma elevada disponibilidade de pesquisas (consultas) bem como escritos (eventos de indexação). A Azure Cognitive Search oferece opções de SLA em todas as ofertas de pesquisa pagas com os seguintes atributos:

+ Duas réplicas para elevada disponibilidade de cargas de trabalho apenas para leitura (consultas)

+ Três ou mais réplicas para elevada disponibilidade de cargas de trabalho de leitura (consultas e indexação)

Para mais detalhes sobre este problema, visite o Acordo de [Nível de Nível de Serviço de Pesquisa Cognitiva Azure.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)

Uma vez que as réplicas são cópias dos seus dados, ter múltiplas réplicas permite que a Pesquisa Cognitiva Azure faça reboots de máquinas e manutenção contra uma réplica, enquanto a execução de consulta continua noutras réplicas. Por outro lado, se tirarréplicas, incorrerá na degradação do desempenho da consulta, assumindo que essas réplicas eram um recurso subutilizado.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Escala para cargas de trabalho geodistribuídas e georedundância

Para cargas de trabalho geodistribuídas, os utilizadores que estão localizados longe do centro de dados do anfitrião terão taxas de latência mais elevadas. Uma mitigação é a prestação de múltiplos serviços de pesquisa em regiões com maior proximidade a estes utilizadores.

A Azure Cognitive Search não fornece atualmente um método automatizado de geo-replicação dos índices de pesquisa cognitiva azure em todas as regiões, mas existem algumas técnicas que podem ser usadas que podem tornar este processo simples de implementar e gerir. Estes estão delineados nas próximas secções.

O objetivo de um conjunto geo-distribuído de serviços de pesquisa é ter dois ou mais índices disponíveis em duas ou mais regiões, onde um utilizador é encaminhado para o serviço de Pesquisa Cognitiva Azure que proporciona a menor latência como visto neste exemplo:

   ![Separadores cruzados de serviços por região][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Mantenha os dados sincronizados em vários serviços

Existem duas opções para manter os seus serviços de pesquisa distribuídos em sincronização, que consistem em utilizar o Indexante de [Pesquisa Cognitiva Azure](search-indexer-overview.md) ou a API Push (também referida como API de [Pesquisa Cognitiva Azure).](https://docs.microsoft.com/rest/api/searchservice/)  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Utilize indexadores para atualizar conteúdos em vários serviços

Se já estiver a utilizar o indexador num só serviço, pode configurar um segundo indexante num segundo serviço para utilizar o mesmo objeto de origem de dados, retirando dados da mesma localização. Cada serviço em cada região tem o seu próprio indexador e um índice-alvo (o seu índice de pesquisa não é partilhado, o que significa que os dados são duplicados), mas cada indexante refere a mesma fonte de dados.

Aqui está um visual de alto nível de como seria essa arquitetura.

   ![Fonte de dados única com indexador distribuído e combinações de serviços][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Use APIs REST para impulsionar atualizações de conteúdo em vários serviços

Se estiver a utilizar a API de pesquisa cognitiva Azure para pressionar o conteúdo no seu índice de [Pesquisa Cognitiva Azure,](https://docs.microsoft.com/rest/api/searchservice/update-index)pode manter os seus vários serviços de pesquisa sincronizados, empurrando alterações para todos os serviços de pesquisa sempre que for necessária uma atualização. No seu código, certifique-se de lidar com casos em que uma atualização de um serviço de pesquisa falha, mas que tenha sucesso para outros serviços de pesquisa.

## <a name="leverage-azure-traffic-manager"></a>Alavancar o Gestor de Tráfego Azure

[O Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) permite-lhe encaminhar pedidos para vários sites geolocalizados que são depois apoiados por vários serviços de pesquisa. Uma das vantagens do Gestor de Tráfego é que pode sondar a Pesquisa Cognitiva Azure para garantir que está disponível e encaminhar os utilizadores para serviços de pesquisa alternativos em caso de tempo de inatividade. Além disso, se estiver a encaminhar pedidos de pesquisa através de Web Sites Azure, o Gestor de Tráfego do Azure permite-lhe carregar casos de equilíbrio onde o Website está em cima, mas não a Pesquisa Cognitiva Azure. Aqui está um exemplo do que a arquitetura que aproveita o Traffic Manager.

   ![Separador transversal de serviços por região, com gerente central de tráfego][3]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os níveis de preços e os limites de serviços para cada um, consulte [os limites de serviço.](search-limits-quotas-capacity.md) Consulte o [Plano de Capacidade](search-capacity-planning.md) para saber mais sobre divisórias e combinações de réplicas.

Para uma discussão sobre o desempenho e demonstrações das técnicas discutidas neste artigo, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
