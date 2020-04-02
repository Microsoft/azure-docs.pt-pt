---
title: Exemplos de utilização do conector de fluxo de dados do Microsoft Azure (Pré-visualização)
description: Aprenda alguns exemplos comuns de utilização do conector Microsoft Flow.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 1b9d593b0f0895e2ba75fae7ab7e78ea883c8907
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521714"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Exemplos de utilização do conector Microsoft Flow (Pré-visualização)

O conector de fluxo do Azure Data Explorer permite ao Azure Data Explorer utilizar as capacidades de [Fluxo do Microsoft Power Automate](https://flow.microsoft.com/) para executar consultas e comandos Kusto automaticamente como parte de uma tarefa programada ou desencadeada. Este documento contém vários exemplos comuns de utilização do conector Microsoft Flow.

Para mais informações, consulte o [conector Microsoft Flow (Pré-visualização)](flow.md).

* [Conector Microsoft Flow e SQL](#microsoft-flow-connector-and-sql)
* [Empurre dados para power bi dataset](#push-data-to-power-bi-dataset)
* [Consultas condicionais](#conditional-queries)
* [Envie vários gráficos de fluxo de explorador de dados Azure](#email-multiple-azure-data-explorer-flow-charts)

## <a name="microsoft-flow-connector-and-sql"></a>Conector Microsoft Flow e SQL

Utilize o conector Microsoft Flow para consultar os seus dados e agrega-os numa base de dados SQL.

> [!Note]
> A inserção SQL é feita separadamente para cada linha. Utilize apenas o conector Microsoft Flow para pequenas quantidades de dados de saída. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> No campo Nome do *Cluster,* introduza o URL do cluster.

## <a name="push-data-to-power-bi-dataset"></a>Empurre dados para power bi dataset

O conector Microsoft Flow pode ser utilizado juntamente com o conector Power BI para empurrar dados das consultas de Kusto para o streaming de dados do Power BI.

1. Crie uma nova ação de consulta de Run e resultados da lista.
1. Selecione **Novo passo**.
1. Selecione **Adicionar uma ação** e procurar power BI.
1. Selecione **Power BI**.
1. **Selecione Adicionar linhas a um conjunto de dados**. 

    ![Conector Flow Power BI](./media/flow-usage/flow-powerbiconnector.png)
1. Introduza o **Espaço de Trabalho,** **Dataset**e **Tabela** para o qual os dados serão empurrados.
1. A partir do diálogo de conteúdo dinâmico, adicione uma Carga útil contendo o seu esquema de conjunto de dados e os resultados de consulta de Kusto relevantes.

    ![Campos Flow Power BI](./media/flow-usage/flow-powerbifields.png)

O fluxo aplica automaticamente a ação Power BI para cada linha da tabela de resultados da consulta Kusto. 

![Ação flow Power BI para cada linha](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Consultas condicionais

Os resultados das consultas kusto podem ser usados como entrada ou condições para as próximas ações de fluxo.

No exemplo seguinte, pedimos a Kusto os incidentes ocorridos durante o último dia. Para cada incidente resolvido, uma mensagem frouxa é publicada e é criada uma notificação push.
Por cada incidente que ainda está ativo, Kusto é questionado sobre mais informações sobre incidentes semelhantes. Envia essa informação como e-mail e abre uma tarefa relacionada com a TFS.

Siga estas instruções para criar um Fluxo semelhante:

1. Crie uma nova ação de consulta de Run e resultados da lista.
1. Selecione **Novo passo**.
1. Selecione **o controlo de condições**.
1. A partir da janela de conteúdo dinâmico, selecione o parâmetro que pretende utilizar como condição para as próximas ações.
1. Selecione o tipo de *Relacionamento* e *Valor* para definir uma condição específica no parâmetro dado.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    O fluxo aplica esta condição em cada linha da tabela de resultados da consulta.
1. Adicione ações para quando a condição é verdadeira e falsa.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

Pode utilizar os valores de resultados da consulta kusto como entrada para as próximas ações. Selecione os valores de resultado saem da janela de conteúdo dinâmico.
No exemplo abaixo, foi adicionado um Slack - Post Message action and Visual Studio - Create a new work item action contendo dados da consulta kusto.

![Folga - Ação pós mensagem](./media/flow-usage/flow-slack.png)

![Ação do Estúdio Visual](./media/flow-usage/flow-visualstudio.png)

Neste exemplo, se um incidente ainda estiver ativo, consulta Kusto novamente para obter informações sobre como os incidentes da mesma fonte foram resolvidos no passado.

![Consulta de condição de fluxo](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> No campo Nome do *Cluster,* introduza o URL do cluster.

Visualize esta informação como um gráfico de tartes e envie-a por e-mail para a equipa.

![E-mail de condição de fluxo](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Envie vários gráficos de fluxo de explorador de dados Azure

1. Crie um novo Fluxo com o gatilho de recorrência e defina o intervalo do Fluxo e a frequência. 
1. Adicione um novo passo, com um ou mais Kusto - Corra a consulta e visualize as ações de resultados. 

    ![Executar várias consultas num fluxo](./media/flow-usage/flow-severalqueries.png)
1. Para cada Kusto - Executar consulta e visualizar resultado, defina os seguintes campos:
    * URL do Cluster
    * Nome da Base de Dados
    * Consulta e Gráfico Tipo (tabela HTML, gráfico de tartes, gráfico de tempo, gráfico de barras ou insira um valor personalizado).

    ![Visualizar resultados com vários anexos](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. Adicione uma ação de e-mail (v2): 
    1. Na secção do corpo, selecione o ícone da vista de código.
    1. No campo **Body,** insira o BodyHtml necessário de modo a que o resultado visualizado da consulta seja incluído no corpo do e-mail.
    1. Para adicionar um anexo ao e-mail, adicione o Nome do Anexo e o Conteúdo do Anexo.
    
    ![E-mail vários anexos](./media/flow-usage/flow-email-multiple-attachments.png)

    Para obter instruções completas sobre a criação de uma ação por e-mail, consulte os resultados da [consulta de Email Kusto](flow.md#email-kusto-query-results). 

Resultados:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [conector microsoft Azure Explorer Logic App,](https://docs.microsoft.com/azure/kusto/tools/logicapps) que é outra forma de executar consultas e comandos Kusto automaticamente como parte de uma tarefa programada ou desencadeada.
