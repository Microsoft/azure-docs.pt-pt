---
title: Determinar as causas da não conformidade
description: Quando um recurso não é conforme, existem muitas razões possíveis. Aprenda a descobrir o que causou o incumprimento.
ms.date: 07/06/2020
ms.topic: how-to
ms.openlocfilehash: a666da4ecb97c24e7176e6c7cfbe2ee24f46f1b7
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648576"
---
# <a name="determine-causes-of-non-compliance"></a>Determinar as causas da não conformidade

Quando um recurso Azure é determinado a não cumprir uma regra política, é útil entender com que parte da regra o recurso não está em conformidade. Também é útil entender que mudança alterou um recurso previamente conforme para torná-lo incompatível. Há duas maneiras de encontrar esta informação:

- [Detalhes de conformidade](#compliance-details)
- [Alterar história (Pré-visualização)](#change-history)

## <a name="compliance-details"></a>Detalhes de conformidade

Quando um recurso não está em conformidade, os detalhes de conformidade desse recurso estão disponíveis na página de conformidade da **Política.** O painel de detalhes de conformidade inclui as seguintes informações:

- Detalhes de recursos como nome, tipo, localização e ID de recursos
- Estado de conformidade e timetamp da última avaliação para a atual atribuição de políticas
- Uma lista de _razões_ para o incumprimento dos recursos

> [!IMPORTANT]
> Como os detalhes _de_ conformidade de um recurso não conforme mostram o valor atual das propriedades nesse recurso, o utilizador deve ter **lido** a operação para o **tipo** de recurso. Por exemplo, se o recurso _não conforme_ for **o Microsoft.Compute/virtualMachines,** então o utilizador deve ter a operação **Microsoft.Compute/virtualMachines/read.** Se o utilizador não tiver a operação necessária, é apresentado um erro de acesso.

Para ver os detalhes de conformidade, siga estes passos:

1. Lançar o serviço Azure Policy no portal Azure selecionando **todos os serviços,** procurando e selecionando **a Política**.

1. Na página **'Visão Geral'** ou **Compliance,** selecione uma política num estado de **conformidade** que _não esteja em conformidade_.

1. No separador **de conformidade de recursos** da página de conformidade da **Política,** clique à direita ou selecione a elipse de um recurso num estado de **conformidade** que não está _em conformidade._ Em seguida, **selecione Ver detalhes de conformidade**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Screenshot do link 'Ver detalhes de conformidade' no separador de conformidade de recursos." border="false":::

1. O painel **de detalhes do Compliance** exibe informações da última avaliação do recurso à atribuição de políticas em curso. Neste exemplo, o campo **Microsoft.Sql/servidors/versão** é encontrado como _sendo 12.0,_ enquanto a definição de política esperava _14.0_. Se o recurso não for conforme por várias razões, cada um está listado neste painel.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Screenshot do painel de detalhes de conformidade e razões para o incumprimento que o valor atual é doze e o valor-alvo é de catorze." border="false":::

   Para uma **auditoriaIfNotExists** ou implementar a definição de **políticaifNotExists,** os detalhes incluem os **detalhes.digitar** propriedade e quaisquer propriedades opcionais. Para obter uma lista, consulte [as propriedades auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [implementar propriedadesIfNotExists](../concepts/effects.md#deployifnotexists-properties). **O último recurso avaliado** é um recurso relacionado a partir da secção de **detalhes** da definição.

   Exemplo **de implementação parcial DefiniçãoIfNotExists:**

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

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Screenshot of Compliance details pane for ifNotExists including evaluated resource count." border="false":::

> [!NOTE]
> Para proteger os dados, quando um valor de propriedade é _um segredo,_ o valor atual exibe asteriscos.

Estes detalhes explicam por que um recurso é atualmente incompatível, mas não mostram quando a alteração foi feita ao recurso que o fez tornar-se incompatível. Para obter esta informação, consulte [alterar a história (Preview)](#change-history) abaixo.

### <a name="compliance-reasons"></a>Razões de conformidade

A seguinte matriz mapeia cada _razão_ possível para a [condição](../concepts/definition-structure.md#conditions) responsável na definição de política:

|Razão | Condição |
|-|-|
|O valor atual deve conter o valor-alvo como chave. |contémKey **not** ou nãoContainsKey |
|O valor atual deve conter o valor-alvo. |contém **not** ou nãoContains |
|O valor atual deve ser igual ao valor-alvo. |é igual **not** ou nãoQuals |
|O valor atual deve ser inferior ao valor-alvo. |menos ou **menos** grandes Equadores |
|O valor atual deve ser superior ou igual ao valor-alvo. |maiorOrEquals ou **não** menos |
|O valor atual deve ser superior ao valor-alvo. |maior ou **não** lessOrQuals |
|O valor atual deve ser inferior ou igual ao valor-alvo. |lessOrEquals ou **não** maior |
|O valor atual deve existir. |existe |
|O valor atual deve estar no valor-alvo. |em ou **não** |
|O valor atual deve ser igual ao valor-alvo. |gosto ou **não** Como |
|O valor atual deve corresponder ao valor-alvo. |corresponder ou **nãoMatch** |
|O valor atual deve corresponder ao valor-alvo. |matchInsensitively **not** ou nãoMatchInsensitively |
|O valor atual não deve conter o valor-alvo como chave. |nãoContainsKey ou **não** contémKey|
|O valor atual não deve conter o valor-alvo. |não Contém ou **não** |
|O valor atual não deve ser igual ao valor-alvo. |não Iguais ou **não** |
|O valor atual não deve existir. |**não** existe  |
|O valor atual não deve estar no valor-alvo. |não Entrar ou **não** em |
|O valor atual não deve ser como o valor-alvo. |não gostar ou **não** |
|O valor atual não deve corresponder ao valor-alvo sensível ao caso. |não Jogo ou **não** |
|O valor atual não deve corresponder ao valor-alvo por casos insensíveis. |notMatchInsensitively ou **não** corresponderInsensitively |
|Nenhum dos recursos relacionados corresponde aos detalhes do efeito na definição de política. |Um recurso do tipo definido em **então.details.type** e relacionado com o recurso definido na **parte** da regra política não existe. |

## <a name="compliance-details-for-guest-configuration"></a>Detalhes de conformidade da Configuração de Convidado

Para as políticas _de auditIfNotExists_ na categoria _Configuração_ de Convidados, pode haver várias configurações avaliadas dentro do VM e você precisará ver detalhes por definição. Por exemplo, se estiver a auditar uma lista de políticas de passwords e apenas uma delas tiver estatuto Não conforme, terá de saber quais as políticas específicas de _palavra-passe_que estão fora de conformidade e porquê.

Também pode não ter acesso ao acesso diretamente ao VM, mas tem de informar por que razão o VM não _está em conformidade._

### <a name="azure-portal"></a>Portal do Azure

Comece por seguir os mesmos passos na secção acima para visualizar detalhes de conformidade da política.

Na vista de detalhes da Conformidade, selecione o link **Último recurso avaliado.**

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Screenshot de visualização da auditoriaIfNotExists definiu detalhes de conformidade." border="false":::

A página **de Atribuição de Hóspedes** exibe todos os detalhes de conformidade disponíveis. Cada linha na vista representa uma avaliação que foi realizada dentro da máquina. Na coluna **Reason,** é mostrada uma frase que descreve por que a atribuição de hóspedes não _é compatível_. Por exemplo, se estiver a auditar políticas de palavra-passe, a coluna **Reason** apresentaria texto, incluindo o valor atual para cada definição.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Screenshot dos detalhes de conformidade da atribuição de hóspedes." border="false":::

### <a name="azure-powershell"></a>Azure PowerShell

Também pode ver detalhes de conformidade da Azure PowerShell. Em primeiro lugar, certifique-se de que tem o módulo de Configuração de Convidados instalado.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Pode ver o estado atual de todas as Atribuições de Hóspedes para um VM utilizando o seguinte comando:

```azurepowershell-interactive
Get-AzVMGuestPolicyStatus -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Para visualizar apenas a frase _da razão_ que descreve por que o VM é _incompatível,_ devolva apenas a propriedade da criança Reason.

```azurepowershell-interactive
Get-AzVMGuestPolicyStatus -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Também pode obter um histórico de conformidade para atribuições de hóspedes no âmbito da máquina. A saída deste comando inclui os detalhes de cada relatório para o VM.

> [!NOTE]
> A saída pode devolver um grande volume de dados. É recomendado para armazenar a saída numa variável.

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

Para simplificar esta vista, utilize o parâmetro **ShowChanged.** A saída deste comando inclui apenas os relatórios que se seguiram a uma alteração do estado de conformidade.

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

## <a name="change-history-preview"></a><a name="change-history"></a>Alterar história (Pré-visualização)

Como parte de uma nova **pré-visualização pública,** os últimos 14 dias de história da mudança estão disponíveis para todos os recursos Azure que suportam [a eliminação completa do modo](../../../azure-resource-manager/templates/complete-mode-deletion.md). O histórico de alterações fornece detalhes sobre quando uma mudança foi detetada e um _diff visual_ para cada mudança. Uma deteção de alterações é desencadeada quando as propriedades do Gestor de Recursos Azure são adicionadas, removidas ou alteradas.

1. Lançar o serviço Azure Policy no portal Azure selecionando **todos os serviços,** procurando e selecionando **a Política**.

1. Na página **'Visão Geral'** ou **Compliance,** selecione uma política em qualquer **estado de conformidade**.

1. No separador **de conformidade de recursos** da página de conformidade da **Política,** selecione um recurso.

1. Selecione o separador Alterar Histórico **(pré-visualização)** na página **'Conformidade com recursos'.** É apresentada uma lista de alterações detetadas, caso existam.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Screenshot do separador Alterar Histórico e detetado tempos de alteração na página de Conformidade de Recursos." border="false":::

1. Selecione uma das alterações detetadas. O _diff visual_ para o recurso é apresentado na página **de história da Mudança.**

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Screenshot do Diff Visual Change History do estado de propriedades antes e depois da página de história da Mudança." border="false":::

Os assessores _de difusão visual_ na identificação de alterações a um recurso. As alterações detetadas podem não estar relacionadas com o estado de conformidade atual do recurso.

Os dados de histórico de alteração são fornecidos pelo [Azure Resource Graph](../../resource-graph/overview.md). Para consultar estas informações fora do portal Azure, consulte [Obter alterações de recursos](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Próximos passos

- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [obter dados de conformidade.](get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)
