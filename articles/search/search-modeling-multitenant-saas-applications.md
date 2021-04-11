---
title: Isolamento multitenancy e conteúdo
titleSuffix: Azure Cognitive Search
description: Saiba mais sobre padrões de design comuns para aplicações SaaS multitenantes enquanto utiliza a Pesquisa Cognitiva Azure.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 7833dcf8fbe2b6460346310a4d094c7bb5d606c4
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581581"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Padrões de design para aplicações SaaS multitenant e Pesquisa Cognitiva Azure

Uma aplicação multitenant é aquela que fornece os mesmos serviços e capacidades a qualquer número de inquilinos que não possam ver ou partilhar os dados de qualquer outro inquilino. Este documento discute estratégias de isolamento de inquilinos para aplicações multitenantes construídas com Azure Cognitive Search.

## <a name="azure-cognitive-search-concepts"></a>Conceitos de Pesquisa Cognitiva Azure

Como solução de pesquisa como serviço, [a Azure Cognitive Search](search-what-is-azure-search.md) permite que os desenvolvedores adicionem experiências de pesquisa ricas a aplicações sem gerir qualquer infraestrutura ou se tornar um especialista em recuperação de informação. Os dados são enviados para o serviço e depois armazenados na nuvem. Utilizando pedidos simples para a Azure Cognitive Search API, os dados podem então ser modificados e pesquisados. 

### <a name="search-services-indexes-fields-and-documents"></a>Serviços de pesquisa, índices, campos e documentos

Antes de discutir padrões de design, é importante entender alguns conceitos básicos.

Ao utilizar a Azure Cognitive Search, subscreva-se um *serviço de pesquisa*. À medida que os dados são enviados para a Azure Cognitive Search, é armazenado num *índice* dentro do serviço de pesquisa. Pode haver uma série de índices dentro de um único serviço. Para utilizar os conceitos familiares de bases de dados, o serviço de pesquisa pode ser comparado a uma base de dados enquanto os índices dentro de um serviço podem ser comparados a tabelas dentro de uma base de dados.

Cada índice dentro de um serviço de pesquisa tem o seu próprio esquema, que é definido por uma série de *campos* personalizáveis . Os dados são adicionados a um índice de Pesquisa Cognitiva Azure sob a forma de *documentos* individuais. Cada documento deve ser enviado para um determinado índice e deve adequar-se ao esquema desse índice. Ao pesquisar dados usando Azure Cognitive Search, as consultas de pesquisa de texto completo são emitidas contra um determinado índice.  Para comparar estes conceitos com os de uma base de dados, os campos podem ser comparados a colunas numa tabela e os documentos podem ser comparados a linhas.

### <a name="scalability"></a>Escalabilidade

