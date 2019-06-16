---
title: Escolha um escalão de preço ou SKU para o serviço Azure Search - Azure Search
description: 'O Azure Search pode ser aprovisionado nestes SKUs: Gratuito, básico e padrão e padrão estão disponível em várias configurações de recursos e níveis de capacidade.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 00422209302bbcc2139be4f6b490f0bb2816c051
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539253"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Escolha um escalão de preço para o Azure Search

Quando cria um serviço de Azure Search, um [recurso é criado](search-create-service-portal.md) num escalão de preço ou SKU é fixa para o tempo de vida do serviço. Os escalões incluem gratuito, básico, Standard e otimizadas para armazenamento. Padrão e otimizadas para armazenamento estão disponíveis com várias configurações e as capacidades.

A maioria dos clientes começam com o escalão gratuito, para que podem avaliar o serviço. Em seguida, atualize para um dos escalões superiores para implementações de desenvolvimento e produção. Pode concluir todos os inícios rápidos e tutoriais utilizando o escalão gratuito, incluindo aquelas para a pesquisa cognitiva com muitos recursos.

> [!NOTE]
> Atualmente a Microsoft está fornecendo os escalões de serviço com otimização de armazenamento numa visualização a um preço com desconto para teste e experimentação, com o objetivo de recolha de comentários. Os preços finais serão anunciado mais tarde quando estas camadas estão disponíveis em geral. Aconselhamos contra a utilização destes escalões para aplicações de produção.

Camadas de refletem as características de hardware que aloja o serviço (em vez de recursos) e são diferenciadas por:

+ O número de índices que pode criar.
+ O tamanho e a velocidade de partições (armazenamento físico).

Embora todos os escalões, incluindo o escalão gratuito, geralmente ofereçam paridade de funcionalidades, cargas de trabalho maiores podem ditar a necessidade de escalões superiores. Por exemplo, [IA indexação com os serviços cognitivos](cognitive-search-concept-intro.md) tem habilidades de longa execução desse tempo limite num serviço gratuito, a menos que o conjunto de dados é pequeno.

> [!NOTE] 
> A exceção a paridade de funcionalidades são [indexadores](search-indexer-overview.md), que não estão disponível no disco rígido de S3.
>

