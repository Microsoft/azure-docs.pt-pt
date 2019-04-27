---
title: Dados do cliente solicitar recursos no Azure Time Series Insights | Documentos da Microsoft
description: Resumo das funcionalidades de pedido de dados de cliente do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
manager: cshankar
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: 30f6b1fd953f89170a18d56bf0353c643853074e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880713"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados do cliente

O Azure Time Series Insights é um serviço cloud gerido com componentes de armazenamento, análise e visualização que tornam fácil ingerir, armazenar, explorar e analisar milhares de milhões de eventos em simultâneo.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Para exibir, exportar e eliminar os dados pessoais que poderão estar sujeito a um pedido do requerente de dados, um administrador de inquilino do Azure Time Series Insights pode utilizar o portal do Azure ou as APIs REST. Utilizar o portal do Azure para pedidos de serviço dos dados, fornece um método menos complexo para executar estas operações que a maioria dos usuários preferem.

## <a name="identifying-customer-data"></a>Identificar os dados do cliente

O Azure Time Series Insights considera os dados pessoais para ser dados associados a administradores e utilizadores do Time Series Insights. O Time Series Insights armazena o ID de objeto do Azure Active Directory dos utilizadores com acesso ao ambiente. O portal do Azure apresenta endereços de e-mail do utilizador, mas estes endereços de e-mail não são armazenados no Time Series Insights, são dinamicamente pesquisados com o ID de objeto do Azure Active Directory no Azure Active Directory.

## <a name="deleting-customer-data"></a>A eliminação de dados do cliente

Um administrador inquilino pode eliminar os dados do cliente no portal do Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

No entanto, antes de eliminar os dados do cliente através do portal, deve remover as políticas de acesso do utilizador do ambiente do Time Series Insights no portal do Azure. Para obter mais informações, consulte [conceder acesso a dados para um ambiente do Time Series Insights com o portal do Azure](time-series-insights-data-access.md).

Também pode efetuar operações de eliminação nas políticas de acesso através da API REST. Para obter mais informações, consulte [políticas de acesso - eliminar](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete).

O Time Series Insights está integrado com o painel de política no portal do Azure. O Time Series Insights e o painel de política permitem-lhe ver, exportar e eliminar dados de utilizador armazenados no serviço. Eliminar qualquer ação executada no painel de política de resultados do portais do Azure a eliminação de dados de utilizador no Time Series Insights. Por exemplo, se um utilizador tiver uma consulta de pessoal guardada, essa consulta é permanentemente eliminada do Explorador do Time Series Insights. Se o utilizador tiver uma consulta guardada partilhada, a consulta persistir, mas as informações de utilizador são eliminadas permanentemente. A nota seguinte contém instruções sobre como realizar estas tarefas.

## <a name="exporting-customer-data"></a>Exportar dados do cliente

Da mesma forma para a eliminação de dados, um administrador inquilino pode ver e exportar dados armazenados no Time Series Insights a partir do painel de política no portal do Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Se for um administrador inquilino, pode ver as políticas de acesso de dados dentro do ambiente do Time Series Insights no portal do Azure. Para obter mais informações, consulte [conceder acesso a dados para um ambiente do Time Series Insights com o portal do Azure](time-series-insights-data-access.md).

Também é possível realizar operações de exportação nas políticas de acesso usando a operação de "lista pelo ambiente" na API REST fornecido. Para obter mais informações, consulte [políticas de acesso - lista pelo ambiente](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Para eliminar os dados armazenados no Time Series Insights

Os dados pessoais podem tornar o seu caminho para o armazenamento do Time Series Insights, um cenário diferente do utilizador e dados de administrador. Se considerar os dados armazenados no Time Series Insights, como os dados pessoais, pode exportar e eliminar esses dados usando os seguintes passos:

**Ver e exportar dados**

Para ver e exportar dados armazenados no Time Series Insights, terá de procurar dados. Pode utilizar o Explorador do Time Series Insights ou as APIs de consulta do Time Series Insights para ver e exportar dados. Para ver e exportar dados com o Explorador do Time Series Insights, primeiro procurar para encontrar os dados de utilizador em questão. Depois de procurar, faça duplo clique no gráfico e selecione **explorar eventos**. A grelha de eventos aparece e apresenta as opções para exportar os dados como CSV e JSON.

Para obter mais informações, consulte [Explorador do Azure Time Series Insights](time-series-insights-explorer.md).

**Eliminar dados**

Atualmente, o Time Series Insights não suporta granular eliminação de dados. No entanto, o Time Series Insights fornece a capacidade de remover os dados de clientes armazenados no Time Series Insights através da configuração de políticas de retenção. Pode ajustar o período de retenção de todo o ambiente do Time Series Insights para qualquer número de dias para suportar os requisitos de eliminação.

Para obter mais informações, consulte [configurar a retenção no Time Series Insights](time-series-insights-how-to-configure-retention.md).