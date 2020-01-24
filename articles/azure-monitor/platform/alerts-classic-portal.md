---
title: Criar, visualizar e gerir alertas métricos clássicos usando o Monitor Azure
description: Aprenda a usar o portal Azure, CLI ou Powershell para criar, visualizar e gerir regras clássicas de alerta métrico.
author: yanivlavi
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: yalavi
ms.openlocfilehash: fd15d6bb9442d91c672fdd444313880001880e07
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705603"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Criar, visualizar e gerir alertas métricos clássicos usando o Monitor Azure

Os alertas métricos clássicos no Monitor Azure fornecem uma forma de ser notificado quando uma das suas métricas atravessa um limiar. Os alertas métricos clássicos são uma funcionalidade mais antiga que permite alertar apenas em métricas não dimensionais. Existe uma funcionalidade mais recente chamada alertas Métricos que melhorou a funcionalidade em relação a alertas métricos clássicos. Pode saber mais sobre a nova funcionalidade de alertas métricos na visão geral dos [alertas métricos](../../azure-monitor/platform/alerts-metric-overview.md). Neste artigo, descreveremos como criar, visualizar e gerir regras clássicas de alerta métrico através do portal Azure, Azure CLI e Powershell.

## <a name="with-azure-portal"></a>Com portal Azure

1. No [portal,](https://portal.azure.com/)localize o recurso que pretende monitorizar e, em seguida, selecione-o.

2. Na secção **MONITOR,** selecione **Alertas (Clássico)** . O texto e o ícone podem variar ligeiramente para diferentes recursos. Se não encontrar **alertas (clássicos)** aqui, poderá encontrá-lo em **Alertas** ou Regras de **Alerta**.

    ![Monitorização](media/alerts-classic-portal/AlertRulesButton.png)

3. Selecione o comando **de alerta métrico Adicionar (clássico)** e, em seguida, preencha os campos.

    ![Adicionar alerta](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Diga** o seu estado de alerta. Em seguida, escolha uma **Descrição**, que também aparece nos e-mails de notificação.

5. Selecione a **Métrica** que pretende monitorizar. Em seguida, escolha uma **Condição** e valor **limiar** para a métrica. Escolha também o **período** de tempo que a regra de métrica deve ser satisfeita antes que o alerta seja disparado. Por exemplo, se utilizar o período "Nos últimos 5 minutos" e o seu alerta procurar um CPU acima de 80%, o alerta dispara quando o CPU tem sido consistentemente superior a 80% durante 5 minutos. Após o primeiro gatilho ocorrer, volta a disparar quando o CPU permanece abaixo dos 80% durante 5 minutos. A medição métrica do CPU acontece a cada minuto.

6. Selecione os proprietários de **e-mail...** se quiser que administradores e coadministradores recebam notificações de e-mail quando o alerta disparar.

7. Se pretender enviar notificações para endereços de e-mail adicionais quando o alerta disparar, adicione-os no campo **de e-mail do Administrador Adicional.** Separe vários e-mails com pontos evívias, no seguinte formato: *e-mail\@contoso.com;email2\@contoso.com*

8. Coloque um URI válido no campo **Webhook** se quiser que seja chamado quando o alerta disparar.

9. Se utilizar a Automação Azure, pode selecionar um livro de execução a ser executado quando o alerta disparar.

10. Selecione **OK** para criar o alerta.

Em alguns minutos, o alerta está ativo e é disparado conforme descrito anteriormente.

Depois de criar um alerta, pode selecioná-lo e fazer uma das seguintes tarefas:

* Veja um gráfico que mostre o limiar métrico e os valores reais do dia anterior.
* Edite ou exclua-o.
* **Desabilite** ou **habilite** -o se quiser interromper temporariamente ou retomar as notificações para esse alerta.

## <a name="with-azure-cli"></a>Com a CLI do Azure

As secções anteriores descreveram como criar, visualizar e gerir regras de alerta métrica usando o portal Azure. Esta secção descreverá como fazer o mesmo utilizando o [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)da plataforma transversal . A forma mais rápida de começar a usar o Azure CLI é através da [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Obtenha todas as regras clássicas de alerta métrico em um grupo de recursos

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Veja detalhes de uma regra de alerta métrica clássica

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Criar uma regra clássica de alerta métrico

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Eliminar uma regra clássica de alerta métrico

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Estas secções mostram como usar comandos PowerShell criar, visualizar e gerir alertas métricos clássicos. Os exemplos do artigo ilustram como pode utilizar cmdlets Do Monitor Azure para alertas métricos clássicos.

1. Se ainda não o fez, instale o PowerShell para executar no seu computador. Para mais informações, consulte [Como Instalar e Configurar powerShell](/powershell/azure/overview). Também pode rever toda a lista de cmdlets De PowerShell do Monitor Azure no [Azure Monitor (Insights) Cmdlets](https://docs.microsoft.com/powershell/module/az.applicationinsights).

2. Primeiro, inicie sessão na sua assinatura Azure.

    ```powershell
    Connect-AzAccount
    ```

3. Verá uma placa no ecrã. Uma vez que você instituna sua Conta, TenantID e ID de subscrição padrão são apresentados. Todos os cmdlets Azure funcionam no contexto da sua subscrição por defeito. Para ver a lista de subscrições a que tem acesso, utilize o seguinte comando:

    ```powershell
    Get-AzSubscription
    ```

4. Para alterar o seu contexto de trabalho para uma subscrição diferente, utilize o seguinte comando:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Você pode recuperar todas as regras clássicas de alerta métrico em um grupo de recursos:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Pode ver detalhes de uma regra clássica de alerta métrico

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Pode recuperar todas as regras de alerta definidas para um recurso-alvo. Por exemplo, todas as regras de alerta estabelecidas num VM.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. As regras clássicas de alerta já não podem ser criadas através da PowerShell. Para criar uma regra de alerta, é necessário utilizar o novo comando ['Add-AzMetricAlertRule'.](/powershell/module/az.monitor/add-azmetricalertrule)

## <a name="next-steps"></a>Passos seguintes

- [Crie um alerta métrico clássico com um modelo de Gestor de Recursos](../../azure-monitor/platform/alerts-enable-template.md).
- [Tenha um alerta métrico clássico que notifique um sistema não Azure usando um webhook](../../azure-monitor/platform/alerts-webhooks.md).
