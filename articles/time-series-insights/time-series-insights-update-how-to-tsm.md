---
title: Modelagem de dados na visualização de Azure Time Series Insights | Microsoft Docs
description: Entenda a modelagem de dados na visualização Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: 245a69f5e5834e68bbbd17a96859a93bc16eacbe
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744373"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelagem de dados na visualização Azure Time Series Insights

Este documento descreve como trabalhar com modelos de série temporal seguindo a Azure Time Series Insights visualização. Ele detalha vários cenários de dados comuns.

Para saber mais sobre como usar a atualização, leia [Azure Time Series insights Gerenciador de visualização](./time-series-insights-update-explorer.md).

## <a name="types"></a>Tipos

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá para o painel seletor de modelos de série temporal e selecione **tipos** no menu. Recolha o painel para se concentrar nos tipos de modelos de série temporal.

    [![Criar um único tipo](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Selecione **+ Adicionar**.
1. Insira todos os detalhes que pertencem aos tipos e selecione **criar**. Essa ação cria tipos no ambiente.

    [![Adicionar um tipo](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Carregar em massa um ou mais tipos

1. Selecione **carregar JSON**.
1. Selecione o arquivo que contém a carga do tipo.
1. Selecione **Upload**.

    [![Carregar JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Editar um único tipo

1. Selecione o tipo e selecione **Editar**. 
1. Faça as alterações necessárias e selecione **salvar**.

    [![Editar um tipo](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Excluir um tipo

1. Selecione o tipo e selecione **excluir**.
1. Se nenhuma instância estiver associada aos tipos, ela será excluída.

    [![Excluir um tipo](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarquias

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá para o painel seletor de modelos de série temporal e selecione **hierarquias** no menu. Recolha o painel para se concentrar nas hierarquias dos modelos de série temporal.

    [![Selecionar hierarquias](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Selecione **+ Adicionar**.

    [![Adicionar uma hierarquia](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Selecione **+ Adicionar nível** no painel direito.

    [![Adicionar um nível](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Insira os detalhes da hierarquia e selecione **criar**.

    [![Criar um nível](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Carregar em massa uma ou mais hierarquias

1. Selecione **carregar JSON**.
1. Selecione o arquivo que contém a carga da hierarquia.
1. Selecione **Upload**.

    [![Hierarquias de carregamento em massa](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

1. Selecione a hierarquia e selecione **Editar**.
1. Faça as alterações necessárias e selecione **salvar**.

    [![Editar uma única hierarquia](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Excluir uma hierarquia

1. Selecione a hierarquia e selecione **excluir**. 
1. Se nenhuma instância estiver associada à hierarquia, ela será excluída.

    [![Excluir uma hierarquia](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>instâncias

### <a name="create-a-single-instance"></a>Criar uma única instância

1. Vá para o painel seletor de modelos de série temporal e selecione **instâncias** no menu. Recolha o painel para se concentrar nas instâncias de modelos de série temporal.

    [![Criar uma única instância](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Selecione **Adicionar**.

    [![Adicionar uma instância](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Insira os detalhes da instância, selecione o tipo e a associação de hierarquia e selecione **criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Carregar em massa uma ou mais instâncias

1. Selecione **carregar JSON**.
1. Selecione o arquivo que contém a carga de instâncias.

    [![Carregar em massa uma ou mais instâncias](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Selecione **Upload**.

### <a name="edit-a-single-instance"></a>Editar uma única instância

1. Selecione a instância e selecione **Editar**. 
1. Faça as alterações necessárias e selecione **salvar**.

    [![Editar uma única instância](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre modelos de série temporal, leia [modelagem de dados](./time-series-insights-update-tsm.md).

- Para saber mais sobre a versão prévia, leia [Visualizar dados no Gerenciador do Azure Time Series insights Preview](./time-series-insights-update-explorer.md).

- Para saber mais sobre as formas JSON com suporte, leia [formas de JSON com suporte](./time-series-insights-send-events.md#json).
