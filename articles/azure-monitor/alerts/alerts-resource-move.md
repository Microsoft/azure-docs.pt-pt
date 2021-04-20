---
title: Como atualizar regras de alerta ou regras de ação quando o seu recurso-alvo se desloca para uma região de Azure diferente
description: Antecedentes e instruções para como atualizar regras de alerta ou regras de ação quando o seu recurso-alvo se desloca para uma região de Azure diferente.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: 727196f274db3abae75a38d3ecdf31a78dec0fab
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725949"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Como atualizar regras de alerta ou regras de ação quando o seu recurso-alvo se desloca para uma região de Azure diferente

Este artigo descreve por que razão [as regras](./alerts-overview.md) de alerta existentes e [as regras de ação](./alerts-action-rules.md) podem ser impactadas quando se movem outros recursos Azure entre regiões e como identificar e resolver essas questões. Consulte a [documentação](../../azure-resource-manager/management/move-resources-overview.md) principal do movimento de recursos para obter informações adicionais sobre quando é que o movimento de recursos entre as regiões é útil e uma lista de verificação de um processo de movimento.

## <a name="why-the-problem-exists"></a>Por que o problema existe

Regras de alerta e regras de ação referem outros recursos da Azure. Exemplos incluem [Azure VMs,](../../site-recovery/azure-to-azure-tutorial-migrate.md) [Azure SQL,](../../azure-sql/database/move-resources-across-regions.md)e [Azure Storage](../../storage/common/storage-account-move.md). Quando se movem os recursos a que essas regras se referem, é provável que as regras deixem de funcionar corretamente porque não conseguem encontrar os recursos a que se referem.

Há duas razões principais pelas quais as suas regras podem deixar de funcionar depois de mover os recursos-alvo:

- O âmbito da sua regra é explicitamente remeter o recurso antigo.
- A sua regra de alerta baseia-se em métricas.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>O âmbito de regras refere-se explicitamente ao recurso antigo

Quando move um recurso, o seu ID de recursos muda na maioria dos casos. Nos bastidores, o sistema replica o recurso na nova região antes de o eliminar da região antiga. Este processo requer que existam dois recursos e, portanto, dois IDs de recursos diferentes existem simultaneamente por um pequeno período de tempo. Uma vez que os IDs de recursos devem ser únicos, um novo ID deve ser criado durante o processo. 

**Como é que a movimentação do recurso afeta as regras existentes?**

As regras de alerta e as regras de ação têm um âmbito de recursos a que se aplicam. O âmbito pode ser uma subscrição completa, um grupo de recursos, ou um ou mais recursos específicos.
Por exemplo, aqui está uma regra com um âmbito com dois recursos (duas máquinas virtuais):

![Regra de alerta de vários recursos](media/alerts-resource-move/multi-resource-alert-rule.png)

Se o âmbito de regra mencionar explicitamente um recurso, e esse recurso tiver mudado e alterado o seu ID de recursos, então essa regra procurará um recurso errado ou inexistente e, portanto, falhará.

**Como resolver o problema?**

Atualize ou recrie a regra afetada para apontar para o novo recurso. O processo de atualização do âmbito encontra-se mais tarde neste artigo.

O problema aplica-se a estes tipos de regras:

