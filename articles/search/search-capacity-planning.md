---
title: Ajustar a capacidade de consulta e cargas de trabalho de índice
titleSuffix: Azure Cognitive Search
description: Ajuste os recursos informáticos de partição e réplica em Pesquisa Cognitiva Azure, onde cada recurso é avaliado em unidades de pesquisa faturadas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: bc90ecb029afe70ed61e94a727c67c53bb968b96
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212549"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Ajuste a capacidade na Pesquisa Cognitiva Azure

Antes de [fornecer um serviço](search-create-service-portal.md) de pesquisa e bloquear um determinado nível de preços, dedique alguns minutos para entender o papel das réplicas e divisórias num serviço, se precisa de divisórias proporcionalmente maiores ou mais rápidas, e como pode configurar o serviço para a carga esperada.

A capacidade é uma função do [nível que escolhe](search-sku-tier.md) (os níveis determinam características de hardware), e a combinação de réplica e partição necessária para cargas de trabalho projetadas. Este artigo centra-se nas combinações e interações de réplicas e divisórias.

## <a name="terminology-replicas-and-partitions"></a>Terminologia: réplicas e divisórias

|||
|-|-|
|*Partições* | Fornece armazenamento de índicee E/S para operações de leitura/escrita (por exemplo, quando se reconstrói ou refresca um índice). Cada partição tem uma parte do índice total. Se alocar três divisórias, o seu índice é dividido em terços. |
|*Réplicas* | Casos do serviço de pesquisa, usado principalmente para carregar operações de consulta de saldo. Cada réplica é uma cópia de um índice. Se alocar três réplicas, terá três cópias de um índice disponível para pedidos de consulta de manutenção.|

## <a name="how-to-allocate-replicas-and-partitions"></a>Como alocar réplicas e divisórias

Inicialmente, é atribuído um serviço a um nível mínimo de recursos constituído por uma partição e uma réplica. 

Um único serviço deve dispor de recursos suficientes para lidar com todas as cargas de trabalho (indexação e consultas). Nem a carga de trabalho corre em segundo plano. Pode agendar indexação para momentos em que os pedidos de consulta são naturalmente menos frequentes, mas o serviço não dará prioridade a uma tarefa em vez de outra.

Ao modificar a atribuição de réplicas e divisórias, recomendamos a utilização do portal Azure. O portal impõe limites às combinações permitidas que se mantêm abaixo dos limites máximos de um nível. No entanto, se necessitar de uma abordagem de provisionamento baseada em scripts ou de código, o [Azure PowerShell](search-manage-powershell.md) ou o [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) são soluções alternativas.

