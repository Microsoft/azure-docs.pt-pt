---
title: Ver registos de atividades do Azure para monitorizar os recursos | Documentos da Microsoft
description: Utilize os registos de atividades para ações de utilizador de revisão e erros. Mostra o Azure PowerShell Portal, da CLI do Azure e REST.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: tomfitz
ms.openlocfilehash: 8348099d778a9ec65e907bb3d21ae995041b9fb6
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802292"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Ver registos de atividades para auditar as ações em recursos

Através dos registos de atividades, pode determinar:

* as operações que foram executadas nos recursos na sua subscrição
* quem iniciou a operação
* Quando ocorreu a operação
* O estado da operação
* Os valores de outras propriedades que podem ajudar a operação de pesquisa

O registo de atividade contém todas as operações de escrita (PUT, POST, DELETE) efetuadas nos seus recursos. Ele não inclui as operações de leitura (GET). Para obter uma lista de ações de recursos, consulte [operações de fornecedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md). Pode utilizar os registos de auditoria para encontrar um erro ao resolver problemas ou para monitorizar a forma como um utilizador na sua organização alterou um recurso.

Registos de Atividades são mantidos durante 90 dias. Pode consultar qualquer intervalo de datas, desde que a data de início não está mais de 90 dias no passado.

Pode recuperar informações de registos de atividade através do portal, PowerShell, CLI do Azure, API de REST de informações, ou [biblioteca de .NET de Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="the-azure-portal"></a>O portal do Azure

1. Para ver os registos de atividade através do portal, selecione **Monitor**.

    ![Selecione monitor](./media/resource-group-audit/select-monitor.png)

1. Selecione **registo de atividades**.

    ![Selecione o registo de atividades](./media/resource-group-audit/select-activity-log.png)

1. Verá um resumo das operações recentes. Um conjunto predefinido de filtros é aplicado às operações.

    ![Ver resumo de operações recentes](./media/resource-group-audit/audit-summary.png)

1. Para executar rapidamente um conjunto predefinido de filtros, selecione **informações rápidas** e escolha uma das opções.

    ![Selecione a consulta](./media/resource-group-audit/quick-insights.png)

1. Para se concentrar em operações específicas, alterar os filtros ou aplicar novas etiquetas. Por exemplo, a imagem seguinte mostra um novo valor para o **Timespan** e **tipo de recurso** está definido como contas de armazenamento. 

    ![Opções de filtro de conjunto](./media/resource-group-audit/set-filter.png)

1. Se tiver de executar a consulta novamente mais tarde, selecione **filtros atuais do Pin**.

    ![Filtros de PIN](./media/resource-group-audit/pin-filters.png)

1. Dê um nome de filtro.

    ![Filtros de nome](./media/resource-group-audit/name-filters.png)

1. O filtro está disponível no dashboard.

    ![Mostrar o filtro de dashboard](./media/resource-group-audit/show-dashboard.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Para obter as entradas de registo, execute o **Get-AzLog** comando. Fornecer parâmetros adicionais para filtrar a lista de entradas. Se não especificar uma hora de início e fim, são devolvidas entradas dos últimos sete dias.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup
  ```

    O exemplo seguinte mostra como utilizar o registo de atividade para operações de pesquisa executadas durante um período de tempo especificado. As datas de início e de fim são especificadas num formato de data.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-01-09T06:00 -EndTime 2019-01-15T06:00
  ```

    Em alternativa, pode utilizar funções de data para especificar o intervalo de datas, tais como os últimos 14 dias.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

* Pode consultar as ações executadas por um utilizador em particular, mesmo para um grupo de recursos que já não existe.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

* Pode filtrar operações falhadas.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -Status Failed
  ```

* Que possa dedicar a um erro ao observar a mensagem de estado para essa entrada.

  ```azurepowershell-interactive
  ((Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties[0].Content.statusMessage | ConvertFrom-Json).error
  ```

* Pode selecionar valores específicos para limitar os dados que são devolvidos.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
  ```

* Consoante a hora de início que especificar, os comandos anteriores podem devolver uma longa lista de operações para o grupo de recursos. Pode filtrar os resultados para o que está procurando, fornecendo os critérios de pesquisa. Por exemplo, pode filtrar pelo tipo de operação.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
  ```

## <a name="azure-cli"></a>CLI do Azure

* Para obter as entradas de registo, execute o [lista de registo de atividade do monitor de az](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) comando com um desvio para indicar o intervalo de tempo.

  ```azurecli-interactive
  az monitor activity-log list --resource-group ExampleGroup --offset 7d
  ```

  O exemplo seguinte mostra como utilizar o registo de atividade para operações de pesquisa executadas durante um período de tempo especificado. As datas de início e de fim são especificadas num formato de data.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --start-time 2019-01-01 --end-time 2019-01-15
  ```

* Pode consultar as ações executadas por um utilizador em particular, mesmo para um grupo de recursos que já não existe.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
  ```

* Pode filtrar operações falhadas.

  ```azurecli-interactive
  az monitor activity-log list -g demoRG --status Failed --offset 1d
  ```

* Que possa dedicar a um erro ao observar a mensagem de estado para essa entrada.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
  ```

* Pode selecionar valores específicos para limitar os dados que são devolvidos.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
  ```

* Consoante a hora de início que especificar, os comandos anteriores podem devolver uma longa lista de operações para o grupo de recursos. Pode filtrar os resultados para o que está procurando, fornecendo os critérios de pesquisa. Por exemplo, pode filtrar pelo tipo de operação.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
  ```

## <a name="rest-api"></a>API REST

As operações REST para trabalhar com o registo de atividade fazem parte dos [API do REST Insights](/rest/api/monitor/). Para obter eventos de registo de atividade, veja [listar os eventos de gestão numa subscrição](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Passos Seguintes

* Registos de atividades do Azure podem ser utilizados com o Power BI para obter mais informações sobre as ações na sua subscrição. Ver [ver e analisar registos de atividades do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Para saber mais sobre como definir políticas de segurança, veja [controlo de acesso baseado em funções do Azure](../role-based-access-control/role-assignments-portal.md).
* Para saber mais sobre os comandos para ver as operações de implementação, consulte [ver as operações de implementação](resource-manager-deployment-operations.md).
* Para saber como impedir eliminações num recurso para todos os utilizadores, veja [bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).
* Para ver a lista de operações disponíveis para cada fornecedor do Microsoft Azure Resource Manager, consulte [operações de fornecedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md)
