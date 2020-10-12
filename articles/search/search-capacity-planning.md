---
title: Ajustar a capacidade de consulta e carga de trabalho de índice
titleSuffix: Azure Cognitive Search
description: Ajuste os recursos de partilha e réplica de computador na Pesquisa Cognitiva Azure, onde cada recurso é avaliado em unidades de pesquisa faturadas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 76084a9ddd6842194bb4c6b25d62e62c2ed2d4a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660306"
---
# <a name="adjust-the-capacity-of-an-azure-cognitive-search-service"></a>Ajuste a capacidade de um serviço de Pesquisa Cognitiva Azure

Antes [de fornecer um serviço de pesquisa](search-create-service-portal.md) e bloquear um nível de preços específico, desloque alguns minutos para entender como funciona a capacidade e como pode ajustar réplicas e divisórias para acomodar a flutuação da carga de trabalho.

A capacidade é uma função do [nível que escolhes](search-sku-tier.md) (os níveis determinam as características do hardware) e a combinação de replicação e partição necessária para cargas de trabalho projetadas. Pode aumentar ou diminuir o número de réplicas ou divisórias individualmente. Dependendo do nível e do tamanho do ajuste, a adição ou redução da capacidade pode demorar entre 15 minutos e várias horas.

Ao modificar a atribuição de réplicas e divisórias, recomendamos a utilização do portal Azure. O portal impõe limites às combinações admissíveis que se mantêm abaixo dos limites máximos de um nível. No entanto, se necessitar de uma abordagem de provisionamento baseada em scripts ou códigos, o [Azure PowerShell](search-manage-powershell.md) ou a [API Management REST](/rest/api/searchmanagement/services) são soluções alternativas.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Conceitos: unidades de pesquisa, réplicas, divisórias, fragmentos

A capacidade é expressa em *unidades de busca* que podem ser atribuídas em combinações de *divisórias* e *réplicas,* utilizando um mecanismo *de fragmentos* subjacente para suportar configurações flexíveis:

| Conceito  | Definição|
|----------|-----------|
|*Unidade de pesquisa* | Um único incremento da capacidade total disponível (36 unidades). É também a unidade de faturação de um serviço de Pesquisa Cognitiva Azure. É necessário um mínimo de uma unidade para executar o serviço.|
|*Réplica* | Casos do serviço de pesquisa, usado principalmente para carregar operações de consulta de equilíbrio. Cada réplica acolhe uma cópia de um índice. Se atribuir três réplicas, terá três cópias de um índice disponível para pedidos de consulta.|
|*Partição* | Armazenamento físico e E/S para operações de leitura/escrita (por exemplo, ao reconstruir ou refrescar um índice). Cada divisória tem uma fatia do índice total. Se atribuir três divisórias, o seu índice é dividido em terços. |
|*Fragmento* | Um pedaço de um índice. A Azure Cognitive Search divide cada índice em fragmentos para tornar o processo de adição de divisórias mais rápido (movendo fragmentos para novas unidades de pesquisa).|

O diagrama seguinte mostra a relação entre réplicas, divisórias, fragmentos e unidades de busca. Mostra um exemplo de como um único índice é distribuído por quatro unidades de pesquisa num serviço com duas réplicas e duas divisórias. Cada uma das quatro unidades de pesquisa armazena apenas metade dos fragmentos do índice. As unidades de busca na coluna esquerda armazenam a primeira metade dos fragmentos, compreendendo a primeira divisória, enquanto as da coluna direita armazenam a segunda metade dos fragmentos, compreendendo a segunda divisória. Como há duas réplicas, há duas cópias de cada fragmento de índice. As unidades de pesquisa na primeira fila armazenam uma cópia, compreendendo a primeira réplica, enquanto as da linha inferior armazenam outra cópia, compreendendo a segunda réplica.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Os índices de pesquisa são fragmentos através de divisórias.&quot;:::

O diagrama acima é apenas um exemplo. Muitas combinações de divisórias e réplicas são possíveis, até um máximo de 36 unidades de pesquisa totais.

Na Pesquisa Cognitiva, a gestão de fragmentos é um detalhe de implementação e não configurável, mas saber que um índice é fragmento ajuda a entender as anomalias ocasionais no ranking e comportamentos autocompletos:

+ Anomalias no ranking: As pontuações de pesquisa são calculadas primeiro no nível de fragmento e, em seguida, agregadas num único conjunto de resultados. Dependendo das características do conteúdo de fragmentos, os jogos de um fragmento podem ser classificados acima dos fósforos em outro. Se notar rankings não intuitivos nos resultados da pesquisa, é mais provável que seja devido aos efeitos do fragmento, especialmente se os índices forem pequenos. Pode evitar estas anomalias no ranking, optando por [calcular pontuações globais em todo o índice](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), mas fazê-lo incorrerá numa penalidade de desempenho.

