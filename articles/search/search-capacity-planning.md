---
title: Dimensionamento partições e réplicas para consulta e indexação - pesquisa do Azure
description: Ajuste os recursos de computador de partição e réplica no Azure Search, onde cada recurso é cobrado em unidades de pesquisa faturável.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 69fce34c55007daff48b2463da590ffb9cd59926
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775327"
---
# <a name="scale-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Dimensionar partições e réplicas para consulta e indexação de cargas de trabalho no Azure Search
Depois de [escolher um escalão de preço](search-sku-tier.md) e [aprovisionar um serviço de pesquisa](search-create-service-portal.md), a próxima etapa é para, opcionalmente, aumentar o número de réplicas ou partições utilizadas pelo seu serviço. Cada escalão oferece um número fixo de unidades de faturação. Este artigo explica como alocar essas unidades para obter uma configuração ideal, que equilibra a seus requisitos para a execução da consulta, indexação e armazenamento.

Configuração do recurso está disponível quando configurar um serviço à [escalão básico](https://aka.ms/azuresearchbasic) ou uma da [escalões Standard](search-limits-quotas-capacity.md). Para os serviços nestas camadas, a capacidade é comprada em incrementos de *unidades de pesquisa* (SUs) em que cada partição e réplica contam como um SU. 

Usando menos SUs resultados numa fatura proporcionalmente inferior. A faturação é em vigor para, desde que o serviço está configurado. Se temporariamente não estiver a utilizar um serviço, a única forma de evitar a faturação é ao eliminar o serviço e, em seguida, criá-lo novamente quando precisa dela.

> [!Note]
> Eliminar um serviço elimina tudo no mesmo. Há um recurso no Azure Search para criar cópias de segurança e restaurar dados de pesquisa de persistentes. Para Reimplementar um índice existente num novo serviço, deve executar o programa utilizado para criar e carregá-lo originalmente. 

## <a name="terminology-replicas-and-partitions"></a>Terminologia: réplicas e partições
Réplicas e partições são os recursos principais que fazer uma cópia de um serviço de pesquisa.

| Recurso | Definição |
|----------|------------|
|*Partições* | Fornece armazenamento de índice e e/s para operações de leitura/escrita (por exemplo, quando reconstruir ou atualizar um índice).|
|*Réplicas* | Instâncias do serviço de pesquisa, usado principalmente para operações de consulta de balanceamento de carga. Cada réplica aloja sempre uma cópia de um índice. Se tiver 12 réplicas, terá 12 cópias de todos os índices carregado no serviço.|

> [!NOTE]
> Não é possível diretamente manipular ou gerir a quais índices executam numa réplica. Uma cópia de cada índice em cada réplica faz parte da arquitetura do serviço.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Como alocar réplicas e partições
No Azure Search, um serviço inicialmente é atribuído um nível mínimo de recursos consiste numa partição e uma réplica. Para os escalões que o suportam, pode ajustar incrementalmente recursos informáticos, aumentando as partições, se precisar de mais armazenamento e e/s ou adicionar mais réplicas de volumes maiores de consulta ou um melhor desempenho. Um único serviço tem de ter recursos suficientes para processar todas as cargas de trabalho (de indexação e de consultas). Não pode subdividir cargas de trabalho entre vários serviços.

Para aumentar ou alterar a alocação das réplicas e partições, recomendamos que utilize o portal do Azure. O portal impõe limites em combinações permitidas que fique abaixo limites máximos. Se necessitar de uma abordagem de aprovisionamento baseado em script ou código, o [do Azure PowerShell](search-manage-powershell.md) ou o [API de REST de gestão](https://docs.microsoft.com/rest/api/searchmanagement/services) são soluções alternativas.

Geralmente, procurar aplicações precisam de mais réplicas de partições, especialmente quando as operações de serviço são inclinadas em direção à cargas de trabalho de consulta. A seção sobre [elevada disponibilidade](#HA) explica o motivo.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.
2. Na **configurações**, abra o **dimensionamento** para modificar as réplicas e partições. 

   Captura de ecrã seguinte mostra um serviço padrão aprovisionado com uma réplica e a partição. A fórmula na parte inferior indica quantas unidades de pesquisa estão a ser utilizado (1). Se o preço unitário de US $100 (não um preço real), o custo mensal de executar este serviço seria US $100 em média.

   ![Página de escala que mostra os valores atuais](media/search-capacity-planning/1-initial-values.png "página de escala que mostra os valores atuais")

3. Utilize o controlo de deslize para aumentar ou diminuir o número de partições. A fórmula na parte inferior indica quantas unidades de pesquisa estão sendo usadas.

   Este exemplo dobra a capacidade, com duas réplicas e partições a cada um. Observe que a contagem de unidades de pesquisa; Agora, é quatro porque a fórmula de faturação é réplicas multiplicadas por partição (2 x 2). Mais do que dobra a capacidade duplica o custo de executar o serviço. Se o custo da unidade de pesquisa foi de US $100, a fatura mensal novo agora seria us $400.

   Para o atual por custos de unidade de cada escalão, visite o [página de preços](https://azure.microsoft.com/pricing/details/search/).

   ![Adicionar réplicas e partições](media/search-capacity-planning/2-add-2-each.png "adicionar réplicas e partições")

3. Clique em **guardar** para confirmar as alterações.

   ![Confirmar alterações à escala e faturação](media/search-capacity-planning/3-save-confirm.png "Confirmar alterações à escala e de faturação")

   As alterações na capacidade demorar várias horas a concluir. Não é possível cancelar uma vez que iniciou o processo e não existe nenhum monitorização em tempo real para a réplica e a partição de ajustes. No entanto, a seguinte mensagem permanece visível enquanto as alterações estão em curso.

   ![Mensagem de estado no portal](media/search-capacity-planning/4-updating.png "mensagem de estado no portal")


> [!NOTE]
> Depois de um serviço é aprovisionado, não é possível atualizar para um SKU superior. Tem de criar um serviço de pesquisa na camada de novo e volte a carregar índices. Ver [criar um serviço Azure Search no portal do](search-create-service-portal.md) para obter ajuda com o serviço de aprovisionamento.
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Combinações de partição e réplica

Um serviço básico pode ter exatamente uma partição e até três réplicas, para um máximo limite de três SUs. O recurso apenas ajustável está réplicas. É necessário um mínimo de duas réplicas para elevada disponibilidade em consultas.

Todos os serviços padrão podem assumir as seguintes combinações das réplicas e partições, sujeita o limite de 36 SU. 

|   | **1 partição** | **2 partições** | **3 partições** | **4 partições** | **6 partições** | **12 partições** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 DE SU |
| **2 réplicas** |2 SU |4 SU |6 SU |8 SU |12 DE SU |24 DE SU |
| **3 réplicas** |3 SU |6 SU |9 SU |12 DE SU |18 DE SU |36 SU |
| **4 réplicas** |4 SU |8 SU |12 DE SU |16 DE SU |24 DE SU |N/A |
| **5 réplicas** |5 SU |10 SU |15 DE SU |20 DE SU |30 SU |N/A |
| **6 réplicas** |6 SU |12 DE SU |18 DE SU |24 DE SU |36 SU |N/A |
| **12 réplicas** |12 DE SU |24 DE SU |36 SU |N/A |N/D |N/A |

SUs, preços e a capacidade são explicados em detalhe no site do Azure. Para obter mais informações, consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> O número de réplicas e partições divide-se uniformemente em 12 (especificamente, 1, 2, 3, 4, 6, 12). Isto acontece porque o Azure Search divide previamente cada índice em partições 12 horizontais para que possam ser distribuído em partes iguais em todas as partições. Por exemplo, se o seu serviço tem três partições e criar um índice, cada partição contém quatro partições horizontais do índice. Como as partições horizontais de Azure Search um índice é um detalhe de implementação, sujeitas a alterações em versões futuras. Embora o número é 12 hoje em dia, não deve esperar que esse número para estar sempre 12 no futuro.
>


<a id="HA"></a>

## <a name="high-availability"></a>Elevada disponibilidade
Como é fácil e relativamente rápida para aumentar verticalmente, em geral, recomendamos que comece com uma partição e uma ou duas réplicas e, em seguida, aumentar verticalmente como volumes de consulta de compilação. Executam cargas de trabalho de consulta principalmente em réplicas. Se precisar de mais débito ou elevada disponibilidade, provavelmente exigirá réplicas adicionais.

Recomendações gerais para elevada disponibilidade são:

* Duas réplicas para elevada disponibilidade de só de leitura cargas de trabalho (consultas)
* Três ou mais réplicas para elevada disponibilidade de cargas de trabalho de leitura/gravação (consultas mais indexação como documentos individuais são adicionados, atualizados ou eliminados)

Contratos de nível de serviço (SLA) para o Azure Search são direcionados em operações de consulta e em atualizações de índice que consistem em Adicionar, atualizar ou eliminar documentos.

Escalão básico anterior numa partição e três réplicas. Se pretender que a flexibilidade para imediatamente responder a flutuações que demandam para débito de indexação e consulta, considere um dos escalões Standard.

### <a name="index-availability-during-a-rebuild"></a>Disponibilidade durante uma reconstrução do índice

Elevada disponibilidade para o Azure Search pertence às consultas e atualizações de índice que não envolvam recompilar um índice. Se eliminar um campo, alterar um tipo de dados ou mude o nome de um campo, terá de recriar o índice. Para recriar o índice, tem de eliminar o índice, recrie o índice e, recarregar os dados.

> [!NOTE]
> Pode adicionar novos campos para um índice da Azure Search sem reconstrua o índice. O valor do novo campo será nulo para todos os documentos já no índice.

Para manter a disponibilidade de índice durante a reconstrução de um, tem de ter uma cópia do índice com um nome diferente no mesmo serviço ou uma cópia do índice com o mesmo nome num serviço diferente e, em seguida, forneça o redirecionamento ou lógica de ativação pós-falha no seu código.

## <a name="disaster-recovery"></a>Recuperação após desastre
Atualmente, não existe nenhum mecanismo incorporado para recuperação após desastre. Adicionar partições ou réplicas seria a estratégia incorreta para cumprir os objetivos de recuperação após desastre. A abordagem mais comum é adicionar redundância no nível de serviço ao configurar um serviço de pesquisa segundo noutra região. Tal como acontece com a disponibilidade durante uma recompilação de índice, o redirecionamento ou lógica de ativação pós-falha deve provir do seu código.

## <a name="increase-query-performance-with-replicas"></a>Aumentar o desempenho de consulta com réplicas
Latência da consulta é um indicador que as réplicas adicionais são necessárias. Em geral, uma primeira etapa para melhorar o desempenho de consulta é adicionar mais deste recurso. À medida que adiciona as réplicas, cópias adicionais do índice são colocadas online para suportar as maiores cargas de trabalho de consulta e carregar equilibrar os pedidos ao longo de várias réplicas.

Não é possível disponibilizamos as estimativas de disco rígidas sobre as consultas por segundo (QPS): consulta desempenho depende da complexidade da consulta e cargas de trabalho concorrentes. Embora adicionar réplicas claramente resulta num melhor desempenho, o resultado não é estritamente linear: adicionar três réplicas não garante a taxa de transferência tripla.

Para obter orientações sobre como estimar QPS das cargas de trabalho, consulte [considerações de desempenho e Otimização da Azure Search](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Aumentar o desempenho da indexação com partições
Procurar aplicações que necessitam de perto a atualização de dados em tempo real serão necessário proporcionalmente mais partições que as réplicas. Adicionar partições propaga o operações de leitura/escrita num número maior de recursos de computação. Também lhe dá mais espaço em disco para armazenar documentos e índices adicionais.

Os índices maiores demoram mais tempo a consulta. Assim, pode achar que cada aumento incremental em partições requer um aumento de menor mas proporcional em réplicas. A complexidade de suas consultas e o volume de consultas será fatorar em quão rapidamente a execução da consulta é se viraram.


## <a name="next-steps"></a>Passos Seguintes

[Escolha um escalão de preço para o Azure Search](search-sku-tier.md)
