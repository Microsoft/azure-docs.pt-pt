---
title: Modelação de dados em ambientes de pré-visualização - Azure Time Series Insights / Microsoft Docs
description: Saiba mais sobre a modelação de dados na pré-visualização da Série de TempoS Azure.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 2fa56217c54ac662a62a5ce835d75e3d85f05bfa
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86041477"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelação de dados em Azure Time Series Insights Preview

Este artigo descreve como trabalhar com o Modelo da Série De Tempo em Azure Time Series Insights Preview. Detalha vários cenários de dados comuns.

> [!TIP]
> * Leia sobre o modelo da Série de Tempo de [Pré-visualização](concepts-model-overview.md).
> * Saiba mais sobre a navegação do UI de pré-visualização no [explorador de pré-visualização da Série de Tempo Azure.](./time-series-insights-update-explorer.md)

## <a name="instances"></a>de Instâncias

O explorador Azure Time Series Insights suporta as operações **Desematada Create**, **READ**, **UPDATE**e **DELETE** dentro do navegador. 

Para começar, selecione a vista **modelo** a partir da vista **de análise** do explorador de séries temporítros.

### <a name="create-a-single-instance"></a>Criar um único exemplo

1. Vá ao seletor do modelo série de tempo e selecione **Instâncias** do menu. Todas as instâncias associadas ao ambiente de Insights de Séries De Tempo selecionadas serão exibidas.

    [![Crie uma única instância selecionando primeiro instâncias.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Selecione **+ Adicionar**.

    [![Adicione uma instância selecionando o botão + Adicionar.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Introduza os detalhes da instância, selecione o tipo e a associação hierarquia e selecione **Criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Upload a granel um ou mais instâncias

> [!TIP]
> Pode guardar as suas instâncias para o seu ambiente de trabalho em JSON. O ficheiro JSON descarregado pode então ser carregado através dos seguintes passos.

1. Selecione **Upload JSON**.
1. Selecione o ficheiro que contém a carga útil das instâncias.

    [![A granel envia instâncias através do JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Selecione **Carregar**.

### <a name="edit-a-single-instance"></a>Editar um único caso

1. Selecione a instância e selecione o ícone **de edição** ou **lápis**. 
1. Faça as alterações necessárias e **selecione Guardar**.

    [![Editar um único caso.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Apagar um caso

1. Selecione a instância e selecione o ícone **de eliminação** ou **de lixo**.

   [![Elimine uma instância selecionando Delete.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Confirme a eliminação selecionando **Delete**.

> [!NOTE]
> Um caso deve passar com sucesso um controlo de validação de campo a ser eliminado.

## <a name="hierarchies"></a>Hierarquias

O explorador Azure Time Series Insights suporta operações hierárquicas **CREATE,** **READ**, **UPDATE**e **DELETE** dentro do navegador. 

Para começar, selecione a vista **modelo** a partir da vista **de análise** do explorador de séries temporítros.

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá ao seletor do modelo série de tempo e selecione **hierarquias** no menu. Todas as hierarquias associadas ao ambiente de Insights de Séries De Tempo selecionadas serão exibidas.

    [![Criar uma hierarquia através do painel.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Selecione **+ Adicionar**.

    [![Hierarquia + Adicionar botão.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Selecione + Adicione o **nível** no painel direito.

    [![Adicione um nível à hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Introduza os detalhes da hierarquia e **selecione Save**.

    [![Especifique detalhes da hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Carregar em massa uma ou mais hierarquias

> [!TIP]
> Pode guardar as suas hierarquias para o seu ambiente de trabalho no JSON. O ficheiro JSON descarregado pode então ser carregado através dos seguintes passos.

1. Selecione **Upload JSON**.
1. Selecione o ficheiro que contém a carga útil da hierarquia.
1. Selecione **Carregar**.

    [![Seleções para carregamento em massa de hierarquias.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

1. Selecione a hierarquia e selecione o ícone **de edição** ou **lápis**.
1. Faça as alterações necessárias e **selecione Guardar**.

    [![Seleções para editar uma única hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Eliminar uma hierarquia

1. Selecione a hierarquia e selecione o ícone **de eliminação** ou **de lixo**. 

    [![Elimine uma hierarquia selecionando o botão Eliminar.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Confirme a eliminação selecionando **Delete**.

## <a name="types"></a>Tipos

O explorador Azure Time Series Insights suporta as operações tipo **CREATE,** **READ**, **UPDATE**e **DELETE** dentro do navegador. 

Para começar, selecione a vista **modelo** a partir da vista **de análise** do explorador de séries temporítros.

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá ao seletor do modelo série de tempo e selecione **Tipos** do menu. Todos os tipos associados ao ambiente de Insights de Séries de Tempo selecionados serão apresentados.

    [![Painel de modelos de séries de tempo.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. **Selecione + Adicione** para exibir o Adicionar um novo **tipo** popup modal.
1. Introduza propriedades e variáveis para o seu tipo. Uma vez introduzido, **selecione Save**. 

    [![Configurações de configuração para adicionar um tipo.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Upload a granel de um ou mais tipos

> [!TIP]
> Pode guardar os seus tipos para o seu ambiente de trabalho em JSON. O ficheiro JSON descarregado pode então ser carregado através dos seguintes passos.

1. Selecione **Upload JSON**.
1. Selecione o ficheiro que contém a carga útil do tipo.
1. Selecione **Carregar**.

    [![Opções de carregamento de tipos de granel.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Editar um único tipo

1. Selecione o tipo e selecione o ícone **de edição** ou **lápis.**
1. Faça as alterações necessárias e **selecione Guardar**.

    [![Edite um tipo no painel.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Eliminar um tipo

1. Selecione o tipo e selecione o ícone **de eliminação** ou **de caixa de resíduos**. .

   [![Elimine um tipo selecionando Eliminar.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Confirme a eliminação selecionando **Delete**.

## <a name="next-steps"></a>Próximos passos

- Para obter mais informações sobre o Modelo série de tempo, leia [modelação de dados](./concepts-model-overview.md).

- Para saber mais sobre a pré-visualização, leia [visualize dados no explorador de pré-visualização da Série de TempoS Azure.](./time-series-insights-update-explorer.md)

- Para aprender sobre as formas JSON suportadas, leia [as formas JSON suportadas.](./time-series-insights-send-events.md#supported-json-shapes)
