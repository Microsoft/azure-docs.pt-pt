---
title: Funcionalidades de pedido de dados do cliente - Azure Time Series Insights / Microsoft Docs
description: Conheça as funcionalidades de pedido de dados dos clientes em Insights da Série Time Azure.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/14/2020
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: 64660e497bb1765b649e00b07fdb5db8c05910f5
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368692"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados do cliente

O Azure Time Series Insights é um serviço de nuvem gerido com componentes de armazenamento, análise e visualização que facilitam a ingestão, loja, exploração e análise de milhares de milhões de eventos simultaneamente.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Para visualizar, exportar e eliminar dados pessoais que possam estar sujeitos a um pedido de titular de dados, um administrador de inquilino da Série De Tempo Azure Insights pode utilizar o portal Azure ou as APIs rest. Utilizando o portal Azure para atender pedidos de dados, o caso fornece um método menos complexo para realizar estas operações que a maioria dos utilizadores prefere.

## <a name="identifying-customer-data"></a>Identificação dos dados dos clientes

A Azure Time Series Insights considera que os dados pessoais são dados associados a administradores e utilizadores de Time Series Insights. Time Series Insights armazena o Azure Ative Directory object-ID dos utilizadores com acesso ao ambiente. O portal Azure exibe endereços de e-mail de utilizador, mas estes endereços de e-mail não são armazenados dentro da Time Series Insights, são analisados dinamicamente usando o Id de objeto sonorizador Azure Ative no Diretório Ativo Azure.

## <a name="deleting-customer-data"></a>A eliminação de dados do cliente

Um administrador de inquilino pode eliminar os dados dos clientes através do portal Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

No entanto, antes de eliminar os dados do cliente através do portal, deverá remover as políticas de acesso do utilizador do ambiente Time Series Insights dentro do portal Azure. Para mais informações, leia o acesso dos dados do Grant a um ambiente time series insights utilizando o [portal Azure](time-series-insights-data-access.md).

Também pode efetuar operações de eliminação de políticas de acesso utilizando a API REST. Para mais informações, leia [Políticas de Acesso - Eliminar](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/delete).

A Time Series Insights está integrada com a lâmina política no portal Azure. Tanto os Insights da Série Time como a lâmina Política permitem-lhe visualizar, exportar e eliminar os dados dos utilizadores armazenados dentro do serviço. Qualquer ação de exclusão tomada dentro da lâmina política do portal Azure resulta na eliminação dos dados dos utilizadores dentro da Time Series Insights. Por exemplo, se um utilizador tiver uma consulta pessoal guardada, essa consulta é permanentemente eliminada do explorador de Insights da Série Time. Se o utilizador tiver uma consulta partilhada guardada, a consulta persiste, mas a informação do utilizador é permanentemente eliminada. A seguinte nota contém instruções sobre como realizar estas tarefas.

## <a name="exporting-customer-data"></a>Exportar dados do cliente

Da mesma forma que a pagando dados, um administrador de inquilino pode visualizar e exportar dados armazenados em Time Series Insights a partir da lâmina política no portal Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Se for administrador de inquilinos, pode ver políticas de acesso a dados dentro do ambiente Time Series Insights no portal Azure. Para mais informações, leia o acesso dos dados do Grant a um ambiente time series insights utilizando o [portal Azure](time-series-insights-data-access.md).

É também possível realizar operações de exportação em políticas de acesso utilizando a operação "lista por ambiente" na API REST fornecida. Para mais informações, leia [Políticas de Acesso - Lista Por Meio Ambiente.](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/listbyenvironment)

## <a name="to-delete-data-stored-within-time-series-insights"></a>Para eliminar os dados armazenados dentro da Time Series Insights

Os dados pessoais podem entrar no armazenamento de Insights da Time Series, um cenário diferente dos dados do utilizador e da administração. Se considerar os dados armazenados na Time Series Insights como dados pessoais, pode exportar e eliminar esses dados utilizando os seguintes passos:

**Ver e exportar dados**

Para visualizar e exportar dados armazenados dentro da Time Series Insights, é necessário pesquisar esses dados. Pode utilizar o explorador time series Insights ou a consulta time series Insights APIs para visualizar e exportar dados. Para visualizar e exportar dados utilizando o explorador Time Series Insights, procure primeiro encontrar os dados do utilizador em questão. Depois de pesquisar, clique à direita na tabela e **selecione Explore eventos**. A grelha de eventos aparece e apresenta opções para exportar os dados como CSV e JSON.

Para mais informações, leia o explorador de Insights da [Série De Tempo azure.](time-series-insights-explorer.md)

**Eliminar dados**

Atualmente, a Time Series Insights não suporta a eliminação granular de dados. No entanto, a Time Series Insights fornece a capacidade de remover os dados do cliente armazenados dentro da Time Series Insights configurando políticas de retenção. Pode ajustar o período de retenção de todo o ambiente time series Insights a qualquer número de dias para suportar os seus requisitos de eliminação.

Para mais informações, leia [a retenção de Configuração em Insights da Série De Tempo](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Passos seguintes

* Leia mais sobre [a concessão de acesso de dados ao seu ambiente DeInsights](./time-series-insights-data-access.md)da Série De Tempo Azure .

* Veja o explorador de Insights da [Série De Tempo Azure.](time-series-insights-explorer.md)

* Saiba sobre [a retenção de configuração em Insights da Série De Tempo](time-series-insights-how-to-configure-retention.md).