---
title: Compreenda relatórios da Insights em Azure Purview
description: Este artigo explica o que são insights em Azure Purview.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: d841fa336b2702a02f3215f97a6403217986d7e0
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554762"
---
# <a name="understand-insights-in-azure-purview"></a>Compreender Insights em Azure Purview

Este artigo fornece uma visão geral da funcionalidade Insights em Azure Purview.

Os insights são um dos pilares fundamentais da Purview. A funcionalidade fornece aos clientes, um único painel de vidro no seu catálogo e tem como objetivo fornecer informações específicas aos administradores de fontes de dados, utilizadores empresariais, administradores de dados, data officer e, administradores de segurança. Atualmente, a Purview tem os seguintes relatórios insights que estarão disponíveis para os clientes na pré-visualização pública.

## <a name="asset-insights"></a>Insights de Ativos

Este relatório dá uma visão de pássaro da sua propriedade de dados, e sua distribuição por tipo de fonte, por classificação e por tamanho de arquivo como algumas das dimensões. Este relatório atende a diferentes tipos de utilizadores que podem estar a gerir a conta Desrespeita e a executar digitalizações ou utilizadores empresariais que possam estar interessados em saber quantos ativos existem com uma determinada classificação dentro do espólio de dados da sua organização. 

O relatório fornece amplos conhecimentos através de gráficos e KPI's e, mais tarde, mergulha profundamente em anomalias específicas, tais como ficheiros extraviados. O relatório também apoia uma experiência de cliente de ponta a ponta, onde o cliente pode ver a contagem de ativos com uma classificação específica, pode repartir a informação por tipos de origem e pastas de topo, e também pode ver a lista de ativos para uma investigação mais aprofundada.

## <a name="scan-insights"></a>Digitalizar Insights

O relatório permite que os administradores compreendam a saúde geral dos exames - quantos conseguiram, quantos falharam, quantos foram cancelados. Este relatório apresenta uma atualização de estado sobre as verificações que foram executadas na conta 'Purview' num período de tempo de sete dias ou últimos 30 dias.

O relatório também permite que os administradores mergulhem e explorem quais os exames que falharam e em que tipos específicos de origem. Para permitir que os utilizadores investiguem, o relatório ajuda-os a navegar na página de histórico de digitalização dentro da experiência "Fontes".

## <a name="glossary-insights"></a>Insights Glossários

Este relatório dá aos utilizadores empresariais e aos administradores de dados um relatório sobre o estado do glossário. Os utilizadores podem ver este relatório para entender a distribuição de termos glossários por estado, saber quantos termos glossários estão ligados aos ativos e quantos ainda não estão ligados a qualquer ativo. Os utilizadores empresariais também podem aprender sobre a completude dos seus termos glossários. 

Este relatório resume os principais itens em que um utilizador de negócios ou um administrador de dados precisa de se concentrar, para criar um glossário completo e utilizável para a sua organização. Os utilizadores também podem navegar na experiência "Glossário" a partir da experiência "Glossary Insights", para fazer alterações num termo glossário específico.

## <a name="classification-insights"></a>Insights de classificação

Este relatório fornece detalhes sobre onde os dados classificados estão localizados, as classificações encontradas durante uma varredura, e um exercício para os próprios ficheiros classificados. Permite que os administradores de segurança compreendam os tipos de informação encontrados no espólio de dados da sua organização. 

Em Azure Purview, as classificações são semelhantes às etiquetas de assunto, e são usadas para marcar e identificar conteúdo de um tipo específico na sua propriedade de dados.

Utilize o relatório Descosição de Insights para identificar conteúdo com classificações específicas e compreender as ações necessárias, tais como adicionar segurança adicional aos repositórios ou mover o conteúdo para um local mais seguro.

Para mais informações, consulte [os insights de classificação sobre os seus dados a partir do Azure Purview.](classification-insights.md)

## <a name="sensitivity-labeling-insights"></a>Insights de rotulagem de sensibilidade

Este relatório fornece detalhes sobre as etiquetas de sensibilidade encontradas durante uma varredura, bem como uma perfuração para os próprios ficheiros rotulados. Permite que os administradores de segurança garantam a segurança das informações encontradas no espólio de dados da sua organização. 

No Azure Purview, os rótulos de sensibilidade são usados para identificar categorias de tipos de classificação, bem como as políticas de segurança de grupo que pretende aplicar a cada categoria.

Utilize o relatório Labeling Insights para identificar as etiquetas de sensibilidade encontradas no seu conteúdo e compreenda as ações necessárias, tais como gerir o acesso a repositórios ou ficheiros específicos.

Para obter mais informações, consulte [insights do rótulo de sensibilidade sobre os seus dados no Azure Purview](sensitivity-insights.md).

## <a name="file-extension-insights"></a>Insights de extensão de ficheiros

Este relatório fornece detalhes sobre as extensões de ficheiros, ou tipos de ficheiros, encontrados durante uma varredura, bem como um aprofundamento dos próprios ficheiros. 

Utilize o relatório de Insights de Extensão de Ficheiros para perceber quantos ficheiros de cada vez que tiver, onde esses ficheiros estão e se são digitalizáveis para dados sensíveis.

Para obter mais informações, consulte [insights de extensão de ficheiros sobre os seus dados a partir do Azure Purview](file-extension-insights.md).

## <a name="next-steps"></a>Passos seguintes

* [Insights glossários](glossary-insights.md)
* [Digitalizar insights](scan-insights.md)
* [Insights de classificação](./classification-insights.md)