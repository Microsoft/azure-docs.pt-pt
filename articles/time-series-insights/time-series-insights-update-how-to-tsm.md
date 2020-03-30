---
title: Modelação de dados em ambientes de pré-visualização - Azure Time Series Insights [ Microsoft Docs
description: Saiba mais sobre modelação de dados na Pré-visualização de Insights da Série De Tempo Azure.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470756"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelação de dados na Pré-visualização de Insights da Série De Tempo azure

Este artigo descreve como trabalhar com o Modelo da Série De Tempo na Pré-visualização de Insights da Série De Tempo azure. Detalha vários cenários comuns de dados.

> [!TIP]
> * Leia sobre o Modelo de [Série de Tempo](time-series-insights-update-tsm.md)de Pré-visualização .
> * Saiba mais sobre navegar na Pré-Visualização uI em [Azure Time Series Insights Preview explorer](./time-series-insights-update-explorer.md).

## <a name="instances"></a>de Instâncias

O explorador de Insights da Série De Tempo Azure suporta as operações de **Criação**de Instâncias, **READ,** **UPDATE**e **DELETE** dentro do navegador. 

Para começar, selecione a visão do **Modelo** a partir da visão **do** explorador de Insights da Série Tempo.

### <a name="create-a-single-instance"></a>Criar uma única instância

1. Vá ao painel seletor do Modelo série de tempo e selecione **Instâncias** do menu. Todas as instâncias associadas ao seu ambiente time series insights selecionados serão exibidas.

    [![Crie uma única instância selecionando os casos.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Selecione **+ Adicionar**.

    [![Adicione uma instância selecionando o botão + Adicionar.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Introduza os detalhes da instância, selecione o tipo e a associação de hierarquias e selecione **Criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Upload a granel uma ou mais instâncias

> [!TIP]
> Pode guardar as suas instâncias para o seu ambiente de trabalho em JSON. O ficheiro JSON descarregado pode então ser carregado através dos seguintes passos.

1. **Selecione upload JSON**.
1. Selecione o ficheiro que contém as instâncias de carga útil.

    [![Upload a granel instâncias através da JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Selecione **Upload**.

### <a name="edit-a-single-instance"></a>Editar uma única instância

1. Selecione a instância e selecione o ícone de **edição** ou **lápis**. 
1. Faça as alterações necessárias e selecione **Guardar**.

    [![Editar uma única instância.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Apagar uma instância

1. Selecione a instância e selecione o ícone **de eliminar** ou **resíduos**.

   [![Elimine uma instância selecionando Apagar.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Confirmar a eliminação selecionando **Apagar**.

> [!NOTE]
> Uma instância deve passar com sucesso um controlo de validação de campo a eliminar.

## <a name="hierarchies"></a>Hierarquias

O explorador de Insights da Série De Tempo Azure suporta as operações de **criação**de hierarquias, **READ,** **UPDATE**e **DELETE** dentro do navegador. 

Para começar, selecione a visão do **Modelo** a partir da visão **do** explorador de Insights da Série Tempo.

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá ao painel seletor do Modelo série de tempo e selecione **Hierarquias** do menu. Todas as hierarquias associadas ao seu ambiente selecionado da Time Series Insights serão exibidas.

    [![Crie uma hierarquia através do painel.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Selecione **+ Adicionar**.

    [![Hierarquia + Adicionar botão.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Selecione + Adicione o **nível** no painel direito.

    [![Adicione um nível à hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Introduza os detalhes da hierarquia e selecione **Guardar**.

    [![Especifique detalhes da hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Carregar a granel uma ou mais hierarquias

> [!TIP]
> Pode guardar as suas hierarquias para o seu ambiente de trabalho na JSON. O ficheiro JSON descarregado pode então ser carregado através dos seguintes passos.

1. **Selecione upload JSON**.
1. Selecione o ficheiro que contém a carga útil da hierarquia.
1. Selecione **Upload**.

    [![Seleções para carregamento a granel de hierarquias.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

1. Selecione a hierarquia e selecione o ícone de **edição** ou **lápis**.
1. Faça as alterações necessárias e selecione **Guardar**.

    [![Seleções para editar uma única hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Apagar uma hierarquia

1. Selecione a hierarquia e selecione o ícone **de eliminar** ou **resíduos**. 

    [![Elimine uma hierarquia selecionando o botão Delete.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Confirmar a eliminação selecionando **Apagar**.

## <a name="types"></a>Tipos

O explorador de Insights da Série De Tempo Azure suporta as operações tipo **CREATE,** **READ,** **UPDATE**e **DELETE** dentro do navegador. 

Para começar, selecione a visão do **Modelo** a partir da visão **do** explorador de Insights da Série Tempo.

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá ao painel seletor do Modelo série de tempo e selecione **Tipos** do menu. Todos os tipos associados ao seu ambiente time series insights selecionados serão apresentados.

    [![Os tipos de modelos da série temporal painel.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Selecione **+ Adicione** para exibir o **novo tipo** popup modal.
1. Introduza propriedades e variáveis para o seu tipo. Uma vez introduzido, selecione **Guardar**. 

    [![Configurações de configuração para adicionar um tipo.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Carregar a granel um ou mais tipos

> [!TIP]
> Pode guardar os seus tipos para o seu ambiente de trabalho em JSON. O ficheiro JSON descarregado pode então ser carregado através dos seguintes passos.

1. **Selecione upload JSON**.
1. Selecione o ficheiro que contém a carga útil do tipo.
1. Selecione **Upload**.

    [![Tipos a granel a carregar opções.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Editar um único tipo

1. Selecione o tipo e selecione o ícone de **edição** ou **lápis**.
1. Faça as alterações necessárias e selecione **Guardar**.

    [![Editar um tipo no painel.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Eliminar um tipo

1. Selecione o tipo e selecione o ícone **de eliminar** ou **resíduos**. .

   [![Elimine um tipo selecionando Apagar.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Confirmar a eliminação selecionando **Apagar**.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o Modelo da Série Tempo, leia [a modelação de dados.](./time-series-insights-update-tsm.md)

- Para saber mais sobre a pré-visualização, leia visualize os dados no explorador de [pré-visualização](./time-series-insights-update-explorer.md)da Série de Tempo Azure Insights .

- Para aprender sobre formas JSON suportadas, leia [as formas JSON suportadas](./time-series-insights-send-events.md#supported-json-shapes).
