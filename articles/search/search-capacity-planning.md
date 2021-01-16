---
title: Capacidade de estimativa para consultas e cargas de trabalho de índice
titleSuffix: Azure Cognitive Search
description: Ajuste os recursos de partilha e réplica de computador na Pesquisa Cognitiva Azure, onde cada recurso é avaliado em unidades de pesquisa faturadas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 4a9a6b61e392ed2efd68cdcb1cf7e53d6bde5ccd
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249734"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>Estimativa e gestão da capacidade de um serviço de Pesquisa Cognitiva Azure

Antes [de fornecer um serviço de pesquisa](search-create-service-portal.md) e bloquear um nível de preços específico, desloque alguns minutos para entender como funciona a capacidade e como pode ajustar réplicas e divisórias para acomodar a flutuação da carga de trabalho.

A capacidade é uma função do nível de [serviço.](search-sku-tier.md) Os níveis são diferenciados pelo armazenamento máximo, armazenamento por partição e os limites máximos no número de objetos que pode criar. O nível Básico é projetado para apps com requisitos de armazenamento modestos (apenas uma partição), mas com a capacidade de executar numa configuração de alta disponibilidade (3 réplicas). Outros níveis são projetados para cargas de trabalho ou padrões específicos, como a multitenência. Internamente, os serviços criados nesses níveis beneficiam de hardware que ajuda nesses cenários.

A arquitetura de escalabilidade em Azure Cognitive Search baseia-se em combinações flexíveis de réplicas e divisórias para que possa variar a capacidade dependendo se você precisa de mais consulta ou potência de indexação. Uma vez criado um serviço, pode aumentar ou diminuir o número de réplicas ou divisórias de forma independente. Os custos aumentarão com cada recurso físico adicional, mas uma vez terminadas as grandes cargas de trabalho, pode reduzir a escala para baixar a sua fatura. Dependendo do nível e do tamanho do ajuste, a adição ou redução da capacidade pode demorar entre 15 minutos e várias horas.

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

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Os índices de pesquisa são fragmentos através de divisórias.":::

O diagrama acima é apenas um exemplo. Muitas combinações de divisórias e réplicas são possíveis, até um máximo de 36 unidades de pesquisa totais.

Na Pesquisa Cognitiva, a gestão de fragmentos é um detalhe de implementação e não configurável, mas saber que um índice é fragmento ajuda a entender as anomalias ocasionais no ranking e comportamentos autocompletos:

+ Anomalias no ranking: As pontuações de pesquisa são calculadas primeiro no nível de fragmento e, em seguida, agregadas num único conjunto de resultados. Dependendo das características do conteúdo de fragmentos, os jogos de um fragmento podem ser classificados acima dos fósforos em outro. Se notar rankings contraintuitivos nos resultados da pesquisa, é mais provável que seja devido aos efeitos do fragmento, especialmente se os índices forem pequenos. Pode evitar estas anomalias no ranking, optando por [calcular pontuações globais em todo o índice](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), mas fazê-lo incorrerá numa penalidade de desempenho.

+ Anomalias autocompletas: Consultas autocompletas, onde os jogos são feitos nos primeiros vários caracteres de um termo parcialmente introduzido, aceitam um parâmetro difuso que perdoa pequenos desvios na ortografia. Para o autocompleto, a correspondência difusa é limitada aos termos dentro do fragmento atual. Por exemplo, se um fragmento contiver "Microsoft" e um termo parcial de "micor" for introduzido, o motor de busca corresponderá à "Microsoft" nesse fragmento, mas não em outros fragmentos que mantenham as partes restantes do índice.

## <a name="how-to-evaluate-capacity-requirements"></a>Como avaliar os requisitos de capacidade

A capacidade e os custos de funcionamento do serviço andam de mãos dadas. Os níveis impõem limites a dois níveis: armazenamento e conteúdo (uma contagem de índices num serviço, por exemplo). É importante considerar ambos porque o limite que se atinge primeiro é o limite efetivo.

Quantidades de índices e outros objetos são tipicamente ditadas por requisitos de negócio e engenharia. Por exemplo, pode ter várias versões do mesmo índice para desenvolvimento ativo, testes e produção.

As necessidades de armazenamento são determinadas pelo tamanho dos índices que espera construir. Não há heurística sólida ou generalidades que ajudem com estimativas. A única maneira de determinar o tamanho de um índice é [construir um.](search-what-is-an-index.md) O seu tamanho basear-se-á em dados importados, análise de texto e configuração de índices, tais como se permite sugestivos, filtragem e triagem.

