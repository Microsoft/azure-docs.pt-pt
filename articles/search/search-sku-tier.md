---
title: Escolha um nível de preços ou SKU
titleSuffix: Azure Cognitive Search
description: 'A Pesquisa Cognitiva Azure pode ser disponibilizada nestes SKUs: Grátis, Básico e Standard, e a Standard está disponível em várias configurações de recursos e níveis de capacidade.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: eb11a5cc2deef372ca91c23a8b9c82e17143c85b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617720"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Escolha um nível de preços para pesquisa cognitiva azure

Quando se cria um serviço de Pesquisa Cognitiva Azure, [é criado](search-create-service-portal.md) um recurso a um nível de preços (ou SKU) que é corrigido durante toda a vida útil do serviço. Os níveis incluem Otimização gratuita, básica, standard e armazenamento. Standard e Storage Otimizado estão disponíveis com várias configurações e capacidades.

A maioria dos clientes começa com o free tier para que possam avaliar o serviço. Após a avaliação, é comum criar um segundo serviço num dos níveis mais elevados para desenvolvimento e implantação de produção.

## <a name="feature-availability-by-tier"></a>Disponibilidade de funcionalidades por nível

Quase todas as funcionalidades estão disponíveis em todos os níveis, incluindo o Free, mas uma funcionalidade ou fluxo de trabalho que seja intensivo de recursos pode não funcionar bem a menos que lhe dê capacidade suficiente. Por exemplo, o enriquecimento de [IA](cognitive-search-concept-intro.md) tem competências de longo prazo que se esgotam num serviço gratuito, a menos que o conjunto de dados seja pequeno.

A tabela seguinte descreve as restrições de características relacionadas com o nível.

| Funcionalidade | Limitações |
|---------|-------------|
| [indexadores](search-indexer-overview.md) | Os indexadores não estão disponíveis no S3 HD. |
| [Chaves de encriptação geridas pelo cliente](search-security-manage-encryption-keys.md) | Não disponível no free tier. |

## <a name="tiers-skus"></a>Níveis (SKUs)

As camadas são diferenciadas por:

+ Quantidade de índices e indexadores que pode criar
+ Tamanho e velocidade das divisórias (armazenamento físico)

