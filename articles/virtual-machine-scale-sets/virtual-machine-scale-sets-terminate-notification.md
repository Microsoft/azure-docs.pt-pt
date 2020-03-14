---
title: Encerrar notificação para casos de conjunto de máquinas virtuais Azure
description: Saiba como ativar a notificação de rescisão para casos de conjunto de máquinas virtuais Azure
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 6023e9bf7539b79446d0135ba731b61be166dd6e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250754"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Encerrar notificação para casos de conjunto de máquinas virtuais Azure
As instâncias definidas por escala podem optar por receber notificações de rescisão por exemplo e definir um prazo de atraso pré-definido para a operação de rescisão. A notificação de rescisão é enviada através do Serviço de Metadados Do Azure – [Eventos Agendados,](../virtual-machines/windows/scheduled-events.md)que fornece notificações e atrasa operações impactantes, como reboots e reimplantação. A solução adiciona mais um evento – Terminate – à lista de Eventos Agendados, e o atraso associado do evento de rescisão dependerá do limite de atraso especificado pelos utilizadores nas configurações do modelo de conjunto de escala.

Uma vez inscritos na funcionalidade, as instâncias de conjunto de escala não precisam de esperar que o tempo limite especificado expire antes de a instância ser eliminada. Depois de receber uma notificação de rescisão, a instância pode optar por ser eliminada a qualquer momento antes de expirar o prazo de rescisão.

## <a name="enable-terminate-notifications"></a>Ativar notificações de rescisão
Existem várias formas de permitir notificações de rescisão nas instâncias de conjunto de escala, conforme detalhado nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

Os seguintes passos permitem a notificação de fim quando criar um novo conjunto de escala. 

1. Vá a conjuntos de **escala de máquinas virtuais.**
1. Selecione **+ Adicione** para criar um novo conjunto de escala.
1. Vá ao separador **Gestão.** 
1. Localize a secção de rescisão de **Instância.**
1. Por **exemplo, notificação**de rescisão , selecione **On**.
1. Para o atraso de **rescisão (minutos)** , der por acaso o tempo de predefinição pretendido.
1. Quando terminar de criar o novo conjunto de escala, selecione **Review + crie** o botão. 

> [!NOTE]
> Não é possível definir notificações de rescisão em conjuntos de escala existentes no portal Azure

### <a name="rest-api"></a>API REST

O exemplo seguinte permite encerrar a notificação no modelo de conjunto de escala.

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

O bloco acima especifica um prazo de 5 minutos (conforme indicado pela *PT5M*) para qualquer operação de rescisão em todas as instâncias do seu conjunto de escala. O campo *não BeforeTimeout* pode ter qualquer valor entre 5 e 15 minutos no formato ISO 8601. Pode alterar o prazo de predefinição para a operação de rescisão modificando a propriedade *não BeforeTimeout* em *'terminateNotificationPerfil'* acima descrita.

Depois de ativar *o siteEventsProfile* no modelo de conjunto de escala e definir o *não BeforeTimeout*, atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

> [!NOTE]
>As notificações de rescisão em casos de conjunto de escala só podem ser ativadas com a versão API 2019-03-01 e acima

### <a name="azure-powershell"></a>Azure PowerShell
Ao criar um novo conjunto de escala, pode ativar notificações de rescisão na escala definida utilizando o cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig)

Este script de amostra sai através da criação de um conjunto de escala e recursos associados usando o ficheiro de configuração: [Criar um conjunto completo](./scripts/powershell-sample-create-complete-scale-set.md)de escala de máquina virtual . Pode fornecer a notificação de fim de configuração adicionando os parâmetros *TerminateScheduleScheduledEvents* e *TerminateScheduledEventNotBeforeTimeoutMinutes* ao objeto de configuração para criar um conjunto de escala. O exemplo seguinte permite que a funcionalidade seja de 10 minutos.

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
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
O exemplo acima permite encerrar notificações num conjunto de escala existente e define um prazo de 15 minutos para o evento de rescisão.

Depois de permitir eventos programados no modelo de conjunto de escala e definir o tempo de paragem, atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

O exemplo que se segue é permitir a notificação de rescisão, criando um novo conjunto de escala.

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

O exemplo acima cria primeiro um grupo de recursos, em seguida, cria um novo conjunto de escala com notificações de terminação ativadas para um tempo de tempo padrão de 10 minutos.

O exemplo que se segue é permitir a notificação de rescisão num conjunto de escala existente.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Obter notificações de rescisão

As notificações de rescisão são entregues através [de Eventos Agendados](../virtual-machines/windows/scheduled-events.md), que é um Serviço de Metadados Azure. O serviço De metadados Azure expõe informações sobre o funcionamento de Máquinas Virtuais utilizando um ponto final REST acessível a partir do VM. A informação está disponível através de um IP não-repreensível para que não seja exposta fora do VM.

Os Eventos Agendados estão ativados para a sua escala definida pela primeira vez que faz um pedido de eventos. Pode esperar uma resposta atrasada na sua primeira chamada de até dois minutos. Consulta periodicamente o ponto final para detetar os próximos eventos de manutenção e o estado das atividades de manutenção em curso.

