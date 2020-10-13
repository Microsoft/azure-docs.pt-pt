---
title: Terminar a notificação das instâncias dos conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como permitir a notificação de rescisão de instâncias de escala de máquina virtual Azure
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: d4b31eb59ed0bae2afe408546ece66eacade9ddb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90603837"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Terminar a notificação das instâncias dos conjuntos de dimensionamento de máquinas virtuais do Azure
As instâncias definidas em escala podem optar por receber notificações de rescisão de casos e definir um prazo de atraso pré-definido para a operação de encerramento. A notificação de rescisão é enviada através do Azure Metadata Service – [Eventos Agendados,](../virtual-machines/windows/scheduled-events.md)que fornece notificações para e atrasar operações impactantes como reboots e redistribuição. A solução adiciona mais um evento – Terminate – à lista de Eventos Agendados, e o atraso associado do evento de terminação dependerá do limite de atraso especificado pelos utilizadores nas configurações do modelo de escala.

Uma vez inscritos na funcionalidade, as instâncias definidas em escala não precisam de esperar que o tempo limite especificado expire antes de a instância ser eliminada. Depois de receber uma notificação de terminação, a instância pode optar por ser eliminada a qualquer momento antes do fim do tempo limite de terminação.

## <a name="enable-terminate-notifications"></a>Permitir notificações terminais
Existem várias formas de permitir notificações de rescisão na sua escala, conforme detalhado nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

Os seguintes passos permitem a notificação de terminação ao criar um conjunto de escala nova. 

1. Aceda aos **conjuntos de escala de máquina virtual**.
1. **Selecione + Adicione** para criar um conjunto de escala nova.
1. Vá ao **separador Gestão.** 
1. Localize a secção **de terminação de instância.**
1. Por **exemplo, notificação de rescisão**, selecione **On**.
1. Para **o atraso de rescisão (minutos)**, desate o tempo limite de tempo pretendido.
1. Quando terminar de criar o novo conjunto de escala, selecione **Rever + criar** o botão. 

> [!NOTE]
> Não é possível definir notificações de terminação em conjuntos de escala existentes no portal Azure

### <a name="rest-api"></a>API REST

O exemplo a seguir permite a notificação de terminação no modelo de conjunto de escala.

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

O bloco acima especifica um atraso de tempo de 5 minutos (como indicado pelo *PT5M*) para qualquer operação de terminação em todas as instâncias do seu conjunto de escala. O campo *nãoBeforeTimeout* pode ter qualquer valor entre 5 e 15 minutos no formato ISO 8601. Pode alterar o tempo limite padrão para a operação de terminação modificando a propriedade *nãoBeforeTimeout* sob *terminaçãoNotificationProfile* descrito acima.

Depois de ativar *o programadoEventsProfile* no modelo de conjunto de escala e definir o *nãoBeforeTimeout,* atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

> [!NOTE]
>As notificações terminadas em instâncias definidas em escala só podem ser ativadas com a versão API 2019-03-01 e acima

### <a name="azure-powershell"></a>Azure PowerShell
Ao criar um novo conjunto de escala, pode ativar notificações de rescisão na escala definida utilizando o cmdlet [New-AzVmsConfig.](/powershell/module/az.compute/new-azvmssconfig)

Este script de amostra percorre a criação de um conjunto de escala e recursos associados utilizando o ficheiro de configuração: [Criar um conjunto completo de escala de máquina virtual](./scripts/powershell-sample-create-complete-scale-set.md). Pode fornecer a notificação de terminação de configuração adicionando os parâmetros *TerminateScheduledEvents* e *TerminateScheduledEventNotBeforeTimeoutInMinutes* ao objeto de configuração para criar conjunto de escala. O exemplo a seguir permite a funcionalidade com um tempo de atraso de 10 minutos.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Utilize o cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) para permitir notificações de rescisão num conjunto de escala existente.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
O exemplo acima permite terminar as notificações num conjunto de escala existente e define um intervalo de 15 minutos para o evento de terminação.

Depois de permitir eventos programados no modelo de definição de escala e definir o tempo limite, atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

O exemplo a seguir é permitir a notificação de rescisão enquanto cria um novo conjunto de escala.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

O exemplo acima cria primeiro um grupo de recursos, em seguida, cria um conjunto de escala nova com notificações terminantes ativadas para um tempo limite de 10 minutos.

O exemplo a seguir é permitir a notificação de rescisão num conjunto de escala existente.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Receber notificações de rescisão

As notificações terminadas são entregues através [de Eventos Agendados,](../virtual-machines/windows/scheduled-events.md)que é um Serviço de Metadados Azure. O serviço Azure Metadata expõe informações sobre a execução de Máquinas Virtuais utilizando um ponto final REST acessível a partir do VM. A informação está disponível através de um IP não-encaminhável para que não seja exposta fora do VM.

Eventos Agendados estão habilitados para a sua escala definida na primeira vez que faz um pedido de eventos. Pode esperar uma resposta atrasada na sua primeira chamada de até dois minutos. Consulte periodicamente o ponto final para detetar os próximos eventos de manutenção e o estado das atividades de manutenção em curso.

