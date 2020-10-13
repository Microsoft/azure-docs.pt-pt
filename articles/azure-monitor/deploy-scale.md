---
title: Implementar monitor de Azure em escala usando a política Azure
description: Implementar as funcionalidades do Monitor Azure em escala utilizando a Política Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/08/2020
ms.openlocfilehash: 4ec7cd2b0f573a9a74f82546da2367edcf721539
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91441461"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Implementar monitor de Azure em escala usando a política Azure
Enquanto algumas funcionalidades do Azure Monitor são configuradas uma ou um número limitado de vezes, outras devem ser repetidas para cada recurso que deseja monitorizar. Este artigo descreve métodos para usar a Política Azure para implementar o Azure Monitor em escala para garantir que a monitorização é configurada de forma consistente e precisa para todos os seus recursos Azure.

Por exemplo, precisa de criar uma definição de diagnóstico para todos os seus recursos Azure existentes e para cada novo recurso que criar. Também precisa de ter um agente instalado e configurado cada vez que cria uma máquina virtual. Pode utilizar métodos como PowerShell ou CLI para executar estas ações, uma vez que estes métodos estão disponíveis para todas as funcionalidades do Azure Monitor. Utilizando a Política Azure, pode ter uma lógica no lugar que irá executar automaticamente a configuração apropriada sempre que criar ou modificar um recurso.


## <a name="azure-policy"></a>Azure Policy
Esta secção fornece uma breve introdução à [Política Azure](../governance/policy/overview.md) que permite avaliar e impor padrões organizacionais em todo o seu grupo de subscrição ou gestão Azure com o mínimo de esforço. Consulte a documentação da [Política Azure](../governance/policy/overview.md) para obter mais detalhes.

Com a Política Azure pode especificar os requisitos de configuração para quaisquer recursos que sejam criados e identificar recursos que estão fora de conformidade, bloquear os recursos de serem criados ou adicionar a configuração necessária. Funciona intercetando chamadas para criar um novo recurso ou para modificar um recurso existente. Pode responder com efeitos como negar o pedido se não corresponder às propriedades esperadas numa definição de política, sinalizando-o para incumprimento ou implantar um recurso relacionado. Pode remediar os recursos existentes com um **deployIfNotExists** ou **modificar** a definição de política.

