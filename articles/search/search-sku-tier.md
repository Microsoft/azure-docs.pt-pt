---
title: Escolha um escalão de preço ou SKU para o serviço Azure Search - Azure Search
description: 'O Azure Search pode ser aprovisionado nestes SKUs: Gratuito, básico e Standard, onde Standard está disponível em várias configurações de recursos e níveis de capacidade.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d325a5dfd57bb6b69e6cf171487adfa8d374512f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762930"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Escolha um escalão de preço para o Azure Search

No Azure Search, um [recurso é criado](search-create-service-portal.md) num escalão de preço ou SKU é fixa para o tempo de vida do serviço. Os escalões incluem **gratuito**, **básica**, ou **padrão**, onde **padrão** está disponível em várias configurações e as capacidades. Começar a maioria dos clientes com o **gratuito** escalão para avaliação e, em seguida, passar gradualmente para **padrão** para implementações de desenvolvimento e produção. Pode concluir todos os inícios rápidos e tutoriais sobre o **gratuito** escalão, inclusive os de muitos recursos de pesquisa cognitiva. 

Camadas de refletem as características de hardware que aloja o serviço (em vez de recursos) e são diferenciadas por:

+ Número de índices que pode criar
+ Tamanho e a velocidade de partições (armazenamento físico)

Embora todas as camadas, incluindo o **gratuito** camada, oferta geralmente paridade de funcionalidades, cargas de trabalho maiores podem ditar os requisitos para escalões superiores. Por exemplo, [pesquisa cognitiva](cognitive-search-concept-intro.md) indexação tem habilidades de longa execução desse tempo limite num serviço gratuito, a menos que o conjunto de dados é pequeno.

> [!NOTE] 
> A exceção a paridade de funcionalidades são [indexadores](search-indexer-overview.md), que não estão disponível em S3HD.
>

