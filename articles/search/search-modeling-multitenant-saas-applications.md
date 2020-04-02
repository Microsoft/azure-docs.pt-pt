---
title: Isolamento multimédia e conteúdo
titleSuffix: Azure Cognitive Search
description: Conheça padrões comuns de design para aplicações SaaS multiarrendatárias enquanto utiliza a Pesquisa Cognitiva Azure.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d8e453336005f3389f67e9571fac438bfc340c1b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549017"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Padrões de design para aplicações SaaS multiarrendatárias e pesquisa cognitiva Azure
Uma aplicação multiarrendatária é aquela que fornece os mesmos serviços e capacidades a qualquer número de inquilinos que não possam ver ou partilhar os dados de qualquer outro inquilino. Este documento discute estratégias de isolamento de inquilinos para aplicações multiarrendatárias construídas com pesquisa cognitiva Azure.

## <a name="azure-cognitive-search-concepts"></a>Conceitos de pesquisa cognitiva azure
Como uma solução de pesquisa como um serviço, a Azure Cognitive Search permite que os desenvolvedores adicionem experiências de pesquisa ricas a aplicações sem gerir qualquer infraestrutura ou tornar-se um especialista em recuperação de informação. Os dados são enviados para o serviço e depois armazenados na nuvem. Utilizando pedidos simples para a API de Pesquisa Cognitiva Azure, os dados podem então ser modificados e pesquisados. Neste [artigo](https://aka.ms/whatisazsearch)pode ser encontrada uma visão geral do serviço. Antes de discutir padrões de design, é importante entender alguns conceitos na Pesquisa Cognitiva Azure.

### <a name="search-services-indexes-fields-and-documents"></a>Serviços de pesquisa, índices, campos e documentos
Ao utilizar a Pesquisa Cognitiva Azure, subscreve-se um serviço de *pesquisa*. À medida que os dados são enviados para a Pesquisa Cognitiva Azure, este é armazenado num *índice* dentro do serviço de pesquisa. Pode haver uma série de índices dentro de um único serviço. Para utilizar os conceitos familiares das bases de dados, o serviço de pesquisa pode ser comparado a uma base de dados enquanto os índices dentro de um serviço podem ser comparados a tabelas dentro de uma base de dados.

Cada índice dentro de um serviço de pesquisa tem o seu próprio esquema, que é definido por uma série de *campos*personalizáveis. Os dados são adicionados a um índice de Pesquisa Cognitiva Azure sob a forma de *documentos*individuais . Cada documento deve ser enviado para um determinado índice e deve caber no esquema desse índice. Ao pesquisar dados utilizando a Pesquisa Cognitiva Azure, as consultas de pesquisa de texto completo são emitidas contra um determinado índice.  Para comparar estes conceitos com os de uma base de dados, os campos podem ser comparados a colunas numa tabela e os documentos podem ser comparados a linhas.

### <a name="scalability"></a>Escalabilidade
Qualquer serviço de Pesquisa Cognitiva Azure no [nível](https://azure.microsoft.com/pricing/details/search/) de preços Standard pode escalar em duas dimensões: armazenamento e disponibilidade.

* *As divisórias* podem ser adicionadas para aumentar o armazenamento de um serviço de pesquisa.
* *As réplicas* podem ser adicionadas a um serviço para aumentar a entrada de pedidos que um serviço de pesquisa pode suportar.

A adição e remoção de divisórias e réplicas permitirão que a capacidade do serviço de pesquisa cresça com a quantidade de dados e tráfego que a aplicação exige. Para que um serviço de pesquisa consiga uma [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)de leitura, requer duas réplicas. Para que um serviço consiga um [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)de leitura, requer três réplicas.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Limites de serviço e índice na Pesquisa Cognitiva Azure
Existem [alguns níveis](https://azure.microsoft.com/pricing/details/search/) de preços diferentes na Pesquisa Cognitiva Azure, cada um dos níveis tem [diferentes limites e quotas.](search-limits-quotas-capacity.md) Alguns destes limites estão ao nível do serviço, alguns estão ao nível do índice, e alguns estão ao nível da partição.

|  | Básico | Standard1 | Padrão2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Réplicas máximas por serviço |3 |12 |12 |12 |12 |
| Divisórias máximas por serviço |1 |12 |12 |12 |3 |
| Unidades de Pesquisa Máximas (Réplicas*Divisórias) por Serviço |3 |36 |36 |36 |36 (alumiver3) |
| Armazenamento Máximo por Serviço |2GB |300 GB |1.2 TB |2.4 TB |600 GB |
| Armazenamento máximo por partição |2GB |25 GB |100 GB |200 GB |200 GB |
| Índices Máximos por Serviço |5 |50 |200 |200 |3000 (máximo 1000 índices/partição) |

#### <a name="s3-high-density"></a>S3 Alta Densidade'
No nível de preços S3 da Azure Cognitive Search, existe uma opção para o modo de alta densidade (HD) projetado especificamente para cenários multiarrendatários. Em muitos casos, é necessário apoiar um grande número de inquilinos menores ao abrigo de um único serviço para alcançar os benefícios da simplicidade e da eficiência dos custos.

O S3 HD permite que os muitos pequenos índices sejam embalados sob a gestão de um único serviço de pesquisa, negociando a capacidade de escalar índices usando divisórias para a capacidade de hospedar mais índices num único serviço.

Um serviço S3 foi concebido para acolher um número fixo de índices (máximo 200) e permitir que cada índice escalasse em tamanho horizontalmente à medida que novas divisórias são adicionadas ao serviço. Adicionar divisórias aos serviços S3 HD aumenta o número máximo de índices que o serviço pode acolher. O tamanho máximo ideal para um índice S3HD individual é de cerca de 50 - 80 GB, embora não exista um limite de tamanho duro em cada índice imposto pelo sistema.

## <a name="considerations-for-multitenant-applications"></a>Considerações para aplicações multiarrendatárias
As aplicações multiarrendatárias devem efetivamente distribuir recursos entre os inquilinos, preservando ao mesmo tempo algum nível de privacidade entre os vários inquilinos. Existem algumas considerações ao conceber a arquitetura para tal aplicação:

* *Isolamento do inquilino:* Os desenvolvedores de aplicações devem tomar as medidas adequadas para garantir que nenhum inquilino tenha acesso não autorizado ou indesejado aos dados de outros inquilinos. Para além da perspetiva da privacidade dos dados, as estratégias de isolamento dos inquilinos requerem uma gestão eficaz dos recursos partilhados e proteção dos vizinhos barulhentos.
* *Custo do recurso em nuvem:* Como em qualquer outra aplicação, as soluções de software devem permanecer competitivas em termos de custos como componente de uma aplicação multiarrendatária.
* *Facilidade de Operações:* Ao desenvolver uma arquitetura multiarrendatária, o impacto nas operações e complexidade da aplicação é uma consideração importante. A Pesquisa Cognitiva Azure tem um SLA de [99,9%.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
* *Pegada global:* As aplicações multiarrendatárias podem precisar de servir efetivamente os inquilinos que são distribuídos por todo o mundo.
* *Escalabilidade:* Os desenvolvedores de aplicações devem considerar como se reconciliam entre manter um nível de complexidade de aplicação suficientemente baixo e conceber a aplicação à escala com o número de inquilinos e a dimensão dos dados dos inquilinos e da carga de trabalho.

A Azure Cognitive Search oferece alguns limites que podem ser usados para isolar os dados dos inquilinos e a carga de trabalho.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Modelação multitenancy com pesquisa cognitiva azure
No caso de um cenário multiarrendatário, o desenvolvedor de aplicações consome um ou mais serviços de pesquisa e divide os seus inquilinos entre serviços, índices ou ambos. A Pesquisa Cognitiva Azure tem alguns padrões comuns ao modelar um cenário multiarrendatário:

1. *Índice por inquilino:* Cada inquilino tem o seu próprio índice dentro de um serviço de pesquisa que é partilhado com outros inquilinos.
2. *Serviço por inquilino:* Cada inquilino tem o seu próprio serviço dedicado de Pesquisa Cognitiva Azure, oferecendo o mais alto nível de data e separação de carga de trabalho.
3. *Mistura de ambos:* Os inquilinos maiores e mais ativos são atribuídos serviços dedicados, enquanto os inquilinos mais pequenos são atribuídos índices individuais dentro de serviços partilhados.

## <a name="1-index-per-tenant"></a>1. Índice por inquilino
![Um retrato do modelo index-per-inquilino](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Num modelo index-per-inquilino, vários inquilinos ocupam um único serviço de Pesquisa Cognitiva Azure onde cada inquilino tem o seu próprio índice.

Os inquilinos obtêm isolamento de dados porque todos os pedidos de pesquisa e operações documentais são emitidos a um nível de índice em Pesquisa Cognitiva Azure. Na camada de candidatura, é necessário sensibilizar os vários inquilinos para os índices adequados, gerindo também recursos ao nível do serviço em todos os inquilinos.

Um atributo-chave do modelo index-per-inquilino é a capacidade de o desenvolvedor de aplicações sobrepor a capacidade de um serviço de pesquisa entre os inquilinos da aplicação. Se os inquilinos tiverem uma distribuição desigual da carga de trabalho, a combinação ideal de inquilinos pode ser distribuída através dos índices de um serviço de pesquisa para acomodar uma série de inquilinos altamente ativos e intensivos de recursos, ao mesmo tempo que servem uma longa cauda de inquilinos menos ativos. A compensação é a incapacidade do modelo para lidar com situações em que cada inquilino é simultaneamente altamente ativo.

O modelo index-per-tenant fornece a base para um modelo de custo variável, onde todo um serviço de Pesquisa Cognitiva Azure é comprado antecipadamente e, em seguida, preenchido com inquilinos. Isto permite que a capacidade não utilizada seja designada para ensaios e contas gratuitas.

Para aplicações com pegada global, o modelo index-per-tenant pode não ser o mais eficiente. Se os inquilinos de uma aplicação forem distribuídos por todo o mundo, pode ser necessário um serviço separado para cada região que possa duplicar os custos em cada um deles.

A Pesquisa Cognitiva Azure permite que a escala dos índices individuais e o número total de índices cresçam. Se for escolhido um nível de preços adequado, as divisórias e réplicas podem ser adicionadas a todo o serviço de pesquisa quando um índice individual dentro do serviço cresce demasiado grande em termos de armazenamento ou tráfego.

Se o número total de índices crescer demasiado para um único serviço, outro serviço tem de ser prestado para acomodar os novos inquilinos. Se os índices tiverem de ser movidos entre os serviços de pesquisa à medida que os novos serviços são adicionados, os dados do índice têm de ser copiados manualmente de um índice para o outro, uma vez que a Pesquisa Cognitiva Azure não permite que um índice seja movido.

## <a name="2-service-per-tenant"></a>2. Serviço por inquilino
![Um retrato do modelo de serviço por inquilino](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Numa arquitetura de serviço por inquilino, cada inquilino tem o seu próprio serviço de pesquisa.

Neste modelo, a aplicação atinge o nível máximo de isolamento para os seus inquilinos. Cada serviço tem armazenamento e entrada dedicados para manusear pedido de pesquisa, bem como chaves API separadas.

Para aplicações em que cada inquilino tem uma grande pegada ou a carga de trabalho tem pouca variabilidade de inquilino para inquilino, o modelo de serviço por inquilino é uma escolha eficaz, uma vez que os recursos não são partilhados entre as cargas de trabalho de vários inquilinos.

Um modelo de serviço por inquilino também oferece o benefício de um modelo de custo fixo previsível. Não há investimento antecipado em todo um serviço de pesquisa até que haja um inquilino para o preencher, no entanto o custo por inquilino é superior a um modelo index-per-inquilino.

O modelo de serviço por inquilino é uma escolha eficiente para aplicações com uma pegada global. Com inquilinos geograficamente distribuídos, é fácil ter o serviço de cada inquilino na região apropriada.

Os desafios na escalação deste padrão surgem quando os inquilinos individuais superam o seu serviço. A Azure Cognitive Search não suporta atualmente a atualização do nível de preços de um serviço de pesquisa, pelo que todos os dados teriam de ser copiados manualmente para um novo serviço.

## <a name="3-mixing-both-models"></a>3. Misturar ambos os modelos
Outro padrão para modelar multitenancy é misturar estratégias de index-per-inquilino e de serviço por inquilino.

Ao misturar os dois padrões, os maiores inquilinos de uma aplicação podem ocupar serviços dedicados, enquanto a longa cauda de inquilinos menos ativos e menores pode ocupar índices num serviço partilhado. Este modelo garante que os maiores inquilinos têm consistentemente um alto desempenho do serviço, ao mesmo tempo que ajudam a proteger os inquilinos mais pequenos de quaisquer vizinhos barulhentos.

No entanto, a implementação desta estratégia baseia-se na previsão de quais os inquilinos que exigirão um serviço dedicado contra um índice num serviço partilhado. A complexidade da aplicação aumenta com a necessidade de gerir ambos estes modelos multitenuantes.

## <a name="achieving-even-finer-granularity"></a>Alcançando ainda mais fina granularidade
Os padrões de design acima para modelar cenários multiarrendatários em Azure Cognitive Search assumem um âmbito uniforme onde cada inquilino é um exemplo de uma aplicação. No entanto, as aplicações podem, por vezes, lidar com muitos âmbitos menores.

Se os modelos de serviço por inquilino e index-per-inquilino não forem suficientemente pequenos, é possível modelar um índice para atingir um grau de granularidade ainda mais fino.

Para ter um único índice a comportar-se de forma diferente para diferentes pontos finais do cliente, um campo pode ser adicionado a um índice que designa um determinado valor para cada possível cliente. Cada vez que um cliente chama Azure Cognitive Search para consultar ou modificar um índice, o código da aplicação do cliente especifica o valor apropriado para esse campo usando a capacidade de [filtro](https://msdn.microsoft.com/library/azure/dn798921.aspx) da Pesquisa Cognitiva Azure no momento da consulta.

Este método pode ser utilizado para obter funcionalidades de contas separadas de utilizadores, níveis de permissão separados e até aplicações completamente separadas.

> [!NOTE]
> A utilização da abordagem acima descrita para configurar um único índice para servir vários inquilinos afeta a relevância dos resultados da pesquisa. As pontuações de relevância da pesquisa são calculadas num âmbito de nível de índice, não num âmbito de nível de inquilino, pelo que todos os dados dos inquilinos são incorporados nas estatísticas subjacentes das notas de relevância, tais como frequência de prazo.
> 
> 

## <a name="next-steps"></a>Passos seguintes
A Pesquisa Cognitiva Azure é uma escolha convincente para muitas aplicações. Ao avaliar os vários padrões de design para aplicações multiarrendatárias, considere os [vários níveis](https://azure.microsoft.com/pricing/details/search/) de preços e os [respetivos limites](search-limits-quotas-capacity.md) de serviço para o melhor alfaiate Azure Cognitive Search para encaixar as cargas de trabalho e arquiteturas de aplicações de todos os tamanhos.

Quaisquer questões sobre a Pesquisa Cognitiva Azure azuresearch_contact@microsoft.come cenários multiarrendatários podem ser direcionadas para .

