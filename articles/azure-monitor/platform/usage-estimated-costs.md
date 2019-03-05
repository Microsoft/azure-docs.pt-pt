---
title: Monitorizar a utilização e custos estimados no Azure Monitor
description: Descrição geral do processo de uso de utilização do Azure Monitor e a página de custos estimados
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 1ae35c30e0379ed7a0f1fac16c279651e3bcd8fd
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315882"
---
# <a name="monitoring-usage-and-estimated-costs"></a>Monitorizar a utilização e custos estimados

> [!NOTE]
> Este artigo descreve como ver a utilização e custos estimados entre várias funcionalidades de monitorização do Azure para diferentes modelos de preços.  Veja os artigos seguintes para obter informações relacionadas.
> - [Gerir os custos ao controlar o volume de dados e a retenção do Log Analytics](../../azure-monitor/platform/manage-cost-storage.md) descreve como controlar os custos ao alterar o período de retenção de dados.
> - [Analisar a utilização de dados do Log Analytics](../../azure-monitor/platform/data-usage.md) descreve como analisar e alerta sobre a utilização de dados.
> - [Gerir preços e volumes de dados no Application Insights](../../azure-monitor/app/pricing.md) descreve como analisar a utilização de dados do Application Insights.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

No hub do Monitor do portal do Azure, o **utilização e custos estimados** página explica a utilização das principais funcionalidades de monitorização, tal como [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/), e [Application Insights do Azure](https://azure.microsoft.com/pricing/details/application-insights/). Para os clientes sobre os planos de preços disponíveis antes de Abril de 2018, isso também inclui a utilização do Log Analytics adquirida por meio das informações e análise da oferta.

Nesta página, os utilizadores podem ver a utilização de recursos nos últimos 31 dias, agregados por cada subscrição. Inícios de exploração de mostram tendências de utilização ao longo do período de 31 dias. Uma grande quantidade de dados tem de se reúnem para esta estimativa, portanto, seja paciente como a página for carregada.

Este exemplo mostra a utilização de monitorização e uma estimativa dos custos resultantes:

![Utilização e custos estimados portal captura de ecrã](./media/usage-estimated-costs/001.png)

Selecione a ligação na coluna mensal de utilização para abrir um gráfico que mostra as tendências de utilização ao longo do último período de 31 dias:

![Incluído por nó de barra captura de ecrã do gráfico](./media/usage-estimated-costs/002.png)

Eis outro uso semelhante e o resumo do custo. Este exemplo mostra uma subscrição no novo Abril de 2018 consumo baseado no modelo de preços. Tenha em atenção a falta de qualquer faturação baseada no nó. Ingestão de dados e o período de retenção para o Log Analytics e Application Insights agora são comunicados num novo medidor comuns.

![Utilização e custos estimados captura de ecrã portal - preços de Abril de 2018](./media/usage-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Novo modelo de preços

Em Abril de 2018, um [monitorização novo modelo de preços foi lançado](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Isso oferece preços de nuvem amigável e baseado no consumo. Paga apenas aquilo que utiliza, sem compromissos com base no nó. Estão disponíveis detalhes do novo modelo de preços para [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Para a integração de clientes para o Log Analytics ou o Application Insights depois de 2 de Abril de 2018, o novo modelo de preços é a única opção. Para os clientes que já utilizam estes serviços, mudar para o novo modelo de preços é opcional.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Avaliar o impacto do novo modelo de preços
O novo modelo de preços terão diferentes impactos sobre cada cliente com base nos seus padrões de utilização de monitorização. Para os clientes que estavam a utilizar o Log Analytics ou o Application Insights antes de 2 de Abril de 2018, o **utilização e custo estimado** qualquer alteração nos custos as estimativas de página no Azure Monitor se eles mover para o novo modelo de preços. Ele fornece a forma de mover uma subscrição para o novo modelo. Para a maioria dos clientes, o novo modelo de preços será vantajoso. Para os clientes com os padrões de utilização de dados especialmente elevado ou em regiões de custo mais elevado, isso pode não ser o caso.

Para ver uma estimativa dos seus custos para as subscrições que escolheu no **utilização e custos estimados** página, selecione a faixa azul junto à parte superior da página. É melhor fazer esta um subscrição por vez, uma vez que é o nível em que o novo modelo de preços pode ser adotado.

![Monitorizar a utilização e custos estimados no novo preço captura de ecrã do modelo](./media/usage-estimated-costs/004.png)

A nova página mostra uma versão semelhante da página anterior com uma faixa verde:

![Monitorizar a utilização e custos estimados no atual preços captura de ecrã do modelo](./media/usage-estimated-costs/005.png)

A página também mostra um conjunto diferente de medidores que correspondem ao novo modelo de preços. Esta lista é um exemplo:

- Informações e Analytics\Overage por nó
- Informações e Analytics\Included por nó
- Dados de utilização excedida de Insights\Basic de aplicação
- Dados da aplicação Insights\Included

O novo modelo de preços não tem as alocações de dados incluídos com base no nó. Por conseguinte, estes medidores de ingestão de dados são combinados num novo medidor de ingestão dados comuns denominado **partilhados ingestão de Services\Data**. 

Há outra alteração aos dados ingeridos no Log Analytics ou o Application Insights nas regiões com custos superiores. Dados para estas regiões de alto custo serão mostrados com os novos medidores regionais. Um exemplo é **ingestão de dados (e.u.a. Centro-Oeste)**.

> [!NOTE]
> O por subscrição estimado custos não fatorar em nível da conta por elegibilidades de nó da subscrição do Operations Management Suite (OMS). Neste caso, consulte seu representante de conta para uma discussão mais aprofundada sobre o novo modelo de preços.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Os novos preços modelo e elegibilidades da subscrição do Operations Management Suite

Os clientes que compraram o Microsoft Operations Management Suite E1 e E2 são elegíveis para elegibilidade de ingestão de dados por nó para [do Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) e [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Para receber esta elegibilidade para áreas de trabalho do Log Analytics ou recursos do Application Insights numa determinada subscrição: 

- Modelo de preços da subscrição tem de permanecer no modelo de pré-Abril de 2018.
- Áreas de trabalho do log Analytics, devem utilizar o "por nó (OMS)" escalão de preço.
- Recursos do Application Insights devem utilizar o plano de preços "Enterprise".

Dependendo do número de nós do conjunto de aplicações adquirido pela sua organização, mover algumas subscrições para o novo modelo de preços podem ser vantajosos, mas isso requer consideração cuidadosa. Em geral, é aconselhável simplesmente para manter o modelo de pré-Abril de 2018, tal como descrito acima.

> [!WARNING]
> Se a sua organização tiver comprado o Microsoft Operations Management Suite E1 e E2, seja normalmente melhor manter as suas subscrições no modelo de preços do pre-Abril de 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Alterações quando estiver a mover para o novo modelo de preços

O novo modelo de preços simplifica o Log Analytics e as opções para apenas uma única camada (ou plano) de preços do Application Insights. Mover uma subscrição para o novo modelo será de preços:

- Alterar o escalão de preço para cada Log Analytics para o novo escalão por GB (chamado "pergb2018" no Azure Resource Manager)
- Quaisquer recursos do Application Insights no plano do Enterprise é alterado para o plano básico.

A estimativa de custos mostra os efeitos dessas alterações.

> [!WARNING]
> Aqui uma observação importante se utilizar o Azure Resource Manager ou o PowerShell para implementar [do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) ou [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) numa subscrição foram movidas para o novo modelo de preços. Se especificar um escalão/plano de preços que não seja "pergb2018" para o Log Analytics ou "Básico" para o Application Insights, em vez a falhar a implementação devido à especificação de um inválido/plano, de escalão de preço conseguirá **mas irá utilizar apenas o válido escalão/plano de preços** (isto não é aplicável para o escalão gratuito de análise do registo em que é gerada uma mensagem de escalão de preço inválida).
>

## <a name="moving-to-the-new-pricing-model"></a>Mover para o novo modelo de preços

Se tiver decidido a adotar o novo modelo de preços para obter uma subscrição, selecione o **seleção de modelo de preços** opção na parte superior a **utilização e custos estimados** página:

![Monitorizar a utilização e custos estimados no novo preço captura de ecrã do modelo](./media/usage-estimated-costs/006.png)

O **seleção de modelo de preços** será aberta a página. Mostra uma lista de cada uma das subscrições que exibida na página anterior:

![Captura de ecrã de seleção de modelo de preços](./media/usage-estimated-costs/007.png)

Para mover uma subscrição para o novo modelo de preços, basta selecionar a caixa e, em seguida, selecione **guardar**. Pode mover novamente para o modelo de preços mais antigo da mesma forma. Tenha em mente que esse proprietário da subscrição ou são necessárias permissões de Contribuidor para alterar o modelo de preços.

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatizar a migração para o novo modelo de preços

Os scripts abaixo exigem o módulo do PowerShell do Azure. Para verificar se tem a versão mais recente, consulte [módulo de instalar o Azure PowerShell](/powershell/azure/install-az-ps).

Assim que tiver a versão mais recente do Azure PowerShell, primeiro terá de executar ``Connect-AzAccount``.

``` PowerShell
# To check if your subscription is eligible to adjust pricing models.
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

Um resultado True em isGrandFatherableSubscription indica que o modelo de preços desta subscrição pode ser movido entre modelos de preços. A falta de um valor em optedInDate significa que esta subscrição está atualmente configurada para o modelo de preços antigo.

```
isGrandFatherableSubscription optedInDate
----------------------------- -----------
                         True            
```

Para migrar esta subscrição para o novo modelo de preços execute:

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
```

Para confirmar que a alteração foi efetuada com êxito volte a executar:

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

Se a migração foi concluída com êxito, o resultado deverá agora ser semelhante:

```
isGrandFatherableSubscription optedInDate                      
----------------------------- -----------                      
                         True 2018-05-31T13:52:43.3592081+00:00
```

O optInDate agora contém um carimbo de quando esta subscrição selecionada para o novo modelo de preços.

Se precisar de reverter para o modelo de preços antigo, deve executar:

```PowerShell
 $ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action rollbacktolegacypricingmodel `
 -Force
```

Se, em seguida, execute novamente o script anterior que tenha ``-Action listmigrationdate``, deverá ver um valor de optedInDate vazia que indica a sua subscrição foi devolvida para o modelo de preços legado.

Se tiver várias subscrições, o que deseja migrar que estão alojados no mesmo inquilino, pode criar seu próprio variante usando partes dos seguintes scripts:

```PowerShell
#Query tenant and create an array comprised of all of your tenants subscription ids
$TenantId = <Your-tenant-id>
$Tenant =Get-AzSubscription -TenantId $TenantId
$Subscriptions = $Tenant.Id
```

Para verificar se todas as subscrições no seu inquilino são elegíveis para o novo modelo de preços, pode executar:

```PowerShell
Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
}
```

O script poderia ser refinado adicional através da criação de um script que gera três matrizes. Uma matriz consistirá em todos os id de subscrição que ter ```isGrandFatherableSubscription``` definida como True e optedInDate não tem atualmente um valor. Uma segunda matriz de quaisquer subscrições atualmente no novo modelo de preços. E uma matriz de terceiro preenchida apenas com ids de subscrição no seu inquilino, que não são elegíveis para o novo modelo de preços:

```PowerShell
[System.Collections.ArrayList]$Eligible= @{}
[System.Collections.ArrayList]$NewPricingEnabled = @{}
[System.Collections.ArrayList]$NotEligible = @{}

Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
$Result= Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force

     if ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $False)
     {
     $Eligible.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $True)
     {
     $NewPricingEnabled.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $False)
     {
     $NotEligible.add($id)
     }
}
```

> [!NOTE]
> Dependendo do número de subscrições, o script acima pode demorar algum tempo para ser executado. Devido ao uso do método .add() a janela do PowerShell irá retornar valores incrementação que itens são adicionados a cada matriz.

Agora que tem as suas subscrições divididas em três matrizes Revise cuidadosamente os seus resultados. Pode querer fazer uma cópia de segurança do conteúdo das matrizes, para que pode facilmente reverter as alterações caso precise no futuro. Se decidiu, pretendesse converter todas as subscrições elegíveis que estão atualmente no antigo modelo de preços para o novo modelo de preços que essa tarefa, agora pode ser realizada com:

```PowerShell
Foreach ($id in $Eligible)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
}

```
