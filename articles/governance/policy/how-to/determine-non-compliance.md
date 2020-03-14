---
title: Determinar as causas da não conformidade
description: Quando um recurso não é compatível, existem muitas razões possíveis. Aprenda a descobrir o que causou o incumprimento.
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264638"
---
# <a name="determine-causes-of-non-compliance"></a>Determinar as causas da não conformidade

Quando um recurso Azure é determinado a não cumprir uma regra de política, é útil entender que parte da regra o recurso não está em conformidade com. Também é útil entender que mudança alterou um recurso anteriormente compatível para torná-lo incompatível. Há duas maneiras de encontrar esta informação:

> [!div class="checklist"]
> - [Detalhes de conformidade](#compliance-details)
> - [Alterar a história (Pré-visualização)](#change-history)

## <a name="compliance-details"></a>Detalhes de conformidade

Quando um recurso não é conforme, os detalhes de conformidade desse recurso estão disponíveis na página de conformidade da **Política.** O painel de detalhes de conformidade inclui as seguintes informações:

- Detalhes de recursos tais como nome, tipo, localização e ID de recursos
- Estado de conformidade e carimbo temporal da última avaliação para a atual atribuição de políticas
- Uma lista de _razões_ para o incumprimento dos recursos

> [!IMPORTANT]
> Como os detalhes de conformidade de um recurso _não conforme_ mostram o valor atual das propriedades nesse recurso, o utilizador deve ter **lido** o funcionamento do **tipo** de recurso. Por exemplo, se o recurso _não conforme_ for **Microsoft.Compute/virtualMachines,** então o utilizador deve ter o funcionamento **Microsoft.Compute/virtualMachines/read.** Se o utilizador não tiver a operação necessária, é apresentado um erro de acesso.

Para ver os detalhes de conformidade, siga estes passos:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Na página **'Visão Geral'** ou **Conformidade,** selecione uma política num estado de **conformidade** que não seja _conforme_.

1. Sob o separador de conformidade do **Recurso** da página de conformidade da **Política,** clique à direita ou selecione a elipse de um recurso num estado de **conformidade** que não seja _conforme_. Em seguida, selecione **Ver detalhes de conformidade**.

   ![Ver opção de detalhes de conformidade](../media/determine-non-compliance/view-compliance-details.png)

1. O painel de detalhes de **conformidade** exibe informações da última avaliação do recurso até à atribuição de políticas em curso. Neste exemplo, o campo **Microsoft.Sql/servers/version** é de _12.0,_ enquanto a definição de política esperada _14.0_. Se o recurso não for compatível por múltiplas razões, cada um está listado neste painel.

   ![Painel de detalhes de conformidade e razões para o incumprimento](../media/determine-non-compliance/compliance-details-pane.png)

   Para uma **auditoriaIfNotExists** ou **implementaDefinição de políticaIfNotExists,** os detalhes incluem os **detalhes.propriedade do tipo** e quaisquer propriedades opcionais. Para uma lista, consulte [auditIfNotExists propriedades](../concepts/effects.md#auditifnotexists-properties) e [implemente propriedades IfNotExists](../concepts/effects.md#deployifnotexists-properties). **O último recurso avaliado** é um recurso relacionado a partir da secção de **detalhes** da definição.

   Exemplo de **implantação parcialIfNotExiste** definição:

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

   ![Painel de detalhes de conformidade - *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Para proteger os dados, quando um valor de propriedade é um _segredo,_ o valor atual exibe asteriscos.

Estes detalhes explicam por que um recurso não está atualmente em conformidade, mas não mostram quando a mudança foi feita para o recurso que o fez tornar-se incompatível. Para obter essa informação, consulte [alterar o histórico (Pré-visualização)](#change-history) abaixo.

### <a name="compliance-reasons"></a>Razões de conformidade

A seguinte matriz mapeia cada _motivo_ possível para a [condição](../concepts/definition-structure.md#conditions) responsável na definição de política:

|Razão | Condição |
|-|-|
|O valor atual deve conter o valor-alvo como chave. |contémChave ou **nãoContémChave** |
|O valor atual deve conter o valor-alvo. |contém ou **nãoContém** |
|O valor atual deve ser igual ao valor-alvo. |igual ou **não** iguala |
|O valor atual deve ser inferior ao valor-alvo. |menos ou **não** maiorOrEquals |
|O valor atual deve ser maior ou igual ao valor-alvo. |maiorOrIgualou **ou não** menos |
|O valor atual deve ser superior ao valor-alvo. |maior ou **não** lessOrEquals |
|O valor atual deve ser inferior ou igual ao valor-alvo. |lessOrEquals ou **não** maior |
|O valor atual deve existir. |existe |
|O valor atual deve estar no valor-alvo. |dentro ou **nãoIn** |
|O valor atual deve ser como o valor-alvo. |como ou **não** |
|O valor atual deve corresponder ao valor-alvo. |jogo ou **nãoMatch** |
|O valor atual deve corresponder ao valor-alvo. |matchInsensitivemente ou nãoMatchInsensitively |
|O valor atual não deve conter o valor-alvo como chave. |notContainsKey ou **não** contémChave|
|O valor atual não deve conter o valor-alvo. |não Contém ou **não** contém |
|O valor atual não deve ser igual ao valor-alvo. |não igual ou **não** igual |
|O valor atual não deve existir. |**não** existe  |
|O valor atual não deve estar no valor-alvo. |não In ou **não** em |
|O valor atual não deve ser como o valor-alvo. |não gostar ou **não** gostar |
|O valor atual não deve corresponder ao valor-alvo. |não Match ou **não** corresponder |
|O valor atual não deve corresponder ao valor-alvo. |nãoMatchInsensitiveou ou **não** corresponder De forma insensível |
|Nenhum recursos relacionados corresponda aos detalhes do efeito na definição de política. |Um recurso do tipo definido em **então.details.type** e relacionado com o recurso definido na **parte** da regra da política não existe. |

## <a name="compliance-details-for-guest-configuration"></a>Detalhes de conformidade para configuração do hóspede

Para auditoriaAs políticas _IfNotExist_ na categoria _de Configuração_ de Hóspedes, pode haver várias configurações avaliadas dentro do VM e terá de visualizar detalhes por definição. Por exemplo, se estiver a auditar uma lista de políticas de passwords e apenas uma delas tiver estatuto _Incompatível,_ terá de saber quais as políticas específicas de senha que estão fora de conformidade e porquê.

Também pode não ter acesso a iniciar sessão diretamente no VM, mas tem de informar sobre o motivo pelo qual o VM não _é compatível_.

### <a name="azure-portal"></a>Portal do Azure

Comece por seguir os mesmos passos na secção acima para visualizar os detalhes da conformidade da política.

Na visão do painel de detalhes de **conformidade** clique no link **Último recurso avaliado**.

   ![Ver auditoriaIfNotExists detalhes de definição](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

A página de **Atribuição de Hóspedes** apresenta todos os detalhes de conformidade disponíveis. Cada linha na vista representa uma avaliação que foi realizada dentro da máquina. Na coluna **Reason,** é mostrada uma frase que descreve por que razão a Atribuição de Convidados não é _compatível._ Por exemplo, se estiver a auditar as políticas de passwords, a coluna **Reason** apresentaria texto, incluindo o valor atual para cada definição.

![Ver detalhes de conformidade](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Também pode ver detalhes de conformidade a partir do Azure PowerShell. Primeiro, certifique-se de que tem o módulo de Configuração do Hóspede instalado.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Pode visualizar o estado atual de todas as Atribuições de Hóspedes para um VM utilizando o seguinte comando:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Para ver apenas a frase _da razão_ que descreve por que o VM é _incompatível,_ devolva apenas a propriedade da criança Reason.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Também pode obter um histórico de conformidade para as Atribuições de Hóspedes no âmbito da máquina. A saída deste comando inclui os detalhes de cada relatório para o VM.

> [!NOTE]
> A saída pode devolver um grande volume de dados. É recomendado armazenar a saída numa variável.

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

Para simplificar esta vista, utilize o parâmetro **ShowChanged.** A saída deste comando apenas inclui os relatórios que se seguiram a uma alteração do estado de conformidade.

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

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Alterar a história (Pré-visualização)

Como parte de uma nova **pré-visualização pública,** os últimos 14 dias de história da mudança estão disponíveis para todos os recursos do Azure que suportam a [eliminação completa](../../../azure-resource-manager/templates/complete-mode-deletion.md)do modo. A história da mudança fornece detalhes sobre quando uma mudança foi detetada e uma _difusão visual_ para cada mudança. Uma deteção de alterações é desencadeada quando as propriedades do Gestor de Recursos são adicionadas, removidas ou alteradas.

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Na página **'Visão Geral'** ou **Conformidade,** selecione uma política em qualquer **estado de conformidade**.

1. Sob o separador de conformidade de **recursos** da página de conformidade da **Política,** selecione um recurso.

1. Selecione o separador 'Histórico de **Alteração' (pré-visualização)** na página de Conformidade de **Recursos.** Uma lista de alterações detetadas, se existirem, são apresentadas.

   ![Separador de histórico de mudança de política azure na página de conformidade de recursos](../media/determine-non-compliance/change-history-tab.png)

1. Selecione uma das alterações detetadas. A _difusão visual_ do recurso é apresentada na página de história da **Mudança.**

   ![Azure Policy Change History Visual Diff na página de história da Mudança](../media/determine-non-compliance/change-history-visual-diff.png)

Os assistentes _de difusão visual_ na identificação de alterações a um recurso. As alterações detetadas podem não estar relacionadas com o estado de conformidade atual do recurso.

Alterar dados de histórico é fornecido pelo [Azure Resource Graph](../../resource-graph/overview.md). Para consultar esta informação fora do portal Azure, consulte [Obter alterações de recursos](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Passos seguintes

- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Compreender como [criar políticas programáticas.](programmatically-create.md)
- Saiba como obter dados de [conformidade.](get-compliance-data.md)
- Aprenda a [remediar recursos não conformes](remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)