Qualquer serviço de Pesquisa Cognitiva Azure no [nível de preços](https://azure.microsoft.com/pricing/details/search/) Standard pode escalar em duas dimensões: armazenamento e disponibilidade.

+ Podem ser *adicionadas divisórias* para aumentar o armazenamento de um serviço de pesquisa.
+ *As réplicas* podem ser adicionadas a um serviço para aumentar o rendimento dos pedidos que um serviço de pesquisa pode lidar.

A adição e remoção de divisórias e réplicas permitirá que a capacidade do serviço de pesquisa cresça com a quantidade de dados e tráfego que a aplicação exige. Para que um serviço de pesquisa consiga um [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)lido, requer duas réplicas. Para que um serviço consiga um [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)de leitura, requer três réplicas.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Limites de serviço e índice na Pesquisa Cognitiva Azure

Existem alguns [níveis de preços diferentes](https://azure.microsoft.com/pricing/details/search/) na Pesquisa Cognitiva Azure, cada um dos níveis tem [limites e quotas diferentes.](search-limits-quotas-capacity.md) Alguns destes limites estão ao nível do serviço, alguns estão ao nível do índice, e alguns estão ao nível da partição.

|  | Básico | Norma1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| **Réplicas máximas por serviço** |3 |12 |12 |12 |12 |
| **Partições máximas por serviço** |1 |12 |12 |12 |3 |
| **Unidades de Pesquisa Máxima (Réplicas*Partições) por Serviço** |3 |36 |36 |36 |36 (máx. 3 divisórias) |
| **Armazenamento máximo por Serviço** |2 GB |300 GB |1.2 TB |2.4 TB |600 GB |
| **Armazenamento máximo por partição** |2 GB |25 GB |100 GB |200 GB |200 GB |
| **Índices Máximos por Serviço** |5 |50 |200 |200 |3000 (máximo 1000 índices/partição) |

#### <a name="s3-high-density"></a>S3 Alta Densidade

No nível de preços S3 da Azure Cognitive Search, existe uma opção para o modo de Alta Densidade (HD) projetado especificamente para cenários multitenntes. Em muitos casos, é necessário apoiar um grande número de inquilinos menores ao abrigo de um único serviço para alcançar os benefícios da simplicidade e da eficiência de custos.

O S3 HD permite que os muitos pequenos índices sejam embalados sob a gestão de um único serviço de pesquisa, negociando a capacidade de escalar índices usando divisórias para a capacidade de hospedar mais índices num único serviço.

Um serviço S3 foi concebido para acolher um número fixo de índices (máximo 200) e permitir que cada índice se escasseia horizontalmente à medida que novas divisórias são adicionadas ao serviço. A adição de divisórias aos serviços S3 HD aumenta o número máximo de índices que o serviço pode acolher. O tamanho máximo ideal para um índice S3HD individual é de cerca de 50 - 80 GB, embora não exista um limite de tamanho rígido em cada índice imposto pelo sistema.

## <a name="considerations-for-multitenant-applications"></a>Considerações para aplicações multitenantes

As aplicações multitenantes devem efetivamente distribuir recursos entre os inquilinos, preservando simultaneamente algum nível de privacidade entre os vários inquilinos. Existem algumas considerações ao conceber a arquitetura para tal aplicação:

+ *Isolamento do inquilino:* Os desenvolvedores de aplicações devem tomar as medidas adequadas para garantir que nenhum inquilino tenha acesso não autorizado ou indesejado aos dados de outros inquilinos. Para além da perspetiva da privacidade dos dados, as estratégias de isolamento dos inquilinos requerem uma gestão eficaz dos recursos partilhados e a proteção dos vizinhos barulhentos.

+ *Custo do recurso em nuvem:* Como em qualquer outra aplicação, as soluções de software devem permanecer competitivas em termos de custos como componente de uma aplicação multitenant.

+ *Facilidade de Operações:* Ao desenvolver uma arquitetura multitenant, o impacto nas operações e complexidade da aplicação é uma consideração importante. A Azure Cognitive Search tem um [SLA de 99,9%.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)

+ *Pegada global:* As aplicações multitenantes podem ter de servir eficazmente os inquilinos que são distribuídos por todo o mundo.

+ *Escalabilidade:* Os promotores de aplicações devem considerar como se conciliam entre manter um nível suficientemente baixo de complexidade de aplicações e conceber a aplicação à escala com o número de inquilinos e a dimensão dos dados e carga de trabalho dos inquilinos.

A Azure Cognitive Search oferece alguns limites que podem ser usados para isolar os dados dos inquilinos e a carga de trabalho.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Modelação multitenancy com Pesquisa Cognitiva Azure

No caso de um cenário multitenante, o desenvolvedor de aplicações consome um ou mais serviços de pesquisa e divide os seus inquilinos entre serviços, índices ou ambos. A Azure Cognitive Search tem alguns padrões comuns ao modelar um cenário multitenante:

+ *Um índice por inquilino:* Cada inquilino tem o seu próprio índice dentro de um serviço de pesquisa que é partilhado com outros inquilinos.

+ *Um serviço por inquilino:* Cada inquilino tem o seu próprio serviço dedicado de Pesquisa Cognitiva Azure, oferecendo o mais alto nível de dados e separação da carga de trabalho.

+ *Mistura de ambos:* Os inquilinos maiores e mais ativos são atribuídos serviços dedicados, enquanto os inquilinos mais pequenos são atribuídos índices individuais dentro dos serviços partilhados.

## <a name="model-1-one-index-per-tenant"></a>Modelo 1: Um índice por inquilino

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png" alt-text="Um retrato do modelo index-per-inquilino" border="false":::

Num modelo de índice por inquilino, vários inquilinos ocupam um único serviço de Pesquisa Cognitiva Azure onde cada inquilino tem o seu próprio índice.

Os inquilinos conseguem o isolamento de dados porque todos os pedidos de pesquisa e operações documentais são emitidos a um nível de índice na Pesquisa Cognitiva Azure. Na camada de aplicação, há a necessidade de sensibilização para direcionar o tráfego dos vários inquilinos para os índices adequados, ao mesmo tempo que gere recursos ao nível do serviço em todos os inquilinos.

Um atributo-chave do modelo index-per-inquilino é a capacidade para o desenvolvedor de aplicações sobrescrever a capacidade de um serviço de pesquisa entre os inquilinos da aplicação. Se os inquilinos tiverem uma distribuição desigual da carga de trabalho, a combinação ideal de inquilinos pode ser distribuída através dos índices de um serviço de pesquisa para acomodar um número de inquilinos altamente ativos e intensivos de recursos, ao mesmo tempo que servem uma longa cauda de inquilinos menos ativos. A contrapartida é a incapacidade do modelo para lidar com situações em que cada inquilino é simultaneamente altamente ativo.

O modelo index-per-tenant fornece a base para um modelo de custo variável, onde todo um serviço de Pesquisa Cognitiva Azure é comprado adiantado e, em seguida, preenchido com inquilinos. Isto permite que a capacidade não utilizada seja designada para ensaios e contas gratuitas.

Para aplicações com uma pegada global, o modelo index-per-inquilino pode não ser o mais eficiente. Se os inquilinos de uma aplicação forem distribuídos por todo o mundo, pode ser necessário um serviço separado para cada região que possa duplicar os custos em cada um deles.

A Azure Cognitive Search permite que a escala dos índices individuais e o número total de índices cresçam. Se for escolhido um nível de preços adequado, podem ser adicionadas divisórias e réplicas a todo o serviço de pesquisa quando um índice individual dentro do serviço cresce demasiado grande em termos de armazenamento ou tráfego.

Se o número total de índices crescer demasiado grande para um único serviço, outro serviço tem de ser prestado para acomodar os novos inquilinos. Se os índices tiverem de ser movidos entre os serviços de pesquisa à medida que novos serviços são adicionados, os dados do índice têm de ser copiados manualmente de um índice para o outro, uma vez que a Azure Cognitive Search não permite que um índice seja movido.

## <a name="model-2-once-service-per-tenant"></a>Modelo 2: Uma vez serviço por inquilino

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png" alt-text="Um retrato do modelo de serviço por inquilino" border="false":::

Numa arquitetura de serviço por inquilino, cada inquilino tem o seu próprio serviço de pesquisa.

Neste modelo, a aplicação atinge o nível máximo de isolamento para os seus inquilinos. Cada serviço tem armazenamento e produção dedicados para lidar com o pedido de pesquisa, bem como chaves API separadas.

Para aplicações em que cada inquilino tem uma grande pegada ou a carga de trabalho tem pouca variabilidade de inquilino para inquilino, o modelo de serviço por inquilino é uma escolha eficaz, uma vez que os recursos não são partilhados em várias cargas de trabalho dos inquilinos.

Um modelo de serviço por arrendatário também oferece o benefício de um modelo de custo fixo previsível. Não há investimento adiantado em todo um serviço de pesquisa até que haja um inquilino para preenchê-lo, no entanto o custo por inquilino é maior do que um modelo de índice por inquilino.

O modelo de serviço por inquilino é uma escolha eficiente para aplicações com uma pegada global. Com inquilinos geograficamente distribuídos, é fácil ter o serviço de cada inquilino na região apropriada.

Os desafios na escala deste padrão surgem quando os inquilinos individuais superam o seu serviço. A Azure Cognitive Search não suporta atualmente a atualização do nível de preços de um serviço de pesquisa, pelo que todos os dados teriam de ser copiados manualmente para um novo serviço.

## <a name="model-3-hybrid"></a>Modelo 3: Híbrido

Outro padrão para modelar a multitenancy é misturar tanto estratégias de índice por inquilino como de serviço por inquilino.

Ao misturar os dois padrões, os maiores inquilinos de uma aplicação podem ocupar serviços dedicados enquanto a longa cauda de inquilinos menos ativos e menores pode ocupar índices em um serviço compartilhado. Este modelo garante que os maiores inquilinos têm um desempenho consistentemente elevado do serviço, ao mesmo tempo que ajudam a proteger os inquilinos menores de quaisquer vizinhos barulhentos.

No entanto, a implementação desta estratégia baseia-se na previsão de quais os inquilinos que exigirão um serviço dedicado contra um índice num serviço partilhado. A complexidade da aplicação aumenta com a necessidade de gerir ambos estes modelos multitenancy.

## <a name="achieving-even-finer-granularity"></a>Alcançar granularidade ainda mais fina

Os padrões de design acima para modelar cenários multitenantes na Azure Cognitive Search assumem um âmbito uniforme onde cada inquilino é um exemplo de uma aplicação. No entanto, as aplicações podem, por vezes, lidar com muitos âmbitos menores.

Se os modelos de serviço por inquilino e índice por inquilino não forem suficientemente pequenos, é possível modelar um índice para atingir um grau ainda mais fino de granularidade.

Para ter um único índice a comportar-se de forma diferente para diferentes pontos finais do cliente, um campo pode ser adicionado a um índice que designa um determinado valor para cada cliente possível. Cada vez que um cliente chama Azure Cognitive Search para consultar ou modificar um índice, o código da aplicação do cliente especifica o valor adequado para esse campo usando a capacidade de [filtro](./query-odata-filter-orderby-syntax.md) da Azure Cognitive Search no momento da consulta.

Este método pode ser usado para obter funcionalidades de contas de utilizador separadas, níveis de permissão separados e até mesmo aplicações completamente separadas.

> [!NOTE]
> A utilização da abordagem acima descrita para configurar um único índice para servir vários inquilinos afeta a relevância dos resultados da pesquisa. As pontuações de relevância de pesquisa são calculadas num âmbito de nível de índice, não num âmbito ao nível do inquilino, pelo que todos os dados dos inquilinos são incorporados nas estatísticas subjacentes das pontuações de relevância, tais como a frequência de prazos.
>

## <a name="next-steps"></a>Passos seguintes

A Azure Cognitive Search é uma escolha convincente para muitas aplicações. Ao avaliar os vários padrões de design para aplicações multitenantes, considere os [vários níveis de preços](https://azure.microsoft.com/pricing/details/search/) e os [respetivos limites](search-limits-quotas-capacity.md) de serviço para melhor adaptar a Azure Cognitive Search para se adaptar às cargas de trabalho de aplicações e arquiteturas de todos os tamanhos.