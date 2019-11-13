---
title: Modelagem de dados em ambientes de visualização – Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre a modelagem de dados na visualização Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: fb1bfb30d531f8b93489c8fc1dfbc6b8172339a3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006465"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelagem de dados na visualização Azure Time Series Insights

Este artigo descreve como trabalhar com o modelo de série temporal na visualização Azure Time Series Insights. Ele detalha vários cenários de dados comuns.

Para saber mais sobre como usar a atualização, leia [Azure Time Series insights Gerenciador de visualização](./time-series-insights-update-explorer.md).

## <a name="types"></a>Tipos

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá para o painel seletor de modelo de série temporal e selecione **tipos** no menu. Recolha o painel para se concentrar nos tipos de modelo de série temporal.

    [![painel "tipos"](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Selecione **+ Adicionar**.
1. Insira todos os detalhes referentes aos tipos e selecione **criar**. Essa ação cria tipos no ambiente.

    [![seleções para adicionar um tipo](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Carregar em massa um ou mais tipos

1. Selecione **carregar JSON**.
1. Selecione o arquivo que contém a carga do tipo.
1. Selecione **Upload**.

    [![seleções de upload em massa de um ou mais tipos](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Editar um único tipo

1. Selecione o tipo e selecione **Editar**. 
1. Faça as alterações necessárias e selecione **salvar**.

    [![seleções para editar um tipo](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Excluir um tipo

1. Selecione o tipo e selecione **excluir**.
1. Se nenhuma instância estiver associada aos tipos, ela será excluída.

    [botão !["excluir"](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarquias

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá para o painel seletor de modelo de série temporal e selecione **hierarquias** no menu. Recolha o painel para se concentrar nas hierarquias do modelo de série temporal.

    [painel de "hierarquias" ![](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Selecione **+ Adicionar**.

    [botão !["Adicionar"](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Selecione **+ Adicionar nível** no painel direito.

    [botão !["Adicionar nível"](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Insira os detalhes da hierarquia e selecione **criar**.

    [detalhes da hierarquia de ![e o botão "criar"](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Carregar em massa uma ou mais hierarquias

1. Selecione **carregar JSON**.
1. Selecione o arquivo que contém a carga da hierarquia.
1. Selecione **Upload**.

    [![seleções para o carregamento em massa de hierarquias](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

1. Selecione a hierarquia e selecione **Editar**.
1. Faça as alterações necessárias e selecione **salvar**.

    [![seleções para editar uma única hierarquia](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Excluir uma hierarquia

1. Selecione a hierarquia e selecione **excluir**. 
1. Se nenhuma instância estiver associada à hierarquia, ela será excluída.

    [botão !["excluir"](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>instâncias

### <a name="create-a-single-instance"></a>Criar uma única instância

1. Vá para o painel seletor de modelo de série temporal e selecione **instâncias** no menu. Recolha o painel para se concentrar nas instâncias do modelo de série temporal.

    [![painel "instâncias"](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Selecione **Adicionar**.

    [![seleções para adicionar uma instância](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Insira os detalhes da instância, selecione o tipo e a associação de hierarquia e selecione **criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Carregar em massa uma ou mais instâncias

1. Selecione **carregar JSON**.
1. Selecione o arquivo que contém a carga de instâncias.

    [![seleções para o carregamento em massa de uma ou mais instâncias](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Selecione **Upload**.

### <a name="edit-a-single-instance"></a>Editar uma única instância

1. Selecione a instância e selecione **Editar**. 
1. Faça as alterações necessárias e selecione **salvar**.

    [![seleções para editar uma única instância](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o modelo de série temporal, leia [modelagem de dados](./time-series-insights-update-tsm.md).

- Para saber mais sobre a versão prévia, leia [Visualizar dados no Gerenciador do Azure Time Series insights Preview](./time-series-insights-update-explorer.md).

- Para saber mais sobre as formas JSON com suporte, leia [formas de JSON com suporte](./time-series-insights-send-events.md#supported-json-shapes).
