---
title: Disponibilidade e continuidade
titleSuffix: Azure Cognitive Search
description: aprender a tornar um serviço de pesquisa altamente disponível e resiliente contra perturbações de período ou mesmo falhas catastróficas.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 493f6759f63f023572f38647076e04425acf9d6a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581534"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Disponibilidade e continuidade de negócios na Pesquisa Cognitiva Azure

Na Pesquisa Cognitiva, a disponibilidade é conseguida através de múltiplas réplicas, enquanto a continuidade do negócio (e recuperação de desastres) é conseguida através de múltiplos serviços de pesquisa. Este artigo fornece orientações que pode usar como ponto de partida para desenvolver uma estratégia que satisfaça os requisitos do seu negócio tanto para a disponibilidade como para operações contínuas.

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>Elevada disponibilidade

Na Pesquisa Cognitiva, réplicas são cópias do seu índice. Ter múltiplas réplicas permite que a Azure Cognitive Search faça reboots e manutenção de máquinas contra uma réplica, enquanto a execução de consulta continua em outras réplicas. Para obter mais informações sobre a adição de réplicas, consulte [Adicionar ou reduzir réplicas e divisórias.](search-capacity-planning.md#adjust-capacity)

Para cada serviço de pesquisa individual, a Microsoft garante pelo menos 99,9% de disponibilidade para configurações que satisfaçam estes critérios: 

+ Duas réplicas para alta disponibilidade de cargas de trabalho apenas de leitura (consultas)

+ Três ou mais réplicas para uma elevada disponibilidade de cargas de trabalho de leitura-escrita (consultas e indexação) 

Não está previsto nenhum SLA para o nível Livre. Para obter mais informações, consulte [sLA para pesquisa cognitiva Azure.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)

<a name="availability-zones"></a>

## <a name="availability-zones"></a>Zonas de Disponibilidade

[As Zonas de Disponibilidade](../availability-zones/az-overview.md) são uma capacidade de plataforma Azure que divide os centros de dados de uma região em grupos de localização física distintos para fornecer alta disponibilidade, dentro da mesma região. Se utilizar Zonas de Disponibilidade para Pesquisa Cognitiva, réplicas individuais são as unidades para atribuição de zona. Um serviço de pesquisa funciona dentro de uma região; suas réplicas funcionam em diferentes zonas.

Pode utilizar Zonas de Disponibilidade com Pesquisa Cognitiva Azure adicionando duas ou mais réplicas ao seu serviço de pesquisa. Cada réplica será colocada numa zona de disponibilidade diferente dentro da região. Se tiver mais réplicas do que Zonas de Disponibilidade, as réplicas serão distribuídas pelas Zonas de Disponibilidade da forma mais homogénea possível. Não existe nenhuma ação específica da sua parte, exceto para [criar um serviço de pesquisa](search-create-service-portal.md) numa região que fornece Zonas de Disponibilidade, e depois para configurar o serviço para usar [várias réplicas.](search-capacity-planning.md#adjust-capacity)

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

## <a name="multiple-services-in-separate-geographic-regions"></a>Múltiplos serviços em regiões geográficas separadas

Embora a maioria dos clientes utilize apenas um serviço, a redundância de serviços pode ser necessária se os requisitos operacionais incluírem o seguinte:

+ [Continuidade do negócio e recuperação de desastres (BCDR)](../best-practices-availability-paired-regions.md) (A Pesquisa Cognitiva não proporciona falhas instantâneas em caso de paragem).
+ Aplicações implantadas globalmente. Se os pedidos de consulta e indexação vierem de todo o mundo, os utilizadores mais próximos do centro de dados do anfitrião terão um desempenho mais rápido. A criação de serviços adicionais em regiões com proximidade a estes utilizadores pode igualar o desempenho de todos os utilizadores.
+ [As arquiteturas multi-arrendatários](search-modeling-multitenant-saas-applications.md) às vezes exigem dois ou mais serviços.

Se precisar de mais dois serviços de pesquisa, criá-los em diferentes regiões pode satisfazer os requisitos de aplicação para continuidade e recuperação, bem como tempos de resposta mais rápidos para uma base de utilizadores global.

A Azure Cognitive Search não fornece atualmente um método automatizado de geo-replicação de índices de pesquisa em todas as regiões, mas existem algumas técnicas que podem ser usadas que podem tornar este processo simples de implementar e gerir. Estes estão delineados nas próximas secções.

O objetivo de um conjunto geo-distribuído de serviços de pesquisa é ter dois ou mais índices disponíveis em duas ou mais regiões, onde um utilizador é encaminhado para o serviço de Pesquisa Cognitiva Azure que fornece a latência mais baixa:

   ![Separador transversal de serviços por região][1]

Pode implementar esta arquitetura criando múltiplos serviços e projetando uma estratégia para a sincronização de dados. Opcionalmente, pode incluir um recurso como O Gestor de Tráfego Azure para pedidos de encaminhamento. Para mais informações, consulte [Criar um serviço de pesquisa.](search-create-service-portal.md)

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>Mantenha os dados sincronizados em vários serviços

Existem duas opções para manter dois ou mais serviços de pesquisa distribuídos em sincronização, que consistem em utilizar o Índice de [Pesquisa Cognitiva Azure](search-indexer-overview.md) ou a API push (também referido como [API de Pesquisa Cognitiva Azure).](/rest/api/searchservice/) 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>Opção 1: Utilizar indexadores para atualizar conteúdos em vários serviços

Se já estiver a utilizar indexante num serviço, pode configurar um segundo indexante num segundo serviço para utilizar o mesmo objeto de origem de dados, retirando dados do mesmo local. Cada serviço em cada região tem o seu próprio indexante e um índice-alvo (o seu índice de pesquisa não é partilhado, o que significa que os dados são duplicados), mas cada indexante refere a mesma fonte de dados.

Aqui está um visual de alto nível de como seria a arquitetura.

   ![Fonte única de dados com indexação distribuída e combinações de serviços][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Opção 2: Utilize APIs REST para impulsionar atualizações de conteúdo em vários serviços

Se estiver a utilizar a Azure Cognitive Search REST API para [empurrar o conteúdo para o seu índice de pesquisa,](tutorial-optimize-indexing-push-api.md)pode manter os seus vários serviços de pesquisa sincronizados, empurrando alterações em todos os serviços de pesquisa sempre que for necessária uma atualização. No seu código, certifique-se de lidar com casos em que uma atualização de um serviço de pesquisa falha, mas que sucede a outros serviços de pesquisa.

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>Use o Gestor de Tráfego Azure para coordenar pedidos

[O Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) permite-lhe encaminhar pedidos para vários sites geo-localizados que são depois apoiados por múltiplos serviços de pesquisa. Uma vantagem do Gestor de Tráfego é que pode sondar a Azure Cognitive Search para garantir que está disponível e encaminhar os utilizadores para serviços de pesquisa alternativos em caso de inatividade. Além disso, se estiver a encaminhar pedidos de pesquisa através dos Web Sites Azure, o Azure Traffic Manager permite-lhe carregar casos de equilíbrio em que o Website está em cima, mas não a Azure Cognitive Search. Aqui está um exemplo do que a arquitetura que alavanca o Gestor de Tráfego.

   ![Separador transversal de serviços por região, com central de Tráfego Manager][3]

## <a name="disaster-recovery-and-service-outages"></a>Recuperação de desastres e interrupções de serviço

Embora possamos resgatar os seus dados, a Azure Cognitive Search não fornece falha instantânea do serviço se houver uma falha no nível do cluster ou do centro de dados. Se um cluster falhar no centro de dados, a equipa de operações detetará e trabalhará para restaurar o serviço. Você vai experimentar tempo de paragem durante a restauração do serviço, mas você pode solicitar créditos de serviço para compensar a indisponibilidade de serviço de acordo com o [Acordo de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se for necessário um serviço contínuo em caso de falhas catastróficas fora do controlo da Microsoft, poderá [providenciar um serviço adicional](search-create-service-portal.md) numa região diferente e implementar uma estratégia de geo-replicação para garantir que os índices são totalmente redundantes em todos os serviços.

Os clientes que usam [indexantes](search-indexer-overview.md) para preencher e atualizar índices podem lidar com a recuperação de desastres através de indexadores geo-específicos alavancando a mesma fonte de dados. Dois serviços em diferentes regiões, cada um com um indexante, poderiam indexar a mesma fonte de dados para alcançar a geo-redundância. Se estiver a indexar a partir de fontes de dados que também são geo-redundantes, esteja ciente de que os indexantes de Pesquisa Cognitiva do Azure só podem realizar indexação incremental (fundindo atualizações de documentos novos, modificados ou eliminados) a partir de réplicas primárias. Num evento de failover, certifique-se de voltar a apontar o indexante para a nova réplica primária. 

Se não utilizar indexadores, utilizará o seu código de aplicação para empurrar objetos e dados para diferentes serviços de pesquisa em paralelo. Para obter mais informações, consulte [Manter os dados sincronizados em vários serviços.](#data-sync)

## <a name="back-up-and-restore-alternatives"></a>Apoiar e restaurar alternativas

Como a Azure Cognitive Search não é uma solução primária de armazenamento de dados, a Microsoft não fornece um mecanismo formal de autosserviço de volta e restauro. No entanto, pode utilizar o código de amostra **de backup-backup** neste repo [de amostra Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) para fazer backup da sua definição de índice e instantâneo para uma série de ficheiros JSON e, em seguida, usar estes ficheiros para restaurar o índice, se necessário. Esta ferramenta também pode mover índices entre os níveis de serviço.

Caso contrário, o seu código de aplicação utilizado para criar e povoar um índice é a opção de restauro de facto se eliminar um índice por engano. Para reconstruir um índice, apagar-se-ia (assumindo que existe), recriaria o índice no serviço e recarregava-se recuperando dados da sua loja de dados primário.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os limites de preços e serviços para cada um, consulte [os limites de Serviço.](search-limits-quotas-capacity.md) Consulte [o Plano de Capacidade](search-capacity-planning.md) para saber mais sobre as combinações de divisórias e réplicas.

Para uma discussão sobre desempenho e demonstrações das técnicas discutidas neste artigo, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png