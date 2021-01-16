---
title: Cálculo de custos
titleSuffix: Azure Cognitive Search
description: Saiba mais sobre eventos faturantes, o modelo de preços e dicas para gerir o custo de gerir um serviço de Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: a708fb76b5a3d0fd0683cdb8915d1a5e1824a57c
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251673"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Como estimar e gerir os custos de um serviço de Pesquisa Cognitiva Azure

Neste artigo, conheça o modelo de preços, eventos faturantes e dicas para gerir o custo de gerir um serviço de Pesquisa Cognitiva Azure.

## <a name="pricing-model"></a>Modelo preços

A arquitetura de escalabilidade em Azure Cognitive Search baseia-se em combinações flexíveis de réplicas e divisórias para que possa variar a capacidade dependendo se você precisa de mais consulta ou poder de indexação, e pagar apenas pelo que você precisa.

A quantidade de recursos utilizados pelo seu serviço de pesquisa, multiplicado pela taxa de faturação estabelecida pelo nível de serviço, determina o custo de execução do serviço. Os custos e a capacidade estão bem limitados. Ao estimar os custos, compreender a capacidade necessária para executar as suas cargas de trabalho de indexação e consulta dá-lhe a melhor ideia sobre quais serão os custos previstos.

Para efeitos de faturação, a Cognitive Search tem o conceito de unidade de *pesquisa* (SU). Um SU é o produto das *réplicas* e *divisórias utilizadas* por um serviço: **(R x P = SU)**. O número de SUs multiplicados pela taxa de faturação **(taxa SU * = gasto mensal)** é o principal determinante dos custos relacionados com a procura. 