A Política Azure consiste nos objetos da tabela seguinte. Consulte [os objetos da Política Azure](../governance/policy/overview.md#azure-policy-objects) para obter uma explicação mais detalhada de cada um.

| Item | Descrição |
|:---|:---|
| Definição de política | Descreve as condições de conformidade dos recursos e o efeito a ter se uma condição for satisfeita. Estes podem ser todos os recursos de um tipo particular ou apenas recursos que correspondam a determinadas propriedades. O efeito pode ser simplesmente sinalizar o recurso para o cumprimento ou para implantar um recurso relacionado. As definições de política são escritas utilizando o JSON, conforme descrito na estrutura de [definição de política Azure](../governance/policy/concepts/definition-structure.md). Os efeitos são descritos nos [efeitos da Política de Azure](../governance/policy/concepts/effects.md).
| Iniciativa política | Um grupo de definições políticas que devem ser aplicadas em conjunto. Por exemplo, pode ter uma definição de política para enviar registos de recursos para um espaço de trabalho do Log Analytics e outro para enviar registos de recursos para centros de eventos. Criar uma iniciativa que inclua ambas as definições políticas e aplique a iniciativa aos recursos em vez das definições políticas individuais. As iniciativas são escritas utilizando o JSON, tal como descrito na [estrutura de iniciativa da Política Azure.](../governance/policy/concepts/initiative-definition-structure.md) |
| Atribuição | Uma definição ou iniciativa política não faz efeito até que seja atribuída a um âmbito. Por exemplo, atribuir uma política a um grupo de recursos para aplicá-la a todos os recursos criados nesse recurso, ou aplicá-la a uma subscrição para aplicá-la a todos os recursos dessa subscrição.  Para mais detalhes, consulte [a estrutura de atribuição da Política Azure.](../governance/policy/concepts/assignment-structure.md) |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Definições políticas incorporadas para o Azure Monitor
A Política Azure inclui várias definições pré-construídas relacionadas com o Azure Monitor. Pode atribuir estas definições de política à subscrição existente ou usá-las como base para criar as suas próprias definições personalizadas. Para obter uma lista completa da política incorporada na categoria **Monitor,** consulte as [definições políticas incorporadas da Azure Policy para o Azure Monitor](samples/policy-samples.md).

Para visualizar as definições de política incorporadas relacionadas com a monitorização, execute o seguinte:

1. Vá à **Política Azure** no portal Azure.
2. Selecione **Definições**.
3. Para **tipo**, selecione *Built-in* e para **categoria**, selecione *Monitoring*.

  ![Screenshot da página Azure Policy Definitions no portal Azure mostrando uma lista de definições de política para a categoria de Monitorização e Tipo Incorporado.](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>Definições de diagnóstico
[As definições](platform/diagnostic-settings.md) de diagnóstico recolhem registos de recursos e métricas de recursos Azure para vários locais, tipicamente para um espaço de trabalho Log Analytics que permite analisar os dados com consultas de [registo](log-query/log-query-overview.md) e [alertas de registo.](platform/alerts-log.md) Utilize a Política para criar automaticamente uma definição de diagnóstico cada vez que criar um recurso.

Cada tipo de recurso Azure tem um conjunto único de categorias que precisam de ser listadas na definição de diagnóstico. Por isso, cada tipo de recurso requer uma definição de política separada. Alguns tipos de recursos têm definições de política incorporadas que pode atribuir sem modificação. Para outros tipos de recursos, é necessário criar uma definição personalizada.

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Definições políticas incorporadas para o Azure Monitor
Existem duas definições de política incorporadas para cada tipo de recurso, uma para enviar para o log analytics espaço de trabalho e outra para o Event Hub. Se precisar apenas de um local, então atribua essa política para o tipo de recurso. Se precisar de ambos, atribuirá ambas as definições de política para o recurso.

Por exemplo, a imagem a seguir mostra as definições de política de definição de diagnóstico incorporada para data lake analytics.

  ![Screenshot parcial da página Azure Policy Definitions mostrando duas definições de política de definição de diagnóstico incorporada para Data Lake Analytics.](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Definições de política personalizada
Para tipos de recursos que não têm uma política incorporada, você precisa criar uma definição de política personalizada. Pode fazê-lo manualmente no portal Azure copiando uma política incorporada existente e modificando depois para o seu tipo de recurso. É mais eficiente, no entanto, criar a política de forma programatical usando um script na PowerShell Gallery.

O script [Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) cria ficheiros de política para um determinado tipo de recurso que pode instalar usando PowerShell ou CLI. Utilize o seguinte procedimento para criar uma definição de política personalizada para configurações de diagnóstico.


1. Certifique-se de que tem [a Azure PowerShell](/powershell/azure/install-az-ps) instalada.
2. Instale o script com o seguinte comando:
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Execute o script utilizando os parâmetros para especificar para onde enviar os registos. Será solicitado que especifique um tipo de subscrição e recurso. Por exemplo, para criar uma definição de política que envie para log analytics espaço de trabalho e centro de eventos, use o seguinte comando.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. Em alternativa, pode especificar uma subscrição e um tipo de recurso no comando. Por exemplo, para criar uma definição de política que envie para log analytics espaço de trabalho e centro de eventos para bases de dados do Azure SQL Server, use o seguinte comando.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. O script cria pastas separadas para cada definição de política, cada uma contendo três ficheiros nomeados azurepolicy.js, azurepolicy.rules.jsligados, azurepolicy.parameters.jsligados. Se pretender criar a política manualmente no portal Azure, pode copiar e colar o conteúdo da azurepolicy.js, uma vez que inclui toda a definição de política. Utilize os outros dois ficheiros com PowerShell ou CLI para criar a definição de política a partir de uma linha de comando.

    Os exemplos a seguir mostram como instalar a definição de política tanto da PowerShell como da CLI. Cada um deles inclui metadados para especificar uma categoria de **Monitorização** para agrupar a nova definição de política com as definições de política incorporada.

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>Iniciativa
Em vez de criar uma atribuição para cada definição de política, uma estratégia comum é criar uma iniciativa que inclua as definições políticas para criar configurações de diagnóstico para cada serviço Azure. Crie uma atribuição entre a iniciativa e um grupo de gestão, subscrição ou grupo de recursos, dependendo da forma como gere o seu ambiente. Esta estratégia oferece os seguintes benefícios:

- Crie uma única atribuição para a iniciativa em vez de múltiplas atribuições para cada tipo de recurso. Utilize a mesma iniciativa para grupos de monitorização múltiplas, subscrições ou grupos de recursos.
- Modifique a iniciativa quando precisar de adicionar um novo tipo de recurso ou destino. Por exemplo, os seus requisitos iniciais podem ser enviar dados apenas para um espaço de trabalho Log Analytics, mas mais tarde pretende adicionar o Event Hub. Modifique a iniciativa em vez de criar novas atribuições.

Consulte [Criar e atribuir uma definição de iniciativa](../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition) para detalhes sobre a criação de uma iniciativa. Considere as seguintes recomendações:

- Definir a **categoria de** **Monitorização** para agrupar com definições de política incorporadas e personalizadas relacionadas.
- Em vez de especificar os detalhes para o espaço de trabalho Log Analytics e o Centro de Eventos para definição de políticas incluídas na iniciativa, use um parâmetro de iniciativa comum. Isto permite especificar facilmente um valor comum para todas as definições políticas e alterar esse valor se necessário.

![Definição de iniciativa](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>Atribuição 
Atribua a iniciativa a um grupo de gestão, subscrição ou grupo de recursos da Azure, dependendo do âmbito dos seus recursos para monitorizar. Um [grupo de gestão](../governance/management-groups/overview.md) é particularmente útil para a política de deteção, especialmente se a sua organização tiver várias subscrições.

![Screenshot das definições para o separador Basics na secção de iniciativa 'Atribuir' as definições de Diagnóstico ao espaço de trabalho do Log Analytics no portal Azure.](media/deploy-scale/initiative-assignment.png)

Ao utilizar parâmetros de iniciativa, pode especificar o espaço de trabalho ou quaisquer outros detalhes uma vez para todas as definições políticas da iniciativa. 

![Parâmetros de iniciativa](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>Remediação
A iniciativa aplicar-se-á a cada máquina virtual à medida que for criada. Uma [tarefa de remediação](../governance/policy/how-to/remediate-resources.md) implementa as definições de política na iniciativa aos recursos existentes, pelo que permite criar configurações de diagnóstico para quaisquer recursos que já foram criados. Quando cria a atribuição utilizando o portal Azure, tem a opção de criar uma tarefa de remediação ao mesmo tempo. Consulte [recursos não conformes com a Política Azure](../governance/policy/how-to/remediate-resources.md) para obter detalhes sobre a reparação.

![Remediação da iniciativa](media/deploy-scale/initiative-remediation.png)


## <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
[O Azure Monitor para VMs](insights/vminsights-overview.md) é a principal ferramenta no Azure Monitor para monitorizar máquinas virtuais. Ativar o Monitor Azure para VMs instala tanto o agente Log Analytics como o agente Dependency. Em vez de executar estas tarefas manualmente, utilize a Política Azure para garantir que cada máquina virtual seja configurada à medida que a cria.

> [!NOTE]
> O Azure Monitor for VMs inclui uma funcionalidade chamada **Azure Monitor for VMs Policy Coverage** que permite descobrir e remediar VMs não conformes no seu ambiente. Pode utilizar esta funcionalidade em vez de trabalhar diretamente com a Azure Policy para VMs Azure e para máquinas virtuais híbridas ligadas ao Arco Azure. Para conjuntos de escala de máquina virtual Azure, deve criar a atribuição utilizando a Política Azure.
 

O Azure Monitor para VMs inclui as seguintes iniciativas incorporadas que instalam ambos os agentes para permitir uma monitorização completa. 

|Nome |Descrição |
|:---|:---|
|Ativar monitor Azure para VMs | Instala o agente log analytics e agente de dependência em VMs Azure e VMs híbridos ligados ao Arco Azure. |
|Ativar o Azure Monitor para conjuntos de escala de máquinas virtuais | Instala o agente Log Analytics e o agente De dependência no conjunto de escala de máquina virtual Azure. |


### <a name="virtual-machines"></a>Máquinas virtuais
Em vez de criar atribuições para estas iniciativas utilizando a interface Azure Policy, o Azure Monitor para VMs inclui uma funcionalidade que permite inspecionar o número de máquinas virtuais em cada âmbito para determinar se a iniciativa foi aplicada. Em seguida, pode configurar o espaço de trabalho e criar as atribuições necessárias utilizando essa interface.

Para obter mais informações sobre este processo, consulte [Enable Azure Monitor para VMs utilizando a Política Azure](./insights/vminsights-enable-policy.md).

![Azure Monitor para a política de VMs](media/deploy-scale/vminsights-policy.png)

### <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais
Para utilizar a Política Azure para permitir a monitorização de conjuntos de escala de máquinas virtuais, atribua o Enable Azure Monitor para a iniciativa **de conjuntos de escalas de máquinas virtuais** a um grupo de gestão, subscrição ou grupo de recursos da Azure, dependendo do âmbito dos seus recursos para monitorizar. Um [grupo de gestão](../governance/management-groups/overview.md) é particularmente útil para a política de deteção, especialmente se a sua organização tiver várias subscrições.

![Screenshot da página de iniciativa Atribuir no portal Azure. A definição de iniciativa está definida para permitir o Monitor Azure para conjuntos de escala de máquinas virtuais.](media/deploy-scale/virtual-machine-scale-set-assign-initiative.png)

Selecione o espaço de trabalho para onde os dados serão enviados. Este espaço de trabalho deve ter a solução *VMInsights* instalada como descrito em []() .

![Selecionar área de trabalho](media/deploy-scale/virtual-machine-scale-set-workspace.png)

Crie uma tarefa de reparação se tiver o conjunto de escala de máquina virtual existente que precisa de ser atribuído a esta política.

![Tarefa de reparação](media/deploy-scale/virtual-machine-scale-set-remediation.png)

### <a name="log-analytics-agent"></a>Agente do Log Analytics
Pode ter cenários em que pretende instalar o agente Log Analytics, mas não o agente de dependência. Não existe uma iniciativa integrada apenas para o agente, mas pode criar a sua própria com base nas definições de política incorporadas fornecidas pelo Azure Monitor para VMs.

> [!NOTE]
> Não haveria razão para implantar o agente de dependência por si só, uma vez que exige que o agente Log Analytics entregue os seus dados ao Azure Monitor.


|Nome |Descrição |
|-----|------------|
|Implementação de agente de Audit Log Analytics – imagem VM (OS) não cotada |Reporta vMs como incompatível se a imagem VM (OS) não estiver definida na lista e o agente não estiver instalado. |
|Implementar agente de Analítica de Registo para Os VMs linux |Implementar o agente Desafinado de Registos para VMs Linux se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |
|Implementar agente de Analítica de Registo para VMs do Windows |Implementar o agente Desajustado de Registo para VMs do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |
| [Pré-visualização]: O agente Log Analytics deve ser instalado nas suas máquinas Linux Azure Arc |Relata as máquinas híbridas Azure Arc como incompatíveis para os VMs Linux se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |
| [Pré-visualização]: O agente Log Analytics deve ser instalado nas suas máquinas Windows Azure Arc |Relata as máquinas híbridas Azure Arc como incompatível com VMs do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |
| [Pré-visualização]: Implementar o agente Desacutivamento de Registos para máquinas Linux Azure Arc |Implementar o agente Log Analytics para máquinas Azure Arc híbridas Linux se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |
| [Pré-visualização]: Implementar o agente Desacutivamento de Registos para máquinas Windows Azure Arc |Implementar o agente Log Analytics para máquinas Azure Arc híbridas do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |
|Implementação de agente de dependência de auditoria em conjuntos de escala de máquina virtual – imagem VM (OS) não cotada |Relata a escala de máquina virtual definida como incompatível se a imagem VM (OS) não estiver definida na lista e o agente não estiver instalado. |
|Implementação de agente de Audit Log Analytics em conjuntos de escala de máquina virtual – imagem VM (OS) não cotada |Relata a escala de máquina virtual definida como incompatível se a imagem VM (OS) não estiver definida na lista e o agente não estiver instalado. |
|Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual Linux |Implementar o agente Log Analytics para conjuntos de escala de máquina virtual Linux se a Imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |
|Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual do Windows |Implementar o agente Desajustado de Analítico para conjuntos de escala de máquina virtual do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |


## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre [a Política Azure.](../governance/policy/overview.md)
- Leia mais sobre [as definições de diagnóstico](platform/diagnostic-settings.md).