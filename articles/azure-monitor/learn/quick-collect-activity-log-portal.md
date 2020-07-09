---
title: Enviar log de atividade azure para log analytics espaço de trabalho usando o portal Azure
description: Utilize o portal Azure para criar um espaço de trabalho log Analytics e uma definição de diagnóstico para enviar o registo de Atividade para Registos monitores Azure.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 6f13a465aa43f0c45fa99d2ea396a5ca92d75d66
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85446394"
---
# <a name="send-azure-activity-log-to-log-analytics-workspace-using-azure-portal"></a>Enviar log de atividade azure para log analytics espaço de trabalho usando o portal Azure
O registo de Atividades é um registo de plataforma no Azure que fornece informações sobre eventos de nível de subscrição. Isto inclui informações como quando um recurso é modificado ou quando uma máquina virtual é iniciada. Pode visualizar o registo de Atividade no portal Azure ou recuperar entradas com PowerShell e CLI. Este quickstart mostra como usar o portal Azure para criar um espaço de trabalho log Analytics e uma definição de diagnóstico para enviar o registo de Atividade para Registos do Monitor Azure, onde pode analisá-lo usando [consultas de registo](../log-query/log-query-overview.md) e ativar [outras funcionalidades,](../platform/alerts-log-query.md) tais como alertas de registo e livros de [trabalho.](../platform/workbooks-overview.md) 

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 



## <a name="create-a-log-analytics-workspace"></a>Criar uma área de trabalho do Log Analytics
No portal Azure, procure e, em seguida, selecione **log analytics espaços de trabalho**. 

![Portal do Azure](media/quick-create-workspace/azure-portal-01.png)
  
Clique **em Adicionar**, e, em seguida, forneça valores para o grupo de **Recursos,** nome **do**espaço de trabalho e **localização**. O nome do espaço de trabalho deve ser único em todas as subscrições do Azure.

![Criar área de trabalho](media/quick-collect-activity-log/create-workspace.png)

Clique **em Rever + criar** para rever as definições e, em seguida, **criar** para criar o espaço de trabalho. Isto irá selecionar um nível de preços predefinido de **Pay-as-you-go** que não incorrerá em quaisquer alterações até que comece a recolher uma quantidade suficiente de dados. Não há qualquer custo para a recolha do registo de Atividade.


## <a name="create-diagnostic-setting"></a>Criar definição de diagnóstico
No portal Azure, procure e, em seguida, selecione **Monitor**. 

![Portal do Azure](media/quick-collect-activity-log/azure-portal-monitor.png)

Selecione **registo de atividades**. Deve ver eventos recentes para a subscrição atual. Clique **nas definições de Diagnóstico** para visualizar as definições de diagnóstico para a subscrição.

![Registo de atividades](media/quick-collect-activity-log/activity-log.png)

Clique **na definição de diagnóstico** para criar uma nova definição. 

![Criar definição de diagnóstico](media/quick-collect-activity-log/create-diagnostic-setting.png)

Digite um nome como *Enviar registo de atividade para o espaço de trabalho*. Selecione cada uma das categorias. Selecione **Enviar para Registar Analytics** como o único destino e, em seguida, especificar o espaço de trabalho que criou. Clique em **Guardar** para criar a definição de diagnóstico e, em seguida, fechar a página.

![Nova definição de diagnóstico](media/quick-collect-activity-log/new-diagnostic-setting.png)

## <a name="generate-log-data"></a>Gerar dados de registo
Apenas novas entradas de registo de atividade serão enviadas para o espaço de trabalho Log Analytics, por isso execute algumas ações na sua subscrição que serão registadas, como iniciar ou parar uma máquina virtual ou criar ou modificar outro recurso. Pode ter de esperar alguns minutos para que a definição de diagnóstico seja criada e que os dados sejam inicialmente escritos no espaço de trabalho. Após este atraso, todos os eventos escritos no registo de atividade serão enviados para o espaço de trabalho dentro de alguns segundos.

## <a name="retrieve-data-with-a-log-query"></a>Recuperar dados com uma consulta de registo

Selecione **Registos** no menu **Azure Monitor.** Feche a página **de consultas Exemplo.** Se o âmbito não estiver definido para o espaço de trabalho que criou, clique em **Selecionar o âmbito** e localizá-lo.

![Log Analytics](media/quick-collect-activity-log/log-analytics-scope.png)

Na janela de consulta, `AzureActivity` escreva e clique em **Executar**. Esta é uma consulta simples que devolve todos os registos na tabela *AzureActivity,* que contém todos os registos enviados a partir do registo de Atividade.

![Consulta simples](media/quick-collect-activity-log/query-01.png)

Expandir um dos registos para ver as suas propriedades detalhadas.

![Expandir propriedades](media/quick-collect-activity-log/expand-properties.png)

Experimente uma consulta mais complexa, como `AzureActivity | summarize count() by CategoryValue` a que dá uma contagem de eventos resumidos por categoria.

![Consulta complexa](media/quick-collect-activity-log/query-02.png)


## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, configuraste o registo de Atividade para ser enviado para um espaço de trabalho do Log Analytics. Pode agora configurar outros dados a serem recolhidos no espaço de trabalho onde pode analisá-los em conjunto usando [consultas de registo](../log-query/log-query-overview.md) no Azure Monitor e funcionalidades de alavancagem, tais como [alertas](../platform/alerts-log-query.md) de registo e livros de [trabalho.](../platform/workbooks-overview.md) Em seguida, deverá recolher [registos](../platform/resource-logs.md) de recursos dos seus recursos Azure que complementam os dados no registo de Atividade fornecendo informações sobre as operações que foram realizadas dentro de cada recurso.


> [!div class="nextstepaction"]
> [Recolher e analisar registos de recursos com o Azure Monitor](tutorial-resource-logs.md)
