---
title: Amostras de modelo de gestor de recursos para insights SQL
description: Experimente os modelos do Gestor de Recursos Azure para implementar e configurar insights SQL.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: dafb7335ef211cb9173ec2fb4565243d603d35fe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730533"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>Amostras de modelo de gestor de recursos para insights SQL
Este artigo inclui [modelos de gestor de recursos Azure para](../../azure-resource-manager/templates/template-syntax.md) permitir insights SQL para monitorizar o SQL em execução em Azure.  Consulte a [documentação sql insights](sql-insights-overview.md) para detalhes sobre a oferta e versões de SQL que apoiamos. Cada amostra inclui um ficheiro de modelo e um ficheiro de parâmetros com valores de amostra para fornecer ao modelo.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>Criar um perfil de monitorização de insights SQL
A amostra a seguir cria um perfil de monitorização de insights SQL, que inclui os dados de monitorização do SQL para recolher, frequência da recolha de dados, e especifica o espaço de trabalho para o qual os dados serão enviados.


### <a name="template-file"></a>Arquivo de modelo

Ver o [ficheiro de modelo no hub git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate).

### <a name="parameter-file"></a>Arquivo de parâmetros

Veja o [ficheiro do parâmetro no hub git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json).


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>Adicione um VM de monitorização a um perfil de monitorização de insights SQL
Uma vez criado um perfil de monitorização, precisa de alocar máquinas virtuais Azure que serão configuradas para recolher remotamente dados a partir dos recursos SQL que especifica na configuração para esse VM.  Consulte os insights SQL para obter mais detalhes.

A amostra que se segue configura um VM de monitorização para recolher os dados dos recursos SQL especificados.


### <a name="template-file"></a>Arquivo de modelo

Ver o [ficheiro de modelo no hub git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate).

### <a name="parameter-file"></a>Arquivo de parâmetros

Veja o [ficheiro do parâmetro no hub git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json).


## <a name="create-an-alert-rule-for-sql-insights"></a>Criar uma regra de alerta para insights SQL
A amostra a seguir cria uma regra de alerta que cobrirá os recursos SQL no âmbito do perfil de monitorização especificado.  Esta regra de alerta aparecerá no SQL insights UI no painel de contexto de UI alerta.  

O ficheiro de parâmetros tem valores a partir de um dos modelos de alerta que fornecemos em insights SQL, pode modificá-lo para alertar outros dados que recolhemos para SQL.  O modelo não especifica um grupo de ação para a regra de alerta.


#### <a name="template-file"></a>Arquivo de modelo

Ver o [ficheiro de modelo no hub git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate).

### <a name="parameter-file"></a>Arquivo de parâmetros

Veja o [ficheiro do parâmetro no hub git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json).





## <a name="next-steps"></a>Passos seguintes

* [Obtenha outros modelos de amostra para Azure Monitor](../resource-manager-samples.md).
* [Saiba mais sobre insights SQL](sql-insights-overview.md).
