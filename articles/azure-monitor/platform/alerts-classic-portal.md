---
title: Criar e gerir alertas métricos clássicos usando o Azure Monitor
description: Saiba como usar o portal Azure, CLI ou Powershell para criar, ver e gerir regras clássicas de alerta métrico.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/18/2018
ms.openlocfilehash: 8b4cdd834c0db6869fdf804b43914eaa74b14319
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525556"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Criar, ver e gerir alertas métricos clássicos usando o Azure Monitor

> [!WARNING]
> Este artigo descreve como criar alertas métricos clássicos mais antigos. O Azure Monitor suporta agora [novos alertas métricos em tempo real e uma nova experiência de alertas.](./alerts-overview.md) Os alertas clássicos são [retirados](./monitoring-classic-retirement.md) para os utilizadores da nuvem pública, embora ainda em uso limitado para recursos que ainda não suportam os novos alertas.
>

Os alertas métricos clássicos no Azure Monitor fornecem uma forma de ser notificado quando uma das suas métricas cruza um limiar. Os alertas métricos clássicos são uma funcionalidade mais antiga que permite alertar apenas em métricas não dimensionais. Existe uma funcionalidade mais recente existente chamada alertas métricos que tem melhorado a funcionalidade em relação aos alertas métricos clássicos. Pode saber mais sobre a nova funcionalidade de alertas métricos na [visão geral dos alertas métricos.](./alerts-metric-overview.md) Neste artigo, descreveremos como criar, ver e gerir regras clássicas de alerta métrico através do portal Azure, Azure CLI e Powershell.

## <a name="with-azure-portal"></a>Com portal Azure

1. No [portal,](https://portal.azure.com/)localize o recurso que pretende monitorizar e, em seguida, selecione-o.

2. Na secção **MONITOR,** selecione **Alertas (Clássicos)**. O texto e o ícone podem variar ligeiramente para diferentes recursos. Se não encontrar **alertas (clássicos)** aqui, poderá encontrá-lo em **Alertas** ou **Regras de Alerta.**

    ![Monitorização](media/alerts-classic-portal/AlertRulesButton.png)

3. Selecione o comando **de alerta métrico Add (clássico)** e, em seguida, preencha os campos.

    ![Adicionar Alerta](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Diga** a sua regra de alerta. Em seguida, escolha uma **Descrição,** que também aparece nos e-mails de notificação.

5. Selecione a **Métrica** que pretende monitorizar. Em seguida, escolha um valor **de Condição** e **Limiar** para a métrica. Escolha também o **período** de tempo que a regra métrica deve ser satisfeita antes do alerta disparar. Por exemplo, se utilizar o período "Nos últimos 5 minutos" e o seu alerta procurar um CPU acima de 80%, o alerta dispara quando o CPU esteve consistentemente acima dos 80% durante 5 minutos. Após o primeiro gatilho, volta a disparar quando o CPU permanece abaixo dos 80% durante 5 minutos. A medição métrica do CPU acontece a cada minuto.

6. Selecione **os proprietários de e-mail...** se quiser que administradores e coadministradores recebam notificações de e-mail quando o alerta disparar.

7. Se pretender enviar notificações para endereços de e-mail adicionais quando o alerta disparar, adicione-as no campo **de e-mails do Administrador Adicional.** Separe vários e-mails com semi-ecolons, no seguinte formato: *email \@ contoso.com;email2 \@ contoso.com*

8. Coloque um URI válido no campo **Webhook** se quiser que seja chamado quando o alerta disparar.

9. Se utilizar a Azure Automation, pode selecionar um livro de execução a ser executado quando o alerta disparar.

10. Selecione **OK** para criar o alerta.

Em poucos minutos, o alerta está ativo e dispara como descrito anteriormente.

Depois de criar um alerta, pode selecioná-lo e fazer uma das seguintes tarefas:

* Veja um gráfico que mostre o limiar métrico e os valores reais do dia anterior.
* Editar ou apagar.
* **Desative** ou **ative-o** se pretender parar ou retomar temporariamente as notificações para esse alerta.

## <a name="with-azure-cli"></a>Com a CLI do Azure

As secções anteriores descreveram como criar, visualizar e gerir regras de alerta métricas utilizando o portal Azure. Esta secção descreverá como fazer o mesmo utilizando o [Azure CLI](/cli/azure/get-started-with-azure-cli)da plataforma transversal . A forma mais rápida de começar a usar o Azure CLI é através da [Azure Cloud Shell](../../cloud-shell/overview.md).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Obtenha todas as regras clássicas de alerta métrico em um grupo de recursos

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Consulte detalhes de uma regra de alerta métrico clássico

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

Estas secções mostram como usar os comandos PowerShell criam, vêem e gerem alertas métricos clássicos. Os exemplos do artigo ilustram como pode utilizar cmdlets Azure Monitor para alertas métricos clássicos.

1. Se ainda não o fez, configura o PowerShell para executar o seu computador. Para obter mais informações, consulte [Como Instalar e Configurar o PowerShell](/powershell/azure/). Também pode rever toda a lista de cmdlets Azure Monitor PowerShell no [Azure Monitor (Insights) Cmdlets](/powershell/module/az.applicationinsights).

2. Primeiro, faça login na sua assinatura Azure.

    ```powershell
    Connect-AzAccount
    ```

3. Verá um sinal no ecrã. Assim que assinar na sua Conta, o TenantID e o ID de subscrição predefinido são apresentados. Todos os cmdlets Azure funcionam no contexto da sua subscrição padrão. Para ver a lista de subscrições a que tem acesso, utilize o seguinte comando:

    ```powershell
    Get-AzSubscription
    ```

4. Para alterar o seu contexto de trabalho para uma subscrição diferente, utilize o seguinte comando:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Pode recuperar todas as regras clássicas de alerta métrico num grupo de recursos:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Você pode ver detalhes de uma regra de alerta métrico clássico

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Pode recuperar todas as regras de alerta definidas para um recurso-alvo. Por exemplo, todas as regras de alerta definidas num VM.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. As regras clássicas de alerta já não podem ser criadas através do PowerShell. Para criar uma regra de alerta, é necessário utilizar o novo comando ['Add-AzMetricAlertRule'.](/powershell/module/az.monitor/add-azmetricalertrule)

## <a name="next-steps"></a>Passos seguintes

- [Crie um alerta métrico clássico com um modelo de Gestor de Recursos.](./alerts-enable-template.md)
- [Tenha um alerta métrico clássico que notifique um sistema não-Azure utilizando um webhook](./alerts-webhooks.md).