Os Eventos Agendados são desativados para o seu conjunto de escala se as instâncias de conjunto de escala não fizerem um pedido por 24 horas.

### <a name="endpoint-discovery"></a>Descoberta de Endpoint
Para VNET ativado VMs, o Serviço de Metadados está disponível a partir de um IP estático não resaída, 169.254.169.254.

O ponto final completo para a versão mais recente dos Eventos Agendados é:
> "http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01"

### <a name="query-response"></a>Resposta de consulta
Uma resposta contém uma série de eventos programados. Uma matriz vazia significa que não há eventos agendados.

No caso de eventos agendados, a resposta contém uma série de eventos. Para um evento "Terminate", a resposta será a seguinte:
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
O *DocumentIncarnation* é um ETag e fornece uma maneira fácil de inspecionar se a carga de eventos mudou desde a última consulta.

Para obter mais informações sobre cada um dos campos acima, consulte a documentação de Eventos Agendados para [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) e [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Responder a eventos
Assim que tiver conhecimento de um evento próximo e ter concluído a sua lógica para uma paragem graciosa, poderá aprovar o evento em destaque fazendo uma chamada POST para o serviço de metadados com o EventId. A chamada POST indica ao Azure que pode continuar com a eliminação do VM.

Abaixo está o json esperado no post request body. O pedido deve conter uma lista de Pedidos de Início. Cada StartRequest contém o EventId para o evento que pretende agilizar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Certifique-se de que cada VM no conjunto de escala só aprova o EventID relevante apenas para esse VM. Um VM pode obter o seu próprio nome VM através de [metadados de instância](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Este nome assume o formulário "{scale-set-name}_{instance-id}", e será apresentado na secção "Recursos" da resposta de consulta acima descrita.

Também pode consultar scripts de amostras para consulta e resposta a eventos usando [PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) e [Python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Sugestões e melhores práticas
-   Encerrar notificações apenas em operações de 'eliminar' – Todas as operações de eliminação (eliminação manual ou scale-in iniciado por escala automática) gerarão eventos Desativados se o seu conjunto de escala tiver *programado EventosPerfil* ativado. Outras operações como reiniciar, reimagem, reimplantação e stop/deallocate não geram eventos Desconhecer. As notificações de rescisão não podem ser ativadas para VMs de baixa prioridade.
-   Não há espera obrigatória para o tempo limite – Pode iniciar a operação de rescisão a qualquer momento após a recebida do evento e antes de expirar o tempo *de não antes* do tempo do evento.
-   Eliminação obrigatória no timeout – Não existe qualquer capacidade de alargar o valor do tempo após a geração de um evento. Uma vez expirado o prazo, o evento pendente de terminação será processado e o VM será eliminado.
-   Valor de tempo limite modificável – Pode modificar o valor de tempo limite a qualquer momento antes de uma instância ser eliminada, modificando a propriedade *não BeforeTimeout* no modelo de conjunto de escala e atualizando as instâncias VM para o modelo mais recente.
-   Aprove todas as eliminações pendentes – Se houver uma eliminação pendente no VM_1 que não seja aprovado, e tiver aprovado outro evento de rescisão no VM_2, então VM_2 não seja apagado até que o evento de rescisão para VM_1 seja aprovado, ou o seu tempo decorrido. Uma vez aprovado o evento de rescisão para VM_1, então tanto VM_1 como VM_2 são eliminados.
-   Aprove todas as exclusões simultâneas – Alargando o exemplo acima referido, se VM_1 e VM_2 tiverem o mesmo *Não Antes* do tempo, então ambos os eventos de terminação devem ser aprovados ou nenhum VM é eliminado antes do prazo expirar.

## <a name="troubleshoot"></a>Resolução de problemas
### <a name="failure-to-enable-scheduledeventsprofile"></a>Falha em ativar o Perfil de Eventos agendados
Se tiver um erro 'BadRequest' com uma mensagem de erro indicando "Não consegui encontrar o membro 'ScheduledEventsProfile' no objeto do tipo 'VirtualMachineProfile'", verifique a versão API utilizada para as operações de conjunto de escala. É necessária a versão Compute API **2019-03-01** ou superior. 

### <a name="failure-to-get-terminate-events"></a>Falha em obter eventos de rescisão
Se não está a receber eventos **de Terminação** através de Eventos Agendados, verifique a versão API utilizada para obter os eventos. É necessário um serviço de metadados Aversão API **2019-01-01** ou superior é necessário para eventos Desativados.
>"http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01"

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Terminar evento com incorreto não antes do tempo  
Depois de ativar *o siteEventsProfile* no modelo de conjunto de escala e definir o *não BeforeTimeout*, atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

## <a name="next-steps"></a>Passos seguintes
Aprenda a implementar a [sua aplicação](virtual-machine-scale-sets-deploy-app.md) em conjuntos de escala de máquinas virtuais.
