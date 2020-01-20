---
title: Terminar notificação para instâncias do conjunto de dimensionamento de máquinas virtuais do Azure
description: Saiba como habilitar a notificação de término para instâncias do conjunto de dimensionamento de máquinas virtuais do Azure
author: shandilvarun
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: vashan
ms.openlocfilehash: a1b1e07fa0622ae25d8086ec65827816ec52a5ce
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271741"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances-preview"></a>Terminar notificação para instâncias do conjunto de dimensionamento de máquinas virtuais do Azure (versão prévia)
As instâncias do conjunto de dimensionamento podem optar por receber notificações de encerramento de instância e definir um tempo limite de atraso predefinido para a operação de encerramento. A notificação de encerramento é enviada por meio do serviço de metadados do Azure – [eventos agendados](../virtual-machines/windows/scheduled-events.md), que fornece notificações e atraso de operações de impacto, como reinicializações e reimplantação. A solução de visualização adiciona outro evento – Terminate – à lista de Eventos Agendados, e o atraso associado do evento Terminate dependerá do limite de atraso conforme especificado pelos usuários em suas configurações de modelo de conjunto de dimensionamento.

Depois de registrado no recurso, as instâncias do conjunto de dimensionamento não precisam aguardar até que o tempo limite especificado expire antes que a instância seja excluída. Depois de receber uma notificação de encerramento, a instância pode optar por ser excluída a qualquer momento antes de expirar o tempo limite de encerramento.

> [!IMPORTANT]
> A notificação de encerramento para instâncias do conjunto de dimensionamento está atualmente em visualização pública. Nenhum procedimento de aceitação é necessário para usar a funcionalidade de visualização pública descrita abaixo.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-terminate-notifications"></a>Habilitar notificações de término
Há várias maneiras de habilitar notificações de encerramento em suas instâncias do conjunto de dimensionamento, conforme detalhado nos exemplos abaixo.

### <a name="rest-api"></a>API REST

O exemplo a seguir habilita a notificação de encerramento no modelo do conjunto de dimensionamento.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

O bloco acima Especifica um atraso de tempo limite de 5 minutos (conforme indicado por *PT5M*) para qualquer operação de término em todas as instâncias em seu conjunto de dimensionamento. O campo *notBeforeTimeout* pode usar qualquer valor entre 5 e 15 minutos no formato ISO 8601. Você pode alterar o tempo limite padrão para a operação de término modificando a propriedade *notBeforeTimeout* em *terminateNotificationProfile* descrito acima.

Depois de habilitar o *scheduledEventsProfile* no modelo do conjunto de dimensionamento e definir o *notBeforeTimeout*, atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

> [!NOTE]
>As notificações de término em instâncias do conjunto de dimensionamento só podem ser habilitadas com a versão de API 2019-03-01 e superior

