---
title: Escolha um nível de preços
titleSuffix: Azure Cognitive Search
description: 'Saiba mais sobre os níveis de preços (ou SKUs) para Azure Cognitive Search. Um serviço de pesquisa pode ser a provisionado nestes níveis: Gratuito, Básico e Standard. A standard está disponível em várias configurações de recursos e níveis de capacidade.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 320f02f6ece106b4d0e14293f95533aa5b4e0743
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693456"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Escolha um nível de preços para Azure Cognitive Search

Parte da [criação](search-create-service-portal.md) de um serviço de pesquisa significa escolher um nível de preços (ou SKU) que seja fixado para o tempo de vida do serviço. Os preços - ou o custo mensal estimado de execução do serviço - são apresentados na página **Select Pricing Tier** do portal quando cria o serviço. Se estiver a provisões através do PowerShell ou do Azure CLI, o nível é especificado através do **`-Sku`** parâmetro, e deve verificar [os preços do serviço](https://azure.microsoft.com/pricing/details/search/) para saber mais sobre os custos estimados.

O nível selecionado determina:

+ Número máximo de índices e outros objetos permitidos no serviço
+ Tamanho e velocidade das divisórias (armazenamento físico)
+ Taxa faturada como um custo mensal fixo, mas também um custo incremental se adicionar capacidade

Em alguns casos, o nível que escolhe determina a disponibilidade de [funcionalidades premium](#premium-features).

> [!NOTE]
> À procura de informações sobre "Azure SKUs"? Comece com [os preços do Azure](https://azure.microsoft.com/pricing/) e, em seguida, desloque-se para baixo para links para páginas de preços por serviço.

## <a name="tier-descriptions"></a>Descrições de nível

Os níveis incluem **Free,** **Basic,** **Standard** e **Storage Optimized**. Standard e Storage Optimized estão disponíveis com várias configurações e capacidades. A imagem que se segue do portal Azure mostra os níveis disponíveis, menos os preços (que pode encontrar no portal e na [página de preços](https://azure.microsoft.com/pricing/details/search/). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Tabela de preços" border="true":::

**O free** cria um serviço de pesquisa limitado para projetos mais pequenos, como executar tutoriais e amostras de código. Internamente, os recursos do sistema são partilhados entre vários subscritores. Não é possível escalar um serviço gratuito ou executar cargas de trabalho significativas.

**Basic** e **Standard** são os níveis de faturação mais utilizados, sendo a **Standard** o padrão porque lhe dá mais flexibilidade na escala para cargas de trabalho. Com recursos dedicados sob o seu controlo, pode implementar projetos maiores, otimizar o desempenho e aumentar a capacidade.

Alguns níveis são projetados para certos tipos de trabalho. Por exemplo, **Standard 3 High Density (S3 HD)** é um *modo de hospedagem* para S3, onde o hardware subjacente é otimizado para um grande número de índices menores e destina-se a cenários de multitenância. O S3 HD tem a mesma carga por unidade que o S3, mas o hardware é otimizado para leituras rápidas de ficheiros num grande número de índices menores.

Os níveis **otimizados de armazenamento** oferecem uma maior capacidade de armazenamento a um preço mais baixo por TB do que os níveis Standard. A troca primária é uma maior latência de consulta, que deve validar para os seus requisitos específicos de aplicação. Para saber mais sobre as considerações de desempenho deste nível, consulte as considerações de [Desempenho e otimização.](search-performance-optimization.md)

Pode saber mais sobre os vários níveis na [página de preços,](https://azure.microsoft.com/pricing/details/search/)nos limites de Serviço no artigo [Azure Cognitive Search](search-limits-quotas-capacity.md) e na página do portal quando estiver a prestar um serviço.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Disponibilidade de recursos por nível

A maioria das funcionalidades estão disponíveis em todos os níveis, incluindo o nível livre. Em alguns casos, o nível que escolher terá impacto na sua capacidade de implementar uma funcionalidade. A tabela a seguir descreve os constrangimentos de funcionalidades que estão relacionados com o nível de serviço.

| Funcionalidade | Limitações |
|---------|-------------|
| [indexadores](search-indexer-overview.md) | Os indexantes não estão disponíveis no S3 HD.  |
| [Melhoramento de IA](search-security-manage-encryption-keys.md) | Funciona no nível Livre, mas não é recomendado. |
| [Identidades geridas ou fidedignas para acesso de saída (indexante)](search-howto-managed-identities-data-sources.md) | Não disponível no nível Livre.|
| [Chaves de encriptação geridas pelo cliente](search-security-manage-encryption-keys.md) | Não disponível no nível Livre. |
| [Acesso a firewall IP](service-configure-firewall.md) | Não disponível no nível Livre. |
| [Ponto final privado (integração com a Azure Private Link)](service-create-private-endpoint.md) | Para ligações de entrada a um serviço de pesquisa, não está disponível no nível Gratuito. Para ligações de saída por indexadores a outros recursos Azure, não disponível em Free ou S3 HD. Para indexadores que usam skillsets, não disponíveis em Free, Basic, S1 ou S3 HD.|

As características intensivas em recursos podem não funcionar bem a menos que lhe dê capacidade suficiente. Por exemplo, [o enriquecimento](cognitive-search-concept-intro.md) de IA tem habilidades de longa duração que o tempo de serviço livre, a menos que o conjunto de dados seja pequeno.

## <a name="upper-limits"></a>Limites superiores

Os níveis determinam o armazenamento máximo do próprio serviço, bem como o número máximo de índices, indexadores, fontes de dados, skillsets e mapas de sinónimo que pode criar. Para uma fuga completa de todos os limites, consulte os limites de [serviço na Pesquisa Cognitiva Azure](search-limits-quotas-capacity.md). 

## <a name="partition-size-and-speed"></a>Tamanho e velocidade da partição

Os preços de nível incluem detalhes sobre o armazenamento por partição que varia entre 2 GB para níveis básicos, até 2 TB para armazenamento otimizado (L2). Outras características do hardware, como a velocidade de operações, latência e taxas de transferência, não são publicadas, mas os níveis que são projetados para arquiteturas de soluções específicas são construídos em hardware que tem as funcionalidades para suportar esses cenários. Para obter mais informações sobre divisórias, consulte [Estimativa e gerir capacidade](search-capacity-planning.md) e Escala para [desempenho.](search-performance-optimization.md)

## <a name="billing-rates"></a>Taxas de faturação

Os níveis têm taxas de faturação diferentes, com taxas mais altas para níveis que funcionam com hardware mais caro ou fornecem funcionalidades mais caras. A taxa de faturação por nível pode ser encontrada nas páginas de preços do [Azure](https://azure.microsoft.com/pricing/details/search/) para a Pesquisa Cognitiva Azure.

Uma vez criado um serviço, a taxa de faturação torna-se simultaneamente um *custo fixo* de funcionamento do serviço 24 horas por dia, e um *custo incremental* se optar por adicionar mais capacidade.

Os serviços de pesquisa são atribuídos recursos informáticos sob a forma de *divisórias* (para armazenamento) e *réplicas* (instâncias do motor de consulta). Inicialmente, um serviço é criado com um de cada, e a taxa de faturação é inclusiva de ambos os recursos. No entanto, se aumentar a capacidade, os custos sobem ou descem em incrementos da taxa de faturação.

O exemplo a seguir fornece uma ilustração. Assuma uma taxa de faturação hipotética de $100 por mês. Se mantiver o serviço de pesquisa na sua capacidade inicial de uma partição e uma réplica, então $100 é o que pode esperar pagar no final do mês. No entanto, se adicionar mais duas réplicas para obter alta disponibilidade, a fatura mensal aumenta para $300 ($100 para o primeiro par de partição de réplica, seguido de $200 para as duas réplicas).

Este modelo de preços baseia-se no conceito de aplicação da taxa de faturação às *unidades de pesquisa* de números (SU) utilizadas por um serviço de pesquisa. Todos os serviços são inicialmente a provisionados num SU, mas pode aumentar as SUs adicionando divisórias ou réplicas para lidar com cargas de trabalho maiores. Para mais informações, consulte [Como estimar os custos de um serviço de pesquisa.](search-sku-manage-costs.md)

## <a name="next-steps"></a>Passos seguintes

A melhor maneira de escolher um nível de preços é começar com um nível de menor custo, e depois permitir que a experiência e teste informem a sua decisão de manter o serviço ou criar um novo num nível mais alto. Para os próximos passos, recomendamos que crie um serviço de pesquisa num nível que possa acomodar o nível de teste que se propõe fazer e, em seguida, rever as seguintes orientações para recomendações sobre a estimativa de custo e capacidade.

+ [Criar um serviço de pesquisa](search-create-service-portal.md)
+ [Cálculo de custos](search-sku-manage-costs.md)
+ [Capacidade de estimativa](search-sku-manage-costs.md)