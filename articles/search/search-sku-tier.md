---
title: Escolha um tipo de preço ou SKU durante o provisionamento do serviço de pesquisa
titleSuffix: Azure Cognitive Search
description: 'Os Pesquisa Cognitiva do Azure podem ser provisionados nestes SKUs: gratuito, básico e Standard, e Standard está disponível em várias configurações de recursos e níveis de capacidade.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 249ce8e51ff61bac5d418d9e173ec2caed24e0e5
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794260"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Escolha um tipo de preço para o Azure Pesquisa Cognitiva

Quando você cria um serviço de Pesquisa Cognitiva do Azure, um [recurso é criado](search-create-service-portal.md) em um tipo de preço (ou SKU) que é corrigido durante o tempo de vida do serviço. As camadas incluem gratuito, básico, Standard e otimizado para armazenamento. O padrão e o armazenamento otimizado estão disponíveis com várias configurações e capacidades.

A maioria dos clientes começa com a camada gratuita para que eles possam avaliar o serviço. Após a avaliação, é comum criar um segundo serviço em uma das camadas mais altas para implantações de desenvolvimento e produção.

Embora todas as camadas, incluindo a camada gratuita, geralmente ofereçam paridade de recursos, cargas de trabalho maiores podem determinar a necessidade de camadas mais altas. Por exemplo, o [enriquecimento de ia](cognitive-search-concept-intro.md) tem habilidades de longa execução que atingirão o tempo limite em um serviço gratuito, a menos que o conjunto de informações seja pequeno.

> [!NOTE] 
> A exceção à paridade de recursos é [indexadores](search-indexer-overview.md), que não estão disponíveis em S3 HD.
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>Camadas disponíveis

As camadas refletem as características do hardware que hospeda o serviço (em vez de recursos) e são diferenciadas por:

+ Quantidade de índices e indexadores que você pode criar
+ Tamanho e velocidade das partições (armazenamento físico)