### <a name="azure-powershell"></a>Azure PowerShell
Ao criar um novo conjunto de dimensionamento, você pode habilitar notificações de encerramento no conjunto de dimensionamento usando o cmdlet [New-AzVmss](/powershell/module/az.compute/new-azvmss) .

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents
```

O exemplo acima cria um novo conjunto de dimensionamento com notificações de término habilitadas com um tempo limite padrão de 5 minutos. Ao criar um novo conjunto de dimensionamento, o parâmetro *TerminateScheduledEvents* não requer um valor. Para alterar o valor de tempo limite, especifique o tempo limite desejado por meio do parâmetro *TerminateScheduledEventNotBeforeTimeoutInMinutes* .

Use o cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) para habilitar notificações de encerramento em um conjunto de dimensionamento existente.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
O exemplo acima habilita as notificações de término em um conjunto de dimensionamento existente e define um tempo limite de 15 minutos para o evento Terminate.

Depois de habilitar os eventos agendados no modelo do conjunto de dimensionamento e definir o tempo limite, atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

## <a name="get-terminate-notifications"></a>Obter notificações de término

As notificações de término são entregues por meio de [eventos agendados](../virtual-machines/windows/scheduled-events.md), que é um serviço de metadados do Azure. O serviço de metadados do Azure expõe informações sobre a execução de máquinas virtuais usando um ponto de extremidade REST acessível de dentro da VM. As informações estão disponíveis por meio de um IP não roteável para que não seja exposta fora da VM.

Eventos Agendados está habilitado para seu conjunto de dimensionamento na primeira vez que você fizer uma solicitação de eventos. Você pode esperar uma resposta atrasada em sua primeira chamada de até dois minutos. Consulte o ponto de extremidade periodicamente para detectar futuros eventos de manutenção e o status das atividades de manutenção em andamento.

Eventos Agendados está desabilitado para seu conjunto de dimensionamento se as instâncias do conjunto de dimensionamento não fizerem uma solicitação por 24 horas.

### <a name="endpoint-discovery"></a>Descoberta de ponto de extremidade
Para VMs habilitadas para VNET, o serviço de metadados está disponível de um IP não roteável estático, 169.254.169.254.

O ponto de extremidade completo para a versão mais recente do Eventos Agendados para essa visualização é:
> "http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01"

### <a name="query-response"></a>Resposta de consulta
Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que não há eventos agendados no momento.

No caso em que há eventos agendados, a resposta contém uma matriz de eventos. Para um evento "Terminate", a resposta terá a seguinte aparência:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
O DocumentIncarnation é uma ETag e fornece uma maneira fácil de inspecionar se a carga de eventos foi alterada desde a última consulta.

Para obter mais informações sobre cada um dos campos acima, consulte a documentação do Eventos Agendados para [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) e [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Responder a eventos
Depois de aprender sobre um evento futuro e concluir sua lógica para desligamento normal, você pode aprovar o evento pendente fazendo uma chamada POST para o serviço de metadados com EventId. A chamada POST indica ao Azure que ele pode continuar com a exclusão da VM.

Abaixo está o JSON esperado no corpo da solicitação POST. A solicitação deve conter uma lista de StartRequests. Cada StartRequest contém o EventId para o evento que você deseja acelerar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Verifique se cada VM no conjunto de dimensionamento está apenas aprovando o EventID relevante somente para essa VM. Uma VM pode obter seu próprio nome de VM [por meio de metadados de instância](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Esse nome usa a forma "{scale-Set-name} _ {Instance-ID}" e será exibido na seção "recursos" da resposta de consulta descrita acima.

Você também pode consultar scripts de exemplos para consultar e responder a eventos usando o [PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) e o [Python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Sugestões e melhores práticas
-   Encerrar notificações somente em operações ' excluir ' – todas as operações de exclusão (exclusão manual ou redução iniciada pelo dimensionamento automático) gerarão eventos de término se o conjunto de dimensionamento tiver *scheduledEventsProfile* habilitado. Outras operações, como reinicializar, refazer imagem, reimplantar e parar/desalocar, não geram eventos Terminate. As notificações de término não podem ser habilitadas para VMs de baixa prioridade.
-   Nenhuma espera obrigatória para tempo limite – você pode iniciar a operação de término a qualquer momento depois que o evento tiver sido recebido e antes de o tempo de falta *antes* do evento ser expirado.
-   Exclusão obrigatória no tempo limite – a visualização não fornece qualquer recurso de estender o valor de tempo limite após a geração de um evento. Quando o tempo limite expirar, o evento de encerramento pendente será processado e a VM será excluída.
-   Valor de tempo limite modificável – você pode modificar o valor de tempo limite a qualquer momento antes que uma instância seja excluída, modificando a propriedade *notBeforeTimeout* no modelo do conjunto de dimensionamento e atualizando as instâncias de VM para o modelo mais recente.
-   Aprovar todas as exclusões pendentes – se houver uma exclusão pendente em VM_1 que não está aprovada e você tiver aprovado outro evento de encerramento em VM_2, VM_2 não será excluído até que o evento de encerramento para VM_1 seja aprovado ou seu tempo limite tenha decorrido. Depois de aprovar o evento Terminate para VM_1, os VM_1 e VM_2 são excluídos.
-   Aprovar todas as exclusões simultâneas – estendendo o exemplo acima, se VM_1 e VM_2 tiverem o mesmo tempo *antes* , então ambos os eventos de término deverão ser aprovados ou nenhuma VM será excluída antes que o tempo limite expire.

## <a name="troubleshoot"></a>Resolução de problemas
### <a name="failure-to-enable-scheduledeventsprofile"></a>Falha ao habilitar scheduledEventsProfile
Se você receber um erro ' BadRequest ' com uma mensagem de erro informando "não foi possível encontrar o membro ' scheduledEventsProfile ' no objeto do tipo ' VirtualMachineProfile '", verifique a versão da API usada para as operações do conjunto de dimensionamento. A versão de API de computação **2019-03-01** ou superior é necessária para esta visualização.

### <a name="failure-to-get-terminate-events"></a>Falha ao obter eventos de encerramento
Se você não estiver obtendo eventos de **término** por meio de eventos agendados, verifique a versão da API usada para obter os eventos. A API do serviço de metadados versão **2019-01-01** ou superior é necessária para eventos de término.
>"http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01"

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Obtendo evento de encerramento com hora incorreta  
Depois de habilitar o *scheduledEventsProfile* no modelo do conjunto de dimensionamento e definir o *notBeforeTimeout*, atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

## <a name="next-steps"></a>Passos seguintes
Saiba como [implantar seu aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais.
