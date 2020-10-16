---
title: Funcionalidades de pedido de dados do cliente - Azure Time Series Insights / Microsoft Docs
description: Saiba mais sobre as funcionalidades de pedido de dados do cliente no Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 5565dd43689d0420fbb9b73f088f24a38a2d54db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665910"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados de clientes

Azure Time Series Insights é um serviço de nuvem gerido com componentes de armazenamento, análise e visualização que facilitam a ingestão, armazenamento, exploração e análise de milhares de milhões de eventos em simultâneo.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Para visualizar, exportar e eliminar dados pessoais que possam estar sujeitos a um pedido de assunto de dados, um administrador inquilino da Azure Time Series Insights pode utilizar o portal Azure ou as APIs REST. Utilizando o portal Azure para solicitar o tratamento de dados, fornece um método menos complexo para executar estas operações que a maioria dos utilizadores prefere.

## <a name="identifying-customer-data"></a>Identificar dados do cliente

A Azure Time Series Insights considera que os dados pessoais são dados associados a administradores e utilizadores da Time Series Insights. A Time Series Insights armazena o ID de objeto do Azure Ative Directory dos utilizadores com acesso ao ambiente. O portal Azure exibe endereços de e-mail do utilizador, mas estes endereços de e-mail não são armazenados no Time Series Insights, são analisados dinamicamente usando o ID do diretório ativo Azure no Diretório Ativo Azure.

## <a name="deleting-customer-data"></a>Eliminação dos dados dos clientes

Um administrador de inquilino pode apagar os dados do cliente usando o portal Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

No entanto, antes de eliminar os dados dos clientes através do portal, deverá remover as políticas de acesso do utilizador do ambiente Time Series Insights dentro do portal Azure. Para obter mais informações, leia [o acesso de dados do Grant a um ambiente de Insights de Séries Tempotadas utilizando o portal Azure](time-series-insights-data-access.md).

Também pode executar operações de eliminação nas políticas de acesso utilizando a API REST. Para mais informações, leia [Políticas de Acesso - Excluir](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/delete).

O Time Series Insights está integrado com a lâmina de política no portal Azure. Tanto o Time Series Insights como a lâmina De Política permitem-lhe visualizar, exportar e eliminar os dados do utilizador armazenados dentro do serviço. Qualquer ação de eliminação tomada dentro da lâmina de política do portal Azure resulta na eliminação dos dados do utilizador dentro do Time Series Insights. Por exemplo, se um utilizador tiver uma consulta pessoal guardada, essa consulta é permanentemente eliminada do explorador time series Insights. Se o utilizador tiver uma consulta partilhada guardada, a consulta persiste, mas a informação do utilizador é permanentemente eliminada. A nota a seguir contém instruções sobre como realizar estas tarefas.

## <a name="exporting-customer-data"></a>Exportação de dados dos clientes

Da mesma forma para eliminar dados, um administrador de inquilino pode ver e exportar dados armazenados em Time Series Insights a partir da lâmina de política no portal Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Se for administrador de inquilinos, pode ver as políticas de acesso a dados dentro do ambiente Time Series Insights no portal Azure. Para obter mais informações, leia [o acesso de dados do Grant a um ambiente de Insights de Séries Tempotadas utilizando o portal Azure](time-series-insights-data-access.md).

É igualmente possível realizar operações de exportação em políticas de acesso utilizando a operação "list by environment" na API providenciada. Para mais informações, leia [Políticas de Acesso - List By Environment](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Para eliminar os dados armazenados no Time Series Insights

Os dados pessoais podem entrar no armazenamento de Time Series Insights, um cenário diferente dos dados do utilizador e da administração. Se considerar os dados armazenados no Time Series Insights como dados pessoais, pode exportar e eliminar esses dados utilizando os seguintes passos:

### <a name="view-and-export-data"></a>Ver e exportar dados

Para visualizar e exportar dados armazenados no Time Series Insights, é necessário procurar esses dados. Pode utilizar o explorador de Insights de Séries Temporiais ou a consulta de Insights de Séries de Tempo para visualizar e exportar dados. Para visualizar e exportar dados utilizando o explorador Time Series Insights, procure pela primeira vez os dados do utilizador em questão. Depois de pesquisar, clique com o botão direito na tabela e selecione **Explore eventos**. A grelha de eventos aparece e apresenta opções para exportar os dados como CSV e JSON.

Para mais informações, leia [o explorador Azure Time Series Insights](time-series-insights-explorer.md).

### <a name="delete-data"></a>Eliminar dados

Atualmente, a Time Series Insights não suporta a eliminação granular de dados. No entanto, o Time Series Insights fornece a capacidade de remover os dados do cliente armazenados dentro do Time Series Insights, configurando políticas de retenção. Pode ajustar o período de retenção de todo o ambiente time series Insights a qualquer número de dias para suportar os seus requisitos de eliminação.

Para obter mais informações, leia [a retenção de configuração em Time Series Insights](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Passos seguintes

* Leia mais sobre [a concessão de acesso de dados ao ambiente Azure Time Series Insights](./time-series-insights-data-access.md).

* Ver o [explorador Azure Time Series Insights](time-series-insights-explorer.md).

* Saiba mais sobre [a configuração da retenção em Time Series Insights](time-series-insights-how-to-configure-retention.md).