Eventos Agendados são desativados para a sua escala definida se as instâncias definidas na escala não fizerem um pedido de 24 horas.

### <a name="endpoint-discovery"></a>Descoberta de ponto final
Para VMs ativados por VNET, o Serviço de Metadados está disponível a partir de um IP estático não roteado, 169.254.169.254.

O ponto final completo para a versão mais recente de Eventos Agendados é:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Resposta de consulta
Uma resposta contém uma série de eventos programados. Uma matriz vazia significa que não há eventos agendados.

No caso de existirem eventos agendados, a resposta contém uma série de eventos. Para um evento "Terminate", a resposta será a seguinte:
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
O *DocumentIncarnation* é um ETag e fornece uma maneira fácil de inspecionar se a carga útil de Eventos mudou desde a última consulta.

Para obter mais informações sobre cada um dos campos acima, consulte a documentação de Eventos Agendados para [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) e [Linux.](../virtual-machines/linux/scheduled-events.md#event-properties)

### <a name="respond-to-events"></a>Responder aos acontecimentos
Assim que tiver conhecimento de um evento que se avizinha e tiver completado a sua lógica de paragem graciosa, poderá aprovar o evento pendente fazendo uma chamada POST para o serviço de metadados com o EventId. A chamada POST indica ao Azure que pode continuar com a eliminação de VM.

Abaixo está o json esperado no órgão de pedido do POST. O pedido deve conter uma lista de StartRequests. Cada StartRequest contém o EventId para o evento que pretende acelerar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Certifique-se de que cada VM no conjunto de escala só está a aprovar o EventID relevante apenas para esse VM. Um VM pode obter o seu próprio nome VM [através de metadados de exemplo](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Este nome toma o formulário "{scale-set-name}_{instance-id}", e será exibido na secção "Recursos" da resposta de consulta acima descrita.

Também pode consultar scripts de amostras para consulta e resposta aos eventos [Python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Sugestões e melhores práticas
-   Terminar as notificações apenas em operações de 'excluir' – Todas as operações de eliminação (eliminação manual ou escala iniciada por autoescala) gerarão eventos de terminação se o seu conjunto de escala tiver *programadoEventsProfile* ativado. Outras operações como reboot, reimagem, recolocação e stop/deallocate não geram eventos de terminação. As notificações terminadas não podem ser ativadas para VMs de baixa prioridade.
-   Sem espera obrigatória para o tempo limite – Pode iniciar a operação de terminação a qualquer momento após a recebida e antes do prazo do evento não ser *possível* expirar.
-   Eliminação obrigatória no timeout – Não existe qualquer capacidade de prolongar o valor do tempo limite após a geração de um evento. Uma vez expirado o prazo, o evento de terminação pendente será processado e o VM será eliminado.
-   Valor de tempo limite modificável – Pode modificar o valor do tempo limite a qualquer momento antes de uma instância ser eliminada, modificando a propriedade *nãoBeforeTimeout* no modelo definido em escala e atualizando as instâncias VM para o modelo mais recente.
-   Aprove todas as eliminações pendentes – Se houver uma eliminação pendente em VM_1 que não esteja aprovada, e tiver aprovado outro evento de terminação no VM_2, então VM_2 não é apagado até que o evento de terminação para VM_1 seja aprovado, ou o seu tempo limite tenha decorrido. Uma vez aprovado o evento de terminação para VM_1, então tanto VM_1 como VM_2 são eliminados.
-   Aprovar todas as eliminações simultâneas – Alargando o exemplo acima, se VM_1 e VM_2 tiverem o mesmo *Não Antes do* tempo, então ambos os eventos terminam devem ser aprovados ou nenhum VM é eliminado antes do prazo expirar.

## <a name="troubleshoot"></a>Resolução de problemas
### <a name="failure-to-enable-scheduledeventsprofile"></a>Falha na ativação programadoEventsProfile
Se obtém um erro de 'BadRequest' com uma mensagem de erro indicando "Não foi possível encontrar o membro 'ProgramadoEventsProfile' no objeto do tipo 'VirtualMachineProfile'", verifique a versão API utilizada para as operações definidas na escala. É necessária a versão API **compute 2019-03-01** ou superior. 

### <a name="failure-to-get-terminate-events"></a>Falha na realização de eventos de terminação
Se não estiver a receber eventos **de terminação** através de Eventos Agendados, verifique a versão API utilizada para obter os eventos. A versão API do Serviço de Metadados **2019-01-01** ou superior é necessária para eventos terminais.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Obter evento de rescisão com incorreto não Antes do tempo  
Depois de ativar *o programadoEventsProfile* no modelo de conjunto de escala e definir o *nãoBeforeTimeout,* atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

## <a name="next-steps"></a>Passos seguintes
Saiba como implementar a [sua aplicação](virtual-machine-scale-sets-deploy-app.md) em conjuntos de escala de máquina virtual.