O nível que selecionar determina a taxa de faturação. A seguinte imagem do portal Azure mostra os níveis disponíveis, menos preços (que pode encontrar no portal e na [página de preços](https://azure.microsoft.com/pricing/details/search/). **Grátis,** **Básicos**e **Standard** são os níveis mais comuns.

**O Free** cria um serviço de pesquisa limitado para projetos menores, incluindo quickstarts e tutoriais. Internamente, réplicas e divisórias partilhadas entre vários subscritores. Não é possível escalar um serviço gratuito ou executar cargas de trabalho significativas.

**Basic** e **Standard** são os níveis de faturação mais utilizados, sendo a **Standard** o padrão. Com recursos dedicados sob o seu controlo, pode implementar projetos maiores, otimizar o desempenho e definir a capacidade.

![Níveis de preços da Pesquisa Cognitiva Azure](media/search-sku-tier/tiers.png "Níveis de preços da Pesquisa Cognitiva Azure")

Alguns níveis são otimizados para certos tipos de trabalho. Por exemplo, a **Standard 3 High Density (S3 HD)** é um modo de *hospedagem* para s3, onde o hardware subjacente é otimizado para um grande número de índices menores e destina-se a cenários multiprazo. O S3 HD tem a mesma carga por unidade que o S3, mas o hardware está otimizado para leituras rápidas de ficheiros num grande número de índices menores.

Os níveis **otimizados** de armazenamento oferecem uma maior capacidade de armazenamento a um preço mais baixo por TB do que os níveis Standard. A troca primária é a latência de consulta mais elevada, que deve validar para os seus requisitos específicos de aplicação.  Para saber mais sobre as considerações de desempenho deste nível, consulte considerações de [Desempenho e otimização.](search-performance-optimization.md)

Pode saber mais sobre os vários níveis na página de [preços,](https://azure.microsoft.com/pricing/details/search/)nos limites de Serviço no artigo [de Pesquisa Cognitiva Azure](search-limits-quotas-capacity.md) e na página do portal quando estiver a fornecer um serviço.

## <a name="billable-events"></a>Eventos de faturação

Uma solução construída sobre a Pesquisa Cognitiva Azure pode incorrer em custos das seguintes formas:

+ Custo fixo do próprio serviço, funcionando 24x7, com configuração mínima (uma divisória e réplica)
+ Custo incremental ao escalonar (adicionar réplicas ou divisórias)
+ Cargas de largura de banda (transferência de dados de saída) 
+ Pesquisa cognitiva (anexação de Serviços Cognitivos para enriquecimento de IA, ou utilização de armazenamento Azure para loja de conhecimento)

### <a name="service-costs"></a>Custos de serviço

Ao contrário de máquinas virtuais ou outros recursos que podem ser "pausados" para evitar encargos, um serviço de Pesquisa Cognitiva Azure está sempre disponível em hardware dedicado ao seu uso exclusivo. Como tal, criar um serviço é um evento que começa quando cria o serviço e termina quando elimina o serviço. 

A carga mínima é a primeira unidade de pesquisa (uma réplica x uma divisória) à taxa de faturação. Este mínimo é fixado para a vida útil do serviço porque o serviço não pode funcionar em nada menos do que esta configuração. Além do mínimo, pode adicionar réplicas e divisórias independentemente umas das outras. Aumentos incrementais de capacidade através de réplicas e divisórias aumentarão a sua conta com base na seguinte fórmula: [(réplicas x divisórias x taxa)](#search-units), onde a taxa que é cobrado depende do nível de preços que selecionar.

Quando estiver a estimar o custo de uma solução de pesquisa, lembre-se que os preços e a capacidade não são lineares. (Duplicar a capacidade mais do que duplica o custo.) Para um exemplo de como a fórmula funciona, veja [como alocar réplicas e divisórias.](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)

### <a name="bandwidth-charges"></a>Cargas de largura de banda

A utilização de indexadores de [pesquisa cognitiva Azure](search-indexer-overview.md) pode afetar a faturação, dependendo da localização dos seus serviços. Pode eliminar totalmente os encargos de egress de dados se criar o serviço de Pesquisa Cognitiva Azure na mesma região que os seus dados. Aqui está algumas informações da página de preços da [largura de banda:](https://azure.microsoft.com/pricing/details/bandwidth/)

+ A Microsoft não cobra quaisquer dados de entrada a qualquer serviço no Azure, ou por quaisquer dados de saída da Pesquisa Cognitiva Do Azure.
+ Em soluções multi-serviços, não há nenhum custo para os dados que cruzam o fio quando todos os serviços estão na mesma região.

Os encargos aplicam-se aos dados de saída se os serviços estiverem em diferentes regiões. Estas acusações não fazem parte da sua conta de pesquisa cognitiva Azure. São mencionados aqui porque se estiver a usar dados ou indexantes enriquecidos com IA para retirar dados de diferentes regiões, verá os custos refletidos na sua conta global.

### <a name="ai-enrichment-with-cognitive-services"></a>Enriquecimento de IA com Serviços Cognitivos

Para enriquecimento de [IA,](cognitive-search-concept-intro.md)deve planear [anexar um recurso de Serviços Cognitivos Azure faturado,](cognitive-search-attach-cognitive-services.md)na mesma região que a Pesquisa Cognitiva Azure, no nível de preços S0 para processamento pay-as-you-go. Não há nenhum custo fixo associado à anexação dos Serviços Cognitivos. Só paga pelo processamento que precisa.

| Operação | Impacto da faturação |
|-----------|----------------|
| Quebra de documentos, extração de texto | Gratuito |
| Quebra de documentos, extração de imagem | Faturado de acordo com o número de imagens extraídas dos seus documentos. Numa [configuração indexante,](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) **a imagemAction** é o parâmetro que desencadeia a extração de imagem. Se **a imagemA ação** estiver definida para "nenhuma" (o padrão), não será cobrado para extração de imagem. A taxa de extração de imagem está documentada na página de detalhes de [preços](https://azure.microsoft.com/pricing/details/search/) para pesquisa cognitiva azure.|
| [Competências cognitivas incorporadas](cognitive-search-predefined-skills.md) | Faturado ao mesmo ritmo que se tivesse realizado a tarefa usando os Serviços Cognitivos diretamente. |
| Competências personalizadas | Uma habilidade personalizada é a funcionalidade que fornece. O custo de usar uma habilidade personalizada depende inteiramente se o código personalizado está chamando outros serviços medidos. |

A funcionalidade de [enriquecimento incremental (pré-visualização)](cognitive-search-incremental-indexing-conceptual.md) permite-lhe fornecer uma cache que permite ao indexante ser mais eficiente em executar apenas as habilidades cognitivas necessárias se modificar a sua habilidade no futuro, poupando-lhe tempo e dinheiro.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Fórmula de faturação (R x P = SU)

O conceito de faturação mais importante a compreender para as operações de Pesquisa Cognitiva Azure é a *unidade de pesquisa* (SU). Como a Pesquisa Cognitiva Azure depende tanto de réplicas como de divisórias para indexação e consultas, não faz sentido faturar por apenas uma ou outra. Em vez disso, a faturação baseia-se num composto de ambos.

SU é o produto das *réplicas* e *divisórias utilizadas* por um serviço: **(R x P = SU)**.

Cada serviço começa com uma SU (uma réplica multiplicada por uma divisória) como o mínimo. O máximo para qualquer serviço é de 36 Us. Este máximo pode ser alcançado de várias maneiras: 6 divisórias x 6 réplicas, ou 3 divisórias x 12 réplicas, por exemplo. É comum usar menos do que a capacidade total (por exemplo, um serviço de 3 réplicas e 3 divisórias faturado como 9 SUs). Consulte o gráfico de [combinações de divisórias e réplicas](search-capacity-planning.md#chart) para obter combinações válidas.

A taxa de faturação é de hora em hora por SU. Cada nível tem uma taxa progressivamente mais alta. Os níveis mais altos vêm com divisórias maiores e mais rápidas, o que contribui para uma taxa horária globalmente mais elevada para esse nível. Pode ver as tarifas para cada nível na página de detalhes de [preços.](https://azure.microsoft.com/pricing/details/search/)

A maioria dos clientes traz apenas uma parte da capacidade total on-line, mantendo o resto na reserva. Para faturação, o número de divisórias e réplicas que você traz on-line, calculado pela fórmula SU, determina o que você paga de hora em hora.

## <a name="how-to-manage-costs"></a>Como gerir custos

As seguintes sugestões podem ajudá-lo a manter os custos no mínimo:

+ Criar todos os recursos na mesma região, ou em tão poucas regiões quanto possível, para minimizar ou eliminar os encargos de largura de banda.

+ Consolidar todos os serviços num só grupo de recursos, como a Pesquisa Cognitiva Azure, os Serviços Cognitivos e quaisquer outros serviços Azure utilizados na sua solução. No portal Azure, encontre o grupo de recursos e utilize os comandos de Gestão de **Custos** para obter informações sobre os gastos reais e projetados.

+ Considere a Web App Azure para a sua aplicação frontal para que os pedidos e respostas permaneçam dentro do limite do data center.

+ Aumentar para operações intensivas de recursos, como indexação, e, em seguida, reajustar para baixo para trabalhos de trabalho de consulta regular. Comece com a configuração mínima para A Pesquisa Cognitiva Azure (uma SU composta por uma divisória e uma réplica), e depois monitorize a atividade do utilizador para identificar padrões de utilização que indiquem a necessidade de mais capacidade. Se houver um padrão previsível, poderá sincronizar a escala com a atividade (teria de escrever código para automatizar isto).

Além disso, visite [a Faturação e a gestão](https://docs.microsoft.com/azure/billing/billing-getting-started) de custos para ferramentas e funcionalidades incorporadas relacionadas com os gastos.

Encerrar um serviço de busca temporariamente não é possível. Os recursos dedicados estão sempre operacionais, atribuídos para o seu uso exclusivo durante toda a vida útil do seu serviço. A eliminação de um serviço é permanente e também elimina os seus dados associados.

Em termos do próprio serviço, a única maneira de baixar a sua conta é reduzir as réplicas e divisórias para um nível que ainda proporciona desempenho aceitável e conformidade com a [SLA,](https://azure.microsoft.com/support/legal/sla/search/v1_0/)ou criar um serviço a um nível inferior (as tarifas s1 por hora são inferiores às taxas S2 ou S3). Assumindo que presta o seu serviço na extremidade inferior das suas projeções de carga, se ultrapassar o serviço, pode criar um segundo serviço de maior nível, reconstruir os seus índices no segundo serviço e, em seguida, apagar o primeiro.

## <a name="how-to-evaluate-capacity-requirements"></a>Como avaliar os requisitos de capacidade

Na Pesquisa Cognitiva Azure, a capacidade é estruturada como *réplicas* e *divisórias.*

+ Réplicas são instâncias do serviço de pesquisa. Cada réplica acolhe uma cópia equilibrada de um índice. Por exemplo, um serviço com seis réplicas tem seis cópias de cada índice carregado no serviço.

+ As divisórias armazenam índices e dividem automaticamente os dados pesquisáveis. Duas divisórias dividem o seu índice ao meio, três divisórias dividem-no em terços, e assim por diante. Em termos de capacidade, o tamanho da *divisória* é a principal característica diferenciadora entre os níveis.

> [!NOTE]
> Todos os níveis Standard e Storage Optimized suportam [combinações flexíveis de réplicas e divisórias](search-capacity-planning.md#chart) para que possa [otimizar o seu sistema para velocidade ou armazenamento](search-performance-optimization.md) alterando o equilíbrio. O nível Básico oferece até três réplicas para alta disponibilidade, mas tem apenas uma divisória. Os níveis gratuitos não fornecem recursos dedicados: os recursos de computação são partilhados por vários subscritores.

### <a name="evaluating-capacity"></a>Capacidade de avaliação

A capacidade e os custos de funcionamento do serviço andam de mãos dadas. Os níveis impõem limites a dois níveis: armazenamento e recursos. Devias pensar em ambos, porque qualquer que seja o limite que atingires primeiro é o limite efetivo.

Os requisitos empresariais normalmente ditam o número de índices que vai precisar. Por exemplo, você pode precisar de um índice global para um grande repositório de documentos. Ou pode precisar de vários índices baseados na região, aplicação ou nicho de negócio.

Para determinar o tamanho de um índice, tem que [construir um.](search-create-index-portal.md) O seu tamanho basear-se-á em dados importados e configuração de índices, tais como se permite sugestões, filtragem e triagem. Para obter mais informações sobre o impacto da configuração no tamanho, consulte [Criar um índice básico ](search-what-is-an-index.md).

Para pesquisa completa de texto, a estrutura de dados primárioé uma estrutura de [índice invertida,](https://en.wikipedia.org/wiki/Inverted_index) que tem características diferentes dos dados de origem. Para um índice invertido, o tamanho e a complexidade são determinados pelo conteúdo, não necessariamente pela quantidade de dados que se alimentam nele. Uma grande fonte de dados com elevada redundância poderia resultar num índice menor do que um conjunto de dados menor que contém conteúdo altamente variável. Portanto, raramente é possível inferir o tamanho do índice com base no tamanho do conjunto de dados original.

> [!NOTE] 
> Mesmo estimando necessidades futuras de índices e armazenamento pode parecer adivinhação, vale a pena fazê-lo. Se a capacidade de um nível se revelar demasiado baixa, terá de fornecer um novo serviço a um nível mais elevado e, em seguida, [recarregar os seus índices](search-howto-reindex.md). Não há atualização de um serviço de um SKU para outro.
>

### <a name="estimate-with-the-free-tier"></a>Estimativa com o nível Livre

Uma abordagem para estimar a capacidade é começar com o nível Livre. Lembre-se que o serviço Free oferece até três índices, 50 MB de armazenamento e 2 minutos de tempo de indexação. Pode ser um desafio estimar um tamanho de índice projetado com estes constrangimentos, mas estes são os passos:

+ [Criar um serviço gratuito.](search-create-service-portal.md)
+ Prepare um pequeno conjunto de dados representativo.
+ [Construa um índice inicial no portal](search-create-index-portal.md) e note o seu tamanho. Características e atributos têm impacto no armazenamento. Por exemplo, adicionar sugestionantes (consultas de pesquisa como o tipo de pesquisa) aumentará os requisitos de armazenamento. Utilizando o mesmo conjunto de dados, poderá tentar criar múltiplas versões de um índice, com diferentes atributos em cada campo, para ver como os requisitos de armazenamento variam. Para obter mais informações, consulte ["Implicações de armazenamento" na Criação de um índice básico](search-what-is-an-index.md#index-size).

Com uma estimativa aproximada na mão, pode duplicar esse montante para o orçamento de dois índices (desenvolvimento e produção) e, em seguida, escolher o seu nível em conformidade.

### <a name="estimate-with-a-billable-tier"></a>Estimativa com um nível de faturação

Os recursos dedicados podem acomodar maiores tempos de amostragem e processamento para estimativas mais realistas da quantidade de índice, tamanho e volumes de consulta durante o desenvolvimento. Alguns clientes saltam com um nível de faturação e depois reavaliam à medida que o projeto de desenvolvimento amadurece.

1. [Reveja os limites](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) de serviço em cada nível para determinar se os níveis inferiores podem suportar o número de índices de que necessita. Nos níveis Básico, S1 e S2, os limites de índice são 15, 50 e 200, respectivamente. O nível Otimizado de Armazenamento tem um limite de 10 índices porque foi projetado para suportar um número baixo de índices muito grandes.

1. [Criar um serviço a um nível de faturação:](search-create-service-portal.md)

    + Comece baixo, no Basic ou S1, se não tiver certeza sobre a carga projetada.
    + Comece alto, em S2 ou mesmo S3, se sabe que vai ter cargas de indexação e consulta em larga escala.
    + Comece com o Armazenamento Otimizado, em L1 ou L2, se estiver a indexar uma grande quantidade de dados e a carga de consulta é relativamente baixa, como acontece com uma aplicação interna de negócios.

1. [Construa um índice inicial](search-create-index-portal.md) para determinar como os dados de origem se traduzem para um índice. Esta é a única maneira de estimar o tamanho do índice.

1. [Monitorizar o armazenamento, os limites](search-monitor-usage.md) de serviço, o volume de consultas e a latência no portal. O portal mostra-lhe consultas por segundo, consultas aceleradas e latência de pesquisa. Todos estes valores podem ajudá-lo a decidir se escolheu o nível certo. 

O número e o tamanho do índice são igualmente importantes para a sua análise. Isto porque os limites máximos são atingidos através da utilização total do armazenamento (divisórias) ou por limites máximos de recursos (índices, indexadores, etc.), o que vier em primeiro lugar. O portal ajuda-o a acompanhar ambos, mostrando o uso atual e os limites máximos lado a lado na página De visão geral.

> [!NOTE]
> Os requisitos de armazenamento podem ser inflacionados se os documentos contiverem dados estranhos. Idealmente, os documentos contêm apenas os dados que precisa para a experiência de pesquisa. Os dados binários não são pesquisáveis e devem ser armazenados separadamente (talvez numa mesa Azure ou num armazenamento de bolhas). Em seguida, deve ser adicionado um campo no índice para manter uma referência URL aos dados externos. O tamanho máximo de um documento individual é de 16 MB (ou menos se estiver a carregar vários documentos num único pedido). Para mais informações, consulte [os limites de serviço na Pesquisa Cognitiva Azure](search-limits-quotas-capacity.md).
>

**Considerações de volume de consulta**

Consultas por segundo (QPS) é uma métrica importante durante a sintonização do desempenho, mas geralmente é apenas uma consideração de nível se você espera um volume de consulta elevado no início.

Os níveis Standard podem fornecer um equilíbrio de réplicas e divisórias. Pode aumentar a reviravolta da consulta adicionando réplicas para equilibrar a carga ou adicionar divisórias para processamento paralelo. Em seguida, pode sintonizar para o desempenho após o fornecimento do serviço.

Se espera volumes de consultas elevados desde o início, deve considerar níveis Standard mais elevados, apoiados por hardware mais potente. Em seguida, pode desligar as divisórias e réplicas, ou até mesmo mudar para um serviço de nível inferior, se esses volumes de consulta não ocorrerem. Para obter mais informações sobre como calcular o resultado da consulta, consulte o desempenho e otimização da [Pesquisa Cognitiva azure](search-performance-optimization.md).

Os níveis otimizados de armazenamento são úteis para grandes cargas de trabalho de dados, suportando um armazenamento de índice mais disponível para quando os requisitos de latência de consulta são menos importantes. Deve ainda utilizar réplicas adicionais para equilibrar a carga e divisórias adicionais para processamento paralelo. Em seguida, pode sintonizar para o desempenho após o fornecimento do serviço.

**Acordos de nível de serviço**

As funcionalidades de nível livre e pré-visualização não fornecem [acordos de nível de serviço (SLAs)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todos os níveis de faturação, os SLAs têm efeito quando prevê redundância suficiente para o seu serviço. Você precisa ter duas ou mais réplicas para consulta (ler) SLAs. Você precisa ter três ou mais réplicas para consulta e indexação (read-write) SLAs. O número de divisórias não afeta as SLAs.

## <a name="tips-for-tier-evaluation"></a>Dicas para avaliação de nível

+ Permitir que as métricas construam em torno de consultas e recolha dados em torno de padrões de utilização (consultas durante o horário de trabalho, indexação durante horas fora do pico). Utilize estes dados para informar as decisões de prestação de serviços. Embora não seja prático numa cadência de hora ou de dia, pode ajustar dinamicamente divisórias e recursos para acomodar alterações planeadas em volumes de consulta. Também pode acomodar alterações não planeadas mas sustentadas se os níveis se mantiverem o tempo suficiente para justificar a tomada de medidas.

+ Lembre-se que a única desvantagem de providenciar é que poderá ter de demolir um serviço se os requisitos reais forem maiores do que as suas previsões. Para evitar perturbações no serviço, criaria um novo serviço a um nível mais elevado e executava-o lado a lado até que todas as aplicações e pedidos visassem o novo ponto final.

## <a name="next-steps"></a>Passos seguintes

Comece com um nível Livre e construa um índice inicial utilizando um subconjunto dos seus dados para entender as suas características. A estrutura de dados em Azure Cognitive Search é uma estrutura de índice invertida. O tamanho e complexidade de um índice invertido é determinado pelo conteúdo. Lembre-se que o conteúdo altamente redundante tende a resultar num índice menor do que o conteúdo altamente irregular. Assim, as características do conteúdo em vez do tamanho do conjunto de dados determinam os requisitos de armazenamento de índices.

Depois de ter uma estimativa inicial do seu tamanho de índice, [disponibilize um serviço de faturação](search-create-service-portal.md) num dos níveis discutidos neste artigo: Basic, Standard ou Storage Optimized. Relaxe quaisquer restrições artificiais no dimensionamento de dados e [reconstrua o seu índice](search-howto-reindex.md) para incluir todos os dados que pretende ser pesquisável.

[Aloque divisórias e réplicas](search-capacity-planning.md) conforme necessário para obter o desempenho e escala que você precisa.

Se o desempenho e a capacidade estão bem, estás feito. Caso contrário, recrie um serviço de pesquisa a um nível diferente que se alinha mais com as suas necessidades.

> [!NOTE]
> Se tiver dúvidas, poste no [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou contacte o [suporte azure](https://azure.microsoft.com/support/options/).
