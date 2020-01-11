---
title: Recursos de solicitação de dados do cliente-Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre os recursos de solicitação de dados do cliente no Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 12/23/2019
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: c33fce5b6999c5b8372fe4c04401bf55898aee8d
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863449"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados do cliente

O Azure Time Series Insights é um serviço de nuvem gerenciado com componentes de armazenamento, análise e visualização que facilitam a ingestão, o armazenamento, a exploração e a análise de bilhões de eventos simultaneamente.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Para exibir, exportar e excluir dados pessoais que podem estar sujeitos a uma solicitação de entidade de dados, um administrador de locatário Azure Time Series Insights pode usar as APIs portal do Azure ou REST. Usando o portal do Azure para atender às solicitações de entidade de dados, o fornece um método menos complexo para executar essas operações que a maioria dos usuários preferem.

## <a name="identifying-customer-data"></a>Identificando dados do cliente

Azure Time Series Insights considera dados pessoais associados a administradores e usuários do Time Series Insights. Time Series Insights armazena o Azure Active Directory objeto-ID de usuários com acesso ao ambiente. O portal do Azure exibe endereços de email do usuário, mas esses endereços de email não são armazenados no Time Series Insights, eles são pesquisados dinamicamente usando a ID do objeto Azure Active Directory no Azure Active Directory.

## <a name="deleting-customer-data"></a>A eliminação de dados do cliente

Um administrador de locatários pode excluir dados do cliente usando o portal do Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

No entanto, antes de excluir os dados do cliente por meio do portal, você deve remover as políticas de acesso do usuário do ambiente de Time Series Insights dentro do portal do Azure. Para obter mais informações, leia [conceder acesso a dados para um ambiente de time Series insights usando portal do Azure](time-series-insights-data-access.md).

Você também pode executar operações de exclusão em políticas de acesso usando a API REST. Para obter mais informações, leia [políticas de acesso-excluir](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/delete).

O Time Series Insights é integrado à folha de política no portal do Azure. Tanto Time Series Insights quanto a folha de política permitem exibir, exportar e excluir dados de usuário armazenados no serviço. Qualquer ação de exclusão executada na folha de política do portal do Azure resulta na exclusão de dados do usuário no Time Series Insights. Por exemplo, se um usuário tiver uma consulta pessoal salva, essa consulta será excluída permanentemente do Time Series Insights Explorer. Se o usuário tiver uma consulta compartilhada salva, a consulta persistirá, mas as informações do usuário serão excluídas permanentemente. A nota a seguir contém instruções sobre como realizar essas tarefas.

## <a name="exporting-customer-data"></a>Exportar dados do cliente

De maneira semelhante à exclusão de dados, um administrador de locatário pode exibir e exportar dados armazenados no Time Series Insights da folha de política no portal do Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Se você for um administrador de locatários, poderá exibir políticas de acesso a dados dentro do ambiente de Time Series Insights no portal do Azure. Para obter mais informações, leia [conceder acesso a dados para um ambiente de time Series insights usando portal do Azure](time-series-insights-data-access.md).

Também é possível executar operações de exportação em políticas de acesso usando a operação "listar por ambiente" na API REST fornecida. Para obter mais informações, leia [políticas de acesso-listar por ambiente](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Para excluir dados armazenados no Time Series Insights

Os dados pessoais podem deixar seu caminho em Time Series Insights armazenamento, um cenário diferente dos dados de usuário e de administrador. Se você considerar os dados armazenados em Time Series Insights como dados pessoais, poderá exportar e excluir esses dados usando as seguintes etapas:

**Exibir e exportar dados**

Para exibir e exportar dados armazenados no Time Series Insights, você precisa pesquisar por esses dados. Você pode usar o Time Series Insights Explorer ou Time Series Insights APIs de consulta para exibir e exportar dados. Para exibir e exportar dados usando o Time Series Insights Explorer, primeiro pesquise para localizar os dados do usuário em questão. Após a pesquisa, clique com o botão direito do mouse no gráfico e selecione **explorar eventos**. A grade de eventos é exibida e apresenta opções para exportar os dados como CSV e JSON.

Para obter mais informações, leia [Azure Time Series insights Explorer](time-series-insights-explorer.md).

**Excluir dados**

Atualmente, Time Series Insights não oferece suporte à exclusão granular de dados. No entanto, Time Series Insights fornece a capacidade de remover os dados do cliente armazenados no Time Series Insights Configurando políticas de retenção. Você pode ajustar o período de retenção de todo o ambiente de Time Series Insights para qualquer número de dias para dar suporte aos seus requisitos de exclusão.

Para obter mais informações, leia [Configurando a retenção em time Series insights](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Passos seguintes

* Leia mais sobre como [conceder acesso a dados ao seu ambiente de Azure Time Series insights](./time-series-insights-data-access.md).

* Exiba o [Azure Time Series insights Explorer](time-series-insights-explorer.md).

* Saiba como [Configurar a retenção em time Series insights](time-series-insights-how-to-configure-retention.md).