Dentro de uma camada, pode [ajustar os recursos de partição e réplica](search-capacity-planning.md) para aumentar ou diminuir a escala. Pode começar com um ou dois de cada e, em seguida, elevar temporariamente a sua capacidade computacional de uma pesada carga de trabalho de indexação. A capacidade de otimizar os níveis de recursos dentro de uma camada adiciona flexibilidade, mas ligeiramente também complica a sua análise. Poderá ter de experimentar para verificar se um escalão mais baixo com mais recursos/réplicas oferece melhor valor e desempenho do que um escalão mais elevado com menos recursos. Para saber mais sobre o quando e por que seria ajuste a capacidade, veja [considerações sobre desempenho e otimização](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Camadas para o Azure Search

A tabela seguinte lista as camadas disponíveis. Pode encontrar mais informações sobre os vários escalões no [página de preços](https://azure.microsoft.com/pricing/details/search/), na [limites de serviço do Azure Search](search-limits-quotas-capacity.md) artigo e no portal de página quando estiver a aprovisionar um serviço.

|Escalão | Capacidade |
|-----|-------------|
|Livre | Partilhado com outros subscritores. Não é escalonável. Limitado a três índices e a 50 MB de armazenamento. |
|Básica | Recursos de computação dedicados para cargas de trabalho de produção em escala mais pequena. Uma partição de 2 GB e até três réplicas. |
|1 Standard (S1) | Para S1 e versões posteriores, dedicado máquinas com mais capacidade de armazenamento e processamento em todos os níveis. Para S1, o tamanho da partição é 25 GB/partição (com um máximo de 300 GB por serviço). |
|Standard 2 (S2) | É semelhante para S1, mas com partições de 100 GB (e um máximo de 1,2 TB por serviço). |
|3 padrão (S3) | Partições de 200 GB (com um máximo de 2,4 TB por serviço). |
|Standard 3 alta densidade (S3 HD) | Alta densidade é uma *modo de alojamento* para S3. O hardware subjacente é otimizado para um grande número de índices menores e destina-se para cenários de arquitetura "multitenancy". S3 HD tem o custo por unidade mesmo como S3, mas o hardware é otimizada para leituras de rápida de ficheiros num grande número de índices menores.|
|Armazenamento otimizado 1 (L1) | Partições de 1 TB (com um máximo de 12 TB por serviço). |
|Armazenamento otimizado 2 (L2) | Partições de 2 TB (com um máximo de 24 TB por serviço). |

> [!NOTE] 
> Os escalões de armazenamento otimizado oferecem maior capacidade de armazenamento por um preço reduzido por TB de escalões Standard. O compromisso principal é a mais alta latência da consulta, que deve validar para os seus requisitos de aplicação específica.  Para saber mais sobre as considerações de desempenho deste escalão, consulte [considerações sobre desempenho e otimização](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Como funciona a faturação

Existem três formas de incorrer em custos no Azure Search, e existem componentes fixos e variáveis. Esta secção descreve os três componentes de faturas: principais custos de serviço, os custos de saída de dados e IA sofisticados de indexação.

### <a name="core-service-costs-fixed-and-variable"></a>Custos de serviço do Core (fixos e variável)

Para o próprio serviço, a cobrança mínima é a primeira unidade de pesquisa (partição da réplica de 1 x 1). Este mínimo é fixa para o tempo de vida do serviço porque o serviço não pode ser executado em nada menos do que esta configuração.

Além do mínimo, pode adicionar réplicas e partições de forma independente. Por exemplo, pode adicionar apenas réplicas ou apenas as partições. Incrementais aumentos de capacidade por meio de réplicas e partições compõem o componente de custo de variável.

A faturação baseia-se numa [fórmula (réplicas x partições x taxa)](#search-units). A tarifa que lhe é cobrada depende de selecionar o escalão de preço.

Na captura de ecrã seguinte, o preço por unidade é indicado para gratuito, Basic e o S1. (S2, S3, L1 e L2 não são apresentados.) Se criar um serviço básico, o custo mensal atingirão a média do valor que é apresentado para *preço-1*. Para um serviço padrão, o custo mensal atingirão a média do valor que é apresentado para *preço-2*. Aumentar os custos da unidade para cada camada, porque a capacidade computacional de energia e de armazenamento é maior em cada camada consecutiva. As taxas do Azure Search estão disponíveis na [página de preços do Azure Search](https://azure.microsoft.com/pricing/details/search/).

![Preço por unidade](./media/search-sku-tier/per-unit-pricing.png "preço por unidade")

Quando estiver a prever o custo de uma solução de pesquisa, tenha em atenção que os preços e capacidade não linear. (Mais do que dobra a capacidade duplica o custo.) Para obter um exemplo de como a fórmula funciona, consulte [como alocar réplicas e partições](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

#### <a name="billing-based-on-search-units"></a>Faturação com base em unidades de pesquisa

O conceito de faturação mais importante para compreender para operações de pesquisa do Azure é o *unidade de pesquisa* (SU). Porque o Azure Search depende de réplicas e partições para a indexação e consulta, não faz sentido para cobrar por apenas um ou outro. Em vez disso, a faturação baseia-se numa composição de ambos.

SU é o produto do *réplicas* e *partições* utilizado por um serviço: **(R x P = SU)** .

Cada serviço é iniciado com um SU (uma réplica multiplicada por partição) como o mínimo. O máximo para qualquer serviço é 36 SUs. É possível aceder este valor máximo de várias formas: réplicas de 6 partições x 6 ou réplicas de 3 partições x 12, por exemplo. É comum usar menos do que a capacidade total (por exemplo, uma réplica de 3, 3-partition serviço cobrada como 9 SUs). Consulte a [combinações de partição e réplica](search-capacity-planning.md#chart) gráfico para combinações válidos.

A taxa de faturação à hora é por SU. Cada escalão tem uma taxa de cada vez maior. Escalões superiores são fornecidos com partições maiores e mais veloz, e isso contribui para uma taxa por hora geral maior para essa camada. Pode ver as tarifas para cada camada sobre o [os detalhes dos preços](https://azure.microsoft.com/pricing/details/search/) página.

A maioria dos clientes trazer apenas uma parte da capacidade total online, que contém o resto em reserva. Para faturação, o número de partições e réplicas que coloque online, calculada pelo SU fórmula, determina o que paga à hora.

### <a name="data-egress-charges-during-indexing"></a>Custos de saída de dados durante a indexação

Usando [indexadores do Azure Search](search-indexer-overview.md) podem afetar a faturação, dependendo da localização dos seus serviços. Pode eliminar os custos de saída de dados, se criar o serviço de pesquisa do Azure na mesma região que seus dados. Eis algumas informações a partir da [página de preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft não cobra para quaisquer dados de entrada para qualquer serviço no Azure ou para quaisquer dados de saída do Azure Search.

+ Em soluções multiservice, não é sem custo para dados enviada pela rede quando todos os serviços estão na mesma região.

São cobradas tarifas para dados de saída, se os serviços estiverem em diferentes regiões. Estes custos não são, na verdade, parte da sua fatura do Azure Search. Eles são mencionados aqui porque se estiver a utilizar dados ou indexadores sofisticados de ia para extrair os dados de regiões diferentes, verá os custos serão refletidos na sua fatura global.

### <a name="ai-enriched-indexing-with-cognitive-services"></a>IA-indexação enriquecida com os serviços cognitivos

Para [IA indexação com os serviços cognitivos](cognitive-search-concept-intro.md), deve planear a anexar um recurso de serviços cognitivos do Azure faturável, na mesma região que o Azure Search, com o escalão de preço S0 para processamento de pay as you go. Não existe nenhum custo fixo associado anexar serviços cognitivos. Só paga o processamento que é necessário.

Extração de imagem durante a decodificação de documento é um custo de Azure Search. É faturada de acordo com o número de imagens extraídos dos seus documentos. Extração de texto é atualmente gratuita.

Outros possível, como o processamento de linguagem natural, é baseiam [incorporadas capacidades cognitivas](cognitive-search-predefined-skills.md) e faturadas relativamente a um recurso dos serviços cognitivos. Estão cobrados à mesma tarifa, como se tivesse executado a tarefa com os serviços cognitivos diretamente. Para obter mais informações, consulte [anexar um recurso de serviços cognitivos com um conjunto de capacidades](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

#### <a name="billing-for-image-extraction-in-cognitive-search"></a>Faturação de extração de imagem na pesquisa cognitiva

Se extrair imagens de ficheiros num pipeline de indexação de pesquisa cognitiva, ser-lhe-á cobrado para essa operação na sua fatura do Azure Search. Num [a configuração do indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** é o parâmetro que aciona a extração de imagem. Se **imageAction** está definido como "none" (predefinição), não será cobrado para extração de imagem.

Os preços estão sujeitas a alterações. Ela é documentada no [os detalhes dos preços](https://azure.microsoft.com/pricing/details/search/) página para o Azure Search.

#### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Faturação de habilidades internas na pesquisa cognitiva

Quando configurar um pipeline de melhoria, qualquer [habilidades internas](cognitive-search-predefined-skills.md) usada no pipeline são baseadas em modelos de aprendizagem automática. Estes modelos são fornecidos por serviços cognitivos. Se usar esses modelos durante a indexação, será cobrado à mesma tarifa como seria se tiver solicitado o recurso diretamente.

Por exemplo, imaginemos que tem um pipeline que utiliza o reconhecimento ótico de carateres (OCR) contra ficheiros JPEG e o texto resultante é enviado para um índice da Azure Search para consultas de pesquisa de forma livre. O pipeline de indexação inclui um indexador com o [habilidade de OCR](cognitive-search-skill-ocr.md), e que a habilidade seria [anexado a um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md). Ao executar o indexador, custos de execução de OCR irão aparecer na sua fatura de recursos cognitivos.

## <a name="tips-for-reducing-costs"></a>Dicas para reduzir os custos

Não pode encerrar o serviço para reduzir a sua fatura. Recursos dedicados estão sempre operacionais, alocado para seu uso exclusivo para o tempo de vida do seu serviço. A única forma de reduzir a sua fatura é reduzir as réplicas e partições para um nível que ainda fornece um desempenho aceitável e [conformidade SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

É uma forma de reduzir os custos de escolher uma camada com uma tarifa por hora mais baixa. Tarifas à hora de S1 são mais baixas que as taxas de S2 ou S3. Supondo que provisionar o serviço no final de suas projeções de carga mais baixo, se ser excedida do serviço, pode criar um segundo serviço maiores em camadas, recriação dos índices no segundo serviço e, em seguida, elimine primeiro.

Se tiver feito o planeamento da capacidade para servidores no local, sabe que é comum "comprar se" para que pode manipular o crescimento previsto. Com um serviço cloud, pode buscar economias de custo mais agressivamente porque não está bloqueado a compra de específica. Pode sempre mudar para um serviço em camadas superiores, se o atual não é suficiente.

### <a name="capacity"></a>Capacidade

No Azure Search, a capacidade está estruturada como *réplicas* e *partições*.

+ Réplicas são instâncias do serviço de pesquisa. Cada réplica aloja uma cópia de um índice com balanceamento de carga. Por exemplo, um serviço com seis réplicas tem seis cópias dos todos os índices carregado no serviço.

+ Partições armazenam índices e automaticamente dividir dados pesquisáveis. Duas partições dividir seu índice em metade, três partições dividi-la em terços e assim por diante. Em termos de capacidade *tamanho de partição* é o recurso diferenciador principal entre camadas.

> [!NOTE]
> Suportam a todos os escalões Standard e do armazenamento otimizado [flexíveis combinações das réplicas e partições](search-capacity-planning.md#chart) para que possa [Otimize o seu sistema de armazenamento ou de velocidade](search-performance-optimization.md) alterando o saldo. O escalão básico oferece até três réplicas para elevada disponibilidade, mas tem apenas uma partição. Escalões gratuitos não fornecem recursos dedicados: computação recursos são partilhados por vários subscritores.

### <a name="more-about-service-limits"></a>Mais informações sobre limites de serviço

Serviços de recursos de anfitrião, como índices e indexadores. Cada escalão impõe [limites de serviço](search-limits-quotas-capacity.md) no número de recursos, pode criar. Portanto, o número máximo de índices (e outros objetos) é o segundo recurso diferenciador entre camadas. Ao analisar cada opção no portal, tenha em atenção os limites no número de índices. Outros recursos, como indexadores e origens de dados e conjuntos de habilidades, são afixação limites de índice.

## <a name="consumption-patterns"></a>Padrões de consumo

A maioria dos clientes começar com o serviço gratuito, o que eles mantenham indefinidamente, e, em seguida, escolha um dos escalões Standard ou o armazenamento otimizado para cargas de trabalho de desenvolvimento ou produção graves.

![O Azure Search escalões de preço](./media/search-sku-tier/tiers.png "escalões de preço do Azure Search")

Nas extremidades baixas e alta, Basic e do S3 HD são para padrões de consumo importante mas atípicos. Basic destina-se a cargas de trabalho de produção de pequena. Ele oferece SLAs, recursos dedicados e elevada disponibilidade, mas ele fornece armazenamento modesto, beirando os total de 2 GB. Este escalão foi desenvolvido para os clientes que consistentemente underutilize capacidade disponível. No final elevada, S3 HD é para cargas de trabalho típicas de ISVs, parceiros, [soluções multi-inquilino](search-modeling-multitenant-saas-applications.md), ou qualquer configuração que chama para um grande número de índices pequeno. Muitas vezes, fica claro quando básico ou S3 HD é o escalão certo. Se pretender que a confirmação, pode postar [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contacte o suporte do Azure](https://azure.microsoft.com/support/options/) para obter orientações.

Os escalões padrão usados com mais frequência, S1 por meio de S3, constituem uma progressão de aumentar os níveis de capacidade. Existem pontos de flexão no tamanho da partição e limites no número de índices, indexadores e recursos de resultado:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Tamanho da partição|  25 GB | 100 GB | 200 GB |  |  |  |  |
| limites de índice e indexador| 50 | 200 | 200 |  |  |  |  |

S1 é uma opção comum para os clientes que precisam de recursos dedicados e várias partições. S1 oferece as partições de 25 GB e até 12 partições, fornecendo um limite por serviço de 300 GB, se maximizar a partições através de réplicas. (Consulte [alocar partições e réplicas](search-capacity-planning.md#chart) para obter mais informações com balanceamento de alocações.)

As páginas de portais e preços colocar o foco no tamanho da partição e o armazenamento, mas, para cada camada, todos os recursos (capacidade de disco, velocidade, CPUs) de computação geralmente aumentam linearmente com o preço. Uma réplica de S2 é mais rápido do que S1 e S3 é mais rápido de S2. Escalões S3 interromper o padrão de preços de computação linear com e/s desproporcional mais rápido. Se necessitar de ser o afunilamento da e/s, tenha em mente que pode obter muito mais IOPS com S3 que pode obter com camadas inferiores.

S3 e S3 HD são apoiadas pela infra-estrutura de alta capacidade idêntica, mas atingirem os limites máximos de formas diferentes. S3 destina-se um número menor de índices muito grandes, portanto, o limite máximo é ligado a recursos (2,4 TB para cada serviço). S3 HD destina-se um grande número de índices muito pequenos. Em 1000 índices, S3 HD atinge seus limites na forma de restrições de índice. Se for um cliente do S3 HD e precisar de mais de 1000 índices, entre em contato com Support da Microsoft para obter informações sobre como proceder.

> [!NOTE]
> Limites do documento foram uma consideração ao mesmo tempo, mas já não são aplicáveis para os novos serviços. Para obter informações sobre as condições em que os limites de documento ainda se aplicam, consulte [documentar limites](search-limits-quotas-capacity.md#document-limits).
>

Camadas de armazenamento otimizado, L1 e L2, são ideais para aplicações com requisitos de dados de grande dimensão, mas com um número relativamente baixo de usuários finais, minimizando a latência da consulta não é a principal prioridade.  

|  | L1 | ERROS DE L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Tamanho da partição|  1 TB | 2 TB |  |  |  |  |  |
| limites de índice e indexador| 10 | 10 |  |  |  |  |  |

Erros de L2 oferece duas vezes a capacidade de armazenamento geral de L1.  Escolha o escalão de com base na quantidade máxima de dados que acredita que precisa de seu índice. As partições de camada de L1 aumentar verticalmente em incrementos de 1 TB para um máximo de 12 TB. As partições de L2 aumentam a 2 TB por partição até um máximo de 24 TB.

## <a name="evaluating-capacity"></a>Avaliar a capacidade

Capacidade e os custos de execução do serviço estão diretamente relacionados. Escalões imponham limites dois níveis: armazenamento e recursos. Deve pensar ambos porque consoante o limite que entrar pela primeira vez é o limite em vigor.

As necessidades comerciais estabeleçam, normalmente, o número de índices, que será necessário. Por exemplo, poderá ter um índice global para um grande repositório de documentos. Ou poderá ter vários índices com base na região, aplicativo ou nicho de negócios.

Para determinar o tamanho de um índice, precisa [criar um](search-create-index-portal.md). A estrutura de dados no Azure Search é principalmente uma [invertido índice](https://en.wikipedia.org/wiki/Inverted_index) estrutura, que possui características diferentes que os dados de origem. Para um índice invertido, tamanho e complexidade são determinadas pelo conteúdo, não necessariamente pela quantidade de dados alimentar nele. Uma origem de dados de grande dimensão com redundância de alta pode resultar num índice menor do que um conjunto de dados mais pequeno que contém conteúdo altamente variável. Então, é raramente possível inferir o tamanho do índice com base no tamanho do conjunto de dados original.

> [!NOTE] 
> Apesar de estimar as necessidades futuras de índices e o armazenamento pode sentir como suposição, ele vale a pena. Se a capacidade de uma camada é demasiado baixo, terá de aprovisionar um novo serviço num escalão mais elevado e, em seguida [recarregar os índices](search-howto-reindex.md). Não existe nenhuma atualização no local de um serviço a partir de um SKU para outro.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>Passo 1: Desenvolver essas estimativas aproximadas com o escalão gratuito

Uma abordagem para estimar a capacidade é começar com o escalão gratuito. Lembre-se de que o serviço gratuito oferece até três índices, a 50 MB de armazenamento e de 2 minutos de tempo de indexação. Ele pode ser um desafio para estimar um tamanho de índice prevista com essas restrições. Esta é uma abordagem que pode efetuar:

+ [Criar um serviço gratuito](search-create-service-portal.md).
+ Prepare um conjunto de dados representativo e pequeno (por exemplo, 5000 documentos e tamanho da amostra de 10 por cento).
+ [Criar um índice inicial](search-create-index-portal.md) e tenha em atenção o seu tamanho no portal (por exemplo, de 30 MB).

Se for representante e 10 por cento da origem de dados completo, um índice de 30 MB torna-se aproximadamente 300 MB se todos os documentos são indexados. Armado com esse número preliminar, poderá duplicar essa quantidade de verba dois índices (desenvolvimento e produção). Isso lhe dá um total de 600 MB em requisitos de armazenamento. Este requisito facilmente for satisfeito pelo escalão básico, para que deve começar por aqui.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>Passo 2: Desenvolver estimativas refinadas com um escalão a cobrar

Alguns clientes preferem começar com recursos dedicados que podem acomodar amostragem maior e tempos de processamento e, em seguida, desenvolver estimativas realistas de quantidade de índice, o tamanho e volumes de consulta durante o desenvolvimento. Inicialmente, um serviço é aprovisionado com base numa estimativa suposição. Em seguida, como o projeto de desenvolvimento amadurece, as equipes normalmente sabem se o serviço existente é sobre ou abaixo da capacidade para cargas de trabalho de produção prevista.

1. [Reveja os limites do serviço em cada camada](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) para determinar se camadas inferiores podem suportar o número de índices, precisa. Entre os escalões básico, S1 e S2, limites de índice são 15, 50 e 200, respectivamente. A camada de armazenamento otimizado tem um limite de 10 índices porque foi concebido para suportar um número reduzido de índices muito grandes.

1. [Criar um serviço num escalão a cobrar](search-create-service-portal.md):

    + Inicie baixa, com básica ou S1, se estiver no início da sua curva de aprendizado.
    + Inicie o alto, S2 ou S3 até mesmo, se souber que precisará ter cargas de indexação e consulta em grande escala.
    + Comece com otimizados de armazenamento, em L1 ou erros de L2, se estiver a indexação uma grande quantidade de dados e a carga de consultas é relativamente baixa, como com uma aplicação empresarial interna.

1. [Criar um índice inicial](search-create-index-portal.md) para determinar como os dados de origem é convertida para um índice. Esta é a única forma de estimar o tamanho do índice.

1. [Monitorizar o armazenamento, limites de serviço, o volume de consulta e latência](search-monitor-usage.md) no portal. O portal mostra as consultas por segundo, limitadas consultas e latência de pesquisa. Todos esses valores podem ajudar a decidir se tiver selecionado o escalão certo. Também pode configurar monitorização profunda de valores, como análise clickthrough Ativando [a análise de tráfego de pesquisa](search-traffic-analytics.md).

Número de índice e tamanho são igualmente importantes para sua análise. Isso ocorre porque os limites máximos são atingidos por meio da utilização total de armazenamento (partições) ou por limites máximos em recursos (índices, indexadores e assim por diante), o que ocorrer primeiro. O portal de ajuda-o a manter o controle de ambos os casos, mostrando utilização atual e os limites máximos lado a lado na página de descrição geral.

> [!NOTE]
> Requisitos de armazenamento podem ser encheram se documentos contêm dados irrelevantes. O ideal é que os documentos contêm apenas os dados que sejam necessárias para a experiência de pesquisa. Dados binários não não pesquisáveis e devem ser armazenados separadamente (talvez num armazenamento de tabela ou um blob do Azure). Em seguida, deve ser adicionado um campo no índice para manter uma referência de URL para os dados externos. O tamanho máximo de um documento individual é 16 MB (ou menos quando estiver em massa vários documentos numa solicitação de carregamento). Para obter mais informações, consulte [limites de serviço do Azure Search](search-limits-quotas-capacity.md).
>

**Considerações de volume de consulta**

Consultas por segundo (QPS) é uma métrica importante durante a otimização de desempenho, mas geralmente é apenas uma consideração de camada se pretender que o volume de consulta elevado no início.

Os escalões Standard podem fornecer um equilíbrio das réplicas e partições. Pode aumentar o processo de consulta ao adicionar réplicas para balanceamento de carga ou adicione partições para processamento paralelo. Pode, em seguida, otimizar o desempenho depois do serviço é aprovisionado.

Se necessitar de volumes de constante de consulta elevado, desde o início, deve considerar os escalões Standard superiores, apoiados por um hardware mais potente. Pode, em seguida, colocar as partições e réplicas offline ou até mesmo se mudar para um serviço de escalão inferior, esses volumes de consulta não ocorrerem. Para obter mais informações sobre como calcular o débito de consulta, consulte [desempenho de pesquisa do Azure e a otimização de](search-performance-optimization.md).

As camadas de armazenamento otimizado são úteis para cargas de trabalho de dados grandes, suporte de mais armazenamento de índice disponível em geral para quando os requisitos de latência da consulta são menos importantes. Devem continuar a utilizar réplicas adicionais para partições de adicionais e balanceamento de carga para processamento paralelo. Pode, em seguida, otimizar o desempenho depois do serviço é aprovisionado.

**Contratos de nível de serviço**

As funcionalidades gratuitas de camada e de pré-visualização não fornecem [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todos os escalões faturáveis, SLAs em vigor quando Aprovisiona redundância suficiente para o seu serviço. Tem de ter duas ou mais réplicas para SLA de consulta (ler). Tem de ter três ou mais réplicas de consulta e indexação de SLAs (leitura / escrita). O número de partições não afeta os SLAs.

## <a name="tips-for-tier-evaluation"></a>Dicas de avaliação de camada

+ Saiba como criar índices eficientes e saiba quais métodos de atualização tenham o menor impacto. Uso [a análise de tráfego de pesquisa](search-traffic-analytics.md) para obter informações sobre a atividade de consulta.

+ Permitir que as métricas para criados ao redor de consultas e recolher dados sobre padrões de utilização (consultas durante o horário comercial, indexação de horas de pico). Utilize estes dados para informar as decisões de aprovisionamento de serviço. Apesar de não é prático numa cadência de cada hora ou diariamente, pode ajustar dinamicamente as partições e recursos para acomodar alterações planejadas em volumes de consulta. Também pode acomodar as alterações não planeadas mas constante se níveis manter suficientemente longo para garantir a tomar.

+ Lembre-se de que a única desvantagem do underprovisioning é que poderá ter de fechar um serviço, se os requisitos reais são maiores que as previsões de indisponibilidade. Para evitar a interrupção do serviço, seria criar um novo serviço na mesma subscrição num escalão mais elevado e executá-lo lado a lado até que o novo ponto final de destino de todas as aplicações e pedidos.

## <a name="next-steps"></a>Passos Seguintes

Comece com um escalão gratuito e criar um índice inicial usando um subconjunto dos seus dados para compreender suas características. A estrutura de dados no Azure Search é uma estrutura de índice invertida. O tamanho e a complexidade de um índice invertida é determinado pelo conteúdo. Lembre-se de que o conteúdo altamente redundante tende a resultar num índice menor do que o conteúdo altamente irregular. Portanto, características de conteúdo em vez do tamanho do conjunto de dados determinar os requisitos de armazenamento de índice.

Depois de ter uma estimativa inicial do seu tamanho de índice [aprovisionar um serviço sujeito a faturação](search-create-service-portal.md) em um dos escalões discutidos neste artigo: Básico, Standard ou com otimização de armazenamento. Relaxe quaisquer restrições artificiais relativamente ao dimensionamento de dados e [reconstruir o índice](search-howto-reindex.md) para incluir todos os dados que pretende que seja pesquisável.

[Alocar partições e réplicas](search-capacity-planning.md) conforme necessário para obter o desempenho e dimensionamento necessitar.

Se o desempenho e a capacidade estão tudo bem, já está. Caso contrário, volte a criar um serviço de pesquisa num escalão diferente que mais de perto se alinha com as suas necessidades.

> [!NOTE]
> Se tiver dúvidas, publique [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contacte o suporte do Azure](https://azure.microsoft.com/support/options/).
