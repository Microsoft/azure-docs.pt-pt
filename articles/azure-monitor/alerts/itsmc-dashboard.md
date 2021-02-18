---
title: Investigar erros utilizando o dashboard ITSMC
description: Saiba como utilizar o painel de controlo do serviço de TI para investigar erros.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5cbd501b9ccb408ee23cb5c8ed9cde2689ef79fb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617842"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Investigar erros utilizando o dashboard ITSMC

Este artigo contém informações sobre o painel de instrumentos de gestão de serviços de TI (ITSMC). O painel ajuda-o a investigar o estado do seu conector.

## <a name="view-errors"></a>Ver erros

Para ver erros no painel de instrumentos:

1. Selecione **Todos os recursos** e, em seguida, encontre o **ServiceDesk *(o seu nome de espaço de trabalho).***

   ![Screenshot que mostra os recursos nos serviços Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

2. Em **Fontes de Dados** do Espaço de Trabalho no painel esquerdo, selecione **Ligações ITSM**:

   ![Screenshot que mostra a seleção de conexões ITSM em Fontes de Dados do Local de Trabalho.](media/itsmc-overview/add-new-itsm-connection.png)

3. Em **Resumo**, na área do **Conector de Gestão de Serviços de TI,** selecione **Ver Resumo:**

   ![Screenshot que mostra o botão Ver Resumo.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Quando um gráfico aparecer na área do **Conector de Gestão de Serviços de TI,** selecione-o:

   ![Screenshot que mostra a seleção de um gráfico.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. O painel aparece. Utilize-o para rever o estado e os erros no seu conector.
   
   ![Screenshot que mostra o estado do conector no painel de instrumentos.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Compreender elementos do painel

O painel contém informações sobre os alertas enviados para a ferramenta ITSM através deste conector. O painel é dividido em quatro partes.

### <a name="created-work-items"></a>Itens de trabalho criados 

Na área **de TRABALHO CRIADO,** o gráfico e a tabela abaixo contêm a contagem dos artigos de trabalho por tipo. Se selecionar o gráfico ou a tabela, poderá ver mais detalhes sobre os itens de trabalho.

![Screenshot que mostra um item de trabalho criado.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>Computadores afetados 

Na área **dos COMPUTADORES IMPACTADOS,** a tabela lista os computadores e os seus itens de trabalho associados. Ao selecionar linhas nas tabelas, pode obter mais detalhes sobre os computadores.

A tabela contém um número limitado de linhas. Se quiser ver todas as linhas, selecione **Ver todas**.

![Screenshot que mostra computadores afetados.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>Estado do conector 

Na área do **status do CONECTOR,** o gráfico e a tabela abaixo contêm mensagens sobre o estado do conector. Ao selecionar o gráfico ou as linhas na tabela, pode obter mais detalhes sobre as mensagens.

A tabela contém um número limitado de linhas. Se quiser ver todas as linhas, selecione **Ver todas**.

Para saber mais sobre as mensagens na tabela, consulte [este artigo.](itsmc-dashboard-errors.md)

![Screenshot que mostra o estado do conector.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>Regras de alertas 

Na área **DE ALERTA REGRAS,** a tabela contém informações sobre o número de regras de alerta que foram detetadas. Ao selecionar linhas na tabela, pode obter mais detalhes sobre as regras detetadas.
    
A tabela contém um número limitado de linhas. Se quiser ver todas as linhas, selecione **Ver todas**.

![Screenshot que mostra regras de alerta.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