Dentro de uma camada, pode [ajustar os recursos de partição e réplica](search-capacity-planning.md) para aumentar ou diminuir a escala. Pode começar com um ou dois de cada uma e, em seguida, elevar temporariamente a sua capacidade computacional de uma pesada carga de trabalho de indexação. A capacidade de otimizar os níveis de recursos dentro de uma camada adiciona flexibilidade, mas ligeiramente também complica a sua análise. Poderá ter de experimentar para verificar se um escalão mais baixo com recursos superior/réplicas oferece melhor valor e desempenho do que um escalão mais elevado com a obtenção de recursos mais baixo. Para saber mais sobre o quando e por que seria ajuste a capacidade, veja [considerações sobre desempenho e otimização](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Camadas para o Azure Search

A tabela seguinte lista as camadas disponíveis. Outras fontes de informações de camada incluem o [página de preços](https://azure.microsoft.com/pricing/details/search/), [limites de serviço e os dados](search-limits-quotas-capacity.md)e a página do portal quando um serviço de aprovisionamento.

|Escalão | Capacidade |
|-----|-------------|
|Gratuito | Partilhado com outros subscritores. Não escaláveis, limitada até 3 índices e 50 MB de armazenamento. |
|Básica | Recursos de computação dedicados para cargas de trabalho de produção em escala mais pequena. Uma partição de 2 GB e até três réplicas. |
|1 Standard (S1) | De S1 em segurança de máquinas dedicadas com mais capacidade de armazenamento e processamento em todos os níveis. Tamanho da partição é 25 GB/partição (máximo de 300 GB de documentos por serviço) para S1. |
|Standard 2 (S2) | É semelhante para S1, mas com 100 GB/partições (máximo 1,2 TB de documentos por serviço) |
|3 padrão (S3) | 200 GB/partição (máximo 2,4 TB de documentos por serviço). |
|Standard 3 alta densidade (S3-HD) | Alta densidade é uma *modo de alojamento* para S3. O hardware subjacente é otimizado para um grande número de índices menores, utilização prevista para cenários de arquitetura "multitenancy". S3 HD tem o custo por unidade mesmo como S3, mas o hardware é otimizada para leituras de rápida de ficheiros num grande número de índices menores.|


## <a name="how-billing-works"></a>Como funciona a faturação

No Azure Search, existem três formas de incorrer em custos no Azure Search e existem componentes fixos e variáveis. Esta secção analisa detalhadamente cada componente de faturação.

### <a name="1-core-service-costs-fixed-and-variable"></a>1. Custos de serviço do Core (fixos e variável)

Para o próprio serviço, a cobrança mínima é a primeira unidade de pesquisa (partição da réplica de 1 x 1), e esta quantidade é constante para o tempo de vida do serviço porque o serviço não pode ser executado em nada menos do que esta configuração. 

Captura de ecrã seguinte, por preço da unidade é indicado para gratuito, Basic e o S1 (S2 e S3 não são mostrados). Se tiver criado um serviço básico ou um serviço padrão, o custo mensal teria média o valor que é apresentado para *preço-1* e *preço-2* , respetivamente. Os custos da unidade subir para cada camada porque a capacidade computacional de energia e de armazenamento é maior em cada camadas consecutivos.

![Por preço da unidade](./media/search-sku-tier/per-unit-pricing.png "por preço da unidade")

Réplicas adicionais e as partições são um suplemento para o custo inicial. Um serviço de pesquisa requer uma partição e réplica, portanto, a configuração mínima é um de cada. Além do mínimo, adicionar réplicas e partições de forma independente. Por exemplo, poderia adicionar apenas réplicas ou apenas as partições. 

Partições e réplicas adicionais são cobradas com base numa [fórmula](#search-units). Os custos não estão lineares (Dobra a capacidade mais do que duplica o custo). Para obter um exemplo de como a fórmula funciona, consulte ["Como alocar réplicas e partições"](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)

### <a name="2-data-egress-charges-during-indexing"></a>2. Custos de saída de dados durante a indexação

Quando extrair dados de uma origem de dados de base de dados do Azure SQL ou Cosmos DB, verá encargos para a transação na fatura para esses recursos. Esses custos não são medidores do Azure Search, mas que são mencionados aqui porque se estiver a utilizar indexadores para extrair dados de base de dados do Azure SQL ou do Azure Cosmos DB, verá esse custo na sua fatura.

### <a name="3-ai-enriched-indexing-using-cognitive-services"></a>3. IA-indexação enriquecida com os serviços cognitivos

Para [pesquisa cognitiva](cognitive-search-concept-intro.md) apenas, extração de imagem durante a decodificação de documento é cobrada com base no número de imagens extraídos dos seus documentos. Extração de texto é atualmente gratuita. Outra possível com base na [incorporadas capacidades cognitivas](cognitive-search-predefined-skills.md) são faturadas relativamente a um recurso dos serviços cognitivos. Possível é faturadas à mesma tarifa como se tivesse executado a tarefa com os serviços cognitivos diretamente.

Se não estiver a utilizar [pesquisa cognitiva](cognitive-search-concept-intro.md) ou [indexadores do Azure Search](search-indexer-overview.md), os custos da sua únicos estão relacionadas com as réplicas e partições de utilização ativa, para cargas de trabalho regulares de indexação e consulta.

<a name="search-units"></a>

### <a name="billing-based-on-search-units"></a>Faturação com base em unidades de pesquisa

Para operações de Azure Search, é o conceito de faturação mais importante para compreender um *unidade de pesquisa* (SU). Porque o Azure Search depende de réplicas e partições para a indexação e consulta, não faz sentido para cobrar por apenas um ou outro. Em vez disso, a faturação baseia-se numa composição de ambos. 

SU é o produto da *réplica* e *partições* utilizado por um serviço: **`(R X P = SU)`**

Cada serviço é iniciado com um SU (uma réplica multiplicada por partição) como o mínimo. O máximo para qualquer serviço é 36 SUs, o que podem ser conseguidos de várias formas: réplicas de 6 partições x 6 ou réplicas de 3 partições x 12, para citar alguns. É comum usar menos do que a capacidade total. Por exemplo, uma réplica de 3, 3-partition service, cobrada como 9 SUs. Pode rever [este gráfico](search-capacity-planning.md#chart) para ver as combinações válidas rapidamente.

É a taxa de faturação **por hora por SU**, com cada escalão de ter uma taxa de cada vez maior. Escalões superiores são fornecidos com partições maiores e mais veloz, que contribuem para uma taxa por hora geral maior para essa camada. As tarifas baixas para cada camada pode ser encontrada no [detalhes de preços](https://azure.microsoft.com/pricing/details/search/). 

A maioria dos clientes trazer apenas uma parte da capacidade total online, que contém o resto em reserva. Em termos de faturação, é o número de partições e réplicas que colocar online, calculada utilizando a fórmula SU, que determina o que pagará, na verdade, numa base horária.

### <a name="billing-for-image-extraction-in-cognitive-search"></a>Faturação de extração de imagem na pesquisa cognitiva

Se está a extrair imagens de ficheiros num pipeline de indexação de pesquisa cognitiva, é-lhe cobrada essa operação na sua fatura do Azure Search. O parâmetro que aciona a extração de imagem é **imageAction** num [a configuração do indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters). Se **imageAction** está definida como none (predefinição), não existem custos para extração de imagem.

Preços estão sujeitas a alterações, mas sempre está documentado no [detalhes de preços](https://azure.microsoft.com/pricing/details/search/) página para o Azure Search. 

### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Faturação de habilidades internas na pesquisa cognitiva

Quando configurar um pipeline de melhoria, qualquer [habilidades internas](cognitive-search-predefined-skills.md) usada no pipeline são baseadas em modelos de aprendizagem automática. Esses modelos são fornecidos por serviços cognitivos. Utilização desses modelos durante a indexação é cobrada a mesma tarifa como se tivesse pediu o recurso diretamente.

Por exemplo, suponha que um pipeline que consiste de reconhecimento ótico de carateres (OCR) em relação a ficheiros JPEG de imagem digitalizada, onde o texto resultante é enviada por push para um índice da Azure Search para consultas de pesquisa de forma livre. O pipeline de indexação inclui um indexador com o [habilidade de OCR](cognitive-search-skill-ocr.md), e que a habilidade seria [anexado a um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md). Ao executar o indexador, encargos são apresentados na sua fatura cognitivos recursos para a execução de OCR.

## <a name="tips-for-reducing-costs"></a>Dicas para reduzir os custos

Não pode encerrar o serviço para reduzir a fatura. Recursos dedicados são operacionais 24x7, alocado para seu uso exclusivo, durante o ciclo de vida do seu serviço. A única forma de reduzir uma fatura está reduzindo as réplicas e partições para um nível baixo que ainda fornece um desempenho aceitável e [conformidade SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Uma alavanca para reduzir os custos é escolher uma camada de uma taxa por hora mais baixo. Tarifas à hora de S1 são mais baixas que as taxas de S2 ou S3. Supondo que aprovisiona um serviço que visa a extremidade inferior do seu projeções de carga, se ser excedida do serviço, poderia criar um segundo serviço maiores em camadas, recriação dos índices nesse serviço segundo e, em seguida, elimine primeiro. 

Se o tiver feito planeamento de capacidade nos servidores locais, sabe que é comum "comprar se" para que pode manipular o crescimento previsto. Mas com um serviço em nuvem, pode buscar economias de custo mais agressivamente porque não está limitado a compra de específica. Pode sempre mudar para um serviço em camadas superiores, se o atual não é suficiente.

### <a name="capacity-drill-down"></a>Capacidade de desagregação

No Azure Search, a capacidade está estruturada como *réplicas* e *partições*. 

+ Réplicas são instâncias de serviço de pesquisa, em que cada réplica aloja uma cópia de um índice com balanceamento de carga. Por exemplo, um serviço com 6 réplicas tem 6 cópias de todos os índices carregado no serviço. 

+ As partições armazenar índices e automaticamente dividir dados pesquisáveis: dividir seu índice no meio, três partições em terços duas partições e assim por diante. Em termos de capacidade *tamanho de partição* é o recurso diferenciador principal entre camadas.

> [!NOTE]
> Todos os **padrão** camadas suporte [réplica combinações flexível e partições](search-capacity-planning.md#chart) para que possa [seu sistema de armazenamento ou de velocidade de importância](search-performance-optimization.md) alterando o saldo. **Básico** oferece segurança de três réplicas para elevada disponibilidade, mas tem apenas uma partição. **Gratuito** camadas não fornece recursos dedicados: computação recursos são partilhados por vários subscritores.

### <a name="more-about-service-limits"></a>Mais informações sobre limites de serviço

Serviços de recursos de anfitrião, como índices, indexadores e assim por diante. Cada escalão impõe [limites de serviço](search-limits-quotas-capacity.md) sobre a quantidade de recursos, pode criar. Como tal, um limite no número de índices (e outros objetos) é o segundo recurso diferenciador em escalões. Ao analisar cada opção no portal, tenha em atenção os limites no número de índices. Outros recursos, como indexadores e origens de dados e conjuntos de habilidades, são estabilizados aos limites de índice.

## <a name="consumption-patterns"></a>Padrões de consumo

Começar a maioria dos clientes com o **gratuito** serviço, o que eles mantenham indefinidamente e, em seguida, escolha uma da **padrão** camadas para cargas de trabalho de desenvolvimento ou produção graves. 

![Escalões do Azure search](./media/search-sku-tier/tiers.png "o Azure search escalões de preço")

Em cada extremidade **básica** e **S3 HD** existem para padrões de consumo importante mas atípicos. **Básico** é para cargas de trabalho de produção de pequena: oferece SLA, dedicado de recursos, elevada disponibilidade, mas armazenamento modesto, beirando os total de 2 GB. Este escalão foi desenvolvido para os clientes que consistentemente em utilizados capacidade disponível. No final, sem dúvida **S3 HD** é para cargas de trabalho típico de ISVs, parceiros, [soluções multi-inquilino](search-modeling-multitenant-saas-applications.md), ou qualquer configuração chamar para um grande número de índices pequeno. Muitas vezes é óbvio quando **básica** ou **S3 HD** camada é adequado, mas se pretender que a confirmação de que pode publicar no [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [entre em contato com Azure Suporte](https://azure.microsoft.com/support/options/) para obter orientações adicionais.

Mudando o foco para os escalões padrão usados com mais frequência, **S1 S3** são uma progressão de aumentar os níveis de capacidade, com pontos de flexão no tamanho da partição e os valores máximos no número de índices, indexadores e recursos de resultado:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Tamanho da partição|  25 GB | 100 GB | 200 GB |  |  |  |  |
| limites de índice e indexador| 50 | 200 | 200 |  |  |  |  |

**S1** é uma escolha comum quando os recursos dedicados e várias partições se tornam uma necessidade. Com partições de 25 GB para até 12 partições, o por serviço limite **S1** é de 300 GB total se maximizar a partições através de réplicas (consulte [alocar partições e réplicas](search-capacity-planning.md#chart) para mais com balanceamento de compositions.)

Páginas de portais e preços colocar o foco no tamanho da partição e o armazenamento, mas para cada camada, todos os recursos (capacidade de disco, velocidade, CPUs) de computação crescer de forma linear com o preço. Uma **S2** réplica é mais rápida que **S1**, e **S3** é mais rápido que **S2**. **S3** escalões quebrar o padrão de preços de computação geralmente linear com e/s desproporcional mais rápido. Se prevê e/s no estrangulamento, um **S3** dá-lhe muito mais IOPS que camadas inferiores.

**S3** e **S3 HD** são apoiadas pela infra-estrutura de capacidade de elevada idênticos, mas cada um atinge o limite máximo de formas diferentes. **S3** destina-se um número menor de índices muito grandes. Como tal, o limite máximo é ligado a recursos (2,4 TB para cada serviço). **S3 HD** destina-se um grande número de índices muito pequenos. Em 1000 índices, **S3 HD** atingir os limites na forma de restrições de índice. Se for um **S3 HD** cliente que requer mais de 1000 índices, entre em contato com Support da Microsoft para obter informações sobre como proceder.

> [!NOTE]
> Anteriormente, os limites de documento eram uma consideração mas já não são aplicáveis para os novos serviços. Para obter mais informações sobre as condições sob as quais os limites de documento ainda se aplicam, consulte [limites de serviço: Documente limites](search-limits-quotas-capacity.md#document-limits).
>

## <a name="evaluate-capacity"></a>Avaliação da capacidade

Capacidade e os custos da execução do service vão lado a lado. Escalões imponham limites dois níveis (armazenamento e recursos), para que deve pensar a respeito ambos porque qualquer que seja um entrar pela primeira vez é o limite em vigor. 

As necessidades comerciais estabeleçam, normalmente, o número de índices, que será necessário. Por exemplo, um índice global para um grande repositório de documentos, ou talvez vários índices, com base na região, aplicativo ou nicho de negócios.

Para determinar o tamanho de um índice, precisa [criar um](search-create-index-portal.md). A estrutura de dados no Azure Search é principalmente uma [invertido índice](https://en.wikipedia.org/wiki/Inverted_index), que tem diferentes características que os dados de origem. Para um índice invertido, tamanho e complexidade são determinadas pelo conteúdo, não necessariamente a quantidade de dados que alimentar nele. Uma origem de dados de grande dimensão com redundância maciça pode resultar num índice menor do que um conjunto de dados mais pequeno que contém conteúdo altamente variável. Como tal, é raramente possível inferir o tamanho do índice com base no tamanho do conjunto de dados original.

> [!NOTE] 
> Embora a estimar as necessidades futuras de índices e o armazenamento pode sentir como suposição, ele vale a pena. Se a capacidade de uma camada é demasiado baixo, terá de aprovisionar um novo serviço, o escalão superior e, em seguida [recarregar os índices](search-howto-reindex.md). Não existe nenhuma atualização in-loco do mesmo serviço de um SKU para outro.
>

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>Passo 1: Desenvolver essas estimativas aproximadas através do escalão gratuito

Uma abordagem para estimar a capacidade é começar com o **gratuito** escalão. Lembre-se de que o **gratuito** serviço oferece até 3 índices, a 50 MB de armazenamento e de 2 minutos de tempo de indexação. Pode ser um desafio para estimar um tamanho de índice prevista com essas restrições, mas o exemplo seguinte ilustra uma abordagem:

+ [Criar um serviço gratuito](search-create-service-portal.md)
+ Preparar um conjunto de dados representativo e pequeno (partem do princípio de cinco mil documentos e o tamanho da amostra de dez por cento)
+ [Criar um índice inicial](search-create-index-portal.md) e tenha em atenção o seu tamanho no portal (partem do princípio de 30 MB)

Partindo do princípio de que a amostra foi de representante e dez por cento da origem de dados completo, um índice de 30 MB torna-se aproximadamente 300 MB se todos os documentos são indexados. Armado com esse número preliminar, poderá duplicar essa quantidade de verba dois índices (desenvolvimento e produção), para um total de 600 MB em requisitos de armazenamento. Isso facilmente for satisfeito pela **básica** tier, para que deve começar por aqui.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>Passo 2: Desenvolver estimativas refinadas com um escalão a cobrar

Alguns clientes preferem começar com recursos dedicados que podem acomodar amostragem maior e tempos de processamento e, em seguida, desenvolva estimativas realistas de quantidade de índice, o tamanho e volumes de consulta durante o desenvolvimento. Inicialmente, um serviço é aprovisionado com base numa estimativa suposição e, em seguida, como o projeto de desenvolvimento amadurece, as equipes, normalmente, sabem se o serviço existente é sobre ou abaixo da capacidade para cargas de trabalho de produção prevista. 

1. [Reveja os limites do serviço em cada camada](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) para determinar se camadas inferiores podem suportar a quantidade de índices, precisa. Entre as **básica**-**S1**- **S2** escalões, limites de índice são 15-50-200, respectivamente.

1. [Criar um serviço num escalão a cobrar](search-create-service-portal.md):

    + Iniciar baixa, no **básica** ou **S1** se estiver no início da sua curva de aprendizado.
    + Iniciar elevado, em **S2** ou até mesmo **S3**, se a cargas de indexação e consulta em grande escala são óbvio.

1. [Criar um índice inicial](search-create-index-portal.md) para determinar como os dados de origem é convertida para um índice. Esta é a única forma de estimar o tamanho do índice.

1. [Monitorizar o armazenamento, limites de serviço, o volume de consulta e latência](search-monitor-usage.md) no portal. O portal mostra as consultas por segundo, limitadas consultas e latência de pesquisa; todos os quais podem ajudar a decidir se está o escalão certo. Além das métricas do portal, pode configurar a monitorização profunda, tais como análise clickthrough, ativando [a análise de tráfego de pesquisa](search-traffic-analytics.md). 

Número de índice e tamanho são igualmente relevantes para sua análise porque os limites máximos são atingidos por meio da utilização total de armazenamento (partições) ou por limites máximos em recursos (índices, indexadores e assim por diante), o que ocorrer primeiro. O portal de ajuda-o a manter o controle de ambos os casos, mostrando utilização atual e os limites máximos lado a lado na página de descrição geral.

> [!NOTE]
> Requisitos de armazenamento podem ser excessivamente inflacionados se documentos contêm dados irrelevantes. O ideal é que os documentos contêm apenas os dados que necessários para a experiência de pesquisa. Dados binários é não pesquisável e devem ser armazenados separadamente (talvez num armazenamento de tabela ou um blob do Azure) com um campo no índice para manter uma referência de URL para os dados externos. O tamanho máximo de um documento individual é 16 MB (ou menos quando estiver em massa vários documentos numa solicitação de carregamento). [Limites de serviço do Azure Search](search-limits-quotas-capacity.md) tem mais informações.
>

**Considerações de volume de consulta**

Consultas por segundo (QPS) é uma métrica que ganha proeminência durante a otimização de desempenho, mas geralmente não é uma consideração de escalão, a menos que espera o volume de consulta elevado no início.

Os escalões standard podem fornecer um equilíbrio entre as réplicas a partições, acelerar o processo de consulta através de réplicas adicionais de suporte para o carregamento de partições de balanceamento e adicionais para processamento paralelo. Pode otimizar o desempenho depois do serviço é aprovisionado.

Clientes que esperam strong sustentada consulta volumes desde o início, devem considerar escalões superiores, apoiados por um hardware mais potente. Pode, em seguida, colocar as partições e réplicas offline ou até mesmo se mudar para um serviço de camada inferior, esses volumes de consulta falharem materializar. Para obter mais informações sobre como calcular o débito de consulta, consulte [desempenho de pesquisa do Azure e a otimização de](search-performance-optimization.md).


**Contratos de nível de serviço**

O **gratuito** não pertencem com funcionalidades de camada e pré-visualização [(SLAs) de contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todos os escalões faturáveis, SLAs em vigor quando Aprovisiona redundância suficiente para o seu serviço. Duas ou mais réplicas são necessárias para o SLA de consulta (ler). Três ou mais réplicas são necessárias para consulta e indexação de SLA (leitura / escrita). O número de partições não é uma consideração de SLA. 

## <a name="tips-for-tier-evaluation"></a>Dicas de avaliação de camada

+ Saiba como criar índices de eficientes e as metodologias de atualização são o menor impacto. Recomendamos [a análise de tráfego de pesquisa](search-traffic-analytics.md) para as informações obtidas na atividade de consulta.

+ Permitir que as métricas criados ao redor de consultas e recolher dados sobre padrões de utilização (consultas durante o horário comercial, indexação de horas de ponta) e utilizar estes dados para informar o serviço futuro decisões de aprovisionamento. Enquanto não é prático numa cadência de cada hora ou diariamente, pode ajustar dinamicamente recursos para acomodar alterações planejadas em volumes de consulta, ou não planeada, mas constante de alterações se níveis manter suficientemente longo para garantir a tomar medidas e partições.

+ Lembre-se de que a única desvantagem do sob aprovisionamento é que poderá ter de fechar um serviço, se os requisitos reais são maiores que estimado. Para evitar a interrupção do serviço, seria criar um novo serviço na mesma subscrição num escalão mais elevado e executá-lo lado a lado até que o novo ponto final de destino de todas as aplicações e pedidos.

## <a name="next-steps"></a>Passos Seguintes

Começar com uma **gratuito** escalão e criar um índice inicial usando um subconjunto dos seus dados para compreender suas características. A estrutura de dados no Azure Search é um índice invertido, em que o tamanho e complexidade de um índice invertida é determinada pelo conteúdo. Lembre-se de que o conteúdo altamente redundante tende a resultar num índice menor do que o conteúdo altamente irregular. Como tal, é características de conteúdo em vez do tamanho do conjunto de dados que determina os requisitos de armazenamento de índice.

Depois de ter uma idéia inicial do tamanho do índice, [aprovisionar um serviço sujeito a faturação](search-create-service-portal.md) em um dos escalões discutidos neste artigo, optar por **básica** ou uma **padrão** escalão. Relaxe quaisquer restrições artificiais em subconjuntos de dados e [reconstruir o índice](search-howto-reindex.md) para incluir todos os dados que pretende, na verdade, ser pesquisável.

[Alocar partições e réplicas](search-capacity-planning.md) conforme necessário para obter o desempenho e dimensionamento necessitar.

Se o desempenho e a capacidade estão tudo bem, isso é tudo. Caso contrário, volte a criar um serviço de pesquisa num escalão diferente que mais de perto se alinha com as suas necessidades.

> [!NOTE]
> Para obter mais ajuda com suas perguntas, poste [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contacte o suporte Azure](https://azure.microsoft.com/support/options/).