Para a pesquisa completa de texto, a estrutura de dados primários é uma estrutura [de índice invertida,](https://en.wikipedia.org/wiki/Inverted_index) que tem características diferentes dos dados de origem. Para um índice invertido, o tamanho e a complexidade são determinados pelo conteúdo, não necessariamente pela quantidade de dados que se alimenta nele. Uma grande fonte de dados com alta redundância poderia resultar num índice menor do que um conjunto de dados menor que contém conteúdo altamente variável. Portanto, raramente é possível inferir o tamanho do índice com base no tamanho do conjunto de dados original.

> [!NOTE] 
> Mesmo estimando necessidades futuras de índices e armazenamento pode parecer adivinhação, vale a pena fazê-lo. Se a capacidade de um nível se revelar demasiado baixa, terá de providenciar um novo serviço a um nível mais elevado e, em seguida, [recarregar os seus índices](search-howto-reindex.md). Não há um upgrade no lugar de um serviço de um nível para outro.
>

### <a name="estimate-with-the-free-tier"></a>Estimativa com o nível Livre

Uma abordagem para estimar a capacidade é começar com o nível Livre. Lembre-se que o serviço Gratuito oferece até três índices, 50 MB de armazenamento e 2 minutos de tempo de indexação. Pode ser um desafio estimar um tamanho de índice projetado com estes constrangimentos, mas estes são os passos:

+ [Crie um serviço gratuito.](search-create-service-portal.md)
+ Prepare um conjunto de dados pequeno e representativo.
+ Crie um índice e carregue os seus dados. Se o conjunto de dados puder ser hospedado numa fonte de dados Azure suportada por indexadores, pode utilizar o [assistente de dados De importação no portal](search-get-started-portal.md) para criar e carregar o índice. Caso contrário, deverá utilizar REST e [Postman](search-get-started-rest.md) ou [Visual Studio Code](search-get-started-vs-code.md) para criar o índice e empurrar os dados. O modelo push requer que os dados sejam sob a forma de documentos JSON, onde os campos no documento correspondem aos campos do índice.
+ Recolher informações sobre o índice, como tamanho. Funcionalidades e atributos têm impacto no armazenamento. Por exemplo, adicionar sugestivos (consultas de pesquisa como você do tipo) aumentará os requisitos de armazenamento. 

  Utilizando o mesmo conjunto de dados, pode tentar criar várias versões de um índice, com diferentes atributos em cada campo, para ver como os requisitos de armazenamento variam. Para obter mais informações, consulte ["Implicações de armazenamento" na Criação de um índice básico.](search-what-is-an-index.md#index-size)

Com uma estimativa aproximada na mão, você pode duplicar esse montante para orçamento para dois índices (desenvolvimento e produção) e, em seguida, escolher o seu nível em conformidade.

### <a name="estimate-with-a-billable-tier"></a>Estimativa com um nível faturada

Os recursos dedicados podem acomodar tempos de amostragem e processamento maiores para estimativas mais realistas de volumes de quantidade, tamanho e consulta do índice durante o desenvolvimento. Alguns clientes saltam para dentro com um nível fatural e depois reavaliam à medida que o projeto de desenvolvimento amadurece.

1. [Reveja os limites de serviço em cada nível](./search-limits-quotas-capacity.md#index-limits) para determinar se os níveis mais baixos podem suportar o número de índices de que necessita. Nos escalões Básico, S1 e S2, os limites de índice são 15, 50 e 200, respectivamente. O nível otimizado de armazenamento tem um limite de 10 índices porque é projetado para suportar um número baixo de índices muito grandes.

1. [Criar um serviço num nível de faturação:](search-create-service-portal.md)

    + Comece baixo, no Basic ou S1, se não tiver certeza sobre a carga projetada.
    + Comece alto, no S2 ou mesmo no S3, se os testes incluirem cargas de indexação e consulta em larga escala.
    + Comece com o Storage Otimizado, em L1 ou L2, se estiver a indexar uma grande quantidade de dados e a carga de consulta é relativamente baixa, como acontece com uma aplicação de negócio interna.

1. [Construa um índice inicial](search-what-is-an-index.md) para determinar como os dados de origem se traduzem num índice. Esta é a única maneira de estimar o tamanho do índice.

1. [Monitorize o armazenamento, os limites de serviço, o volume de consulta e](search-monitor-usage.md) a latência no portal. O portal mostra-lhe consultas por segundo, consultas estranguladas e latência de pesquisa. Todos estes valores podem ajudá-lo a decidir se selecionou o nível certo.

1. Adicione réplicas se precisar de alta disponibilidade ou se experimentar um desempenho de consulta lenta.

   Não existem diretrizes sobre quantas réplicas são necessárias para acomodar cargas de consulta. O desempenho da consulta depende da complexidade da consulta e das cargas de trabalho concorrentes. Embora a adição de réplicas resulte claramente num melhor desempenho, o resultado não é estritamente linear: adicionar três réplicas não garante o triplo rendimento. Para obter orientações para estimar o QPS para a sua solução, consulte [Scale para obter](search-performance-optimization.md)consultas de desempenho e [monitor](search-monitor-queries.md).

> [!NOTE]
> Os requisitos de armazenamento podem ser inflacionados se incluir dados que nunca serão pesquisados. Idealmente, os documentos contêm apenas os dados de que necessita para a experiência de pesquisa. Os dados binários não são pesmáveis e devem ser armazenados separadamente (talvez numa mesa Azure ou armazenamento de bolhas). Um campo deve então ser adicionado no índice para manter uma referência URL aos dados externos. O tamanho máximo de um documento de pesquisa individual é de 16 MB (ou menos se estiver a carregar vários documentos num único pedido). Para obter mais informações, consulte [os limites de serviço na Pesquisa Cognitiva Azure.](search-limits-quotas-capacity.md)
>

**Considerações de volume de consulta**

Consultas por segundo (QPS) é uma métrica importante durante a afinação do desempenho, mas geralmente é apenas uma consideração de nível se você espera um alto volume de consulta no início.

Os níveis Standard podem fornecer um equilíbrio de réplicas e divisórias. Pode aumentar a reviravolta da consulta adicionando réplicas para equilibrar a carga ou adicionar divisórias para processamento paralelo. Em seguida, pode sintonizar para desempenho após o serviço ser prestado.

Se espera volumes de consultas elevados e sustentados desde o início, deve considerar níveis standard mais elevados, apoiados por hardware mais potente. Em seguida, pode tirar divisórias e réplicas offline, ou até mesmo mudar para um serviço de nível inferior, se esses volumes de consulta não ocorrerem. Para obter mais informações sobre como calcular o rendimento da consulta, consulte [o desempenho e otimização da Pesquisa Cognitiva Azure.](search-performance-optimization.md)

Os níveis otimizados de armazenamento são úteis para grandes cargas de trabalho de dados, suportando um armazenamento de índice mais disponível para quando os requisitos de latência de consulta são menos importantes. Deve ainda utilizar réplicas adicionais para equilibrar cargas e divisórias adicionais para processamento paralelo. Em seguida, pode sintonizar para desempenho após o serviço ser prestado.

**Acordos de nível de serviço**

As funcionalidades de nível gratuito e de pré-visualização não fornecem [acordos de nível de serviço (SLAs)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todos os níveis de faturação, as SLAs fazem efeito quando fornece redundância suficiente para o seu serviço. Precisa de duas ou mais réplicas para consulta (ler) SLAs. Precisa de três ou mais réplicas para consulta e indexação (ler-escrever) SLAs. O número de divisórias não afeta as AEA.

## <a name="tips-for-capacity-planning"></a>Dicas para planeamento de capacidades

+ Permitir que as métricas construam em torno de consultas e recolham dados em torno dos padrões de utilização (consultas durante o horário comercial, indexação durante as horas de ponta). Utilize estes dados para informar as decisões de prestação de serviços. Embora não seja prático a uma cadência horária ou diária, pode ajustar dinamicamente as divisórias e recursos para acomodar as mudanças planeadas nos volumes de consulta. Também pode acomodar alterações não planeadas mas sustentadas se os níveis aguentarem o tempo suficiente para justificar a tomada de medidas.

+ Lembre-se que a única desvantagem do fornecimento é que você pode ter que demolir um serviço se os requisitos reais são maiores do que as suas previsões. Para evitar perturbações no serviço, criaria um novo serviço num nível mais alto e executá-lo-ia lado a lado até que todas as aplicações e pedidos direcionem o novo ponto final.

## <a name="when-to-add-partitions-and-replicas"></a>Quando adicionar divisórias e réplicas

Inicialmente, é atribuído um serviço a um nível mínimo de recursos constituído por uma divisória e uma réplica.

Um único serviço deve ter recursos suficientes para lidar com todas as cargas de trabalho (indexação e consultas). Nenhuma carga de trabalho corre em segundo plano. Pode agendar a indexação para os horários em que os pedidos de consulta são naturalmente menos frequentes, mas o serviço não priorizará de outra forma uma tarefa em vez de outra. Além disso, uma certa quantidade de redundância suaviza o desempenho da consulta quando os serviços ou nós são atualizados internamente.

Regra geral, as aplicações de pesquisa tendem a necessitar de mais réplicas do que divisórias, especialmente quando as operações de serviço são tendenciosas para consultas de carga de trabalho. A secção sobre [alta disponibilidade](#HA) explica porquê.

O [nível que escolhe](search-sku-tier.md) determina o tamanho e a velocidade da partição, e cada nível é otimizado em torno de um conjunto de características que se encaixam em vários cenários. Se escolher um nível superior, poderá precisar de menos divisórias do que se for com S1. Uma das perguntas que terá de responder através de testes auto-dirigidos é se uma partição maior e mais cara produz melhor desempenho do que duas divisórias mais baratas num serviço prestado num nível mais baixo.

As aplicações de pesquisa que requerem uma atualização de dados em tempo real precisarão proporcionalmente mais de divisórias do que réplicas. A adição de divisórias espalha operações de leitura/escrita através de um maior número de recursos computacional. Também lhe dá mais espaço em disco para armazenar índices e documentos adicionais.

Índices maiores demoram mais tempo a consultar. Como tal, pode descobrir que cada aumento incremental de divisórias requer um aumento menor, mas proporcional, de réplicas. A complexidade das suas consultas e volume de consultas terá em conta a rapidez com que a execução de consultas é virada.

> [!NOTE]
> Adicionar mais réplicas ou divisórias aumenta o custo de execução do serviço, e pode introduzir ligeiras variações na forma como os resultados são encomendados. Certifique-se de verificar a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para entender as implicações de faturação de adicionar mais nós. O [gráfico abaixo](#chart) pode ajudá-lo a cruzar o número de unidades de pesquisa necessárias para uma configuração específica. Para obter mais informações sobre como as réplicas adicionais impactam o processamento, consulte [os resultados da encomenda](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Como alocar réplicas e divisórias

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.

1. Em **Definições,** abra a página **Escala** para modificar réplicas e divisórias. 

   A imagem que se segue mostra uma Norma Básica a provisionada com uma réplica e partição. A fórmula na parte inferior indica quantas unidades de busca estão a ser utilizadas (1). Se o preço unitário fosse de 100 dólares (não um preço real), o custo mensal de funcionamento deste serviço seria de 100 dólares, em média.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Página de escala mostrando valores atuais" border="true":::

1. Utilize o deslizador para aumentar ou diminuir o número de divisórias. A fórmula na parte inferior indica quantas unidades de busca estão a ser utilizadas. Selecione **Guardar**.

   Este exemplo adiciona uma segunda réplica e partição. Note a contagem da unidade de pesquisa; são agora quatro porque a fórmula de faturação é réplica multiplicada por divisórias (2 x 2). Duplicar a capacidade mais do que duplica o custo de funcionamento do serviço. Se o custo da unidade de pesquisa fosse $100, a nova conta mensal seria agora $400.

   Para os custos atuais por unidade de cada nível, visite [a página de Preços](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Adicionar réplicas e divisórias" border="true":::

1. Depois de poupar, pode verificar notificações para confirmar que a ação foi bem sucedida.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Guardar alterações" border="true":::

   As mudanças de capacidade podem demorar até várias horas para ser concluída. Não é possível cancelar uma vez iniciado o processo e não existe monitorização em tempo real para ajustes de replicação e partição. No entanto, a seguinte mensagem permanece visível enquanto as alterações estão em curso.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Mensagem de estado no portal" border="true":::

> [!NOTE]
> Depois de um serviço ser prestado, não pode ser atualizado para um nível mais elevado. Tem de criar um serviço de pesquisa no novo nível e recarregar os seus índices. Consulte [Criar um serviço de Pesquisa Cognitiva Azure no portal](search-create-service-portal.md) para obter ajuda na prestação de serviços.
>
> Além disso, as divisórias e réplicas são geridas exclusiva e internamente pelo serviço. Não existe nenhum conceito de afinidade do processador, ou atribuir uma carga de trabalho a um nó específico.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Combinações de partição e réplica

Um serviço básico pode ter exatamente uma divisória e até três réplicas, para um limite máximo de três SUs. O único recurso ajustável são réplicas. Você precisa de um mínimo de duas réplicas para alta disponibilidade em consultas.

Todos os serviços de pesquisa Standard e Storage Optimized podem assumir as seguintes combinações de réplicas e divisórias, sujeitas ao limite de 36 SU permitido para estes níveis. 

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

+ Duas réplicas para alta disponibilidade de cargas de trabalho apenas de leitura (consultas)

+ Três ou mais réplicas para uma elevada disponibilidade de cargas de trabalho de leitura/escrita (consultas mais indexação à medida que documentos individuais são adicionados, atualizados ou eliminados)

Os acordos de nível de serviço (SLA) para a Azure Cognitive Search são direcionados para operações de consulta e em atualizações de índices que consistem em adicionar, atualizar ou eliminar documentos.

O nível básico supera com uma divisória e três réplicas. Se quiser que a flexibilidade responda imediatamente às flutuações da procura tanto para indexar como para a produção de consultas, considere um dos níveis Standard.  Se descobrir que os seus requisitos de armazenamento estão a crescer muito mais rapidamente do que a sua produção de consulta, considere um dos níveis otimizados de armazenamento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como estimar e gerir custos](search-sku-manage-costs.md)