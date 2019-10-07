---
title: Determinar as causas da não conformidade
description: Quando um recurso não está em conformidade, há muitos motivos possíveis. Saiba como descobrir o que causou a não conformidade.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 556a8029b9b5f831ac3ace7ed0fcc474fb2e262e
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978098"
---
# <a name="determine-causes-of-non-compliance"></a>Determinar as causas da não conformidade

Quando um recurso do Azure é determinado como não compatível com uma regra de política, é útil entender a parte da regra com a qual o recurso não está em conformidade. Também é útil entender qual alteração alterou um recurso compatível anteriormente para torná-lo não compatível. Há duas maneiras de encontrar essas informações:

> [!div class="checklist"]
> - [Detalhes de conformidade](#compliance-details)
> - [Histórico de alterações (versão prévia)](#change-history)

## <a name="compliance-details"></a>Detalhes de conformidade

Quando um recurso não está em conformidade, os detalhes de conformidade desse recurso estão disponíveis na página de **conformidade da política** . O painel de detalhes de conformidade inclui as seguintes informações:

- Detalhes do recurso, como nome, tipo, local e ID do recurso
- Estado de conformidade e carimbo de data/hora da última avaliação para a atribuição de política atual
- Uma lista de _motivos_ para a não conformidade do recurso

> [!IMPORTANT]
> Como os detalhes de conformidade para um recurso _sem conformidade_ mostram o valor atual das propriedades nesse recurso, o usuário deve ter a operação de **leitura** para o **tipo** de recurso. Por exemplo, se o recurso _sem conformidade_ for **Microsoft. Compute/virtualMachines** , o usuário deverá ter a operação **Microsoft. Compute/virtualMachines/Read** . Se o usuário não tiver a operação necessária, um erro de acesso será exibido.

Para exibir os detalhes de conformidade, siga estas etapas:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Na página **visão geral** ou **conformidade** , selecione uma política em um **estado de conformidade** que _não esteja em_conformidade.

1. Na guia **conformidade de recursos** da página **conformidade de política** , clique com o botão direito do mouse ou selecione as reticências de um recurso em um **estado de conformidade** que não está em _conformidade_. Em seguida, selecione **Exibir detalhes de conformidade**.

   ![Opção Exibir detalhes de conformidade](../media/determine-non-compliance/view-compliance-details.png)

1. O painel **detalhes de conformidade** exibe informações da avaliação mais recente do recurso para a atribuição de política atual. Neste exemplo, o campo **Microsoft. SQL/Servers/Version** é considerado _12,0_ enquanto a definição de política era esperada _14,0_. Se o recurso não estiver em conformidade por vários motivos, cada um será listado nesse painel.

   ![Painel de detalhes de conformidade e motivos para não conformidade](../media/determine-non-compliance/compliance-details-pane.png)

   Para uma definição de política **auditIfNotExists** ou **deployIfNotExists** , os detalhes incluem a propriedade **Details. Type** e todas as propriedades opcionais. Para obter uma lista, consulte [Propriedades de auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e propriedades de [deployIfNotExists](../concepts/effects.md#deployifnotexists-properties). O **último recurso avaliado** é um recurso relacionado da seção de **detalhes** da definição.

   Exemplo de definição de **deployIfNotExists** parcial:

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

   ![Painel de detalhes de conformidade-* ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Para proteger os dados, quando um valor de propriedade é um _segredo_ , o valor atual exibe asteriscos.

Esses detalhes explicam por que um recurso não está em conformidade no momento, mas não mostram quando a alteração foi feita no recurso que fez com que ele se torne incompatível. Para obter essas informações, consulte [histórico de alterações (versão prévia)](#change-history) abaixo.

### <a name="compliance-reasons"></a>Motivos de conformidade

A matriz a seguir mapeia cada possível _motivo_ para a [condição](../concepts/definition-structure.md#conditions) responsável na definição de política:

|Reason | Condição |
|-|-|
|O valor atual deve conter o valor de destino como uma chave. |containsKey ou **não** notContainsKey |
|O valor atual deve conter o valor de destino. |Contains ou **não** contém |
|O valor atual deve ser igual ao valor de destino. |é igual a ou **não** é diferente de |
|O valor atual deve ser menor que o valor de destino. |menos ou **não** greaterOrEquals |
|O valor atual deve ser maior ou igual ao valor de destino. |greaterOrEquals ou **não** menos |
|O valor atual deve ser maior que o valor de destino. |maior ou **não** lessOrEquals |
|O valor atual deve ser menor ou igual ao valor de destino. |lessOrEquals ou **não** é maior |
|O valor atual deve existir. |existe |
|O valor atual deve estar no valor de destino. |in ou **not** notIn |
|O valor atual deve ser semelhante ao valor de destino. |Like ou **não** like |
|O valor atual deve diferenciar maiúsculas de minúsculas no valor de destino. |corresponder ou **não** corresponder |
|O valor atual deve diferenciar maiúsculas e minúsculas corresponde ao valor de destino. |matchInsensitively ou **não** notMatchInsensitively |
|O valor atual não deve conter o valor de destino como uma chave. |notContainsKey ou **não** ContainsKey|
|O valor atual não deve conter o valor de destino. |Não contém ou **não** contém |
|O valor atual não deve ser igual ao valor de destino. |Não é igual a ou **não** é igual a |
|O valor atual não deve existir. |**não** existe  |
|O valor atual não deve estar no valor de destino. |notIn ou **não** em |
|O valor atual não deve ser igual ao valor de destino. |não curtir ou **não** gostar |
|O valor atual não deve diferenciar maiúsculas de minúsculas no valor de destino. |não corresponder ou **não** corresponder |
|O valor atual não deve diferenciar maiúsculas de minúsculas para corresponder ao valor de destino. |notMatchInsensitively ou **não** matchInsensitively |
|Nenhum recurso relacionado corresponde aos detalhes de efeito na definição de política. |Um recurso do tipo definido em **then. Details. Type** e relacionado ao recurso definido na parte **If** da regra de política não existe. |

## <a name="compliance-details-for-guest-configuration"></a>Detalhes de conformidade para configuração de convidado

Para políticas de _auditIfNotExists_ na categoria de _configuração de convidado_ , pode haver várias configurações avaliadas dentro da VM e você precisará exibir os detalhes por configuração. Por exemplo, se você estiver auditando uma lista de políticas de senha e apenas uma delas tiver o status _não compatível_, você precisará saber quais políticas de senha específicas estão fora de conformidade e por quê.

Você também pode não ter acesso para entrar na VM diretamente, mas precisa relatar por que a VM _não está em conformidade_.

### <a name="azure-portal"></a>Portal do Azure

Comece seguindo as mesmas etapas na seção acima para exibir os detalhes de conformidade da política.

Na exibição do painel **detalhes de conformidade** , clique no link **último recurso avaliado**.

   ![Exibir detalhes da definição de auditIfNotExists](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

A página **atribuição de convidado** exibe todos os detalhes de conformidade disponíveis. Cada linha na exibição representa uma avaliação que foi executada dentro da máquina. Na coluna **motivo** , uma frase que descreve por que a atribuição de convidado _não é compatível_ é mostrada. Por exemplo, se você estiver auditando políticas de senha, a coluna **motivo** exibiria texto, incluindo o valor atual de cada configuração.

![Ver detalhes de conformidade](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Você também pode exibir detalhes de conformidade de Azure PowerShell. Primeiro, verifique se você tem o módulo configuração de convidado instalado.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Você pode exibir o status atual de todas as atribuições de convidado para uma VM usando o seguinte comando:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Para exibir apenas a frase de _motivo_ que descreve por que a VM _não está em conformidade_, retorne apenas a propriedade filho Reason.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Você também pode gerar um histórico de conformidade para as atribuições de convidado no escopo da máquina. A saída desse comando inclui os detalhes de cada relatório para a VM.

> [!NOTE]
> A saída pode retornar um grande volume de dados. É recomendável armazenar a saída em uma variável.

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

Para simplificar essa exibição, use o parâmetro **showchanged** . A saída desse comando inclui apenas os relatórios que seguiram uma alteração no status de conformidade.

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

## <a name="a-namechange-historychange-history-preview"></a>Histórico de <a name="change-history"/>Change (versão prévia)

Como parte de uma nova **Visualização pública**, os últimos 14 dias de histórico de alterações estão disponíveis para todos os recursos do Azure que dão suporte à [exclusão de modo completo](../../../azure-resource-manager/complete-mode-deletion.md). O histórico de alterações fornece detalhes sobre quando uma alteração foi detectada e uma _diferença visual_ para cada alteração. Uma detecção de alteração é disparada quando as propriedades do Resource Manager são adicionadas, removidas ou alteradas.

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Na página **visão geral** ou **conformidade** , selecione uma política em qualquer **estado de conformidade**.

1. Na guia **conformidade de recursos** da página **conformidade de política** , selecione um recurso.

1. Selecione a guia **histórico de alterações (versão prévia)** na página **conformidade de recursos** . Uma lista de alterações detectadas, se houver, será exibida.

   ![Azure Policy guia histórico de alterações na página conformidade de recursos](../media/determine-non-compliance/change-history-tab.png)

1. Selecione uma das alterações detectadas. A _diferença visual_ para o recurso é apresentada na página **histórico de alterações** .

   ![Azure Policy diferença visual do histórico de alterações na página Histórico de alterações](../media/determine-non-compliance/change-history-visual-diff.png)

O _Visual diff_ auxilia na identificação de alterações em um recurso. As alterações detectadas podem não estar relacionadas ao estado de conformidade atual do recurso.

Os dados do histórico de alterações são fornecidos pelo [grafo de recursos do Azure](../../resource-graph/overview.md). Para consultar essas informações fora do portal do Azure, consulte [obter alterações de recurso](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Passos seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [obter dados de conformidade](getting-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
