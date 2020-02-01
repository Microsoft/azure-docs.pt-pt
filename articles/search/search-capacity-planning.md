---
title: Capacidade de escala para consultas e cargas de trabalho indexadas
titleSuffix: Azure Cognitive Search
description: Ajuste os recursos informáticos de partição e réplica em Pesquisa Cognitiva Azure, onde cada recurso é avaliado em unidades de pesquisa faturadas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 349587063c528fef1cbdb09d84e61e82443d45d1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906725"
---
# <a name="scale-up-partitions-and-replicas-to-add-capacity-for-query-and-index-workloads-in-azure-cognitive-search"></a>Aumente as divisórias e réplicas para adicionar capacidade para consultas e cargas de trabalho indexadas na Pesquisa Cognitiva Azure

Depois de [escolher um nível](search-sku-tier.md) de preços e fornecer um serviço de [pesquisa,](search-create-service-portal.md)o próximo passo é aumentar opcionalmente o número de réplicas ou divisórias utilizadas pelo seu serviço. Cada nível oferece um número fixo de unidades de faturação. Este artigo explica como alocar essas unidades para alcançar uma configuração ideal que equilibra os seus requisitos de execução, indexação e armazenamento de consultas.

A configuração de recursos está disponível quando configura um serviço no [nível Básico](https://aka.ms/azuresearchbasic) ou num dos [níveis Standard ou Storage Optimized](search-limits-quotas-capacity.md). Para serviços a estes níveis, a capacidade é adquirida em incrementos de *unidades* de pesquisa (UsS) onde cada divisória e réplica conta como uma SU. 

A utilização de menos SUs resulta numa fatura proporcionalmente mais baixa. A faturação está em vigor enquanto o serviço estiver configurado. Se não estiver temporariamente a utilizar um serviço, a única forma de evitar a faturação é apagando o serviço e, em seguida, recriando-o quando precisa.

> [!Note]
> A eliminação de um serviço elimina todo o seu conteúdo. Não existe nenhuma instalação no Azure Cognitive Search para fazer cópia de segurança e restaurar dados de pesquisa persistentes. Para reimplantar um índice existente num novo serviço, deve executar o programa usado para criá-lo e carregá-lo originalmente. 

## <a name="terminology-replicas-and-partitions"></a>Terminologia: réplicas e divisórias
Réplicas e divisórias são os recursos primários que apoiam um serviço de pesquisa.

| Recurso | Definição |
|----------|------------|
|*Partições* | Fornece armazenamento de índicee E/S para operações de leitura/escrita (por exemplo, quando se reconstrói ou refresca um índice).|
|*Réplicas* | Casos do serviço de pesquisa, usado principalmente para carregar operações de consulta de saldo. Cada réplica acolhe sempre uma cópia de um índice. Se tiver 12 réplicas, terá 12 cópias de cada índice carregado no serviço.|

> [!NOTE]
> Não há forma de manipular ou gerir diretamente quais os índices que funcionam numa réplica. Uma cópia de cada índice em cada réplica faz parte da arquitetura de serviço.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Como alocar réplicas e divisórias
Na Pesquisa Cognitiva Azure, um serviço é inicialmente atribuído um nível mínimo de recursos constituído por uma partição e uma réplica. Para os níveis que o suportam, pode ajustar gradualmente os recursos computacionais aumentando as divisórias se precisar de mais armazenamento e I/O, ou adicionar mais réplicas para volumes de consulta maiores ou melhor desempenho. Um único serviço deve dispor de recursos suficientes para lidar com todas as cargas de trabalho (indexação e consultas). Não é possível subdividir as cargas de trabalho entre vários serviços.

Para aumentar ou alterar a atribuição de réplicas e divisórias, recomendamos a utilização do portal Azure. O portal impõe limites às combinações permitidas que se mantêm abaixo dos limites máximos. Se necessitar de uma abordagem de provisionamento baseada em scripts ou de código, o [Azure PowerShell](search-manage-powershell.md) ou o [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) são soluções alternativas.

Geralmente, as aplicações de pesquisa precisam de mais réplicas do que divisórias, especialmente quando as operações de serviço são tendenciosas para a carga de trabalho de consulta. A secção de [alta disponibilidade](#HA) explica porquê.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.

2. Em **Definições,** abra a página **Escala** para modificar réplicas e divisórias. 

   A imagem seguinte mostra um serviço padrão aprovisionado com uma réplica e partição. A fórmula na parte inferior indica quantas unidades de pesquisa estão a ser utilizadas (1). Se o preço unitário fosse de 100 dólares (não um preço real), o custo mensal de funcionamento deste serviço seria de 100 dólares, em média.

   ![Página de escala mostrando valores atuais](media/search-capacity-planning/1-initial-values.png "Página de escala mostrando valores atuais")

3. Utilize o slider para aumentar ou diminuir o número de divisórias. A fórmula na parte inferior indica quantas unidades de pesquisa estão a ser usadas.

   Este exemplo duplica a capacidade, com duas réplicas e divisórias cada. Note a contagem da unidade de pesquisa; são agora quatro porque a fórmula de faturação é réplicas multiplicadas por divisórias (2 x 2). Duplicar a capacidade mais do que duplica o custo de funcionamento do serviço. Se o custo da unidade de pesquisa fosse $100, a nova conta mensal seria agora $400.

   Para os custos atuais por unidade de cada nível, visite a [página de Preços](https://azure.microsoft.com/pricing/details/search/).

   ![Adicione réplicas e divisórias](media/search-capacity-planning/2-add-2-each.png "Adicione réplicas e divisórias")

3. Clique em **Guardar** para confirmar as alterações.

   ![Confirmar alterações à escala e faturação](media/search-capacity-planning/3-save-confirm.png "Confirmar alterações à escala e faturação")

   As mudanças de capacidade demoram várias horas a ser concluídas. Não é possível cancelar uma vez que o processo tenha começado e não exista monitorização em tempo real para adaptações de réplicas e divisórias. No entanto, a seguinte mensagem permanece visível enquanto estão em curso alterações.

   ![Mensagem de estado no portal](media/search-capacity-planning/4-updating.png "Mensagem de estado no portal")


> [!NOTE]
> Depois de um serviço ser prestado, não pode ser atualizado para um SKU mais elevado. Deve criar um serviço de pesquisa no novo nível e recarregar os seus índices. Consulte Criar um serviço de [Pesquisa Cognitiva Azure no portal](search-create-service-portal.md) para obter ajuda no fornecimento de serviços.
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Combinações de divisórias e réplicas

Um serviço Básico pode ter exatamente uma divisória e até três réplicas, para um limite máximo de três US. O único recurso ajustável são as réplicas. Você precisa de um mínimo de duas réplicas para alta disponibilidade em consultas.

Todos os serviços de pesquisa otimizados Standard e Storage podem assumir as seguintes combinações de réplicas e divisórias, sujeitos ao limite de 36-SU. 

|   | **1 partição** | **2 divisórias** | **3 divisórias** | **4 divisórias** | **6 divisórias** | **12 divisórias** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 réplicas** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 réplicas** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 réplicas** |4 SU |8 SU |12 SU |16 SU |24 SU |N/A |
| **5 réplicas** |5 SU |10 SU |15 SU |20 SU |30 SU |N/A |
| **6 réplicas** |6 SU |12 SU |18 SU |24 SU |36 SU |N/A |
| **12 réplicas** |12 SU |24 SU |36 SU |N/A |N/A |N/A |

As SUs, os preços e a capacidade são explicados em detalhe no site do Azure. Para mais informações, consulte [Detalhes de Preços](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> O número de réplicas e divisórias divide-se uniformemente em 12 (especificamente, 1, 2, 3, 4, 6, 12). Isto porque a Pesquisa Cognitiva Azure pré-divide cada índice em 12 fragmentos para que possa ser espalhado em partes iguais em todas as divisórias. Por exemplo, se o seu serviço tiver três divisórias e criar um índice, cada partição conterá quatro fragmentos do índice. Como a Pesquisa Cognitiva Azure fragmentos de um índice é um detalhe de implementação, sujeito a alterações em futuras versões. Embora o número seja 12 hoje, não deve esperar que esse número seja sempre 12 no futuro.
>


<a id="HA"></a>

## <a name="high-availability"></a>Elevada disponibilidade
Como é fácil e relativamente rápido de escalar, recomendamos geralmente que comece com uma divisória e uma ou duas réplicas, e depois aumente à medida que os volumes de consulta se acumulam. As cargas de trabalho de consulta funcionam principalmente em réplicas. Se precisar de mais entrada ou alta disponibilidade, provavelmente necessitará de réplicas adicionais.

Recomendações gerais para alta disponibilidade são:

* Duas réplicas para elevada disponibilidade de cargas de trabalho apenas para leitura (consultas)

* Três ou mais réplicas para elevada disponibilidade de cargas de trabalho de leitura/escrita (consultas mais indexação à medida que os documentos individuais são adicionados, atualizados ou eliminados)

Os acordos de nível de serviço (SLA) para a Pesquisa Cognitiva Azure são direcionados para operações de consulta e para atualizações de índices que consistem em adicionar, atualizar ou apagar documentos.

O nível básico é superior a uma divisória e três réplicas. Se quiser que a flexibilidade responda imediatamente às flutuações da procura tanto de indexação como de consulta, considere um dos níveis Standard.  Se descobrir que os seus requisitos de armazenamento estão a crescer muito mais rapidamente do que o seu serviço de consulta, considere um dos níveis Otimizados de Armazenamento.

### <a name="index-availability-during-a-rebuild"></a>Disponibilidade do índice durante uma reconstrução

A elevada disponibilidade para a Pesquisa Cognitiva Azure diz respeito a consultas e atualizações de índices que não envolvem a reconstrução de um índice. Se eliminar um campo, alterar um tipo de dados ou mudar o nome de um campo, terá de reconstruir o índice. Para reconstruir o índice, deve eliminar o índice, recriar o índice e recarregar os dados.

> [!NOTE]
> Pode adicionar novos campos a um índice de Pesquisa Cognitiva Azure sem reconstruir o índice. O valor do novo campo será nulo para todos os documentos já no índice.

Ao reconstruir o índice, haverá um período de tempo em que os dados estão a ser adicionados ao novo índice. Se quiser continuar a disponibilizar o seu antigo índice durante este período, deve ter uma cópia do antigo índice com um nome diferente no mesmo serviço, ou uma cópia do índice com o mesmo nome num serviço diferente. , e, em seguida, fornecer redirecionamento ou falha lógica no seu código.

## <a name="disaster-recovery"></a>Recuperação após desastre
Atualmente, não existe um mecanismo incorporado para a recuperação de desastres. Adicionar divisórias ou réplicas seria a estratégia errada para cumprir os objetivos de recuperação de desastres. A abordagem mais comum é adicionar redundância ao nível do serviço através da criação de um segundo serviço de pesquisa noutra região. Tal como acontece com a disponibilidade durante uma reconstrução do índice, a lógica de reorientação ou falha deve vir do seu código.

## <a name="increase-query-performance-with-replicas"></a>Aumentar o desempenho da consulta com réplicas
A latência de consultas é um indicador de que são necessárias réplicas adicionais. Geralmente, um primeiro passo para melhorar o desempenho da consulta é adicionar mais deste recurso. À medida que adiciona réplicas, cópias adicionais do índice são trazidas on-line para suportar maiores cargas de trabalho de consulta e para carregar o equilíbrio dos pedidos sobre as múltiplas réplicas.

Não podemos fornecer estimativas rígidas sobre consultas por segundo (QPS): o desempenho da consulta depende da complexidade da consulta e das cargas de trabalho concorrentes. Embora adicionar réplicas resulte claramente num melhor desempenho, o resultado não é estritamente linear: adicionar três réplicas não garante o triplo de entrada.

Para obter orientações na estimativa do QPS para as suas cargas de trabalho, consulte [o desempenho da Pesquisa Cognitiva Azure e considerações de otimização.](search-performance-optimization.md)

## <a name="increase-indexing-performance-with-partitions"></a>Aumentar o desempenho indexante com divisórias
As aplicações de pesquisa que requerem uma atualização de dados em tempo real precisarão proporcionalmente de mais divisórias do que réplicas. A adição de divisórias espalha operações de leitura/escrita através de um maior número de recursos computacionais. Também lhe dá mais espaço em disco para armazenar índices e documentos adicionais.

Os índices maiores demoram mais tempo a consultar. Como tal, pode descobrir que cada aumento incremental de divisórias requer um aumento menor mas proporcional de réplicas. A complexidade das suas consultas e volume de consultas irá influenciar a rapidez com que a execução da consulta é virada.


## <a name="next-steps"></a>Passos seguintes

[Escolha um nível de preços para pesquisa cognitiva azure](search-sku-tier.md)
