---
title: Resolução de problemas Azure Monitor insights baseados em livros
description: Fornece orientações de resolução de problemas para insights baseados em livros do Azure Monitor para serviços como Azure Key Vault, Azure CosmosDB, Azure Storage e Azure Cache para Redis.
services: azure-monitor
ms.author: lagayhar
author: lgayhardt
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: a823b5e2ae31e62ff6d876f0b3468953c0ed9c35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100587159"
---
# <a name="troubleshooting-workbook-based-insights"></a>Insights baseados em livros de resolução de problemas

Este artigo irá ajudá-lo com o diagnóstico e resolução de problemas de algumas das questões comuns que poderá encontrar ao utilizar insights baseados em livros do Azure Monitor.


## <a name="why-can-i-only-see-200-resources"></a>Por que só posso ver 200 recursos

O número de recursos selecionados tem um limite de 200, independentemente do número de subscrições selecionadas.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>O que acontece quando clico num azulejo recentemente preso no tablier

* Se clicar em qualquer lugar do azulejo, irá levá-lo ao separador de onde o azulejo foi fixado. Por exemplo, se fixar um gráfico no separador "Visão Geral" então, quando clicar nesse azulejo no painel de instrumentos, abrirá essa vista padrão, no entanto, se fixar um gráfico da sua própria cópia guardada, abrirá a vista da sua cópia guardada.
* O ícone do filtro na parte superior esquerda do título abre o separador "Configurações de azulejos configurantes configure".
* O ícone de elipse no topo direito irá dar-lhe as opções para "Personalizar dados de títulos", "personalizar", "refrescar" e "remover do painel de instrumentos".

## <a name="what-happens-when-i-save-a-workbook"></a>O que acontece quando guardo um livro

* Quando guarda um livro, permite criar uma nova cópia do livro com as suas edições e alterar o título. A poupança não substitui excessivamente o livro, o livro atual será sempre a vista padrão.
* Um **livro não salvo** é apenas a vista padrão.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Por que não vejo todas as minhas assinaturas no portal

O portal mostrará apenas dados para subscrições selecionadas no lançamento do portal. Para alterar as subscrições selecionadas, vá para a direita superior e clique no portátil com um ícone de filtro. Esta opção mostrará o **separador Diretório + subscrições.**

![Diretório + subscrição](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>O que é o intervalo de tempo

O intervalo de tempo mostra-lhe dados de um determinado período de tempo. Por exemplo, se o intervalo de tempo for de 24 horas, então está a mostrar dados das últimas 24 horas.

## <a name="what-is-time-granularity-time-grain"></a>O que é a granularidade do tempo (grão de tempo)

A granularidade temporal é a diferença de tempo entre dois pontos de dados. Por exemplo, se o grão de tempo for definido para 1 segundo, significa que as métricas são recolhidas a cada segundo.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Qual é o tempo de granularidade uma vez que colocamos qualquer parte dos livros de trabalho em um painel de instrumentos

O tempo padrão de granularidade é definido para automático, atualmente não pode ser alterado neste momento.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Como posso alterar o intervalo de tempo/intervalo do passo do livro no meu painel de instrumentos

Por predefinição, o intervalo tempo/tempo no seu azulejo do painel de instrumentos está definido para 24 horas, para alterar este clique nas elipses no direito superior, **selecione Personalize os dados de azulejos,** verifique "sobrepor as definições de tempo do painel de instrumentos no nível do título" e, em seguida, escolha uma intervalo de tempo utilizando o menu dropdown.  

![Selecione as elipses no canto direito do azulejo e escolha Personalizar estes dados](./media/storage-insights-overview/fqa-data-settings.png)

![Nas definições de azulejos configurantes, selecione a queda da escala de tempo para alterar o intervalo de tempo/intervalo de tempo](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Como posso mudar o título do livro ou um passo de livro que fixei a um painel de instrumentos

O título do livro ou passo do livro que é fixado a um dashboard mantém o mesmo nome que tinha no livro. Para alterar o título, tem de guardar a sua própria cópia do livro. Em seguida, poderá nomear o livro antes de premir o save.

![Selecione guardar na parte superior para guardar uma cópia do livro e alterar o nome do mesmo](./media/storage-insights-overview/fqa-change-workbook-name.png)

Para alterar o nome de um passo no seu livro de trabalho guardado, selecione editar sob o degrau e, em seguida, selecione a engrenagem na parte inferior das definições.

![Selecione editar na parte inferior de um passo de livro para abrir as definições ](./media/storage-insights-overview/fqa-edit.png)
 ![ Nas definições selecione a engrenagem na parte inferior, para ser capaz de alterar o nome do passo](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os cenários que os livros de trabalho são projetados para apoiar, como autorizar novos e personalizar relatórios existentes, e mais através da revisão [de relatórios de criar relatórios interativos com os livros de trabalho do Azure Monitor.](../visualize/workbooks-overview.md)
