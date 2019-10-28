---
title: Criar exibições para analisar dados de log em Azure Monitor | Microsoft Docs
description: Usando o designer de exibição no Azure Monitor, você pode criar exibições personalizadas que são exibidas na portal do Azure e conter uma variedade de visualizações nos dados no espaço de trabalho Log Analytics. Este artigo contém uma visão geral do designer de exibição e apresenta procedimentos para criar e editar exibições personalizadas.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: a1a4dbffed37480178d1b94a77587ca251396db6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931942"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Criar exibições personalizadas usando o designer de exibição no Azure Monitor
Usando o designer de exibição no Azure Monitor, você pode criar uma variedade de exibições personalizadas na portal do Azure que podem ajudá-lo a Visualizar dados em seu espaço de trabalho do Log Analytics. Este artigo apresenta uma visão geral do designer de exibição e dos procedimentos para criar e editar exibições personalizadas.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para obter mais informações sobre o designer de exibição, consulte:

* [Referência de bloco](view-designer-tiles.md): fornece um guia de referência para as configurações de cada um dos blocos disponíveis em suas exibições personalizadas.
* [Referência de parte de visualização](view-designer-parts.md): fornece um guia de referência para as configurações para as partes de visualização que estão disponíveis em suas exibições personalizadas.


## <a name="concepts"></a>Conceitos
As exibições são exibidas na página de **visão geral** Azure Monitor no portal do Azure. Abra essa página no menu **Azure monitor** clicando em **mais** na seção **insights** . Os blocos em cada exibição personalizada são exibidos em ordem alfabética e os blocos para as soluções de monitoramento são instalados no mesmo espaço de trabalho.

![Página de visão geral](media/view-designer/overview-page.png)

As exibições criadas com o designer de exibição contêm os elementos descritos na tabela a seguir:

| Parte | Descrição |
|:--- |:--- |
| Blocos | São exibidos na sua página de **visão geral** do Azure monitor. Cada bloco exibe um resumo Visual da exibição personalizada que ele representa. Cada tipo de peça fornece uma visualização diferente de seus registros. Selecione um bloco para exibir uma exibição personalizada. |
| Exibição personalizada | Exibido quando você seleciona um bloco. Cada exibição contém uma ou mais partes de visualização. |
| Partes de visualização | Apresente uma visualização dos dados no espaço de trabalho Log Analytics com base em uma ou mais [consultas de log](../log-query/log-query-overview.md). A maioria das partes inclui um cabeçalho, que fornece uma visualização de alto nível e uma lista, que exibe os resultados principais. Cada tipo de parte fornece uma visualização diferente dos registros no espaço de trabalho Log Analytics. Você seleciona elementos na parte para executar uma consulta de log que fornece registros detalhados. |

## <a name="required-permissions"></a>Permissões obrigatórias
Você precisa de pelo menos [permissões de nível de colaborador](manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho log Analytics para criar ou modificar modos de exibição. Se você não tiver essa permissão, a opção designer de exibição não será exibida no menu.


## <a name="work-with-an-existing-view"></a>Trabalhar com uma exibição existente
As exibições criadas com o designer de exibição exibem as seguintes opções:

![Menu visão geral](media/view-designer/overview-menu.png)

As opções são descritas na tabela a seguir:

| Opção | Descrição |
|:--|:--|
| Atualizar   | Atualiza a exibição com os dados mais recentes. | 
| Registos      | Abre o [log Analytics](../log-query/portals.md) para analisar dados com consultas de log. |
| Editar       | Abre o modo de exibição no designer de exibição para editar seu conteúdo e configuração.  |
| Clone      | Cria uma nova exibição e a abre no designer de exibição. O nome da nova exibição é o mesmo que o nome original, mas com *cópia* acrescentada a ela. |
| Intervalo de datas | Defina o filtro de intervalo de data e hora para os dados que estão incluídos na exibição. Esse intervalo de datas é aplicado antes de quaisquer intervalos de datas definidos em consultas na exibição.  |
| +          | Defina um filtro personalizado que é definido para a exibição. |


## <a name="create-a-new-view"></a>Criar uma nova exibição
Você pode criar um novo modo de exibição no designer de exibição selecionando o **Designer de exibição** no menu do seu espaço de trabalho do log Analytics.

![Bloco do designer de exibição](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Trabalhar com o designer de exibição
Use o designer de exibição para criar novas exibições ou editar as existentes. 

O designer de exibição tem três painéis: 
* **Design**: contém a exibição personalizada que você está criando ou editando. 
* **Controles**: contém os blocos e as partes que você adiciona ao painel **design** . 
* **Propriedades**: exibe as propriedades dos blocos ou das partes selecionadas.

![Ver Estruturador](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Configurar o bloco de exibição
Uma exibição personalizada pode ter apenas um único bloco. Para exibir o bloco atual ou selecionar um alternativo, selecione a guia **bloco** no painel de **controle** . O painel **Propriedades** exibe as propriedades do bloco atual. 

Você pode configurar as propriedades do bloco de acordo com as informações na [referência de bloco](view-designer-tiles.md) e, em seguida, clicar em **aplicar** para salvar as alterações.

### <a name="configure-the-visualization-parts"></a>Configurar as partes de visualização
Uma exibição pode incluir qualquer número de partes de visualização. Para adicionar partes a uma exibição, selecione a guia **Exibir** e, em seguida, selecione uma parte de visualização. O painel **Propriedades** exibe as propriedades da parte selecionada. 

Você pode configurar as propriedades da exibição de acordo com as informações na [referência da parte de visualização](view-designer-parts.md) e, em seguida, clicar em **aplicar** para salvar as alterações.

As exibições têm apenas uma linha de partes de visualização. Você pode reorganizar as partes existentes arrastando-as para um novo local.

Você pode remover uma parte de visualização do modo de exibição selecionando o **X** na parte superior direita da parte.


### <a name="menu-options"></a>Opções de menu
As opções para trabalhar com modos de exibição no modo de edição são descritas na tabela a seguir.

![Menu Editar](media/view-designer/edit-menu.png)

| Opção | Descrição |
|:--|:--|
| Guardar        | Salva as alterações e fecha a exibição. |
| Cancelar      | Descarta as alterações e fecha a exibição. |
| Excluir modo de exibição | Exclui a exibição. |
| Exportar      | Exporta a exibição para um [modelo de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) que você pode importar para outro espaço de trabalho. O nome do arquivo é o nome da exibição e tem uma extensão *omsview* . |
| Importar      | Importa o arquivo *omsview* que você exportou de outro espaço de trabalho. Essa ação substitui a configuração da exibição existente. |
| Clone       | Cria uma nova exibição e a abre no designer de exibição. O nome da nova exibição é o mesmo que o nome original, mas com *cópia* acrescentada a ela. |

## <a name="next-steps"></a>Passos seguintes
* Adicione [blocos](view-designer-tiles.md) à exibição personalizada.
* Adicione [partes de visualização](view-designer-parts.md) à sua exibição personalizada.
