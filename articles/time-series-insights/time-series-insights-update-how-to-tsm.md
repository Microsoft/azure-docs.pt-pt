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
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: df94290c5e62b898b6490c78ef0ae1ee79437240
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64716959"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelação de dados na pré-visualização do Azure Time Series Insights

Este documento descreve como trabalhar com modelos de série de tempo após o pré-visualização do Azure Time Series Insights. Ele detalha os vários cenários de dados comuns.

Para saber mais sobre como utilizar a atualização, leia [Explorador de pré-visualização do Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Tipos

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá para o painel de Seletor de modelos de série de tempo e selecione **tipos** no menu. Fechar o painel para se concentrar os tipos de modelos de série de tempo.

    ![Portal_one][1]

1. Selecione **Adicionar**.
1. Entrada de todos os detalhes que dizem respeito a tipos e selecione **criar**. Esta ação cria tipos no ambiente.

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>Um ou mais tipos de carregamento em volume

1. Selecione **carregar JSON**.
1. Selecione o ficheiro que contém o payload de tipo.
1. Selecione **Upload**.

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>Editar um único tipo

Selecione o tipo e selecione **editar**. Faça as alterações necessárias e selecione **guardar**.

![Portal_four][4]

### <a name="delete-a-type"></a>Eliminar um tipo

Selecione o tipo e selecione **eliminar**. Se não existem instâncias estão associadas com os tipos, este é eliminado.

![Portal_five][5]

## <a name="hierarchies"></a>Hierarquias

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá para o painel de Seletor de modelos de série de tempo e selecione **hierarquias** no menu. Fechar o painel para se concentrar as hierarquias de modelos de série de tempo.

    ![Portal_six][6]

1. Selecione **Adicionar**.

    ![Portal_seven][7]

1. Selecione **nível adicionar** no painel da direita.

    ![Portal_eight][8]

1. Introduza os detalhes de hierarquia e selecione **criar**.

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>Uma ou mais hierarquias de carregamento em volume

1. Selecione **carregar JSON**.
1. Selecione o ficheiro que contém o payload de hierarquia.
1. Selecione **Upload**.

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

Selecione a hierarquia e selecione **editar**. Faça as alterações necessárias e selecione **guardar**.

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>Eliminar uma hierarquia

Selecione a hierarquia e selecione **eliminar**. Se não existem instâncias estão associadas com a hierarquia, este é eliminado.

![Portal_twelve][12]

## <a name="instances"></a>Instâncias

### <a name="create-a-single-instance"></a>Criar uma única instância

1. Vá para o painel de Seletor de modelos de série de tempo e selecione **instâncias** no menu. Fechar o painel para se concentrar nas instâncias de modelos de série de tempo.

    ![Portal_thirteen][13]

1. Selecione **Adicionar**.

    ![Portal_fourteen][14]

1. Introduza os detalhes de instância, selecione a associação de tipo e a hierarquia e selecione **criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Uma ou mais instâncias de carregamento em volume

1. Selecione **carregar JSON**.
1. Selecione o ficheiro que contém a carga de instâncias.

    ![Portal_fifteen][15]

1. Selecione **Upload**.

### <a name="edit-a-single-instance"></a>Editar uma única instância

Selecione a instância e selecione **editar**. Faça as alterações necessárias e selecione **guardar**.

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>Eliminar uma instância

Selecione a instância e selecione **eliminar**. Se não existem eventos estão associados com as instâncias, é eliminado.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre os modelos de série de tempo, leia [modelação de dados](./time-series-insights-update-tsm.md).

- Para saber mais sobre a pré-visualização, leia [visualizar dados no Explorador de pré-visualização do Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Para saber mais sobre formas JSON suportadas, leia [formas de JSON suportado](./time-series-insights-send-events.md#json).

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png