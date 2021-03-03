---
title: Ver e filtrar informações sobre recursos do Azure
description: Filtre informações e use diferentes pontos de vista para entender melhor os seus recursos Azure.
ms.topic: how-to
ms.date: 09/11/2020
ms.openlocfilehash: d3ad0c225a260fd0ae6b131b951b53c28042f4d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738638"
---
# <a name="view-and-filter-azure-resource-information"></a>Ver e filtrar informações sobre recursos do Azure

O portal Azure permite-lhe navegar informações detalhadas sobre recursos através das suas subscrições Azure. Este artigo mostra-lhe como filtrar informações e usar diferentes pontos de vista para entender melhor os seus recursos.

O artigo centra-se no ecrã **de todos os recursos** mostrados na imagem seguinte. Os ecrãs para tipos individuais de recursos, como máquinas virtuais, têm diferentes opções, tais como iniciar e parar um VM.

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Vista do portal Azure de todos os recursos":::

## <a name="filter-resources"></a>Filtrar recursos

Comece a explorar **todos os recursos** utilizando filtros para se concentrar num subconjunto dos seus recursos. A imagem que se segue mostra a filtragem em grupos de recursos, selecionando dois dos seis grupos de recursos numa subscrição.

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="Vista de filtro baseada em grupos de recursos":::

Pode combinar filtros, incluindo os baseados em pesquisas por texto, como mostra a imagem a seguir. Neste caso, os resultados são alargados a recursos que contenham "SimpleWinVM" num dos dois grupos de recursos já selecionados.

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="Vista de filtro com base na entrada de texto":::

Para alterar quais colunas estão incluídas numa vista, **selecione 'Gerir' e** **editar colunas**.

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="Editar colunas mostradas à vista":::

## <a name="save-use-and-delete-views"></a>Guardar, usar e apagar vistas

Pode guardar vistas que incluam os filtros e colunas que selecionou. Para guardar e utilizar uma vista:

1. **Selecione'S's Manage view** then **Save view**.

1. Introduza um nome para a vista e, em seguida, selecione **OK**. A vista guardada aparece agora no menu **'Ver's Manage.**

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="Vista salva":::

1. Para utilizar uma vista, altere entre **o Padrão** e uma das suas próprias opiniões para ver como isso afeta a lista de recursos apresentados.

Para eliminar uma vista:

1. **Selecione Verção De gestão** e, em **seguida, navegue em todas as vistas**.

1. Nas **vistas guardadas para o painel "Todos os recursos",** selecione a vista e selecione o ícone **Eliminar** ícone Eliminar o ![ ícone ](media/manage-filter-resource-views/icon-delete.png) .

## <a name="export-information-from-a-view"></a>Informação sobre exportação de vista

Pode exportar a informação de recursos de uma vista de pontos de vista. Para exportar informações em formato CSV:

1. Selecione **Export para CSV**.

    :::image type="content" source="media/manage-filter-resource-views/export-csv.png" alt-text="Screenshot da exportação para o formato CSV":::

1. Guarde o ficheiro localmente e abra em Excel ou outra aplicação que suporte o formato CSV. 

À medida que se desloca em torno do portal, você verá outras áreas onde você pode exportar informações, como um grupo de recursos individuais.

## <a name="summarize-resources-with-visuals"></a>Resumir recursos com visuais

As opiniões que analisámos até agora foram _pontos de vista_ da lista, mas também existem _visões sumárias_ que incluem visuais. Você pode guardar e usar estas vistas assim como você pode listar vistas. Os filtros persistem entre os dois tipos de vistas. Existem vistas padrão, como a vista **de localização** mostrada abaixo, bem como pontos de vista relevantes para serviços específicos, como a vista de **estado** para o armazenamento Azure.

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="Resumo dos recursos numa vista de mapa":::

Para guardar e utilizar uma visão sumária:

1. No menu 'ver' selecione **Resumo.**

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="Menu de visualização sumária":::

1. A vista sumária permite-lhe resumir por diferentes atributos, incluindo **Localização** e **Tipo**. Selecione um **Resumo por** opção e um visual apropriado. A imagem que se segue mostra o **resumo do tipo** com um gráfico de **barras** visual.

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="Resumo do tipo mostrando um gráfico de barras":::

1. **Selecione 'Gerir'** e **guarde** esta vista como fez com a vista da lista.

1. Na vista resumida, em **resumo do tipo,** selecione uma barra na tabela. A seleção da barra fornece uma lista filtrada para um tipo de recurso.

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="Todos os recursos filtrados por tipo":::

## <a name="run-queries-in-azure-resource-graph"></a>Executar consultas no Gráfico de Recursos Azure

O Azure Resource Graph fornece uma exploração eficiente e performante de recursos com a capacidade de consultar em escala através de um conjunto de subscrições. O ecrã **de todos os recursos** no portal Azure inclui um link para abrir uma consulta de Gráfico de Recurso que é rastreada para a vista filtrada atual.

Para executar uma consulta de Gráfico de Recurso:

1. Selecione **Consulta aberta**.

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Consulta de gráfico de recurso Azure open":::

1. No **Azure Resource Graph Explorer**, selecione **consulta de execução** para ver os resultados.

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Executar consulta de gráfico de recurso Azure":::

    Para obter mais informações, consulte [executar a sua primeira consulta de Gráfico de Recurso utilizando o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md).

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do portal do Azure](azure-portal-overview.md)

[Criar e partilhar dashboards no portal do Azure](azure-portal-dashboards.md)
