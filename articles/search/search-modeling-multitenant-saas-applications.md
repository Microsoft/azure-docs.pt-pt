---
title: Modelando multilocação para isolamento de conteúdo em um Azure Search de serviço
description: Saiba mais sobre padrões de design comuns para aplicativos SaaS multilocatários ao usar Azure Search.
manager: jlembicz
author: LiamCavanagh
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: liamca
ms.openlocfilehash: b3e47fc0c46c638a51e6555ccbdc1885f081c149
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640552"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Padrões de design para aplicativos SaaS multilocatários e Azure Search
Um aplicativo multilocatário é aquele que fornece os mesmos serviços e funcionalidades para qualquer número de locatários que não podem ver ou compartilhar os dados de qualquer outro locatário. Este documento discute estratégias de isolamento de locatários para aplicativos multilocatários criados com Azure Search.

## <a name="azure-search-concepts"></a>Conceitos de Azure Search
Como uma solução de pesquisa como serviço, Azure Search permite aos desenvolvedores adicionar experiências de pesquisa avançada a aplicativos sem gerenciar qualquer infraestrutura ou se tornar um especialista em recuperação de informações. Os dados são carregados no serviço e, em seguida, armazenados na nuvem. Usando solicitações simples para a API de Azure Search, os dados podem ser modificados e pesquisados. Uma visão geral do serviço pode ser encontrada neste [artigo](https://aka.ms/whatisazsearch). Antes de discutir os padrões de design, é importante entender alguns conceitos em Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Pesquisar serviços, índices, campos e documentos
Ao usar Azure Search, um assina um serviço de *pesquisa*. À medida que os dados são carregados no Azure Search, eles são armazenados em um *índice* dentro do serviço de pesquisa. Pode haver vários índices em um único serviço. Para usar os conceitos familiares dos bancos de dados, o serviço de pesquisa pode ser comparados a um banco de dados, enquanto os índices dentro de um serviço podem ser comparadosdos para tabelas em um banco de dados.

Cada índice em um serviço de pesquisa tem seu próprio esquema, que é definido por um número de *campos*personalizáveis. Os dados são adicionados a um índice de Azure Search na forma de *documentos*individuais. Cada documento deve ser carregado para um índice específico e deve ajustar o esquema desse índice. Ao pesquisar dados usando Azure Search, as consultas de pesquisa de texto completo são emitidas em relação a um índice específico.  Para comparar esses conceitos com os de um banco de dados, os campos podem ser comparadosdos para colunas em uma tabela e os documentos podem ser comparadosdos em linhas.

### <a name="scalability"></a>Escalabilidade
Qualquer serviço de Azure Search no [tipo de preço](https://azure.microsoft.com/pricing/details/search/) Standard pode ser dimensionado em duas dimensões: armazenamento e disponibilidade.

* As *partições* podem ser adicionadas para aumentar o armazenamento de um serviço de pesquisa.
* As réplicas podem ser adicionadas a um serviço para aumentar a taxa de transferência de solicitações que um serviço de pesquisa pode manipular.

Adicionar e remover partições e réplicas em permitirá que a capacidade do serviço de pesquisa cresça com a quantidade de dados e o tráfego exigido pelo aplicativo. Para que um serviço de pesquisa obtenha um [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)de leitura, ele requer duas réplicas. Para que um serviço obtenha um [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)de leitura/gravação, ele requer três réplicas.

### <a name="service-and-index-limits-in-azure-search"></a>Limites de serviço e índice no Azure Search
Há alguns [tipos de preço](https://azure.microsoft.com/pricing/details/search/) diferentes em Azure Search, cada uma das camadas tem [limites e cotas](search-limits-quotas-capacity.md)diferentes. Alguns desses limites estão no nível de serviço, alguns estão no nível de índice e alguns estão no nível de partição.

|  | Básica | Standard1 | Standard2 | Standard3 | HD Standard3 |
| --- | --- | --- | --- | --- | --- |
| Máximo de réplicas por serviço |3 |12 |12 |12 |12 |
| Máximo de partições por serviço |1 |12 |12 |12 |3 |
| Máximo de unidades de pesquisa (réplicas * partições) por serviço |3 |36 |36 |36 |36 (máximo de 3 partições) |
| Armazenamento máximo por serviço |2 GB |300 GB |1.2 TB |2.4 TB |600 GB |
| Armazenamento máximo por partição |2 GB |25 GB |100 GB |200 GB |200 GB |
| Máximo de índices por serviço |5 |50 |200 |200 |3000 (máximo de 1000 índices/partição) |

#### <a name="s3-high-density"></a>Alta densidade S3 '
No tipo de preço S3 do Azure Search, há uma opção para o modo HD (alta densidade) projetada especificamente para cenários multilocatários. Em muitos casos, é necessário dar suporte a um grande número de locatários menores em um único serviço para obter os benefícios de simplicidade e eficiência de custo.

O S3 HD permite que os vários índices pequenos sejam empacotados sob o gerenciamento de um único serviço de pesquisa, negociando a capacidade de escalar horizontalmente os índices usando partições para a capacidade de hospedar mais índices em um único serviço.

Concretamente, um serviço S3 poderia ter entre 1 e 200 índices que juntos poderiam hospedar até 1.400.000.000 documentos. Um HD S3, por outro lado, permitiria que índices individuais passem até 1 milhão documentos, mas ele pode lidar com até 1000 índices por partição (até 3000 por serviço) com uma contagem total de documentos de 200 milhões por partição (até 600 milhões por serviço).

## <a name="considerations-for-multitenant-applications"></a>Considerações para aplicativos multilocatários
Os aplicativos multilocatários devem distribuir efetivamente os recursos entre os locatários e, ao mesmo tempo, preservar algum nível de privacidade entre os vários locatários. Há algumas considerações ao criar a arquitetura para um aplicativo como esse:

* *Isolamento de locatário:* Os desenvolvedores de aplicativos precisam tomar medidas apropriadas para garantir que nenhum locatário tenha acesso não autorizado ou indesejado aos dados de outros locatários. Além da perspectiva da privacidade dos dados, as estratégias de isolamento do locatário exigem o gerenciamento eficaz de recursos compartilhados e a proteção contra vizinhos com ruídos.
* *Custo de recursos de nuvem:* Assim como acontece com qualquer outro aplicativo, as soluções de software devem permanecer competitivas como um componente de um aplicativo multilocatário.
* *Facilidade de operações:* Ao desenvolver uma arquitetura multilocatário, o impacto sobre as operações e a complexidade do aplicativo é uma consideração importante. Azure Search tem um [SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Superfície global:* Os aplicativos multilocatários podem precisar atender aos locatários que são distribuídos em todo o mundo de forma eficaz.
* *Escalabilidade* Os desenvolvedores de aplicativos precisam considerar como eles reconciliam entre manter um nível suficientemente baixo de complexidade do aplicativo e projetar o aplicativo para dimensionar com o número de locatários e o tamanho dos dados e da carga de trabalho dos locatários.

O Azure Search oferece alguns limites que podem ser usados para isolar dados e carga de trabalho dos locatários.

## <a name="modeling-multitenancy-with-azure-search"></a>Modelando multilocação com Azure Search
No caso de um cenário multilocatário, o desenvolvedor do aplicativo consome um ou mais serviços de pesquisa e divide seus locatários entre serviços, índices ou ambos. Azure Search tem alguns padrões comuns ao modelar um cenário multilocatário:

1. *Índice por locatário:* Cada locatário tem seu próprio índice em um serviço de pesquisa que é compartilhado com outros locatários.
2. *Serviço por locatário:* Cada locatário tem seu próprio serviço de Azure Search dedicado, oferecendo mais alto nível de dados e separação de carga de trabalho.
3. *Combinação de ambos:* Locatários maiores e mais ativos são atribuídos a serviços dedicados, enquanto os locatários menores são atribuídos a índices individuais nos serviços compartilhados.

## <a name="1-index-per-tenant"></a>1. Índice por locatário
![Um retrata do modelo de índice por locatário](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Em um modelo de índice por locatário, vários locatários ocupam um único serviço de Azure Search em que cada locatário tem seu próprio índice.

Os locatários obtêm isolamento de dados porque todas as solicitações de pesquisa e operações de documentos são emitidas em um nível de índice no Azure Search. Na camada de aplicativo, há a necessidade de conscientizar para direcionar o tráfego de vários locatários para os índices apropriados, ao mesmo tempo em que gerencia recursos no nível de serviço em todos os locatários.

Um atributo de chave do modelo de índice por locatário é a capacidade do desenvolvedor do aplicativo de assinar a capacidade de um serviço de pesquisa entre os locatários do aplicativo. Se os locatários tiverem uma distribuição desigual da carga de trabalho, a combinação ideal de locatários poderá ser distribuída pelos índices de um serviço de pesquisa para acomodar vários locatários altamente ativos e com uso intensivo de recursos, enquanto atende simultaneamente a uma cauda longa de menos locatários ativos. A desvantagem é a incapacidade do modelo de lidar com situações em que cada locatário é simultaneamente altamente ativo.

O modelo de índice por locatário fornece a base para um modelo de custo variável, em que um serviço de Azure Search inteiro é comprado antecipadamente e, posteriormente, preenchido com locatários. Isso permite que a capacidade não utilizada seja designada para avaliações e contas gratuitas.

Para aplicativos com uma superfície global, o modelo de índice por locatário pode não ser o mais eficiente. Se os locatários de um aplicativo forem distribuídos em todo o mundo, um serviço separado poderá ser necessário para cada região, o que pode duplicar os custos em cada um deles.

Azure Search permite que a escala de índices individuais e o número total de índices aumentem. Se um tipo de preço apropriado for escolhido, as partições e réplicas poderão ser adicionadas a todo o serviço de pesquisa quando um índice individual dentro do serviço ficar muito grande em termos de armazenamento ou tráfego.

Se o número total de índices ficar muito grande para um único serviço, outro serviço precisará ser provisionado para acomodar os novos locatários. Se os índices precisarem ser movidos entre os serviços de pesquisa à medida que novos serviços forem adicionados, os dados do índice precisarão ser copiados manualmente de um índice para o outro, pois Azure Search não permite que um índice seja movido.

## <a name="2-service-per-tenant"></a>2. Serviço por locatário
![Um retrata do modelo de serviço por locatário](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Em uma arquitetura de serviço por locatário, cada locatário tem seu próprio serviço de pesquisa.

Nesse modelo, o aplicativo obtém o nível máximo de isolamento para seus locatários. Cada serviço tem armazenamento dedicado e taxa de transferência para manipular a solicitação de pesquisa, bem como chaves de API separadas.

Para aplicativos em que cada locatário tem uma grande superfície ou a carga de trabalho tem pouca variabilidade de locatário para locatário, o modelo de serviço por locatário é uma opção eficaz, pois os recursos não são compartilhados entre cargas de trabalho de vários locatários.

Um modelo de serviço por locatário também oferece o benefício de um modelo de custo fixo e previsível. Não há nenhum investimento antecipado em um serviço de pesquisa inteiro até que haja um locatário para preenchê-lo, no entanto, o custo por locatário é maior do que um modelo de índice por locatário.

O modelo de serviço por locatário é uma opção eficiente para aplicativos com uma superfície global. Com locatários distribuídos geograficamente, é fácil ter o serviço de cada locatário na região apropriada.

Os desafios no dimensionamento desse padrão surgem quando locatários individuais superam seu serviço. No momento, o Azure Search não dá suporte à atualização do tipo de preço de um serviço de pesquisa, portanto, todos os dados precisariam ser copiados manualmente para um novo serviço.

## <a name="3-mixing-both-models"></a>3. Misturando os dois modelos
Outro padrão para modelar a multilocação é misturar as estratégias de índice por locatário e de serviço por locatário.

Ao misturar os dois padrões, os maiores locatários de um aplicativo podem ocupar serviços dedicados, enquanto a longa cauda de locatários menos ativos e menores pode ocupar índices em um serviço compartilhado. Esse modelo garante que os maiores locatários tenham um desempenho consistentemente alto do serviço, ao mesmo tempo em que ajudam a proteger os locatários menores de vizinhos com ruídos.

No entanto, a implementação dessa estratégia depende antecipação na previsão de quais locatários exigirão um serviço dedicado em vez de um índice em um serviço compartilhado. A complexidade do aplicativo aumenta com a necessidade de gerenciar esses dois modelos de multilocação.

## <a name="achieving-even-finer-granularity"></a>Obtendo uma granularidade ainda mais definada
Os padrões de design acima para modelar cenários de multilocatário em Azure Search pressupõem um escopo uniforme em que cada locatário é uma instância inteira de um aplicativo. No entanto, os aplicativos podem, às vezes, lidar com muitos escopos menores.

Se os modelos de serviço por locatário e índice por locatário não forem escopos suficientemente pequenos, será possível modelar um índice para atingir um grau ainda maior de granularidade.

Para que um único índice se comporte de forma diferente para diferentes pontos de extremidade do cliente, um campo pode ser adicionado a um índice que designa um determinado valor para cada cliente possível. Cada vez que um cliente chama Azure Search para consultar ou modificar um índice, o código do aplicativo cliente especifica o valor apropriado para esse campo usando a capacidade de [filtro](https://msdn.microsoft.com/library/azure/dn798921.aspx) do Azure Search no momento da consulta.

Esse método pode ser usado para obter a funcionalidade de contas de usuário separadas, níveis de permissão separados e até mesmo aplicativos completamente separados.

> [!NOTE]
> Usar a abordagem descrita acima para configurar um único índice para atender a vários locatários afeta a relevância dos resultados da pesquisa. As pontuações de relevância de pesquisa são computadas em um escopo de nível de índice, não em um escopo de nível de locatário, de modo que os dados de todos os locatários sejam incorporados nas estatísticas subjacentes das pontuações de relevância, como a frequência de termos.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Azure Search é uma opção atraente para muitos aplicativos, [Leia mais sobre os recursos robustos do serviço](https://aka.ms/whatisazsearch). Ao avaliar os vários padrões de design para aplicativos multilocatários, considere os [vários tipos de preço](https://azure.microsoft.com/pricing/details/search/) e os respectivos [limites de serviço](search-limits-quotas-capacity.md) para melhor adequar Azure Search para ajustar as cargas de trabalho e arquiteturas de todos os tamanhos do aplicativo.

Quaisquer perguntas sobre cenários de Azure Search e multilocatário podem ser direcionadas para azuresearch_contact@microsoft.como.

