---
title: Determinar as causas da não conformidade
description: Quando um recurso está em conformidade, existem muitas razões possíveis. Aprenda a saber o que causou a não conformidade.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fb7f238bb5c04bb03ee500b1b953895cc88c0596
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298932"
---
# <a name="determine-causes-of-non-compliance"></a>Determinar as causas da não conformidade

Quando um recurso do Azure é identificado como não conforme a uma regra de política, é útil compreender qual a parte da regra o recurso não está em conformidade com. Também é útil para entender quais alteração alterado um recurso anteriormente em conformidade para torná-lo em não conformidade. Existem duas formas de localizar estas informações:

> [!div class="checklist"]
> - [Detalhes de conformidade](#compliance-details)
> - [Histórico de alterações (pré-visualização)](#change-history-preview)

## <a name="compliance-details"></a>Detalhes de conformidade

Quando um recurso está em conformidade, os detalhes de conformidade para esse recurso estão disponíveis a partir da **conformidade com a política** página. O painel de detalhes de conformidade inclui as seguintes informações:

- Detalhes do recurso, como o nome, tipo, localização e ID de recurso
- Estado de conformidade e timestamp da última avaliação para a atribuição de política atual
- Uma lista de _motivos_ para a recurso não conformidade

> [!IMPORTANT]
> Como os detalhes de conformidade para um _não compatível_ Recurso mostra o valor atual de propriedades esse recurso, o utilizador tem de ter **ler** operação para o **tipo** de recurso. Por exemplo, se o _não compatível_ recurso está **Microsoft.Compute/virtualMachines** , em seguida, o utilizador tem de ter o **Microsoft.Compute/virtualMachines/read** operação. Se o utilizador não tem a operação necessária, é apresentado um erro de acesso.

Para ver os detalhes de conformidade, siga estes passos:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Sobre o **descrição geral** ou **conformidade** , selecione uma política num **estado de conformidade** ou seja _em não conformidade_.

1. Sob o **conformidade do recurso** separador da **conformidade com a política** página, faça duplo clique ou selecione as reticências de um recurso num **estado de conformidade** ou seja  _Não compatível_. Em seguida, selecione **ver os detalhes de conformidade**.

   ![Opção de exibição de detalhes de conformidade](../media/determine-non-compliance/view-compliance-details.png)

1. O **detalhes de conformidade** painel apresenta informações de avaliação mais recente do recurso para a atribuição de política atual. Neste exemplo, o campo **Microsoft.Sql/servers/version** for _12.0_ enquanto a definição de política esperada _14,0_. Se o recurso está em conformidade por vários motivos, cada um está listado neste painel.

   ![Painel de detalhes de conformidade e motivos de não conformidade](../media/determine-non-compliance/compliance-details-pane.png)

   Para uma **auditIfNotExists** ou **deployIfNotExists** definição de política, os detalhes incluem o **details.type** propriedade e todas as propriedades opcionais. Para obter uma lista, consulte [propriedades de auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [deployIfNotExists propriedades](../concepts/effects.md#deployifnotexists-properties). **Avaliado pela última vez resource** é um recurso relacionado do **detalhes** secção da definição.

   Exemplo parcial **deployIfNotExists** definição:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![Painel de detalhes de conformidade - * ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Para proteger os dados, quando um valor de propriedade é um _segredo_ o valor atual apresenta asteriscos.

Estes detalhes explicam por que um recurso está atualmente em não conformidade, mas não mostram quando a alteração foi feita para o recurso que provocou a se tornar não conforme sua. Para obter essas informações, consulte [(pré-visualização) de histórico de alterações](#change-history-preview) abaixo.

### <a name="compliance-reasons"></a>Motivos de conformidade

A matriz seguinte mapeia cada possível _motivo_ para o responsável [condição](../concepts/definition-structure.md#conditions) na definição de política:

|Reason | Condição |
|-|-|
|Valor atual tem de conter o valor de destino como uma chave. |containsKey ou **não** notContainsKey |
|Valor atual tem de conter o valor de destino. |contém ou **não** notContains |
|Valor atual tem de ser igual ao valor de destino. |é igual a ou **não** notEquals |
|Valor atual tem de ser inferior ao valor de destino. |menos ou **não** greaterOrEquals |
|Valor atual tem de ser maior ou igual ao valor de destino. |greaterOrEquals ou **não** menos |
|Valor atual tem de ser maior que o valor de destino. |maior ou **não** lessOrEquals |
|Valor atual tem de ser menor ou igual ao valor de destino. |lessOrEquals ou **não** maior |
|Valor atual tem de existir. |Existe |
|Valor atual tem de ser o valor de destino. |em ou **não** notIn |
|Valor atual tem de ser como o valor de destino. |como ou **não** notLike |
|Valor atual tem o valor de destino de correspondência de maiúsculas e minúsculas. |corresponder ou **não** notMatch |
|Valor atual tem o valor de destino de correspondência de maiúsculas e minúsculas. |matchInsensitively ou **não** notMatchInsensitively |
|Valor atual não tem de conter o valor de destino como uma chave. |notContainsKey ou **não** containsKey|
|Valor atual não tem de conter o valor de destino. |notContains ou **não** contém |
|Valor atual não tem de ser igual ao valor de destino. |notEquals ou **não** é igual a |
|Valor atual não tem de existir. |**não** existe  |
|Valor atual não tem de ser o valor de destino. |notIn ou **não** no |
|Valor atual não tem de ser como o valor de destino. |notLike ou **não** como |
|Valor atual tem correspondência não diferencia maiúsculas de minúsculas, o valor de destino. |notMatch ou **não** corresponder |
|Valor atual tem correspondência não maiúsculas de minúsculas, o valor de destino. |notMatchInsensitively ou **não** matchInsensitively |
|Não existem recursos relacionados correspondem os detalhes de efeito na definição de política. |Um recurso do tipo definido na **then.details.type** e esteja relacionado com o recurso definido no **se** parte da regra de política não existe. |

## <a name="compliance-details-for-guest-configuration"></a>Detalhes de conformidade para a configuração de convidado

Para _auditar_ políticas no _configuração de convidado_ categoria, pode haver várias definições avaliadas dentro da VM e precisará ver os detalhes por definição. Por exemplo, se estiver a auditoria para obter uma lista de aplicativos instalados e o estado de atribuição é _em não conformidade_, precisará saber quais aplicativos específicos estão em falta.

Também poderá não ter acesso ao iniciar sessão na VM diretamente, mas terá de gerar relatórios sobre por que a VM estiver _em não conformidade_. Por exemplo, pode auditar que as VMs estão associadas ao domínio correto e incluem a associação de domínio atual nos detalhes de geração de relatórios.

### <a name="azure-portal"></a>Portal do Azure

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Sobre o **descrição geral** ou **conformidade** , selecione uma atribuição de política para qualquer iniciativa que contém uma definição de política de configuração de convidado que _em não conformidade_.

1. Selecione um _auditar_ política na iniciativa isso _em não conformidade_.

   ![Ver detalhes de definição de auditoria](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. Sobre o **conformidade do recurso** guia, as seguintes informações são fornecidas:

   - **Nome** -o nome das atribuições de configuração de convidado.
   - **Recurso principal** -a máquina virtual num _em não conformidade_ estado para a atribuição de convidado de configuração selecionada.
   - **Tipo de recurso** – a _guestConfigurationAssignments_ nome completo.
   - **Última avaliada** – a última vez que o serviço de configuração de convidado notificado Azure Policy sobre o estado da máquina virtual de destino.

   ![Ver detalhes de conformidade](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. Selecione o nome da atribuição de configuração de convidado no **Name** coluna para abrir o **conformidade do recurso** página.

1. Selecione o **recursos de exibição** botão na parte superior da página para abrir o **atribuição de convidados** página.

O **atribuição de convidados** página apresenta todos os detalhes de conformidade disponíveis. Cada linha na vista representa uma avaliação que foi executada dentro da máquina virtual. Na **motivo** coluna, uma expressão que descreve por que é a atribuição de convidado _em não conformidade_ é mostrado. Por exemplo, se estiver auditando que VMs devem ser associadas a um domínio, o **motivo** coluna seria exibida texto, incluindo a associação de domínio atual.

![Ver detalhes de conformidade](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Também pode ver os detalhes de conformidade do Azure PowerShell. Em primeiro lugar, certifique-se de que tenha o módulo de configuração de convidado instalado.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Pode ver o estado atual de todas as atribuições de convidado para uma VM com o seguinte comando:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Para ver apenas os _motivo_ expressão que descreve o motivo pelo qual a VM está _em não conformidade_, devolver apenas a propriedade child do motivo.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Também pode emitir um histórico de conformidade a existência de atribuições de convidado no âmbito para a máquina virtual. O resultado deste comando inclui os detalhes de cada relatório para a VM.

> [!NOTE]
> A saída pode retornar um grande volume de dados. É recomendado para armazenar a saída numa variável.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Para simplificar esta vista, utilize o **ShowChanged** parâmetro. O resultado deste comando inclui apenas os relatórios que seguida de uma alteração de estado de conformidade.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Histórico de alterações (pré-visualização)

Como parte de um novo **pré-visualização pública**, os últimos 14 dias do histórico de alterações estão disponíveis para todos os recursos do Azure que suportam [concluir a eliminação de modo](../../../azure-resource-manager/complete-mode-deletion.md). Histórico de alterações fornece detalhes sobre o quando foi detetada uma alteração e um _visual diff_ para cada alteração. Uma deteção de alteração é acionada quando são adicionadas, remover ou alterar as propriedades do Gestor de recursos.

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Sobre o **descrição geral** ou **conformidade** , selecione uma política em qualquer **estado de conformidade**.

1. Sob o **conformidade do recurso** separador da **conformidade com a política** , selecione um recurso.

1. Selecione o **histórico de alterações (pré-visualização)** guia a **conformidade do recurso** página. Uma lista de detetou alterações, se qualquer exist, são apresentados.

   ![Separador de histórico de alterações de política do Azure na página de recursos de conformidade](../media/determine-non-compliance/change-history-tab.png)

1. Selecione uma das alterações detetadas. O _visual diff_ para o recurso é apresentado no **histórico de alterações** página.

   ![Azure política alteração histórico Visual Diff na página de histórico de alteração](../media/determine-non-compliance/change-history-visual-diff.png)

O _visual diff_ aides na identificação de alterações a um recurso. Foram detetadas as alterações podem não estar relacionados com o atual estado de conformidade do recurso.

Dados de histórico de alterações são fornecidos pela [gráfico de recursos do Azure](../../resource-graph/overview.md). Para consultar estas informações fora do portal do Azure, veja [obter as alterações de recursos](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Passos Seguintes

- Reveja exemplos em [exemplos do Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Compreender como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [obter dados de conformidade](getting-compliance-data.md).
- Saiba como [remediar recursos incompatíveis](remediate-resources.md).
- Revisão que um grupo de gestão é com [organizar os recursos com grupos de gestão do Azure](../../management-groups/overview.md).