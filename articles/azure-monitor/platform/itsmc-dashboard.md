---
title: Investigar erros usando o painel de instrumentos
description: Este documento contém informações sobre erros no painel ITSMC.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: ebd59e637e498b8088fe9a302b1bb12efdf2c173
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089280"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Investigar erros utilizando o dashboard ITSMC

Este artigo contém informações sobre o painel de instrumentos de gestão de serviços de TI (ITSMC). O painel ajuda-o a investigar o estado do ITSMC.

## <a name="view-the-dashboard"></a>Ver o dashboard

Siga estes passos para abrir o painel de instrumentos.

1. Selecione **Todos os recursos** e, em seguida, encontre o **ServiceDesk *(o seu nome de espaço de trabalho).***

   ![Screenshot que mostra os recursos nos serviços Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

1. No painel esquerdo, selecione **Fontes de Dados do Espaço de Trabalho** e, em seguida, selecione **ItsM Connections**.

   ![Screenshot que mostra a seleção de conexões ITSM em Fontes de Dados do Local de Trabalho.](media/itsmc-overview/add-new-itsm-connection.png)

1. Na secção **Resumo,** selecione **Ver Resumo** para ver um gráfico de resumo.

    ![Screenshot que mostra a opção 'Ver Resumo' na secção Resumo.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

1. Selecione o gráfico na secção **Resumo** para abrir o painel de instrumentos.

    ![Screenshot que mostra a seleção do gráfico Resumo.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

1. Reveja o painel de instrumentos para obter o estado e quaisquer erros no seu conector.
    ![Screenshot que mostra o painel de instrumentos.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Compreender elementos do painel

O painel contém informações sobre os alertas enviados para a ferramenta ITSM utilizando este conector.

O painel de instrumentos é dividido em quatro secções:

- **ITENS DE TRABALHO CRIADOS**: O gráfico e a tabela mostram o número de artigos de trabalho por tipo. Selecione o gráfico ou a tabela para saber mais sobre os seus itens de trabalho.
      ![Screenshot que mostra os itens de trabalho criados secção.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
- **COMPUTADORES IMPACTADOS**: A tabela contém detalhes sobre os itens de configuração que criaram itens de trabalho.
      Selecione linhas nas tabelas para mais detalhes sobre os itens de configuração.
      A tabela contém um número limitado de linhas. Para ver toda a lista, **selecione Ver tudo.**
      ![Screenshot que mostra a secção de computadores impactados.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
- **ESTADO DO CONECTOR**: O gráfico e a tabela mostram informações sobre o estado do conector. Selecione o gráfico ou as mensagens na tabela para mais detalhes. A tabela mostra um número limitado de filas. Para ver toda a lista, **selecione Ver tudo.**
      ![Screenshot que mostra a secção de estado do conector.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
- **REGRAS DE ALERTA**: Esta secção mostra informações sobre o número de regras de alerta detetadas. Selecione linhas nas tabelas para mais detalhes sobre as regras que foram detetadas. A mesa tem um número limitado de filas. Para ver toda a lista, **selecione Ver tudo.**
      ![Screenshot que mostra a secção de regras de alerta.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

## <a name="next-steps"></a>Passos seguintes

Confira os [erros de estado do conector comum](itsmc-dashboard-errors.md).