Cada serviço começa com um SU (uma réplica multiplicada por uma partição) como mínimo. O máximo para qualquer serviço é 36 SUs. Este máximo pode ser alcançado de várias maneiras: 6 divisórias x 6 réplicas, ou 3 divisórias x 12 réplicas, por exemplo. É comum usar menos do que a capacidade total (por exemplo, um serviço de 3 réplicas, 3 divisórias faturadas como 9 SUs). Consulte o gráfico de [combinações de divisórias e réplicas](search-capacity-planning.md#chart) para obter combinações válidas.

A taxa de faturação é de hora a hora por SU. Cada nível tem uma taxa progressivamente mais alta. Os níveis mais altos vêm com divisórias maiores e mais rápidas, o que contribui para uma taxa horária globalmente mais alta para esse nível. Pode ver as tarifas de cada nível na página [de detalhes de preços.](https://azure.microsoft.com/pricing/details/search/)

A maioria dos clientes traz apenas uma parte da capacidade total on-line, mantendo o resto em reserva. Para faturação, o número de divisórias e réplicas que traz on-line, calculadas pela fórmula SU, determina o que se paga de hora a hora. 

## <a name="billable-events"></a>Eventos faturais

Uma solução construída na Azure Cognitive Search pode incorrer em custos das seguintes formas:

+ [Custo do próprio serviço,](#service-costs) com execução 24x7, na configuração mínima (uma divisória e réplica), à taxa base. Pode pensar nisto como o custo fixo de gerir o serviço.

+ Capacidade de adição (réplicas ou divisórias), onde os custos aumentam a incrementos da taxa de faturação. Se a alta disponibilidade for um requisito de negócio, você precisará de 3 réplicas.

+ Taxas de largura de banda (transferência de dados de saída)

+ Serviços adicionais necessários para capacidades ou funcionalidades específicas:

  + Enriquecimento de IA (requer [Serviços Cognitivos)](https://azure.microsoft.com/pricing/details/cognitive-services/)
  + loja de conhecimento (requer [armazenamento Azure)](https://azure.microsoft.com/pricing/details/storage/)
  + enriquecimento incremental (requer [armazenamento Azure,](https://azure.microsoft.com/pricing/details/storage/)aplica-se ao enriquecimento de IA)
  + chaves geridas pelo cliente e encriptação dupla (requer [cofre de chave Azure)](https://azure.microsoft.com/pricing/details/key-vault/)
  + pontos finais privados para um modelo de acesso sem internet (requer [Azure Private Link)](https://azure.microsoft.com/pricing/details/private-link/)

### <a name="service-costs"></a>Custos de serviço

Ao contrário de máquinas virtuais ou outros recursos que podem ser "pausados" para evitar custos, um serviço de Pesquisa Cognitiva Azure está sempre disponível em hardware dedicado para uso exclusivo. Como tal, criar um serviço é um evento faturalável que começa quando cria o serviço, e termina quando apaga o serviço. 

A carga mínima é a primeira unidade de pesquisa (uma réplica x uma partição) à taxa faturada. Este mínimo é fixado para o tempo de vida do serviço porque o serviço não pode funcionar em nada menos do que esta configuração. 

Além do mínimo, pode adicionar réplicas e divisórias independentemente umas das outras. Aumentos incrementais de capacidade através de réplicas e divisórias aumentarão a sua fatura com base na seguinte fórmula: **(replicações x partições x taxa de faturação)**, onde a taxa que é cobrada depende do nível de preços que seleciona.

Quando estimar o custo de uma solução de pesquisa, lembre-se que os preços e a capacidade não são lineares (duplicar a capacidade mais do que duplica o custo). Para um exemplo de como funciona a fórmula, consulte [Como atribuir réplicas e divisórias.](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)

### <a name="bandwidth-charges"></a>Taxas de largura de banda

A utilização [de indexantes](search-indexer-overview.md) pode afetar a faturação se a fonte de dados do Azure estiver numa região diferente da Pesquisa Cognitiva de Azure. Neste cenário, pode haver um custo para a transferência de dados de saída da fonte de dados Azure para a Azure Cognitive Search. Para mais detalhes, consulte as páginas de preços da plataforma de dados Azure em questão.

Pode eliminar totalmente as taxas de saída de dados se criar o serviço de Pesquisa Cognitiva Azure na mesma região que os seus dados. Aqui estão algumas informações da página de preços da [largura de banda:](https://azure.microsoft.com/pricing/details/bandwidth/)

+ Dados de entrada: A Microsoft não cobra por quaisquer dados de entrada em qualquer serviço no Azure. 

+ Dados de saída: Os dados de saída referem-se aos resultados da consulta. A Pesquisa Cognitiva não cobra por dados de saída, mas os encargos de saída do Azure são possíveis se os serviços estiverem em diferentes regiões.

  Estas acusações não fazem parte da sua conta de Pesquisa Cognitiva Azure. São mencionados aqui porque se estiver a enviar resultados para outras regiões ou aplicações não-Azure, poderá ver esses custos refletidos na sua conta geral.

### <a name="ai-enrichment-with-cognitive-services"></a>Enriquecimento de IA com Serviços Cognitivos

Para [o enriquecimento de IA,](cognitive-search-concept-intro.md)você deve [planejar anexar um recurso de Serviços Cognitivos Azure,](cognitive-search-attach-cognitive-services.md)na mesma região que a Azure Cognitive Search, no nível de preços S0 para processamento pay-as-you-go. Não há um custo fixo associado à anexação dos Serviços Cognitivos. Paga-se apenas pelo processamento de que precisa.

| Operação | Impacto da faturação |
|-----------|----------------|
| Impressão de documentos, extração de texto | Gratuito |
| Rachadura de documento, extração de imagem | Faturado de acordo com o número de imagens extraídas dos seus documentos. Numa [configuração indexante,](/rest/api/searchservice/create-indexer#indexer-parameters) **a imageAction** é o parâmetro que desencadeia a extração de imagem. Se **a imageAction** estiver definida como "nenhuma" (o padrão), não será cobrado para a extração de imagem. A taxa de extração de imagem está documentada na página [de detalhes de preços](https://azure.microsoft.com/pricing/details/search/) para Azure Cognitive Search.|
| [Competências cognitivas incorporadas](cognitive-search-predefined-skills.md) | Faturado ao mesmo ritmo que se tivesse realizado a tarefa usando os Serviços Cognitivos diretamente. |
| Competências personalizadas | Uma habilidade personalizada é a funcionalidade que fornece. O custo de utilização de uma habilidade personalizada depende inteiramente de se o código personalizado está a chamar outros serviços medidos. |

A funcionalidade [de enriquecimento incremental (pré-visualização)](cognitive-search-incremental-indexing-conceptual.md) permite-lhe fornecer uma cache que permite ao indexante ser mais eficiente na execução apenas das capacidades cognitivas necessárias se modificar o seu skillset no futuro, poupando-lhe tempo e dinheiro.

## <a name="tips-for-managing-costs"></a>Dicas para gerir custos

As seguintes orientações podem ajudá-lo a baixar os custos ou gerir os custos de forma mais eficaz:

+ Criar todos os recursos na mesma região, ou no menor tempo possível, para minimizar ou eliminar as taxas de largura de banda.

+ Consolidar todos os serviços num único grupo de recursos, como a Azure Cognitive Search, Serviços Cognitivos e quaisquer outros serviços Azure utilizados na sua solução. No portal Azure, encontre o grupo de recursos e utilize os comandos **de Gestão** de Custos para obter informações sobre os gastos reais e projetados.

+ Considere a Azure Web App para a sua aplicação frontal para que os pedidos e respostas permaneçam dentro do limite do data center.

+ Dimensione para operações intensivas de recursos como indexação e, em seguida, reajuste para baixo para cargas de trabalho regulares de consulta. Comece com a configuração mínima para Azure Cognitive Search (um SU composto por uma partição e uma réplica), e depois monitorize a atividade do utilizador para identificar padrões de utilização que indiquem a necessidade de mais capacidade. Se houver um padrão previsível, poderá sincronizar a escala com a atividade (precisaria de escrever código para automatizar isto).

+ A gestão de custos está integrada na infraestrutura Azure. [Reveja a faturação e a gestão de custos](../cost-management-billing/cost-management-billing-overview.md) para obter mais informações sobre custos de rastreio, ferramentas e APIs.

Não é possível encerrar um serviço de pesquisa temporariamente. Os recursos dedicados estão sempre operacionais, alocados para uso exclusivo para o tempo de vida do seu serviço. A eliminação de um serviço é permanente e também elimina os seus dados associados.

Em termos do próprio serviço, a única maneira de baixar a sua fatura é reduzir as réplicas e divisórias para um nível que ainda proporciona um desempenho aceitável e [conformidade com o SLA,](https://azure.microsoft.com/support/legal/sla/search/v1_0/)ou criar um serviço a um nível inferior (as tarifas S1 por hora são inferiores às de S2 ou S3). Assumindo que presta o seu serviço na parte inferior das suas projeções de carga, se superar o serviço, pode criar um segundo serviço de maior nível, reconstruir os seus índices no segundo serviço e, em seguida, apagar o primeiro.

## <a name="next-steps"></a>Passos seguintes

Saiba como monitorizar e gerir os custos através da sua subscrição Azure.

> [!div class="nextstepaction"]
> [Documentação do Azure Cost Management e Faturação](../cost-management-billing/cost-management-billing-overview.md)