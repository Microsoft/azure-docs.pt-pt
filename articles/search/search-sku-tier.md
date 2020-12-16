---
title: Escolha um nível de preços
titleSuffix: Azure Cognitive Search
description: 'A Azure Cognitive Search pode ser aprovisionada nestes níveis: Gratuito, Básico e Standard, e a Standard está disponível em várias configurações de recursos e níveis de capacidade.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 062bd41b0803cbb08f74fbcbcebb89bbddeb0d45
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559808"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Escolha um nível de preços para Azure Cognitive Search

Ao [criar um serviço de pesquisa,](search-create-service-portal.md)escolha um nível de preços que seja fixado para o tempo de vida do serviço. O nível selecionado determina:

+ Quantidade de índices e outros objetos que pode criar (limites máximos)
+ Tamanho e velocidade das divisórias (armazenamento físico)
+ Taxa faturada, um custo mensal fixo, mas também um custo incremental se adicionar divisórias ou réplicas

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

A maioria das funcionalidades estão disponíveis em todos os níveis, incluindo o nível livre. Em alguns casos, o nível que escolher terá impacto na sua capacidade de implementar uma funcionalidade. A tabela a seguir descreve os constrangimentos de funcionalidades que estão relacionados com o nível de serviço.

| Funcionalidade | Limitações |
|---------|-------------|
| [indexadores](search-indexer-overview.md) | Os indexantes não estão disponíveis no S3 HD.  |
| [Melhoramento de IA](search-security-manage-encryption-keys.md) | Funciona no nível Livre, mas não é recomendado. |
| [Chaves de encriptação geridas pelo cliente](search-security-manage-encryption-keys.md) | Não disponível no nível Livre. |
| [Acesso a firewall IP](service-configure-firewall.md) | Não disponível no nível Livre. |
| [Ponto final privado (integração com a Azure Private Link)](service-create-private-endpoint.md) | Para ligações de entrada a um serviço de pesquisa, não está disponível no nível Gratuito. Para ligações de saída por indexadores a outros recursos Azure, não disponível em Free ou S3 HD. Para indexadores que usam skillsets, não disponíveis em Free, Basic, S1 ou S3 HD.|

As características intensivas em recursos podem não funcionar bem a menos que lhe dê capacidade suficiente. Por exemplo, [o enriquecimento](cognitive-search-concept-intro.md) de IA tem habilidades de longa duração que o tempo de serviço livre, a menos que o conjunto de dados seja pequeno.

## <a name="billable-events"></a>Eventos faturais

Uma solução construída na Azure Cognitive Search pode incorrer em custos das seguintes formas:

+ [Custo do próprio serviço,](#service-costs) com execução 24x7, na configuração mínima (uma divisória e réplica), à taxa base. Pode pensar nisto como o custo fixo de gerir o serviço.

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

A utilização [de indexantes](search-indexer-overview.md) pode afetar a faturação se a fonte de dados do Azure estiver numa região diferente da Pesquisa Cognitiva de Azure. Neste cenário, este é um custo para a transferência de dados de saída da fonte de dados Azure para a Azure Cognitive Search. 

Pode eliminar totalmente as taxas de saída de dados se criar o serviço de Pesquisa Cognitiva Azure na mesma região que os seus dados. Aqui estão algumas informações da página de preços da [largura de banda:](https://azure.microsoft.com/pricing/details/bandwidth/)

+ A Microsoft não cobra por quaisquer dados de entrada em qualquer serviço no Azure.
+ Não há nenhuma carga de dados de saída da Azure Cognitive Search. Por exemplo, se o seu serviço de pesquisa estiver no Oeste dos EUA, e uma aplicação Azure Web estiver no Leste dos EUA, a Microsoft não cobra pelas cargas de resposta de consulta que são originárias do Oeste dos EUA.
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

## <a name="next-steps"></a>Passos seguintes

A gestão de custos é parte integrante do planeamento de capacidades. Como próximo passo, continue com o seguinte artigo de orientação sobre como estimar a capacidade e gerir os custos.

> [!div class="nextstepaction"]
> [Como gerir custos e estimar capacidade na Pesquisa Cognitiva Azure](search-sku-manage-costs.md)