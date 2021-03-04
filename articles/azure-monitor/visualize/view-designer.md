---
title: Criar vistas para analisar dados de registo no Azure Monitor | Microsoft Docs
description: Ao utilizar o View Designer no Azure Monitor, pode criar vistas personalizadas que são exibidas no portal Azure e conter uma variedade de visualizações de dados no espaço de trabalho Log Analytics. Este artigo contém uma visão geral do View Designer e apresenta procedimentos para criar e editar vistas personalizadas.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/04/2020
ms.openlocfilehash: cb0274260022c55ae657b5b28b2c9ad1903d0296
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043274"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Crie vistas personalizadas utilizando o View Designer no Azure Monitor
Ao utilizar o View Designer no Azure Monitor, pode criar uma variedade de visualizações personalizadas no portal Azure que podem ajudá-lo a visualizar dados no seu espaço de trabalho Log Analytics. Este artigo apresenta uma visão geral do View Designer e procedimentos para criar e editar vistas personalizadas.

> [!IMPORTANT]
> As opiniões no Azure Monitor foram transitadas para livros que fornecem [funcionalidades](workbooks-overview.md) adicionais. Consulte [o Azure Monitor ver designer para guia de transição de livros](view-designer-conversion-overview.md) de trabalho para obter detalhes sobre a conversão das suas vistas existentes em livros de trabalho.
 


Para mais informações sobre o View Designer, consulte:

* [Referência de azulejos](view-designer-tiles.md): Fornece um guia de referência às definições de cada um dos azulejos disponíveis nas suas vistas personalizadas.
* [Referência da parte de visualização](view-designer-parts.md): Fornece um guia de referência às definições das peças de visualização que estão disponíveis nas suas vistas personalizadas.


## <a name="concepts"></a>Conceitos
As vistas são exibidas na página **geral** do Monitor Azure no portal Azure. Abra esta página a partir do menu **Azure Monitor** clicando **mais** na secção **Insights.** Os azulejos de cada vista personalizada são exibidos alfabeticamente, e os azulejos para as soluções de monitorização são instalados no mesmo espaço de trabalho.

![Página de descrição geral](media/view-designer/overview-page.png)

As vistas que cria com o View Designer contêm os elementos descritos na tabela seguinte:

| Parte | Descrição |
|:--- |:--- |
| Mosaicos | São apresentados na sua página **de visão geral do** Monitor Azure. Cada azulejo apresenta um resumo visual da vista personalizada que representa. Cada tipo de azulejo fornece uma visualização diferente dos seus registos. Selecione um azulejo para exibir uma vista personalizada. |
| Vista personalizada | Apresentado quando seleciona um azulejo. Cada vista contém uma ou mais partes de visualização. |
| Peças de visualização | Apresentar uma visualização de dados no espaço de trabalho Log Analytics com base numa ou mais [consultas de registo](../logs/log-query-overview.md). A maioria das partes inclui um cabeçalho, que fornece uma visualização de alto nível, e uma lista, que apresenta os melhores resultados. Cada tipo de peça fornece uma visualização diferente dos registos no espaço de trabalho Log Analytics. Selecione elementos na peça para realizar uma consulta de registo que forneça registos detalhados. |

## <a name="required-permissions"></a>Permissões obrigatórias
Necessita de pelo menos permissões de [nível de contribuinte](../logs/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho Log Analytics para criar ou modificar visualizações. Se não tiver esta permissão, a opção 'Ver Designer' não será apresentada no menu.


## <a name="work-with-an-existing-view"></a>Trabalhar com uma vista existente
As vistas que foram criadas com o View Designer exibem as seguintes opções:

![Menu geral](media/view-designer/overview-menu.png)

As opções são descritas no quadro seguinte:

| Opção | Descrição |
|:--|:--|
| Atualizar   | Refresca a vista com os dados mais recentes. | 
| Registos      | Abre o [Log Analytics](../logs/log-query-overview.md) para analisar dados com consultas de registo. |
| Editar       | Abre a vista no View Designer para editar os seus conteúdos e configurações.  |
| Clone      | Cria uma nova vista e abre-a no View Designer. O nome da nova vista é o mesmo que o nome original, mas com *Copy* anexado a ele. |
| Intervalo de datas | Desa estada o filtro de data e intervalo de tempo para os dados incluídos na vista. Este intervalo de datas é aplicado antes de qualquer intervalo de data definido em consultas na vista.  |
| +          | Defina um filtro personalizado definido para a vista. |


## <a name="create-a-new-view"></a>Criar uma nova vista
Pode criar uma nova vista no View Designer selecionando **o View Designer** no menu do seu espaço de trabalho Log Analytics.

![Ver azulejo do designer](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Trabalhar com o View Designer
Utiliza o View Designer para criar novas vistas ou editar as existentes. 

Ver Designer tem três vidraças: 
* **Design**: Contém a vista personalizada que está a criar ou a editar. 
* **Controlos**: Contém os azulejos e peças que adiciona ao painel **de design.** 
* **Propriedades**: Exibe as propriedades dos azulejos ou peças selecionadas.

![Estruturador de Vista](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Configure o azulejo da vista
Uma vista personalizada pode ter apenas um único azulejo. Para visualizar o azulejo atual ou selecionar um alternativo, selecione o **separador Azulejo** no **painel de controlo.** O painel **Propriedades** exibe as propriedades do azulejo atual. 

Pode configurar as propriedades do azulejo de acordo com as informações na [referência do Azulejo](view-designer-tiles.md) e clicar em **Aplicar** para guardar as alterações.

### <a name="configure-the-visualization-parts"></a>Configure as peças de visualização
Uma vista pode incluir qualquer número de peças de visualização. Para adicionar peças a uma vista, selecione o separador **Ver** e, em seguida, selecione uma peça de visualização. O painel **Propriedades** exibe as propriedades da peça selecionada. 

Pode configurar as propriedades de visualização de acordo com as informações na [referência da parte visualização](view-designer-parts.md) e, em seguida, clicar em **Aplicar** para guardar as alterações.

As vistas têm apenas uma fileira de peças de visualização. Pode reorganizar as peças existentes arrastando-as para um novo local.

Pode remover uma peça de visualização da vista selecionando o **X** no canto superior direito da peça.


### <a name="menu-options"></a>Opções de menu
As opções para trabalhar com visualizações no modo de edição são descritas na tabela seguinte.

![Editar menu](media/view-designer/edit-menu.png)

| Opção | Descrição |
|:--|:--|
| Guardar        | Guarda as alterações e fecha a vista. |
| Cancelar      | Descarta as alterações e fecha a vista. |
| Eliminar Ver | Elimina a vista. |
| Exportar      | Exporta a vista para um [modelo de Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) que você pode importar para outro espaço de trabalho. O nome do ficheiro é o nome da vista, e tem uma extensão *omsview.* |
| Importar      | Importa o ficheiro *omsview* que exportou de outro espaço de trabalho. Esta ação substitui a configuração da vista existente. |
| Clone       | Cria uma nova vista e abre-a no View Designer. O nome da nova vista é o mesmo que o nome original, mas com *Copy* anexado a ele. |

## <a name="next-steps"></a>Passos seguintes
* Adicione [azulejos](view-designer-tiles.md) à sua vista personalizada.
* Adicione [peças de visualização](view-designer-parts.md) à sua vista personalizada.