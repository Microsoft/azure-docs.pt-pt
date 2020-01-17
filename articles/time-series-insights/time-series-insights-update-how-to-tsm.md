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
ms.date: 12/20/2019
ms.custom: seodec18
ms.openlocfilehash: 591233154ab79b9493d77030727462bae68ea7d5
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152362"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelagem de dados na visualização Azure Time Series Insights

Este artigo descreve como trabalhar com o modelo de série temporal na visualização Azure Time Series Insights. Ele detalha vários cenários de dados comuns.

> [!TIP]
> * Leia sobre o [modelo de série temporal](time-series-insights-update-tsm.md)de visualização.
> * Saiba mais sobre como navegar na interface do usuário da versão prévia no [Azure Time Series insights Explorer Preview](./time-series-insights-update-explorer.md).

## <a name="instances"></a>Instâncias

O Azure Time Series Insights Explorer dá suporte a operações de **criação**, **leitura**, **atualização**e **exclusão** no navegador. 

Para começar, selecione o modo de exibição de **modelo** na exibição de **análise** do time Series insights Explorer.

### <a name="create-a-single-instance"></a>Criar uma única instância

1. Vá para o painel seletor de modelo de série temporal e selecione **instâncias** no menu. Todas as instâncias associadas ao ambiente de Time Series Insights selecionado serão exibidas.

    [![criar uma única instância selecionando primeiro as instâncias.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Selecione **+ Adicionar**.

    [![adicionar uma instância selecionando o botão + Adicionar.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Insira os detalhes da instância, selecione o tipo e a associação de hierarquia e selecione **criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Carregar em massa uma ou mais instâncias

> [!TIP]
> Você pode salvar suas instâncias em sua área de trabalho em JSON. O arquivo JSON baixado pode ser carregado por meio das etapas a seguir.

1. Selecione **carregar JSON**.
1. Selecione o arquivo que contém a carga de instâncias.

    [![instâncias de upload em massa por meio de JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Selecione **Upload**.

### <a name="edit-a-single-instance"></a>Editar uma única instância

1. Selecione a instância e selecione o ícone **Editar** ou **lápis**. 
1. Faça as alterações necessárias e selecione **salvar**.

    [![editar uma única instância.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Excluir uma instância

1. Selecione a instância e selecione o ícone **excluir** ou **perder lixeira**.

   [![excluir uma instância selecionando excluir.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Confirme a exclusão selecionando **excluir**.

> [!NOTE]
> Uma instância do deve passar uma verificação de validação de campo com êxito para ser excluída.

## <a name="hierarchies"></a>Hierarquias

O Azure Time Series Insights Explorer dá suporte a operações de **criação**, **leitura**, **atualização**e **exclusão** de hierarquia no navegador. 

Para começar, selecione o modo de exibição de **modelo** na exibição de **análise** do time Series insights Explorer.

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá para o painel seletor de modelo de série temporal e selecione **hierarquias** no menu. Todas as hierarquias associadas ao ambiente de Time Series Insights selecionado serão exibidas.

    [![criar uma hierarquia por meio do painel.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Selecione **+ Adicionar**.

    [![hierarquia + adicionar botão.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Selecione **+ Adicionar nível** no painel direito.

    [![adicionar um nível à hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Insira os detalhes da hierarquia e selecione **salvar**.

    [![especificar detalhes da hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Carregar em massa uma ou mais hierarquias

> [!TIP]
> Você pode salvar suas hierarquias em sua área de trabalho em JSON. O arquivo JSON baixado pode ser carregado por meio das etapas a seguir.

1. Selecione **carregar JSON**.
1. Selecione o arquivo que contém a carga da hierarquia.
1. Selecione **Upload**.

    [![seleções para o upload em massa de hierarquias.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

1. Selecione a hierarquia e selecione o ícone **Editar** ou **lápis**.
1. Faça as alterações necessárias e selecione **salvar**.

    [![seleções para editar uma única hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Excluir uma hierarquia

1. Selecione a hierarquia e selecione o ícone **excluir** ou **perder lixeira**. 

    [![excluir uma hierarquia selecionando o botão excluir.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Confirme a exclusão selecionando **excluir**.

## <a name="types"></a>Tipos

O Azure Time Series Insights Explorer dá suporte ao tipo **criar**, **ler**, **Atualizar**e **excluir** operações no navegador. 

Para começar, selecione o modo de exibição de **modelo** na exibição de **análise** do time Series insights Explorer.

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá para o painel seletor de modelo de série temporal e selecione **tipos** no menu. Todos os tipos associados ao ambiente de Time Series Insights selecionado serão exibidos.

    [![painel de tipos de modelo de série temporal.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Selecione **+ Adicionar** para exibir a janela pop-up **Adicionar um novo tipo** .
1. Insira Propriedades e variáveis para seu tipo. Depois de inserida, selecione **salvar**. 

    [![definições de configuração para adicionar um tipo.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Carregar em massa um ou mais tipos

> [!TIP]
> Você pode salvar seus tipos em sua área de trabalho em JSON. O arquivo JSON baixado pode ser carregado por meio das etapas a seguir.

1. Selecione **carregar JSON**.
1. Selecione o arquivo que contém a carga do tipo.
1. Selecione **Upload**.

    [![opções de carregamento de tipos em massa.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Editar um único tipo

1. Selecione o tipo e selecione o ícone **Editar** ou **lápis**.
1. Faça as alterações necessárias e selecione **salvar**.

    [![editar um tipo no painel.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Excluir um tipo

1. Selecione o tipo e selecione o ícone **excluir** ou **perder lixeira**. .

   [![excluir um tipo selecionando excluir.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Confirme a exclusão selecionando **excluir**.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o modelo de série temporal, leia [modelagem de dados](./time-series-insights-update-tsm.md).

- Para saber mais sobre a versão prévia, leia [Visualizar dados no Gerenciador do Azure Time Series insights Preview](./time-series-insights-update-explorer.md).

- Para saber mais sobre as formas JSON com suporte, leia [formas de JSON com suporte](./time-series-insights-send-events.md#supported-json-shapes).
