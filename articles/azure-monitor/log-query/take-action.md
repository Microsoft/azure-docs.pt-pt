---
title: Iniciado pelo utilizador ação de Runbook da automatização do Azure no Log Analytics | Documentos da Microsoft
description: Este artigo descreve como executar um runbook da automatização a partir de uma Log Analytics pesquisa resultado sob demanda.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 9194d5fe6553607ac5a0bb4e133da97f53790984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61424759"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Agir com um Runbook da automatização de um resultado de pesquisa de registos do Log Analytics

> [!NOTE]
> Iniciar um runbook a partir dos resultados de pesquisa é uma funcionalidade de pesquisa de registos do portal clássico do que vai ser preterido no dia 15 de Fevereiro de 2019. Pode configurar um grupo de ação que pode iniciar um runbook, além de outras ações de um [regra de alerta](../platform/alerts-log.md) no Azure Monitor.

De um resultado de pesquisa de registo no Azure Log Analytics, pode agora selecionar **agir** para executar um runbook da automatização.  O runbook pode ser utilizado para corrigir o problema ou execute uma ação, tais como recolher informações de resolução de problemas, envie um e-mail ou criar um pedido de serviço. 


## <a name="components-and-features-used"></a>Componentes e funcionalidades utilizados
* [Conta de automatização do Azure](../../automation/automation-quickstart-create-account.md)
* [Área de trabalho do Log Analytics](../../azure-monitor/log-query/log-query-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Para iniciar o runbook a partir de pesquisa de registos

Para tomar medidas num evento e iniciar um runbook a partir de seus resultados de pesquisa de registo, começa criando uma pesquisa de registos e dos resultados pode invocar uma runbook a pedido. Isso pode ser obtido a partir da funcionalidade de pesquisa de registo clássico no [portal do Azure](../../azure-monitor/log-query/log-query-overview.md). Neste exemplo, realizar uma pesquisa de registos do portal do Azure com uma demonstração desta funcionalidade básica.

1. No portal do Azure, clique em **todos os serviços** e selecione **do Log Analytics**.  
2. Selecione a área de trabalho do Log Analytics.
3. Na área de trabalho, selecione **registos (clássico)** .  
4. Na página de pesquisa de registos, realizar uma pesquisa de registos.  
5. Os resultados da pesquisa de registo, clique nas reticências à esquerda de um dos campos e para o pop-up, selecione **tomar medidas em**.<br><br> ![Selecione a ação demorar de resultado de pesquisa](./media/take-action/log-search-takeaction-menuoption.png) 
6. Selecione **executar um runbook** e selecione um runbook seja executado.  Pode selecionar qualquer runbook na conta de automatização está ligada a área de trabalho do Log Analytics.  Tenha em atenção o seguinte:

    * Runbooks são organizados por etiquetas
    * Valores de parâmetro de entrada do Runbook podem ser selecionadas diretamente a partir dos campos do resultado da pesquisa.  Uma lista de baixo aparecerá a apresentar todos os campos disponíveis do resultado para selecionar a partir de.  
    * Pode optar por executar o runbook num [runbook worker híbrido](../../automation/automation-hybrid-runbook-worker.md) que tem instalado no computador que tenha o problema se tiver um grupo de trabalho de Runbook híbrida correspondente, que contém apenas este computador como membro.  Se o nome do grupo de função de trabalho híbrida corresponde ao nome do computador que está no resultado da pesquisa de registo, o grupo é selecionado automaticamente.    

6. Depois de clicar em **executar**, é aberta a página da tarefa de runbook para que possa rever o estado da tarefa.   

Se selecionar um runbook que foi configurado para ser [chamado a partir de um alerta do Log Analytics](../../automation/automation-create-alert-triggered-runbook.md), tem um parâmetro de entrada chamado **WebhookData** ou seja **objeto** tipo.  Se o parâmetro de entrada é obrigatório, tem de passar os resultados da pesquisa ao runbook para que ele pode converter a cadeia de caracteres formatada em JSON para um tipo de objeto, permitindo-lhe filtrar os itens específicos que irá referenciar nos atividades do runbook.  Fazer isso selecionando **resultado (objeto) de pesquisa** na lista pendente.<br><br> ![Selecione o objeto de dados do Webhook para o parâmetro de runbook](media/take-action/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Passos Seguintes

* Reveja os [referência de pesquisa de registos do Log Analytics](../../azure-monitor/log-query/log-query-overview.md) para ver todos os campos de pesquisa e facetas disponíveis no Log Analytics.
* Para saber como invocar um runbook da automatização automaticamente, reveja [invocar um runbook da automatização do Azure a partir de um alerta do Log Analytics](../../automation/automation-create-alert-triggered-runbook.md).  