- Regras de alerta de registo de atividade
- Regras de ação
- Alertas métricos – Para mais informações, consulte as [próximas regras de alerta com base em métricas](#alert-rules-based-on-metrics).

> [!NOTE]
> As regras de alerta de pesquisa de registo e as regras de alerta de detetores inteligentes não são afetadas porque o seu âmbito de aplicação é um espaço de trabalho ou Insights de Aplicação. Nenhum destes âmbitos apoia atualmente os movimentos da região.

## <a name="alert-rules-based-on-metrics"></a>Regras de alerta baseadas em métricas

As métricas que os recursos da Azure emitem são regionais. Sempre que um recurso se desloca para uma nova região, começa a emitir as suas métricas naquela nova região. Como resultado, quaisquer regras de alerta baseadas em métricas precisam de ser atualizadas ou recriadas para que apedentem o fluxo métrico atual na região correta.

Esta explicação aplica-se tanto às [regras de alerta métrico](alerts-metric-overview.md) como [às regras de alerta de teste de disponibilidade](../app/monitor-web-app-availability.md).

Se **todos os** recursos no âmbito se moveram, não precisa recriar a regra. Pode apenas atualizar qualquer campo da regra de alerta, como a descrição da regra de alerta, e guardá-la.
Se **apenas alguns** dos recursos no âmbito se deslocaram, é necessário retirar os recursos movidos da regra existente e criar uma nova regra que cubra apenas os recursos movidos.

## <a name="procedures-to-fix-problems"></a>Procedimentos para corrigir problemas

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Identificar regras associadas a um recurso movido a partir do portal Azure

- **Para regras de alerta** - Navegue para Alertas > Gerir as regras de alerta > filtrar pela subscrição contendo e pelo recurso movido.
> [!NOTE]
> As regras de alerta de registo de atividades não suportam este processo. Não é possível atualizar o âmbito de uma regra de alerta de registo de atividade e fazê-la apontar para um recurso em outra subscrição. Em vez disso, pode criar uma nova regra que substituirá a antiga.

- **Para regras de ação** - Navegue para alertas > Gerir as ações > Regras de Ação (pré-visualização) > filtro pela subscrição contendo e pelo recurso movido.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Alterar o âmbito de uma regra a partir do portal Azure

1. Abra a regra que identificou no passo anterior clicando nela.
2. Em **Recurso,** clique em **Editar** e ajuste o âmbito, conforme necessário.
3. Ajuste outras propriedades da regra conforme necessário.
4. Clique em **Guardar**.

![Alterar o âmbito da regra de alerta](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Alterar o âmbito de uma regra utilizando modelos de Gestor de Recursos Azure

1. Obtenha o modelo de Gestor de Recursos Azure da regra.   Para exportar o modelo de uma regra a partir do portal Azure:
   1. Navegue na secção Grupos de Recursos no portal e abra o grupo de recursos que contém a regra.
   2. Na secção Visão Geral, verifique a caixa de verificação **do tipo oculta Show** e filtre pelo tipo relevante da regra.
   3. Selecione a regra relevante para ver os seus detalhes.
   4. Em **Definições**, selecione **Modelo de exportação**.
2. Modifique o modelo. Se necessário, divida em duas regras (relevantes para alguns casos de alertas métricos, como referido acima).
3. Reimplantar o modelo.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Alterar o âmbito de uma regra utilizando a API REST

1. Obtenha a regra existente[(alertas métricos,](/rest/api/monitor/metricalerts/get) [alertas de registo de atividade)](/rest/api/monitor/activitylogalerts/get)
2. Modificar o âmbito[(alertas de registo de atividade)](/rest/api/monitor/activitylogalerts/update)
3. Reimplantar a regra[(alertas métricos,](/rest/api/monitor/metricalerts/createorupdate) [alertas de registo de atividade)](/rest/api/monitor/activitylogalerts/createorupdate)

### <a name="change-scope-of-a-rule-using-powershell"></a>Alterar o âmbito de uma regra utilizando o PowerShell

1. Obtenha a regra existente ([alertas métricos, alertas](/powershell/module/az.monitor/get-azmetricalertrulev2) [de registo de atividade, regras](/powershell/module/az.monitor/get-azactivitylogalert)de [ação).](/powershell/module/az.alertsmanagement/get-azactionrule)
2. Modifique o âmbito. Se necessário, divida em duas regras (relevantes para alguns casos de alertas métricos, como referido acima).
3. Reimplantar a regra ([alertas métricos, alertas](/powershell/module/az.monitor/add-azmetricalertrulev2) [de registo de atividade, regras](/powershell/module/az.monitor/enable-azactivitylogalert)de [ação).](/powershell/module/az.alertsmanagement/set-azactionrule)

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Alterar o âmbito de uma regra utilizando o Azure CLI

1.  Obtenha a regra existente[(alertas métricos, alertas](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-show) [de registo de atividade).](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)
2.  Atualizar o âmbito de regras diretamente[(alertas métricos,](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update) [alertas de registo de atividade)](/cli/azure/monitor/activity-log/alert/scope)
3.  Se necessário, divida em duas regras (relevantes para alguns casos de alertas métricos, como referido acima).

## <a name="next-steps"></a>Passos seguintes

Saiba a resolução de outros problemas com [notificações de alerta,](alerts-troubleshoot.md) [alertas métricos](alerts-troubleshoot-metric.md)e alertas de [registo.](alerts-troubleshoot-log.md)