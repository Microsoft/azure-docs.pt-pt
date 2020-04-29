---
title: Criar pontos de vista para analisar dados de registo no Monitor Do Azure [ Monitor] Microsoft Docs
description: Ao utilizar o View Designer no Monitor Azure, pode criar vistas personalizadas que são exibidas no portal Azure e conter uma variedade de visualizações sobre dados no espaço de trabalho log Analytics. Este artigo contém uma visão geral do View Designer e apresenta procedimentos para criar e editar vistas personalizadas.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/10/2019
ms.openlocfilehash: 9a7521f61dc59bd954629a05638c159ab0e70556
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658494"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Crie vistas personalizadas usando o View Designer no Monitor Azure
Ao utilizar o View Designer no Monitor Azure, pode criar uma variedade de vistas personalizadas no portal Azure que podem ajudá-lo a visualizar dados no seu espaço de trabalho Log Analytics. Este artigo apresenta uma visão geral do View Designer e procedimentos para criar e editar vistas personalizadas.

> [!IMPORTANT]
> As vistas no Monitor Azure estão a ser gradualmente eliminadas e substituídas por [livros](workbooks-overview.md) que fornecem funcionalidades adicionais. Consulte o designer de [visualização do Azure Monitor para](view-designer-conversion-overview.md) obter detalhes sobre a conversão das suas vistas existentes em livros de trabalho.

Para mais informações sobre o View Designer, consulte:

* [Referência de azulejos](view-designer-tiles.md): Fornece um guia de referência para as definições de cada um dos azulejos disponíveis nas suas vistas personalizadas.
* [Referência da parte](view-designer-parts.md)de visualização : Fornece um guia de referência para as definições para as peças de visualização que estão disponíveis nas suas vistas personalizadas.


## <a name="concepts"></a>Conceitos
As vistas são exibidas na página de **visão geral** do Monitor Azure no portal Azure. Abra esta página a partir do menu **Do Monitor Azure** clicando **mais** na secção **Insights.** Os azulejos em cada vista personalizada são exibidos alfabeticamente, e os azulejos para as soluções de monitorização são instalados no mesmo espaço de trabalho.

![Página de visão geral](media/view-designer/overview-page.png)

As vistas que cria com o View Designer contêm os elementos descritos na tabela seguinte:

| Parte | Descrição |
|:--- |:--- |
| Mosaicos | Estão expostos na sua página de **visão geral** do Monitor Azure. Cada azulejo apresenta um resumo visual da vista personalizada que representa. Cada tipo de azulejo proporciona uma visualização diferente dos seus registos. Selecione um azulejo para exibir uma vista personalizada. |
| Vista personalizada | Exibido quando seleciona um azulejo. Cada vista contém uma ou mais peças de visualização. |
| Peças de visualização | Apresentar uma visualização de dados no espaço de trabalho do Log Analytics com base numa ou mais consultas de [registo](../log-query/log-query-overview.md). A maioria das peças inclui um cabeçalho, que proporciona uma visualização de alto nível, e uma lista, que apresenta os melhores resultados. Cada tipo de peça proporciona uma visualização diferente dos registos no espaço de trabalho log Analytics. Selecione elementos da peça para realizar uma consulta de registo que forneça registos detalhados. |

## <a name="required-permissions"></a>Permissões obrigatórias
É necessário, pelo menos, [permissões de nível contributivo](manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho do Log Analytics para criar ou modificar vistas. Se não tiver esta permissão, a opção 'Ver Designer' não será exibida no menu.


## <a name="work-with-an-existing-view"></a>Trabalhar com uma vista existente
Vistas que foram criadas com o View Designer exibem as seguintes opções:

![Menu de visão geral](media/view-designer/overview-menu.png)

As opções são descritas na tabela seguinte:

| Opção | Descrição |
|:--|:--|
| Atualizar   | Atualiza a vista com os dados mais recentes. | 
| Registos      | Abre o [Log Analytics](../log-query/portals.md) para analisar dados com consultas de registo. |
| Editar       | Abre a vista no View Designer para editar os seus conteúdos e configuração.  |
| Clone      | Cria uma nova vista e abre-a no View Designer. O nome da nova vista é o mesmo que o nome original, mas com *Copy* anexado a ele. |
| Intervalo de datas | Detete o filtro de data e hora para os dados incluídos na vista. Esta gama de datas é aplicada antes de qualquer intervalo de data definido em consultas na vista.  |
| +          | Defina um filtro personalizado que esteja definido para a vista. |


## <a name="create-a-new-view"></a>Criar uma nova vista
Pode criar uma nova vista no View Designer selecionando **o View Designer** no menu do seu espaço de trabalho Log Analytics.

![Ver azulejo designer](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Trabalhar com o Designer de Vistas
Usa o View Designer para criar novas vistas ou editar as existentes. 

Ver Designer tem três vidraças: 
* **Design**: Contém a visão personalizada que está a criar ou editar. 
* **Comandos**: Contém os azulejos e as peças que adiciona ao painel **de design.** 
* **Propriedades**: Exibe as propriedades dos azulejos ou peças selecionadas.

![Estruturador de Vista](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Configure o azulejo de vista
Uma vista personalizada pode ter apenas um único azulejo. Para ver o azulejo atual ou selecione um alternado, selecione o separador **Tile** no painel **de controlo.** O painel **Properties** exibe as propriedades do azulejo atual. 

Pode configurar as propriedades do azulejo de acordo com as informações na [referência tile](view-designer-tiles.md) e, em seguida, clicar **em Aplicar** para guardar as alterações.

### <a name="configure-the-visualization-parts"></a>Configure as peças de visualização
Uma vista pode incluir qualquer número de peças de visualização. Para adicionar peças a uma vista, selecione o separador **'Ver'** e, em seguida, selecione uma peça de visualização. O painel **Properties** exibe as propriedades da peça selecionada. 

Pode configurar as propriedades da visualização de acordo com a informação na referência da [peça visualização](view-designer-parts.md) e, em seguida, clicar **em Aplicar** para guardar as alterações.

As vistas têm apenas uma linha de peças de visualização. Pode reorganizar as peças existentes arrastando-as para um novo local.

Pode remover uma parte de visualização da vista selecionando o **X** na parte superior direita da peça.


### <a name="menu-options"></a>Opções de menu
As opções para trabalhar com pontos de vista no modo de edição são descritas na tabela seguinte.

![Editar menu](media/view-designer/edit-menu.png)

| Opção | Descrição |
|:--|:--|
| Guardar        | Economiza as suas alterações e fecha a vista. |
| Cancelar      | Descarta as suas alterações e fecha a vista. |
| Excluir vista | Apaga a vista. |
| Exportar      | Exporta a vista para um modelo de Gestor de [Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) que você pode importar para outro espaço de trabalho. O nome do ficheiro é o nome da vista, e tem uma extensão *omsview.* |
| Importar      | Importa o ficheiro *omsview* que exportou de outro espaço de trabalho. Esta ação substitui a configuração da vista existente. |
| Clone       | Cria uma nova vista e abre-a no View Designer. O nome da nova vista é o mesmo que o nome original, mas com *Copy* anexado a ele. |

## <a name="next-steps"></a>Passos seguintes
* Adicione [azulejos](view-designer-tiles.md) à sua vista personalizada.
* Adicione [peças de visualização](view-designer-parts.md) à sua vista personalizada.