Regra geral, as aplicações de pesquisa tendem a necessitar de mais réplicas do que divisórias, especialmente quando as operações de serviço são tendenciosas para a carga de trabalho de consulta. A secção de [alta disponibilidade](#HA) explica porquê.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.

1. Em **Definições,** abra a página **Escala** para modificar réplicas e divisórias. 

   A imagem seguinte mostra um serviço padrão aprovisionado com uma réplica e partição. A fórmula na parte inferior indica quantas unidades de pesquisa estão a ser utilizadas (1). Se o preço unitário fosse de 100 dólares (não um preço real), o custo mensal de funcionamento deste serviço seria de 100 dólares, em média.

   ![Página de escala mostrando valores atuais](media/search-capacity-planning/1-initial-values.png "Página de escala mostrando valores atuais")

1. Utilize o slider para aumentar ou diminuir o número de divisórias. A fórmula na parte inferior indica quantas unidades de pesquisa estão a ser usadas.

   Este exemplo duplica a capacidade, com duas réplicas e divisórias cada. Note a contagem da unidade de pesquisa; são agora quatro porque a fórmula de faturação é réplicas multiplicadas por divisórias (2 x 2). Duplicar a capacidade mais do que duplica o custo de funcionamento do serviço. Se o custo da unidade de pesquisa fosse $100, a nova conta mensal seria agora $400.

   Para os custos atuais por unidade de cada nível, visite a [página de Preços](https://azure.microsoft.com/pricing/details/search/).

   ![Adicione réplicas e divisórias](media/search-capacity-planning/2-add-2-each.png "Adicione réplicas e divisórias")

1. Clique em **Guardar** para confirmar as alterações.

   ![Confirmar alterações à escala e faturação](media/search-capacity-planning/3-save-confirm.png "Confirmar alterações à escala e faturação")

   As mudanças de capacidade demoram várias horas a ser concluídas. Não é possível cancelar uma vez que o processo tenha começado e não exista monitorização em tempo real para adaptações de réplicas e divisórias. No entanto, a seguinte mensagem permanece visível enquanto estão em curso alterações.

   ![Mensagem de estado no portal](media/search-capacity-planning/4-updating.png "Mensagem de estado no portal")

> [!NOTE]
> Após a prestação de um serviço, não pode ser atualizado para um nível mais elevado. Deve criar um serviço de pesquisa no novo nível e recarregar os seus índices. Consulte Criar um serviço de [Pesquisa Cognitiva Azure no portal](search-create-service-portal.md) para obter ajuda no fornecimento de serviços.
>
> Além disso, divisórias e réplicas são geridas exclusiva e internamente pelo serviço. Não existe nenhum conceito de afinidade do processador, ou atribuir uma carga de trabalho a um nó específico.
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
| **4 réplicas** |4 SU |8 SU |12 SU |16 SU |24 SU |N/D |
| **5 réplicas** |5 SU |10 SU |15 SU |20 SU |30 SU |N/D |
| **6 réplicas** |6 SU |12 SU |18 SU |24 SU |36 SU |N/D |
| **12 réplicas** |12 SU |24 SU |36 SU |N/D |N/D |N/D |

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

## <a name="disaster-recovery"></a>Recuperação após desastre

Atualmente, não existe um mecanismo incorporado para a recuperação de desastres. Adicionar divisórias ou réplicas seria a estratégia errada para cumprir os objetivos de recuperação de desastres. A abordagem mais comum é adicionar redundância ao nível do serviço através da criação de um segundo serviço de pesquisa noutra região. Tal como acontece com a disponibilidade durante uma reconstrução do índice, a lógica de reorientação ou falha deve vir do seu código.

## <a name="estimate-replicas"></a>Estimar réplicas

Num serviço de produção, deve alocar três réplicas para fins sla. Se experimentar um desempenho de consulta lenta, um remédio é adicionar réplicas para que cópias adicionais do índice sejam trazidas on-line para suportar maiores cargas de trabalho de consulta e para carregar o equilíbrio dos pedidos sobre as múltiplas réplicas.

Não fornecemos orientações sobre quantas réplicas são necessárias para acomodar cargas de consulta. O desempenho da consulta depende da complexidade da consulta e das cargas de trabalho concorrentes. Embora adicionar réplicas resulte claramente num melhor desempenho, o resultado não é estritamente linear: adicionar três réplicas não garante o triplo de entrada.

Para obter orientação na estimativa do QPS para a sua solução, consulte [Scale para avaliação](search-performance-optimization.md)de desempenho e [consultas de monitor](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Estimar divisórias

O [nível que escolher](search-sku-tier.md) determina o tamanho e a velocidade da divisória, e cada nível é otimizado em torno de um conjunto de características que se adequam a vários cenários. Se escolher um nível de gama superior, pode precisar de menos divisórias do que se for com o S1.

As aplicações de pesquisa que requerem uma atualização de dados em tempo real precisarão proporcionalmente de mais divisórias do que réplicas. A adição de divisórias espalha operações de leitura/escrita através de um maior número de recursos computacionais. Também lhe dá mais espaço em disco para armazenar índices e documentos adicionais.

Os índices maiores demoram mais tempo a consultar. Como tal, pode descobrir que cada aumento incremental de divisórias requer um aumento menor mas proporcional de réplicas. A complexidade das suas consultas e volume de consultas irá influenciar a rapidez com que a execução da consulta é virada.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escolha um nível de preços para pesquisa cognitiva azure](search-sku-tier.md)