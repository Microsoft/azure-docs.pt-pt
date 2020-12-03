---
title: Escolha um nível de preços
titleSuffix: Azure Cognitive Search
description: 'A Azure Cognitive Search pode ser aprovisionada nestes níveis: Gratuito, Básico e Standard, e a Standard está disponível em várias configurações de recursos e níveis de capacidade.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 1b23d6c7952e60ee693bb481fec04d358654632c
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530498"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Escolha um nível de preços para Azure Cognitive Search

Ao [criar um serviço de pesquisa,](search-create-service-portal.md)escolha um nível de preços que seja fixado para o tempo de vida do serviço. O nível selecionado determina:

+ Quantidade de índices e outros objetos (limites máximos)
+ Tamanho e velocidade das divisórias (armazenamento físico)
+ Taxa faturada, um custo fixo que também flexiona com o número de divisórias e réplicas em uso

Além disso, algumas [funcionalidades premium](#premium-features) vêm com requisitos de nível.

## <a name="tier-descriptions"></a>Descrições de nível

Os níveis incluem **Free,** **Basic,** **Standard** e **Storage Optimized**. Standard e Storage Optimized estão disponíveis com várias configurações e capacidades.

A imagem que se segue do portal Azure mostra os níveis disponíveis, menos os preços (que pode encontrar no portal e na [página de preços](https://azure.microsoft.com/pricing/details/search/). 

![Níveis de preços da Pesquisa Cognitiva Azure](media/search-sku-tier/tiers.png "Níveis de preços da Pesquisa Cognitiva Azure")

**O free** cria um serviço de pesquisa limitado para projetos mais pequenos, como executar tutoriais e amostras de código. Internamente, réplicas e divisórias são partilhadas entre vários subscritores. Não é possível escalar um serviço gratuito ou executar cargas de trabalho significativas.

**Basic** e **Standard** são os níveis de faturação mais utilizados, sendo a **Standard** o padrão. Com recursos dedicados sob o seu controlo, pode implementar projetos maiores, otimizar o desempenho e aumentar a capacidade.

Alguns níveis são otimizados para certos tipos de trabalho. Por exemplo, **Standard 3 High Density (S3 HD)** é um *modo de hospedagem* para S3, onde o hardware subjacente é otimizado para um grande número de índices menores e destina-se a cenários de multitenância. O S3 HD tem a mesma carga por unidade que o S3, mas o hardware é otimizado para leituras rápidas de ficheiros num grande número de índices menores.

Os níveis **otimizados de armazenamento** oferecem uma maior capacidade de armazenamento a um preço mais baixo por TB do que os níveis Standard. A troca primária é uma maior latência de consulta, que deve validar para os seus requisitos específicos de aplicação. Para saber mais sobre as considerações de desempenho deste nível, consulte as considerações de [Desempenho e otimização.](search-performance-optimization.md)

Pode saber mais sobre os vários níveis na [página de preços,](https://azure.microsoft.com/pricing/details/search/)nos limites de Serviço no artigo [Azure Cognitive Search](search-limits-quotas-capacity.md) e na página do portal quando estiver a prestar um serviço.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Disponibilidade de recursos por nível

A tabela a seguir descreve restrições de características relacionadas com o nível.

| Funcionalidade | Limitações |
|---------|-------------|
| [indexadores](search-indexer-overview.md) | Os indexantes não estão disponíveis no S3 HD.  |
| [Melhoramento de IA](search-security-manage-encryption-keys.md) | Funciona no nível Livre, mas não é recomendado. |
| [Chaves de encriptação geridas pelo cliente](search-security-manage-encryption-keys.md) | Não disponível no nível Livre. |
| [Acesso a firewall IP](service-configure-firewall.md) | Não disponível no nível Livre. |
| [Ponto final privado (integração com a Azure Private Link)](service-create-private-endpoint.md) | Para ligações de entrada a um serviço de pesquisa, não está disponível no nível Gratuito. Para ligações de saída por indexadores a outros recursos Azure, não disponível em Free ou S3 HD. Para indexadores que usam skillsets, não disponíveis em Free, Basic, S1 ou S3 HD.|

A maioria das funcionalidades estão disponíveis em todos os níveis, incluindo funcionalidades gratuitas, mas funcionalidades intensivas de recursos podem não funcionar bem a menos que lhe dê capacidade suficiente. Por exemplo, [o enriquecimento](cognitive-search-concept-intro.md) de IA tem habilidades de longa duração que o tempo de serviço livre, a menos que o conjunto de dados seja pequeno.

## <a name="billable-events"></a>Eventos faturais

Uma solução construída na Azure Cognitive Search pode incorrer em custos das seguintes formas:

+ [Custo do próprio serviço,](#service-costs) com execução 24x7, na configuração mínima (uma partição e réplica), à taxa base

+ Capacidade de adição (réplicas ou divisórias), onde os custos aumentam a incrementos da taxa de faturação

+ Taxas de largura de banda (transferência de dados de saída)

+ Serviços adicionais necessários para capacidades ou funcionalidades específicas:

  + Enriquecimento de IA (requer [Serviços Cognitivos)](https://azure.microsoft.com/pricing/details/cognitive-services/)
  + loja de conhecimento (requer [armazenamento Azure)](https://azure.microsoft.com/pricing/details/storage/)
  + enriquecimento incremental (requer [armazenamento Azure,](https://azure.microsoft.com/pricing/details/storage/)aplica-se ao enriquecimento de IA)
  + chaves geridas pelo cliente e encriptação dupla (requer [cofre de chave Azure)](https://azure.microsoft.com/pricing/details/key-vault/)
  + pontos finais privados para um modelo de acesso sem internet (requer [Azure Private Link)](https://azure.microsoft.com/pricing/details/private-link/)

### <a name="service-costs"></a>Custos de serviço

Ao contrário de máquinas virtuais ou outros recursos que podem ser "pausados" para evitar custos, um serviço de Pesquisa Cognitiva Azure está sempre disponível em hardware dedicado para uso exclusivo. Como tal, criar um serviço é um evento faturalável que começa quando cria o serviço, e termina quando apaga o serviço. 

A carga mínima é a primeira unidade de pesquisa (uma réplica x uma partição) à taxa faturada. Este mínimo é fixado para o tempo de vida do serviço porque o serviço não pode funcionar em nada menos do que esta configuração. Além do mínimo, pode adicionar réplicas e divisórias independentemente umas das outras. Os aumentos incrementais de capacidade através de réplicas e divisórias aumentarão a sua fatura com base na seguinte fórmula: [(replicações x partições x taxa)](#search-units), onde a taxa que é cobrada depende do nível de preços que seleciona.

Quando estimar o custo de uma solução de pesquisa, lembre-se que os preços e a capacidade não são lineares. (Duplicar a capacidade mais do que duplica o custo.) Para um exemplo de como funciona a fórmula, consulte [Como atribuir réplicas e divisórias.](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)

### <a name="bandwidth-charges"></a>Taxas de largura de banda

A utilização [de indexantes](search-indexer-overview.md) pode afetar a faturação, dependendo da localização dos seus serviços. Pode eliminar totalmente as taxas de saída de dados se criar o serviço de Pesquisa Cognitiva Azure na mesma região que os seus dados. Aqui estão algumas informações da página de preços da [largura de banda:](https://azure.microsoft.com/pricing/details/bandwidth/)

+ A Microsoft não cobra por quaisquer dados de entrada a qualquer serviço no Azure, nem por quaisquer dados de saída da Azure Cognitive Search.
+ Em soluções multi-serviços, não há qualquer custo para os dados que cruzam o fio quando todos os serviços estão na mesma região.

Os encargos aplicam-se aos dados de saída se os serviços estiverem em diferentes regiões. Estas acusações não fazem parte da sua conta de Pesquisa Cognitiva Azure. São mencionados aqui porque se estiver a usar dados ou indexadores enriquecidos com IA para extrair dados de diferentes regiões, verá os custos refletidos na sua conta geral.

### <a name="ai-enrichment-with-cognitive-services"></a>Enriquecimento de IA com Serviços Cognitivos

Para [o enriquecimento de IA,](cognitive-search-concept-intro.md)você deve [planejar anexar um recurso de Serviços Cognitivos Azure,](cognitive-search-attach-cognitive-services.md)na mesma região que a Azure Cognitive Search, no nível de preços S0 para processamento pay-as-you-go. Não há um custo fixo associado à anexação dos Serviços Cognitivos. Paga-se apenas pelo processamento de que precisa.

| Operação | Impacto da faturação |
|-----------|----------------|
| Impressão de documentos, extração de texto | Gratuito |
| Rachadura de documento, extração de imagem | Faturado de acordo com o número de imagens extraídas dos seus documentos. Numa [configuração indexante,](/rest/api/searchservice/create-indexer#indexer-parameters) **a imageAction** é o parâmetro que desencadeia a extração de imagem. Se **a imageAction** estiver definida como "nenhuma" (o padrão), não será cobrado para a extração de imagem. A taxa de extração de imagem está documentada na página [de detalhes de preços](https://azure.microsoft.com/pricing/details/search/) para Azure Cognitive Search.|
| [Competências cognitivas incorporadas](cognitive-search-predefined-skills.md) | Faturado ao mesmo ritmo que se tivesse realizado a tarefa usando os Serviços Cognitivos diretamente. |
| Competências personalizadas | Uma habilidade personalizada é a funcionalidade que fornece. O custo de utilização de uma habilidade personalizada depende inteiramente de se o código personalizado está a chamar outros serviços medidos. |

A funcionalidade [de enriquecimento incremental (pré-visualização)](cognitive-search-incremental-indexing-conceptual.md) permite-lhe fornecer uma cache que permite ao indexante ser mais eficiente na execução apenas das capacidades cognitivas necessárias se modificar o seu skillset no futuro, poupando-lhe tempo e dinheiro.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Fórmula de faturação (R x P = SU)

O conceito de faturação mais importante para entender para as operações de Pesquisa Cognitiva Azure é a *unidade de pesquisa* (SU). Como a Pesquisa Cognitiva Azure depende de réplicas e divisórias para indexação e consultas, não faz sentido faturar por apenas um ou outro. Em vez disso, a faturação baseia-se num composto de ambos.

Su é o produto das *réplicas* e *divisórias utilizadas* por um serviço: **(R x P = SU)**.

Cada serviço começa com um SU (uma réplica multiplicada por uma partição) como mínimo. O máximo para qualquer serviço é 36 SUs. Este máximo pode ser alcançado de várias maneiras: 6 divisórias x 6 réplicas, ou 3 divisórias x 12 réplicas, por exemplo. É comum usar menos do que a capacidade total (por exemplo, um serviço de 3 réplicas, 3 divisórias faturadas como 9 SUs). Consulte o gráfico de [combinações de divisórias e réplicas](search-capacity-planning.md#chart) para obter combinações válidas.

A taxa de faturação é de hora a hora por SU. Cada nível tem uma taxa progressivamente mais alta. Os níveis mais altos vêm com divisórias maiores e mais rápidas, o que contribui para uma taxa horária globalmente mais alta para esse nível. Pode ver as tarifas de cada nível na página [de detalhes de preços.](https://azure.microsoft.com/pricing/details/search/)

A maioria dos clientes traz apenas uma parte da capacidade total on-line, mantendo o resto em reserva. Para faturação, o número de divisórias e réplicas que traz on-line, calculadas pela fórmula SU, determina o que se paga de hora a hora.

## <a name="how-to-manage-costs"></a>Como gerir os custos

As seguintes sugestões podem ajudá-lo a baixar os custos ou gerir os custos de forma mais eficaz:

+ Criar todos os recursos na mesma região, ou no menor tempo possível, para minimizar ou eliminar as taxas de largura de banda.

+ Consolidar todos os serviços num único grupo de recursos, como a Azure Cognitive Search, Serviços Cognitivos e quaisquer outros serviços Azure utilizados na sua solução. No portal Azure, encontre o grupo de recursos e utilize os comandos **de Gestão** de Custos para obter informações sobre os gastos reais e projetados.

+ Considere a Azure Web App para a sua aplicação frontal para que os pedidos e respostas permaneçam dentro do limite do data center.

+ Dimensione para operações intensivas de recursos como indexação e, em seguida, reajuste para baixo para cargas de trabalho regulares de consulta. Comece com a configuração mínima para Azure Cognitive Search (um SU composto por uma partição e uma réplica), e depois monitorize a atividade do utilizador para identificar padrões de utilização que indiquem a necessidade de mais capacidade. Se houver um padrão previsível, poderá sincronizar a escala com a atividade (precisaria de escrever código para automatizar isto).

Além disso, visite [Billing e gestão](../cost-management-billing/cost-management-billing-overview.md) de custos para ferramentas e funcionalidades incorporadas relacionadas com os gastos.

Não é possível encerrar um serviço de pesquisa temporariamente. Os recursos dedicados estão sempre operacionais, alocados para uso exclusivo para o tempo de vida do seu serviço. A eliminação de um serviço é permanente e também elimina os seus dados associados.

Em termos do próprio serviço, a única maneira de baixar a sua fatura é reduzir as réplicas e divisórias para um nível que ainda proporciona um desempenho aceitável e [conformidade com o SLA,](https://azure.microsoft.com/support/legal/sla/search/v1_0/)ou criar um serviço a um nível inferior (as tarifas S1 por hora são inferiores às de S2 ou S3). Assumindo que presta o seu serviço na parte inferior das suas projeções de carga, se superar o serviço, pode criar um segundo serviço de maior nível, reconstruir os seus índices no segundo serviço e, em seguida, apagar o primeiro.

## <a name="how-to-evaluate-capacity-requirements"></a>Como avaliar os requisitos de capacidade

Na Pesquisa Cognitiva Azure, a capacidade é estruturada como *réplicas* e *divisórias.*

+ Réplicas são casos do serviço de pesquisa. Cada réplica acolhe uma cópia equilibrada de um índice. Por exemplo, um serviço com seis réplicas tem seis cópias de cada índice carregado no serviço.

+ As divisórias armazenam índices e dividem automaticamente os dados pesmáveis. Duas divisórias dividem o seu índice ao meio, três divisórias dividem-no em terços, e assim por diante. Em termos de capacidade, o tamanho da *partição* é a característica principal de diferenciação entre os níveis.

> [!NOTE]
> Todos os níveis Standard e Storage Optimized suportam [combinações flexíveis de réplicas e divisórias](search-capacity-planning.md#chart) para que possa [otimizar o seu sistema para velocidade ou armazenamento](search-performance-optimization.md) alterando o equilíbrio. O nível Básico oferece até três réplicas para alta disponibilidade, mas tem apenas uma divisória. Os níveis gratuitos não fornecem recursos dedicados: os recursos de computação são partilhados por vários subscritores.

### <a name="evaluating-capacity"></a>Avaliação da capacidade

A capacidade e os custos de funcionamento do serviço andam de mãos dadas. Os níveis impõem limites a dois níveis: armazenamento e conteúdo (número de índices, por exemplo). Devias pensar em ambos, porque qualquer que seja o limite que atinges primeiro é o limite efetivo.

Os requisitos de negócio normalmente ditam o número de índices que você precisa. Por exemplo, pode precisar de um índice global para um grande repositório de documentos. Ou pode precisar de vários índices baseados na região, aplicação ou nicho de negócio.

Para determinar o tamanho de um índice, tem que [construir um.](search-what-is-an-index.md) O seu tamanho basear-se-á em dados importados e na configuração do índice, tais como se permite sugestivos, filtragem e triagem.

Para a pesquisa completa de texto, a estrutura de dados primários é uma estrutura [de índice invertida,](https://en.wikipedia.org/wiki/Inverted_index) que tem características diferentes dos dados de origem. Para um índice invertido, o tamanho e a complexidade são determinados pelo conteúdo, não necessariamente pela quantidade de dados que se alimenta nele. Uma grande fonte de dados com alta redundância poderia resultar num índice menor do que um conjunto de dados menor que contém conteúdo altamente variável. Portanto, raramente é possível inferir o tamanho do índice com base no tamanho do conjunto de dados original.

> [!NOTE] 
> Mesmo estimando necessidades futuras de índices e armazenamento pode parecer adivinhação, vale a pena fazê-lo. Se a capacidade de um nível se revelar demasiado baixa, terá de providenciar um novo serviço a um nível mais elevado e, em seguida, [recarregar os seus índices](search-howto-reindex.md). Não há um upgrade no lugar de um serviço de um nível para outro.
>

### <a name="estimate-with-the-free-tier"></a>Estimativa com o nível Livre

Uma abordagem para estimar a capacidade é começar com o nível Livre. Lembre-se que o serviço Gratuito oferece até três índices, 50 MB de armazenamento e 2 minutos de tempo de indexação. Pode ser um desafio estimar um tamanho de índice projetado com estes constrangimentos, mas estes são os passos:

+ [Crie um serviço gratuito.](search-create-service-portal.md)
+ Prepare um conjunto de dados pequeno e representativo.
+ [Construa um índice inicial no portal](search-get-started-portal.md) e note o seu tamanho. Funcionalidades e atributos têm impacto no armazenamento. Por exemplo, adicionar sugestivos (consultas de pesquisa como você do tipo) aumentará os requisitos de armazenamento. Utilizando o mesmo conjunto de dados, pode tentar criar várias versões de um índice, com diferentes atributos em cada campo, para ver como os requisitos de armazenamento variam. Para obter mais informações, consulte ["Implicações de armazenamento" na Criação de um índice básico.](search-what-is-an-index.md#index-size)

Com uma estimativa aproximada na mão, você pode duplicar esse montante para orçamento para dois índices (desenvolvimento e produção) e, em seguida, escolher o seu nível em conformidade.

### <a name="estimate-with-a-billable-tier"></a>Estimativa com um nível faturada

Os recursos dedicados podem acomodar tempos de amostragem e processamento maiores para estimativas mais realistas de volumes de quantidade, tamanho e consulta do índice durante o desenvolvimento. Alguns clientes saltam para dentro com um nível fatural e depois reavaliam à medida que o projeto de desenvolvimento amadurece.

1. [Reveja os limites de serviço em cada nível](./search-limits-quotas-capacity.md#index-limits) para determinar se os níveis mais baixos podem suportar o número de índices de que necessita. Nos escalões Básico, S1 e S2, os limites de índice são 15, 50 e 200, respectivamente. O nível otimizado de armazenamento tem um limite de 10 índices porque é projetado para suportar um número baixo de índices muito grandes.

1. [Criar um serviço num nível de faturação:](search-create-service-portal.md)

    + Comece baixo, no Basic ou S1, se não tiver certeza sobre a carga projetada.
    + Comece alto, no S2 ou mesmo no S3, se souber que vai ter cargas de indexação e consulta em larga escala.
    + Comece com o Storage Otimizado, em L1 ou L2, se estiver a indexar uma grande quantidade de dados e a carga de consulta é relativamente baixa, como acontece com uma aplicação de negócio interna.

1. [Construa um índice inicial](search-what-is-an-index.md) para determinar como os dados de origem se traduzem num índice. Esta é a única maneira de estimar o tamanho do índice.

1. [Monitorize o armazenamento, os limites de serviço, o volume de consulta e](search-monitor-usage.md) a latência no portal. O portal mostra-lhe consultas por segundo, consultas estranguladas e latência de pesquisa. Todos estes valores podem ajudá-lo a decidir se selecionou o nível certo. 

O número e o tamanho do índice são igualmente importantes para a sua análise. Isto porque os limites máximos são atingidos através da utilização integral do armazenamento (divisórias) ou por limites máximos de recursos (índices, indexadores, etc.), o que vier em primeiro lugar. O portal ajuda-o a acompanhar ambos, mostrando a utilização atual e os limites máximos lado a lado na página 'Vista Geral'.

> [!NOTE]
> Os requisitos de armazenamento podem ser inflacionados se os documentos contiverem dados extra-suficientes. Idealmente, os documentos contêm apenas os dados de que necessita para a experiência de pesquisa. Os dados binários não são pesmáveis e devem ser armazenados separadamente (talvez numa mesa Azure ou armazenamento de bolhas). Um campo deve então ser adicionado no índice para manter uma referência URL aos dados externos. O tamanho máximo de um documento individual é de 16 MB (ou menos se estiver a carregar vários documentos num único pedido). Para obter mais informações, consulte [os limites de serviço na Pesquisa Cognitiva Azure.](search-limits-quotas-capacity.md)
>

**Considerações de volume de consulta**

Consultas por segundo (QPS) é uma métrica importante durante a afinação do desempenho, mas geralmente é apenas uma consideração de nível se você espera um alto volume de consulta no início.

Os níveis Standard podem fornecer um equilíbrio de réplicas e divisórias. Pode aumentar a reviravolta da consulta adicionando réplicas para equilibrar a carga ou adicionar divisórias para processamento paralelo. Em seguida, pode sintonizar para desempenho após o serviço ser prestado.

Se espera volumes de consultas elevados e sustentados desde o início, deve considerar níveis standard mais elevados, apoiados por hardware mais potente. Em seguida, pode tirar divisórias e réplicas offline, ou até mesmo mudar para um serviço de nível inferior, se esses volumes de consulta não ocorrerem. Para obter mais informações sobre como calcular o rendimento da consulta, consulte [o desempenho e otimização da Pesquisa Cognitiva Azure.](search-performance-optimization.md)

Os níveis otimizados de armazenamento são úteis para grandes cargas de trabalho de dados, suportando um armazenamento de índice mais disponível para quando os requisitos de latência de consulta são menos importantes. Deve ainda utilizar réplicas adicionais para equilibrar cargas e divisórias adicionais para processamento paralelo. Em seguida, pode sintonizar para desempenho após o serviço ser prestado.

**Acordos de nível de serviço**

As funcionalidades de nível gratuito e de pré-visualização não fornecem [acordos de nível de serviço (SLAs)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todos os níveis de faturação, as SLAs fazem efeito quando fornece redundância suficiente para o seu serviço. Precisa de duas ou mais réplicas para consulta (ler) SLAs. Precisa de três ou mais réplicas para consulta e indexação (ler-escrever) SLAs. O número de divisórias não afeta as AEA.

## <a name="tips-for-tier-evaluation"></a>Dicas para avaliação de nível

+ Permitir que as métricas construam em torno de consultas e recolham dados em torno dos padrões de utilização (consultas durante o horário comercial, indexação durante as horas de ponta). Utilize estes dados para informar as decisões de prestação de serviços. Embora não seja prático a uma cadência horária ou diária, pode ajustar dinamicamente as divisórias e recursos para acomodar as mudanças planeadas nos volumes de consulta. Também pode acomodar alterações não planeadas mas sustentadas se os níveis aguentarem o tempo suficiente para justificar a tomada de medidas.

+ Lembre-se que a única desvantagem do fornecimento é que você pode ter que demolir um serviço se os requisitos reais são maiores do que as suas previsões. Para evitar perturbações no serviço, criaria um novo serviço num nível mais alto e executá-lo-ia lado a lado até que todas as aplicações e pedidos direcionem o novo ponto final.

## <a name="next-steps"></a>Passos seguintes

Comece com um nível Livre e construa um índice inicial utilizando um subconjunto dos seus dados para compreender as suas características. A estrutura de dados na Azure Cognitive Search é uma estrutura de índice invertida. O tamanho e complexidade de um índice invertido é determinado pelo conteúdo. Lembre-se que o conteúdo altamente redundante tende a resultar num índice menor do que um conteúdo altamente irregular. Assim, as características do conteúdo em vez do tamanho do conjunto de dados determinam os requisitos de armazenamento do índice.

Depois de ter uma estimativa inicial do seu tamanho de índice, [provisão de um serviço faturado](search-create-service-portal.md) num dos níveis discutidos neste artigo: Básico, Standard ou Storage Otimizado. Relaxe quaisquer constrangimentos artificiais no dimensionamento de dados e [reconstrua o seu índice](search-howto-reindex.md) para incluir todos os dados que pretende ser pesmável.

[Aloque divisórias e réplicas](search-capacity-planning.md) conforme necessário para obter o desempenho e a escala que você precisa.

Se o desempenho e a capacidade estiverem bem, estás feito. Caso contrário, recossar um serviço de pesquisa num nível diferente que se alinhe mais com as suas necessidades.

> [!NOTE]
> Se tiver dúvidas, publique no [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contacte o suporte da Azure](https://azure.microsoft.com/support/options/).