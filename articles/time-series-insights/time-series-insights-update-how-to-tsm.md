---
title: Modelação de dados na pré-visualização do Azure Time Series Insights | Documentos da Microsoft
description: Compreenda a modelação de dados na pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 1c8886cada80c02e99782159099aa626da35fc50
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466865"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelação de dados na pré-visualização do Azure Time Series Insights

Este documento descreve como trabalhar com modelos de série de tempo após o pré-visualização do Azure Time Series Insights. Ele detalha os vários cenários de dados comuns.

Para saber mais sobre como utilizar a atualização, leia [Explorador de pré-visualização do Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Tipos

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá para o painel de Seletor de modelos de série de tempo e selecione **tipos** no menu. Fechar o painel para se concentrar os tipos de modelos de série de tempo.

    [![Criar um único tipo](media/v2-update-how-to-tsm/portal_one.png)](media/v2-update-how-to-tsm/portal_one.png#lightbox)

1. Selecione **Adicionar**.
1. Entrada de todos os detalhes que dizem respeito a tipos e selecione **criar**. Esta ação cria tipos no ambiente.

    [![Adicionar um tipo](media/v2-update-how-to-tsm/portal_two.png)](media/v2-update-how-to-tsm/portal_two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Um ou mais tipos de carregamento em volume

1. Selecione **carregar JSON**.
1. Selecione o ficheiro que contém o payload de tipo.
1. Selecione **Upload**.

    [![Carregar JSON](media/v2-update-how-to-tsm/portal_three.png)](media/v2-update-how-to-tsm/portal_three.png#lightbox)

### <a name="edit-a-single-type"></a>Editar um único tipo

1. Selecione o tipo e selecione **editar**. 
1. Faça as alterações necessárias e selecione **guardar**.

    [![Editar um tipo](media/v2-update-how-to-tsm/portal_four.png)](media/v2-update-how-to-tsm/portal_four.png#lightbox)

### <a name="delete-a-type"></a>Eliminar um tipo

1. Selecione o tipo e selecione **eliminar**.
1. Se não existem instâncias estão associadas com os tipos, este é eliminado.

    [![Eliminar um tipo](media/v2-update-how-to-tsm/portal_five.png)](media/v2-update-how-to-tsm/portal_five.png#lightbox)

## <a name="hierarchies"></a>Hierarquias

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá para o painel de Seletor de modelos de série de tempo e selecione **hierarquias** no menu. Fechar o painel para se concentrar as hierarquias de modelos de série de tempo.

    [![Selecione hierarquias](media/v2-update-how-to-tsm/portal_six.png)](media/v2-update-how-to-tsm/portal_six.png#lightbox)

1. Selecione **Adicionar**.

    [![Adicionar uma hierarquia](media/v2-update-how-to-tsm/portal_seven.png)](media/v2-update-how-to-tsm/portal_seven.png#lightbox)

1. Selecione **nível adicionar** no painel da direita.

    [![Adicionar um nível](media/v2-update-how-to-tsm/portal_eight.png)](media/v2-update-how-to-tsm/portal_eight.png#lightbox)

1. Introduza os detalhes de hierarquia e selecione **criar**.

    [![Criar um nível](media/v2-update-how-to-tsm/portal_nine.png)](media/v2-update-how-to-tsm/portal_nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Uma ou mais hierarquias de carregamento em volume

1. Selecione **carregar JSON**.
1. Selecione o ficheiro que contém o payload de hierarquia.
1. Selecione **Upload**.

    [![Hierarquias de carregamento em massa](media/v2-update-how-to-tsm/portal_ten.png)](media/v2-update-how-to-tsm/portal_ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

1. Selecione a hierarquia e selecione **editar**.
1. Faça as alterações necessárias e selecione **guardar**.

    [![Editar uma única hierarquia](media/v2-update-how-to-tsm/portal_eleven.png)](media/v2-update-how-to-tsm/portal_eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Eliminar uma hierarquia

1. Selecione a hierarquia e selecione **eliminar**. 
1. Se não existem instâncias estão associadas com a hierarquia, este é eliminado.

    [![Eliminar uma hierarquia](media/v2-update-how-to-tsm/portal_twelve.png)](media/v2-update-how-to-tsm/portal_twelve.png#lightbox)

## <a name="instances"></a>Instâncias

### <a name="create-a-single-instance"></a>Criar uma única instância

1. Vá para o painel de Seletor de modelos de série de tempo e selecione **instâncias** no menu. Fechar o painel para se concentrar nas instâncias de modelos de série de tempo.

    [![Criar uma única instância](media/v2-update-how-to-tsm/portal_thirteen.png)](media/v2-update-how-to-tsm/portal_thirteen.png#lightbox)

1. Selecione **Adicionar**.

    [![Adicionar uma instância](media/v2-update-how-to-tsm/portal_fourteen.png)](media/v2-update-how-to-tsm/portal_fourteen.png#lightbox)

1. Introduza os detalhes de instância, selecione a associação de tipo e a hierarquia e selecione **criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Uma ou mais instâncias de carregamento em volume

1. Selecione **carregar JSON**.
1. Selecione o ficheiro que contém a carga de instâncias.

    [![Uma ou mais instâncias de carregamento em volume](media/v2-update-how-to-tsm/portal_fifteen.png)](media/v2-update-how-to-tsm/portal_fifteen.png#lightbox)

1. Selecione **Upload**.

### <a name="edit-a-single-instance"></a>Editar uma única instância

1. Selecione a instância e selecione **editar**. 
1. Faça as alterações necessárias e selecione **guardar**.

    [![Editar uma única instância](media/v2-update-how-to-tsm/portal_sixteen.png)](media/v2-update-how-to-tsm/portal_sixteen.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre os modelos de série de tempo, leia [modelação de dados](./time-series-insights-update-tsm.md).

- Para saber mais sobre a pré-visualização, leia [visualizar dados no Explorador de pré-visualização do Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Para saber mais sobre formas JSON suportadas, leia [formas de JSON suportado](./time-series-insights-send-events.md#json).