+ Anomalias autocompletas: Consultas autocompletas, onde os jogos são feitos nos primeiros vários caracteres de um termo parcialmente introduzido, aceitam um parâmetro difuso que perdoa pequenos desvios na ortografia. Para o autocompleto, a correspondência difusa é limitada aos termos dentro do fragmento atual. Por exemplo, se um fragmento contiver &quot;Microsoft&quot; e um termo parcial de &quot;micor&quot; for introduzido, o motor de busca corresponderá à &quot;Microsoft" nesse fragmento, mas não em outros fragmentos que mantenham as partes restantes do índice.

## <a name="when-to-add-nodes"></a>Quando adicionar os nódes

Inicialmente, é atribuído um serviço a um nível mínimo de recursos constituído por uma divisória e uma réplica.

Um único serviço deve ter recursos suficientes para lidar com todas as cargas de trabalho (indexação e consultas). Nenhuma carga de trabalho corre em segundo plano. Pode agendar a indexação para os horários em que os pedidos de consulta são naturalmente menos frequentes, mas o serviço não priorizará de outra forma uma tarefa em vez de outra. Além disso, uma certa quantidade de redundância suaviza o desempenho da consulta quando os serviços ou nós são atualizados internamente.

Regra geral, as aplicações de pesquisa tendem a necessitar de mais réplicas do que divisórias, especialmente quando as operações de serviço são tendenciosas para consultas de carga de trabalho. A secção sobre [alta disponibilidade](#HA) explica porquê.

> [!NOTE]
> Adicionar mais réplicas ou divisórias aumenta o custo de execução do serviço, e pode introduzir ligeiras variações na forma como os resultados são encomendados. Certifique-se de verificar a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para entender as implicações de faturação de adicionar mais nós. O [gráfico abaixo](#chart) pode ajudá-lo a cruzar o número de unidades de pesquisa necessárias para uma configuração específica. Para obter mais informações sobre como as réplicas adicionais impactam o processamento, consulte [os resultados da encomenda](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Como alocar réplicas e divisórias

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.

1. Em **Definições,** abra a página **Escala** para modificar réplicas e divisórias. 

   A imagem que se segue mostra um serviço padrão a ser prestado com uma réplica e partição. A fórmula na parte inferior indica quantas unidades de busca estão a ser utilizadas (1). Se o preço unitário fosse de 100 dólares (não um preço real), o custo mensal de funcionamento deste serviço seria de 100 dólares, em média.

   ![Página de escala mostrando valores atuais](media/search-capacity-planning/1-initial-values.png "Página de escala mostrando valores atuais")

1. Utilize o deslizador para aumentar ou diminuir o número de divisórias. A fórmula na parte inferior indica quantas unidades de busca estão a ser utilizadas.

   Este exemplo duplica a capacidade, com duas réplicas e divisórias cada. Note a contagem da unidade de pesquisa; são agora quatro porque a fórmula de faturação é réplica multiplicada por divisórias (2 x 2). Duplicar a capacidade mais do que duplica o custo de funcionamento do serviço. Se o custo da unidade de pesquisa fosse $100, a nova conta mensal seria agora $400.

   Para os custos atuais por unidade de cada nível, visite [a página de Preços](https://azure.microsoft.com/pricing/details/search/).

   ![Adicionar réplicas e divisórias](media/search-capacity-planning/2-add-2-each.png "Adicionar réplicas e divisórias")

1. **Selecione Guardar** para confirmar as alterações.

   ![Confirmar alterações na escala e faturação](media/search-capacity-planning/3-save-confirm.png "Confirmar alterações na escala e faturação")

   As mudanças de capacidade demoram várias horas a ser concluídas. Não é possível cancelar uma vez iniciado o processo e não existe monitorização em tempo real para ajustes de replicação e partição. No entanto, a seguinte mensagem permanece visível enquanto as alterações estão em curso.

   ![Mensagem de estado no portal](media/search-capacity-planning/4-updating.png "Mensagem de estado no portal")

> [!NOTE]
> Depois de um serviço ser prestado, não pode ser atualizado para um nível mais elevado. Tem de criar um serviço de pesquisa no novo nível e recarregar os seus índices. Consulte [Criar um serviço de Pesquisa Cognitiva Azure no portal](search-create-service-portal.md) para obter ajuda na prestação de serviços.
>
> Além disso, as divisórias e réplicas são geridas exclusiva e internamente pelo serviço. Não existe nenhum conceito de afinidade do processador, ou atribuir uma carga de trabalho a um nó específico.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Combinações de partição e réplica

Um serviço básico pode ter exatamente uma divisória e até três réplicas, para um limite máximo de três SUs. O único recurso ajustável são réplicas. Você precisa de um mínimo de duas réplicas para alta disponibilidade em consultas.

Todos os serviços de pesquisa Standard e Storage Optimized podem assumir as seguintes combinações de réplicas e divisórias, sujeitas ao limite de 36 SU. 

|   | **1 partição** | **2 partições** | **3 divisórias** | **4 divisórias** | **6 divisórias** | **12 divisórias** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 réplicas** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 réplicas** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 réplicas** |4 SU |8 SU |12 SU |16 SU |24 SU |N/D |
| **5 réplicas** |5 SU |10 SU |15 SU |20 SU |30 SU |N/D |
| **6 réplicas** |6 SU |12 SU |18 SU |24 SU |36 SU |N/D |
| **12 réplicas** |12 SU |24 SU |36 SU |N/D |N/D |N/D |

As SUs, os preços e a capacidade são explicados em detalhe no site da Azure. Para mais informações, consulte [detalhes de preços.](https://azure.microsoft.com/pricing/details/search/)

> [!NOTE]
> O número de réplicas e divisórias divide-se uniformemente em 12 (especificamente, 1, 2, 3, 4, 6, 12). Isto porque a Azure Cognitive Search pré-divide cada índice em 12 fragmentos para que possa ser espalhado em partes iguais em todas as divisórias. Por exemplo, se o seu serviço tiver três divisórias e criar um índice, cada divisória conterá quatro fragmentos do índice. Como a Azure Cognitive Search fragmentos de um índice é um detalhe de implementação, sujeito a alterações em futuras versões. Embora o número seja 12 hoje, não deve esperar que esse número seja sempre 12 no futuro.
>

<a id="HA"></a>

## <a name="high-availability"></a>Elevada disponibilidade

Como é fácil e relativamente rápido escalar, geralmente recomendamos que comece com uma partição e uma ou duas réplicas, e depois aumente à medida que os volumes de consulta se constroem. As cargas de trabalho de consulta funcionam principalmente em réplicas. Se precisar de mais produção ou alta disponibilidade, provavelmente necessitará de réplicas adicionais.

As recomendações gerais para a elevada disponibilidade são:

* Duas réplicas para alta disponibilidade de cargas de trabalho apenas de leitura (consultas)

* Três ou mais réplicas para uma elevada disponibilidade de cargas de trabalho de leitura/escrita (consultas mais indexação à medida que documentos individuais são adicionados, atualizados ou eliminados)

Os acordos de nível de serviço (SLA) para a Azure Cognitive Search são direcionados para operações de consulta e em atualizações de índices que consistem em adicionar, atualizar ou eliminar documentos.

O nível básico supera com uma divisória e três réplicas. Se quiser que a flexibilidade responda imediatamente às flutuações da procura tanto para indexar como para a produção de consultas, considere um dos níveis Standard.  Se descobrir que os seus requisitos de armazenamento estão a crescer muito mais rapidamente do que a sua produção de consulta, considere um dos níveis otimizados de armazenamento.

## <a name="disaster-recovery"></a>Recuperação após desastre

Atualmente, não existe um mecanismo integrado para a recuperação de desastres. Adicionar divisórias ou réplicas seria a estratégia errada para cumprir os objetivos de recuperação de desastres. A abordagem mais comum é adicionar redundância ao nível do serviço através da criação de um segundo serviço de pesquisa noutra região. Tal como acontece com a disponibilidade durante uma reconstrução de índice, a lógica de reorientação ou falha deve vir do seu código.

## <a name="estimate-replicas"></a>Estimativa de réplicas

Num serviço de produção, deverá alocar três réplicas para fins SLA. Se experimentar um desempenho de consulta lenta, pode adicionar réplicas para que cópias adicionais do índice sejam trazidas on-line para suportar cargas de trabalho de consulta maiores e para carregar os pedidos sobre as múltiplas réplicas.

Não fornecemos diretrizes sobre quantas réplicas são necessárias para acomodar cargas de consulta. O desempenho da consulta depende da complexidade da consulta e das cargas de trabalho concorrentes. Embora a adição de réplicas resulte claramente num melhor desempenho, o resultado não é estritamente linear: adicionar três réplicas não garante o triplo rendimento.

Para obter orientações na estimativa de QPS para a sua solução, consulte [Scale para consultas](search-performance-optimization.md)de desempenho e [monitor](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Estimativa de divisórias

O [nível que escolhe](search-sku-tier.md) determina o tamanho e a velocidade da partição, e cada nível é otimizado em torno de um conjunto de características que se encaixam em vários cenários. Se escolher um nível superior, poderá precisar de menos divisórias do que se for com S1. Uma das perguntas que terá de responder através de testes auto-dirigidos é se uma partição maior e mais cara produz melhor desempenho do que duas divisórias mais baratas num serviço prestado num nível mais baixo.

As aplicações de pesquisa que requerem uma atualização de dados em tempo real precisarão proporcionalmente mais de divisórias do que réplicas. A adição de divisórias espalha operações de leitura/escrita através de um maior número de recursos computacional. Também lhe dá mais espaço em disco para armazenar índices e documentos adicionais.

Índices maiores demoram mais tempo a consultar. Como tal, pode descobrir que cada aumento incremental de divisórias requer um aumento menor, mas proporcional, de réplicas. A complexidade das suas consultas e volume de consultas terá em conta a rapidez com que a execução de consultas é virada.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escolha um nível de preços para Azure Cognitive Search](search-sku-tier.md)