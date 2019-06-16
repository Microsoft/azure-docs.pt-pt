---
title: Modelagem de arquitetura "multitenancy" para isolamento de conteúdo num único serviço - Azure Search
description: Saiba mais sobre os padrões de design comuns para aplicações SaaS multi-inquilino, ao utilizar o Azure Search.
manager: jlembicz
author: LiamCavanagh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 58d7ca65a14f9f774b19796c9beae2a7c84102ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61288726"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Padrões de design para aplicações SaaS multi-inquilino e Azure Search
Uma aplicação multi-inquilino é aquele que fornece as mesmas serviços e recursos para qualquer número de inquilinos que não é possível ver ou partilhar os dados de qualquer outro inquilino. Este documento discute as estratégias de isolamento de inquilino para aplicações multi-inquilino criadas com o Azure Search.

## <a name="azure-search-concepts"></a>Conceitos de pesquisa do Azure
Como uma solução de pesquisa-como-serviço, o Azure Search permite aos desenvolvedores adicionar experiências de pesquisa avançadas às aplicações sem gerir qualquer infraestrutura ou se tornar um especialista na obtenção de informações. Dados são carregados para o serviço e, em seguida, armazenados na cloud. Com simples pedidos à API de pesquisa do Azure, os dados podem, em seguida, ser modificados e pesquisados. Uma descrição geral do serviço pode ser encontrada na [este artigo](https://aka.ms/whatisazsearch). Antes de discutir os padrões de design, é importante compreender alguns conceitos no Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Serviços de pesquisa, índices, campos e documentos
Ao utilizar o Azure Search, um assina um *serviço de pesquisa*. Como os dados serem carregados para o Azure Search, ele é armazenado numa *índice* dentro do serviço de pesquisa. Pode haver um número de índices num único serviço. Para utilizar os conceitos familiares de bases de dados, o serviço de pesquisa pode ser likened para uma base de dados enquanto os índices dentro de um serviço podem ser likened às tabelas numa base de dados.

Cada índice dentro de um serviço de pesquisa tem seu próprio esquema, o que é definida por um número de personalizável *campos*. Dados são adicionados a um índice da Azure Search na forma de indivíduo *documentos*. Cada documento tem de ser carregado para um índice específico e deverá ser colocado esquema desse índice. Ao pesquisar dados com o Azure Search, as consultas de pesquisa em texto completo emitidas em relação a um índice específico.  Para comparar esses conceitos de um banco de dados, campos podem ser likened às colunas numa tabela e documentos podem ser likened a linhas.

### <a name="scalability"></a>Escalabilidade
Qualquer serviço Azure Search no padrão [escalão de preço](https://azure.microsoft.com/pricing/details/search/) pode dimensionar em duas dimensões: armazenamento e disponibilidade.

* *Partições* podem ser adicionados para aumentar o armazenamento de um serviço de pesquisa.
* *Réplicas* podem ser adicionados a um serviço para aumentar o débito de pedidos que pode processar um serviço de pesquisa.

Adicionando e removendo as partições e réplicas, permitirá que a capacidade do serviço de pesquisa para crescer com a quantidade de dados e os pedidos de aplicações de tráfego. Para que um serviço de pesquisa obter uma leitura [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), ele requer duas réplicas. Para que um serviço obter uma leitura / escrita [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), ele requer três réplicas.

### <a name="service-and-index-limits-in-azure-search"></a>Limites de serviço e o índice na Azure Search
Há algumas diferentes [escalões de preço](https://azure.microsoft.com/pricing/details/search/) no Azure Search, cada uma das camadas tem diferentes [limites e quotas](search-limits-quotas-capacity.md). Alguns destes limites são ao nível do serviço, algumas são ao nível do índice e alguns são ao nível da partição.

|  | Básica | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Réplicas máximas por serviço |3 |12 |12 |12 |12 |
| Máximas partições por serviço |1 |12 |12 |12 |3 |
| Unidades de pesquisa máximo (réplicas * partições) por serviço |3 |36 |36 |36 |36 (máximas, 3, partições) |
| Armazenamento máximo por serviço |2 GB |300 GB |1.2 TB |2.4 TB |600 GB |
| Armazenamento máximo por partição |2 GB |25 GB |100 GB |200 GB |200 GB |
| Índices máximos por serviço |5 |50 |200 |200 |3000 (máximo 1000 índices/partição) |

#### <a name="s3-high-density"></a>S3 Alta densidade '
No escalão de preço S3 do Azure Search, existe uma opção para o modo de alta densidade (HD) desenvolvido especificamente para cenários multi-inquilino. Em muitos casos, é necessário suportar um grande número de inquilinos menores num único serviço a alcançar os benefícios da eficiência de simplicidade e o custo.

S3 HD permite que os índices vários pequenas ser empacotado em gestão de um serviço de pesquisa único, negociando a capacidade de aumentar horizontalmente índices com partições para a capacidade para alojar mais índices num único serviço.

Concretamente, um serviço de S3 poderia ter entre 1 e 200 índices que em conjunto, podem alojar até 1,4 milhares de milhões de documentos. Um S3 HD por outro lado permitiria índices individuais Ir apenas até 1 milhão de documentos, mas pode processar até 1000 índices por partição (até 3000 por serviço) com uma contagem de documentos total de 200 milhões por partição (até 600 milhões por serviço).

## <a name="considerations-for-multitenant-applications"></a>Considerações para aplicações multi-inquilino
Aplicações multi-inquilino com eficiência tem de distribuir recursos entre inquilinos, preservando a algum nível de privacidade, entre os vários inquilinos. Existem algumas considerações ao projetar a arquitetura para uma aplicação desse tipo:

* *Isolamento de inquilino:* Os desenvolvedores de aplicativos precisam tomar as medidas adequadas para se certificar de que não existem inquilinos tenham não autorizado ou indesejável acesso aos dados de outros inquilinos. Além da perspectiva de privacidade dos dados, estratégias de isolamento de inquilino, requerem um gerenciamento eficiente de recursos partilhados e proteção contra vizinhos ruidosos.
* *Custo de recursos de nuvem:* Tal como acontece com qualquer outro aplicativo, soluções de software tem de permanecer competitivo como um componente de aplicação multi-inquilino de custo.
* *Facilidade de operações:* Ao desenvolver uma arquitetura de multi-inquilino, o impacto sobre operações e a complexidade do aplicativo é uma consideração importante. O Azure Search tem um [SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Requisitos de espaço global:* Aplicações multi-inquilino poderá atender com eficiência os inquilinos que são distribuídos em todo o mundo.
* *Escalabilidade:* Os desenvolvedores de aplicativos precisam considerar como elas reconciliar entre manter um nível suficientemente baixo de complexidade de aplicativos e criar a aplicação para dimensionamento com o número de inquilinos e o tamanho dos dados e a carga de trabalho dos inquilinos.

O Azure Search oferece alguns limites que podem ser utilizados para isolar a carga de trabalho e dados dos inquilinos.

## <a name="modeling-multitenancy-with-azure-search"></a>Modelagem de arquitetura "multitenancy" com o Azure Search
No caso de um cenário de multi-inquilino, o desenvolvedor do aplicativo consome um ou mais serviços de pesquisa e dividir os respetivos inquilinos entre serviços, índices ou ambos. O Azure Search tem alguns padrões comuns ao modelar um cenário de multi-inquilino:

1. *Índice por inquilino:* Cada inquilino tem seu próprio índice dentro de um serviço de pesquisa que é partilhado com outros inquilinos.
2. *Serviço por inquilino:* Cada inquilino tem seu próprio serviço de Azure Search dedicado, oferecendo mais alto nível de separação de dados e a carga de trabalho.
3. *Combinação de ambos:* Inquilinos maiores e mais-Active Directory são atribuídos serviços dedicados, enquanto os inquilinos mais pequenos são atribuídos a índices individuais dentro de serviços compartilhados.

## <a name="1-index-per-tenant"></a>1. Índice por inquilino
![Um portrayal do modelo de índice por inquilino](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Um modelo de índice por inquilino, vários inquilinos ocupam um único serviço de Azure Search em que cada inquilino tem seu próprio índice.

Os inquilinos alcançar isolamento de dados, uma vez que todos os pedidos de pesquisa e operações de documento são emitidas num nível de índice no Azure Search. Na camada de aplicativo, há o reconhecimento da necessidade para direcionar o tráfego de vários dos inquilinos para os índices adequados ao gerir também a recursos no nível de serviço em todos os inquilinos.

Um atributo-chave do modelo de índice por inquilino é a capacidade para o desenvolvedor de aplicativos exceder a capacidade de um serviço de pesquisa entre inquilinos do aplicativo. Se os inquilinos têm uma distribuição desigual da carga de trabalho, a combinação ideal de inquilinos pode ser distribuída em índices de um serviço de pesquisa para acomodar um número de inquilinos altamente ativos, com muitos recursos enquanto processa simultaneamente uma cauda longa de menor inquilinos do Active Directory. A desvantagem é a incapacidade do modelo de lidar com situações em que cada inquilino em simultâneo é altamente ativo.

O modelo de índice por inquilino fornece a base para um modelo de custos de variável, em que um serviço de Azure Search completo é comprou inicial e, em seguida, subsequentemente, preenchido com inquilinos. Isto permite uma capacidade não utilizada ser designadas de contas gratuitas e de avaliação.

Para aplicações com uma presença global, o modelo de índice por inquilino pode não ser mais eficiente. Se os inquilinos de um aplicativo são distribuídos em todo o mundo, um serviço separado pode ser necessário para cada região que pode duplicar os custos em cada um deles.

O Azure Search permite que o dimensionamento de índices individuais e o número total de índices para crescer. Se um preço adequado camada é escolhida, réplicas e partições podem ser adicionadas para o serviço de pesquisa inteiro, quando um índice individual no serviço ficar demasiado grande em termos de armazenamento ou tráfego.

Se o número total de índices ficar demasiado grande para um único serviço, o outro serviço tem de ser aprovisionada para acomodar novos inquilinos. Se os índices têm de ser movidos entre os serviços de pesquisa à medida que são adicionados novos serviços, os dados do índice tem de ser copiado manualmente a partir de um índice para o outro como o Azure Search não permite que um índice de ser movidos.

## <a name="2-service-per-tenant"></a>2. Serviço por inquilino
![Um portrayal do modelo de serviço por inquilino](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Numa arquitetura de serviço por inquilino, a cada inquilino tem seu próprio serviço de pesquisa.

Nesse modelo, o aplicativo atinge o nível máximo de isolamento para os seus inquilinos. Cada serviço dedicou armazenamento e débito para manipular a solicitação de pesquisa, bem como as chaves de API separadas.

Para aplicações, onde cada inquilino tem um grande volume de memória ou a carga de trabalho tiver menos de um inquilino para o inquilino, o modelo de serviço por inquilino é uma opção em vigor como recursos não são partilhados entre cargas de trabalho de vários inquilinos.

Um serviço por modelo de inquilino também oferece o benefício de um modelo de custo fixa e previsível. Não há nenhum investimento inicial de um serviço de pesquisa inteiro até que haja um inquilino para preenchê-lo, no entanto, o custo por inquilino é maior do que um modelo de índice por inquilino.

O modelo de serviço por inquilino é uma opção eficiente para aplicações com uma presença global. Com inquilinos distribuídos geograficamente, é fácil para que o serviço de cada inquilino na região adequada.

Os desafios no dimensionamento esse padrão surgem quando o serviço ser excedida de inquilinos individuais. O Azure Search não suporta atualmente a atualizar o escalão de preço de um serviço de pesquisa, para que todos os dados tinha que ser copiado manualmente para um novo serviço.

## <a name="3-mixing-both-models"></a>3. Misturar ambos os modelos
Outro padrão para modelar a arquitetura "multitenancy" é mistura de estratégias de índice por inquilino e o serviço por inquilino.

Pela mistura dois padrões, inquilinos de maior de um aplicativo podem ocupam serviços dedicados, embora a cauda longa de inquilinos, menos ativos mais pequenos, pode ocupar a índices num serviço partilhado. Esse modelo garante que os inquilinos maiores têm consistentemente alto desempenho do serviço, ajudando a proteger os inquilinos mais pequenos de qualquer vizinhos ruidosos.

No entanto, implementar esta estratégia baseia-se percepção na previsão do que os inquilinos exigirá um serviço dedicado em comparação com um índice num serviço compartilhado. Aumenta a complexidade de aplicativos com a necessidade de gerir ambos esses modelos de arquitetura "multitenancy".

## <a name="achieving-even-finer-granularity"></a>Obter ainda mais granularidade
Os padrões de design acima para modelar cenários multi-inquilino no Azure Search partem do princípio de um âmbito uniforme, onde cada inquilino é uma instância de inteira de uma aplicação. No entanto, aplicativos, às vezes, podem processar vários âmbitos menores.

Se os modelos de serviço por inquilino e índice por inquilino não são suficientemente pequenos âmbitos, é possível modelar um índice para atingir um grau de granularidade ainda mais.

Para que um único índice que se comportam de forma diferente para pontos finais de cliente diferentes, um campo pode ser adicionado a um índice que designa um determinado valor para cada cliente possíveis. Sempre que um cliente chama o Azure Search para consultar ou modificar um índice, o código da aplicação cliente especifica o valor apropriado para esse campo com o Azure Search [filtro](https://msdn.microsoft.com/library/azure/dn798921.aspx) capacidade no momento da consulta.

Esse método pode ser utilizado para alcançar a funcionalidade de contas de utilizador em separado, níveis de permissão separada e aplicativos até mesmo completamente diferentes.

> [!NOTE]
> Usando a abordagem descrita acima configurar um único índice para servir vários inquilinos afeta a relevância dos resultados da pesquisa. Pontuação de relevância de pesquisa é calculadas num âmbito de nível de índice, não é um âmbito de nível de inquilino, para que os dados de todos os inquilinos são incorporados ao estatísticas subjacentes das pontuações relevância como a frequência de termo.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
O Azure Search é uma opção atraente para muitos aplicativos, [Leia mais sobre as capacidades robustas do serviço](https://aka.ms/whatisazsearch). Ao avaliar os vários padrões de design para aplicações multi-inquilino, considere a [vários escalões de preço](https://azure.microsoft.com/pricing/details/search/) e respetivos [limites de serviço](search-limits-quotas-capacity.md) para melhor ajustar o Azure Search, de acordo com as aplicações cargas de trabalho e arquiteturas de todos os tamanhos.

Dúvidas sobre os cenários de multi-inquilino e Azure Search podem ser direcionadas para azuresearch_contact@microsoft.com.