A camada selecionada determina a taxa faturável. A captura de tela a seguir de portal do Azure mostra as camadas disponíveis, menos preços (que você pode encontrar no portal e na [página de preços](https://azure.microsoft.com/pricing/details/search/). **Gratuita**, **básica**e **Standard** são as camadas mais comuns.

**Gratuito** cria um serviço de pesquisa limitado em um cluster, compartilhado com outros assinantes. Você pode concluir projetos pequenos, incluindo guias de início rápido e tutoriais, mas não pode dimensionar o serviço ou executar cargas de trabalho significativas. **Básico** e **Standard** são as camadas faturáveis usadas com mais frequência, com **padrão** sendo o padrão.

![Tipos de preço do Azure Pesquisa Cognitiva](media/search-sku-tier/tiers.png "Tipos de preço do Azure Pesquisa Cognitiva")

Algumas camadas são otimizadas para determinados tipos de trabalho. Por exemplo, a **alta densidade padrão (S3 HD)** é um *modo de hospedagem* para S3, em que o hardware subjacente é otimizado para um grande número de índices menores e destina-se a cenários de multilocação. S3 HD tem o mesmo encargo por unidade que S3, mas o hardware é otimizado para leituras rápidas de arquivos em um grande número de índices menores.

As camadas de **armazenamento otimizado** oferecem maior capacidade de armazenamento a um preço menor por TB do que as camadas padrão. A compensação primária é maior latência de consulta, que você deve validar para os requisitos de aplicativo específicos.  Para saber mais sobre as considerações de desempenho dessa camada, consulte [Considerações sobre desempenho e otimização](search-performance-optimization.md).

Você pode saber mais sobre as várias camadas na [página de preços](https://azure.microsoft.com/pricing/details/search/), no artigo limites de [serviço no Azure pesquisa cognitiva](search-limits-quotas-capacity.md) e na página do portal quando estiver provisionando um serviço.

## <a name="billable-events"></a>Eventos faturáveis

Uma solução criada no Azure Pesquisa Cognitiva pode gerar custos das seguintes maneiras:

+ Custo base do serviço na configuração mínima (criar um serviço)
+ Custo incremental ao escalar verticalmente (adicionar réplicas ou partições)
+ Encargos de largura de banda (transferência de dados de saída) 
+ Pesquisa cognitiva (anexe serviços cognitivas para enriquecimento de ia, armazenamento do Azure para armazenamento de conhecimento)

### <a name="service-costs"></a>Custos de serviço

Ao contrário das máquinas virtuais ou outros recursos que podem ser "pausados" para evitar cobranças, um serviço de Pesquisa Cognitiva do Azure está sempre disponível em hardware dedicado para seu uso exclusivo. Dessa forma, a criação de um serviço é um evento cobrável que começa quando você cria o serviço e termina quando você exclui o serviço. 

O encargo mínimo é a primeira unidade de pesquisa (uma réplica x uma partição) na taxa faturável. Esse mínimo é fixo durante o tempo de vida do serviço, pois o serviço não pode ser executado em nada menor que essa configuração. Além do mínimo, você pode adicionar réplicas e partições independentemente umas das outras. Aumentos incrementais na capacidade por meio de réplicas e partições aumentarão sua fatura com base na fórmula a seguir: [(réplicas x partições x taxa)](#search-units), em que a taxa cobrada depende do tipo de preço selecionado.

Quando estiver estimando o custo de uma solução de pesquisa, tenha em mente que os preços e a capacidade não são lineares. (Dobrar a capacidade mais do que o dobro do custo.) Para obter um exemplo de como a fórmula funciona, consulte [como alocar réplicas e partições](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Cobranças de largura de banda

O uso de [indexadores do Azure pesquisa cognitiva](search-indexer-overview.md) pode afetar a cobrança, dependendo do local de seus serviços. Você pode eliminar os encargos de saída de dados inteiramente se criar o serviço de Pesquisa Cognitiva do Azure na mesma região que seus dados. Aqui estão algumas informações da [página de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/):

+ A Microsoft não cobra por nenhum dado de entrada para nenhum serviço no Azure, ou para qualquer dado de saída do Azure Pesquisa Cognitiva.
+ Em soluções de multiatendimento, não há nenhum encargo para dados que cruzam a conexão quando todos os serviços estão na mesma região.

Os encargos se aplicam a dados de saída se os serviços estiverem em regiões diferentes. Esses encargos não são, na verdade, parte da sua conta de Pesquisa Cognitiva do Azure. Eles são mencionados aqui porque, se você estiver usando os dados ou indexadores aprimorados de ia para efetuar pull de dados de regiões diferentes, verá os custos refletidos na sua fatura geral.

### <a name="ai-enrichment-with-cognitive-services"></a>Enriquecimento de ia com serviços cognitivas

Para [aprimorar o ia](cognitive-search-concept-intro.md), você deve planejar [anexar um recurso de serviços cognitivas do Azure cobrável](cognitive-search-attach-cognitive-services.md), na mesma região que o Azure pesquisa cognitiva, no tipo de preço S0 para processamento pago conforme o uso. Não há nenhum custo fixo associado à anexação de serviços cognitivas. Você paga apenas pelo processamento de que precisa.

| Operação | Impacto de cobrança |
|-----------|----------------|
| Quebra de documentos, extração de texto | Gratuito |
| Quebra de documento, extração de imagem | Cobrado de acordo com o número de imagens extraídas dos seus documentos. Em uma [configuração de indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageaction** é o parâmetro que dispara a extração de imagem. Se **imageaction** for definido como "None" (o padrão), você não será cobrado pela extração de imagem. A taxa de extração de imagem está documentada na página de [detalhes de preços](https://azure.microsoft.com/pricing/details/search/) do Azure pesquisa cognitiva.|
| [Habilidades cognitivas internas](cognitive-search-predefined-skills.md) | Cobrado na mesma taxa que se você executou a tarefa usando serviços cognitivas diretamente. |
| Competências personalizadas | Uma habilidade personalizada é A funcionalidade que você fornece. O custo de usar uma habilidade personalizada depende totalmente de se o código personalizado está chamando outros serviços medidos. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Fórmula de cobrança (R x P = SU)

O conceito de cobrança mais importante a ser compreendido para as operações de Pesquisa Cognitiva do Azure é a Su ( *unidade de pesquisa* ). Como o Azure Pesquisa Cognitiva depende de réplicas e partições para indexação e consultas, não faz sentido cobrar por apenas um ou outro. Em vez disso, a cobrança é baseada em uma composição de ambos.

SU é o produto das *réplicas* e *partições* usadas por um serviço: **(R x P = su)** .

Cada serviço começa com uma SU (uma réplica multiplicada por uma partição) como o mínimo. O máximo de qualquer serviço é o SUs 36. Esse máximo pode ser alcançado de várias maneiras: 6 partições x 6 réplicas ou 3 partições x 12 réplicas, por exemplo. É comum usar a capacidade menor que o total (por exemplo, um serviço de 3 réplicas, de três partições cobrado como 9 SUs). Consulte o gráfico de [combinações de partição e réplica](search-capacity-planning.md#chart) para obter combinações válidas.

A taxa de cobrança é por hora por SU. Cada camada tem uma taxa progressivamente maior. As camadas mais altas vêm com partições maiores e speediers, e isso contribui para uma taxa geral maior por hora para essa camada. Você pode exibir as taxas de cada camada na página de [detalhes de preços](https://azure.microsoft.com/pricing/details/search/) .

A maioria dos clientes traz apenas uma parte da capacidade total online, mantendo o restante em reserva. Para cobrança, o número de partições e réplicas que você coloca online, calculadas pela fórmula SU, determina o que você paga por hora.

## <a name="how-to-manage-and-reduce-costs"></a>Como gerenciar e reduzir custos

Além das seguintes sugestões, visite [cobrança e gerenciamento de custos](https://docs.microsoft.com/azure/billing/billing-getting-started).

- Crie todos os recursos na mesma região, ou no menor número de regiões possível, para minimizar ou eliminar encargos de largura de banda.

- Consolide todos os serviços em um grupo de recursos, como o Azure Pesquisa Cognitiva, serviços cognitivas e quaisquer outros serviços do Azure usados em sua solução. No portal do Azure, encontre o grupo de recursos e use os comandos de **Gerenciamento de custos** para obter informações sobre gastos reais e projetados.

- Considere o aplicativo Web do Azure para seu aplicativo front-end para que as solicitações e respostas permaneçam dentro do limite de data center.

- Escalar verticalmente para operações com uso intensivo de recursos, como indexação, e reajustar verticalmente para cargas de trabalho de consulta regular. Comece com a configuração mínima do Azure Pesquisa Cognitiva (uma SU composta de uma partição e uma réplica) e, em seguida, monitore a atividade do usuário para identificar os padrões de uso que indicariam a necessidade de mais capacidade. Se houver um padrão previsível, você poderá sincronizar a escala com a atividade (você precisaria escrever código para automatizar isso).

Não é possível desligar um serviço de pesquisa para reduzir sua fatura. Os recursos dedicados estão sempre operacionais, alocados para seu uso exclusivo durante o tempo de vida do seu serviço. Em termos do próprio serviço, a única maneira de reduzir sua conta é reduzir as réplicas e partições para um nível que ainda fornece desempenho aceitável e [conformidade de SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), ou criar um serviço em uma camada mais baixa (as tarifas de S1 por hora são menores do que as taxas S2 ou S3). Supondo que você provisione seu serviço na extremidade inferior de suas projeções de carga, se você ultrapassar o serviço, poderá criar um segundo serviço em camadas maior, recriar os índices no segundo serviço e, em seguida, excluir o primeiro.

## <a name="how-to-evaluate-capacity-requirements"></a>Como avaliar os requisitos de capacidade

No Azure Pesquisa Cognitiva, a capacidade é estruturada como *réplicas* e *partições*.

+ Réplicas são instâncias do serviço de pesquisa. Cada réplica hospeda uma cópia com balanceamento de carga de um índice. Por exemplo, um serviço com seis réplicas tem seis cópias de cada índice carregado no serviço.

+ As partições armazenam índices e dividem automaticamente os dados pesquisáveis. Duas partições dividem o índice na metade, três partições as dividem em terços e assim por diante. Em termos de capacidade, o *tamanho da partição* é o principal recurso de diferenciação entre camadas.

> [!NOTE]
> Todas as camadas padrão e de armazenamento otimizadas dão suporte a [combinações flexíveis de réplicas e partições](search-capacity-planning.md#chart) para que você possa [otimizar seu sistema para velocidade ou armazenamento](search-performance-optimization.md) alterando o equilíbrio. A camada básica oferece até três réplicas para alta disponibilidade, mas tem apenas uma partição. As camadas gratuitas não fornecem recursos dedicados: os recursos de computação são compartilhados por vários assinantes.

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>Avaliando a capacidade

A capacidade e os custos de executar o serviço são disponibilizados em mãos. As camadas impõem limites em dois níveis: armazenamento e recursos. Você deve pensar em ambos porque qualquer limite que atingir primeiro é o limite efetivo.

Os requisitos de negócios normalmente ditam o número de índices que você precisará. Por exemplo, você pode precisar de um índice global para um repositório de documentos grande. Ou talvez você precise de vários índices com base na região, no aplicativo ou no nicho de negócios.

Para determinar o tamanho de um índice, você precisa [criar um](search-create-index-portal.md). A estrutura de dados no Azure Pesquisa Cognitiva é principalmente uma estrutura de [índice invertida](https://en.wikipedia.org/wiki/Inverted_index) , que tem características diferentes dos dados de origem. Para um índice invertido, o tamanho e a complexidade são determinados pelo conteúdo, não necessariamente pela quantidade de dados que você feedu nele. Uma fonte de dados grande com alta redundância pode resultar em um índice menor do que um conjunto de dado menor que contém conteúdo altamente variável. Portanto, raramente é possível inferir o tamanho do índice com base no tamanho do conjunto de datas original.

> [!NOTE] 
> Mesmo que a estimativa de necessidades futuras de índices e armazenamento possa parecer uma tarefa de adivinhação, vale a pena fazer isso. Se a capacidade de uma camada for muito baixa, você precisará provisionar um novo serviço em uma camada mais alta e, em seguida, [recarregar os índices](search-howto-reindex.md). Não há nenhuma atualização in-loco de um serviço de uma SKU para outra.
>

### <a name="estimate-with-the-free-tier"></a>Estimar com a camada gratuita

Uma abordagem para estimar a capacidade é começar com a camada gratuita. Lembre-se de que o serviço gratuito oferece até três índices, 50 MB de armazenamento e 2 minutos de tempo de indexação. Pode ser desafiador estimar um tamanho de índice projetado com essas restrições, mas estas são as etapas:

+ [Crie um serviço gratuito](search-create-service-portal.md).
+ Prepare um DataSet pequeno e representativo.
+ [Crie um índice inicial no portal](search-create-index-portal.md) e anote seu tamanho. Os recursos e atributos têm um impacto no armazenamento. Por exemplo, adicionar sugestores (typeahead) aumentará os requisitos de armazenamento. Usando o mesmo conjunto de dados, você pode tentar criar várias versões de um índice, com atributos diferentes em cada campo, para ver como os requisitos de armazenamento variam. Para obter mais informações, consulte ["implicações de armazenamento" em criar um índice básico](search-what-is-an-index.md#storage-implications).

Com uma estimativa aproximada em mãos, você pode dobrar esse valor para o orçamento de dois índices (desenvolvimento e produção) e, em seguida, escolher a camada adequadamente.

### <a name="estimate-with-a-billable-tier"></a>Estimar com uma camada Faturável

Os recursos dedicados podem acomodar grandes períodos de amostragem e processamento para estimativas mais realistas de quantidade de índice, tamanho e volumes de consulta durante o desenvolvimento. Alguns clientes saltam diretamente com uma camada Faturável e, em seguida, reavaliam à medida que o projeto de desenvolvimento amadurece.

1. [Examine os limites de serviço em cada camada](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) para determinar se as camadas inferiores podem dar suporte ao número de índices de que você precisa. Nas camadas básica, S1 e S2, os limites de índice são 15, 50 e 200, respectivamente. A camada de armazenamento otimizado tem um limite de 10 índices porque ele foi projetado para dar suporte a um número baixo de índices muito grandes.

1. [Criar um serviço em uma camada Faturável](search-create-service-portal.md):

    + Comece de baixo, em básico ou S1, se você não tiver certeza sobre a carga projetada.
    + Comece de alto, em S2 ou mesmo S3, se você souber que vai ter carregamentos de consulta e indexação em larga escala.
    + Comece com o armazenamento otimizado, em L1 ou L2, se você estiver indexando uma grande quantidade de dados e a carga de consulta for relativamente baixa, assim como com um aplicativo de negócios interno.

1. [Crie um índice inicial](search-create-index-portal.md) para determinar como os dados de origem são convertidos em um índice. Essa é a única maneira de estimar o tamanho do índice.

1. [Monitore o armazenamento, os limites de serviço, o volume de consulta e a latência](search-monitor-usage.md) no Portal. O portal mostra as consultas por segundo, as consultas limitadas e a latência de pesquisa. Todos esses valores podem ajudá-lo a decidir se selecionou a camada certa. 

O número de índice e o tamanho são igualmente importantes para sua análise. Isso ocorre porque os limites máximos são atingidos por meio da utilização total de armazenamento (partições) ou por limites máximos de recursos (índices, indexadores e assim por diante), o que vier primeiro. O portal ajuda você a manter o controle de ambos, mostrando o uso atual e os limites máximos lado a lado na página Visão geral.

> [!NOTE]
> Os requisitos de armazenamento podem ser inalterados se os documentos contiverem dados estranhos. O ideal é que os documentos contenham apenas os dados de que você precisa para a experiência de pesquisa. Os dados binários não são pesquisáveis e devem ser armazenados separadamente (talvez em um armazenamento de BLOBs ou de tabelas do Azure). Em seguida, um campo deve ser adicionado no índice para manter uma referência de URL aos dados externos. O tamanho máximo de um documento individual é de 16 MB (ou menos se você estiver carregando vários documentos em massa em uma solicitação). Para obter mais informações, consulte [limites de serviço no Azure pesquisa cognitiva](search-limits-quotas-capacity.md).
>

**Considerações de volume de consulta**

Consultas por segundo (QPS) é uma métrica importante durante o ajuste de desempenho, mas geralmente é apenas uma consideração de camada se você espera um alto volume de consulta no início.

As camadas padrão podem fornecer um equilíbrio entre réplicas e partições. Você pode aumentar o retorno da consulta adicionando réplicas para balanceamento de carga ou adicionar partições para processamento paralelo. Em seguida, você pode ajustar o desempenho depois que o serviço for provisionado.

Se você espera grandes volumes de consulta sustentados desde o início, considere as camadas mais altas Standard, apoiadas por hardware mais potente. Você pode pegar partições e réplicas offline ou até mesmo alternar para um serviço de camada inferior, se esses volumes de consulta não ocorrerem. Para obter mais informações sobre como calcular a taxa de transferência de consulta, consulte [desempenho e otimização do Azure pesquisa cognitiva](search-performance-optimization.md).

As camadas de armazenamento otimizadas são úteis para cargas de trabalho de dados grandes, dando suporte a armazenamento de índice mais geral disponível para quando os requisitos de latência de consulta são menos importantes. Você ainda deve usar réplicas adicionais para balanceamento de carga e partições adicionais para processamento paralelo. Em seguida, você pode ajustar o desempenho depois que o serviço for provisionado.

**Contratos de nível de serviço**

Os recursos de camada gratuita e visualização não fornecem [contratos de nível de serviço (SLAs)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todas as camadas faturáveis, os SLAs entram em vigor quando você provisiona redundância suficiente para seu serviço. Você precisa ter duas ou mais réplicas para SLAs de consulta (leitura). Você precisa ter três ou mais réplicas para os SLAs de consulta e indexação (leitura/gravação). O número de partições não afeta os SLAs.

## <a name="tips-for-tier-evaluation"></a>Dicas para avaliação de camada

+ Permitir que as métricas sejam compiladas em consultas e coletar dados sobre padrões de uso (consultas durante o horário comercial, indexação fora do horário de pico). Use esses dados para informar as decisões de provisionamento do serviço. Embora não seja prático em uma cadência de hora ou diária, você pode ajustar dinamicamente as partições e os recursos para acomodar as alterações planejadas nos volumes de consulta. Você também pode acomodar alterações não planejadas, mas sustentadas, se os níveis tiverem tempo suficiente para garantir a tomada de ações.

+ Lembre-se de que a única desvantagem de subprovisionamento é que você pode precisar subdividir um serviço se os requisitos reais forem maiores do que suas previsões. Para evitar a interrupção do serviço, você deve criar um novo serviço na mesma assinatura em uma camada superior e executá-lo lado a lado até que todos os aplicativos e solicitações tenham como destino o novo ponto de extremidade.

## <a name="next-steps"></a>Passos seguintes

Comece com uma camada gratuita e crie um índice inicial usando um subconjunto de seus dados para entender suas características. A estrutura de dados no Azure Pesquisa Cognitiva é uma estrutura de índice invertida. O tamanho e a complexidade de um índice invertido são determinados pelo conteúdo. Lembre-se de que o conteúdo altamente redundante tende a resultar em um índice menor do que conteúdo altamente irregular. Portanto, as características de conteúdo, em vez do tamanho do conjunto de os, determinam os requisitos de armazenamento de índice

Depois de ter uma estimativa inicial do tamanho do índice, [provisione um serviço Faturável](search-create-service-portal.md) em uma das camadas discutidas neste artigo: básico, Standard ou armazenamento otimizado. Relaxe quaisquer restrições artificiais no dimensionamento de dados e [recompile o índice](search-howto-reindex.md) para incluir todos os dados que você deseja que sejam pesquisáveis.

[Aloque partições e réplicas](search-capacity-planning.md) conforme necessário para obter o desempenho e a escala necessários.

Se o desempenho e a capacidade estiverem corretos, você estará pronto. Caso contrário, recrie um serviço de pesquisa em uma camada diferente que se alinhe melhor às suas necessidades.

> [!NOTE]
> Se você tiver dúvidas, poste no [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [entre em contato com o suporte do Azure](https://azure.microsoft.com/support/options/).
