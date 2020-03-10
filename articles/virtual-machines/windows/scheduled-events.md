---
title: Eventos agendados para VMs windows em Azure
description: Eventos agendados utilizando o serviço de Metadados Azure para as suas máquinas virtuais Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 2b3aa5d50822863e3aa46fcf9970e0b3e67a6f69
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944481"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Serviço de Metadados Azure: Eventos agendados para VMs do Windows

Eventos Agendados é um Serviço de Metadados Azure que dá tempo à sua aplicação para se preparar para a manutenção de máquinas virtuais. Fornece informações sobre os próximos eventos de manutenção (por exemplo, reiniciar) para que a sua aplicação possa preparar-se para eles e limitar a perturbação. Está disponível para todos os tipos de Máquinas Virtuais Azure, incluindo PaaS e IaaS tanto no Windows como no Linux. 

Para obter informações sobre eventos agendados no Linux, consulte [eventos agendados para VMs Linux](../linux/scheduled-events.md).

> [!Note] 
> Os Eventos Agendados estão geralmente disponíveis em todas as regiões do Azure. Consulte a [Versão e a Disponibilidade da Região](#version-and-region-availability) para obter informações mais recentes sobre o lançamento.

## <a name="why-scheduled-events"></a>Por que eventos agendados?

Muitas aplicações podem beneficiar do tempo para se preparar para a manutenção virtual da máquina. O tempo pode ser usado para executar tarefas específicas de aplicação que melhorem a disponibilidade, fiabilidade e serviço, incluindo: 

- Posto de controlo e restauro
- Drenagem de ligação
- Falha na réplica primária 
- Remoção da piscina de equilíbrio de carga
- Exploração madeireira de eventos
- Encerramento gracioso 

Utilizando Eventos Agendados, a sua aplicação pode descobrir quando a manutenção ocorrerá e desencadear tarefas para limitar o seu impacto. Permitir eventos programados dá à sua máquina virtual um tempo mínimo antes da atividade de manutenção ser realizada. Consulte a secção de Agendamento de Eventos abaixo para mais detalhes.

Eventos Agendados fornece eventos nos seguintes casos de utilização:
- [Plataforma iniciada manutenção](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (por exemplo, VM reboot, migração ao vivo ou atualizações de preservação de memória para hospedeiro)
- A máquina virtual está a funcionar com hardware de [anfitrião degradado](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) que se prevê que falhe em breve
- Manutenção iniciada pelo utilizador (por exemplo, reinicia ou reimplanta um VM)
- [Spot VM](spot-vms.md) e [Spot escala definir](../../virtual-machine-scale-sets/use-spot.md) despejos de instância

## <a name="the-basics"></a>As noções básicas  

O serviço De metadados Azure expõe informações sobre o funcionamento de Máquinas Virtuais utilizando um ponto final REST acessível a partir do VM. A informação está disponível através de um IP não resaída para que não seja exposta fora do VM.

### <a name="endpoint-discovery"></a>Descoberta endpoint
Para VNET ativado VMs, o serviço de metadados está disponível a partir de um IP estático não resaída, `169.254.169.254`. O ponto final completo para a versão mais recente dos Eventos Agendados é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Se a Máquina Virtual não for criada dentro de uma Rede Virtual, os casos padrão para serviços na nuvem e VMs clássicos, é necessária uma lógica adicional para descobrir o endereço IP para usar. Consulte esta amostra para aprender a [descobrir o ponto final do hospedeiro](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Versão e Disponibilidade da Região
O Serviço de Eventos Agendados é versão. As versões são obrigatórias e a versão atual é `2019-01-01`.

| Versão | Tipo de Versão | Regiões | Release Notes (Notas de Lançamento) | 
| - | - | - | - |
| 2019-01-01 | Disponibilidade Geral | Todas | <li> Suporte adicional para conjuntos de escala de máquina virtual 'Terminate' |
| 2017-11-01 | Disponibilidade Geral | Todas | <li> Suporte adicional para o evento de despejo Spot VM 'Preempt'<br> | 
| 2017-08-01 | Disponibilidade Geral | Todas | <li> Sublinhado de recursos para VMs IaaS<br><li>Requisito de cabeçalho de metadados imposto para todos os pedidos | 
| 2017-03-01 | Pré-visualização | Todas |<li>Versão inicial |

> [!NOTE] 
> Os lançamentos anteriores de pré-visualização de eventos agendados suportavam {mais recente} como a versão api. Este formato já não é suportado e será depreciado no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Eventos Agendados de Habilitação e Desativação
Os Eventos Agendados estão programados para o seu serviço na primeira vez que faz um pedido de eventos. Deve esperar uma resposta atrasada na sua primeira chamada de até dois minutos. Deve consultar periodicamente o ponto final para detetar os próximos eventos de manutenção, bem como o estado das atividades de manutenção que estão a ser realizadas.

Os Eventos Agendados são desativados para o seu serviço se não fizer um pedido por 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção Iniciada do Utilizador
O utilizador iniciou a manutenção virtual da máquina através do portal Azure, API, CLI ou PowerShell resulta num evento agendado. Isto permite-lhe testar a lógica de preparação de manutenção na sua aplicação e permite que a sua aplicação se prepare para a manutenção iniciada pelo utilizador.

Reiniciar uma máquina virtual agenda um evento com tipo `Reboot`. A recolocação de uma máquina virtual marca um evento com tipo `Redeploy`.

## <a name="using-the-api"></a>Utilização da API

### <a name="headers"></a>Cabeçalhos
Ao consultar o Serviço de Metadados, deve fornecer o cabeçalho `Metadata:true` para garantir que o pedido não foi redirecionado involuntariamente. O cabeçalho `Metadata:true` é necessário para todos os pedidos de eventos agendados. A não intenção de incluir o cabeçalho no pedido resultará numa resposta de Pedido De mau pedido do Serviço de Metadados.

### <a name="query-for-events"></a>Consulta para eventos
Você pode consultar eventos agendados simplesmente fazendo a seguinte chamada:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 -H @{"Metadata"="true"}
```

Uma resposta contém uma série de eventos programados. Uma matriz vazia significa que não há eventos agendados.
No caso de eventos agendados, a resposta contém uma série de eventos: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
O DocumentIncarnation é um ETag e fornece uma maneira fácil de inspecionar se a carga de eventos mudou desde a última consulta.

### <a name="event-properties"></a>Propriedades do Evento
|Propriedade  |  Descrição |
| - | - |
| EventId | Identificador globalmente único para este evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Tipo de evento | Impacto que este evento causa. <br><br> Valores: <br><ul><li> `Freeze`: A Máquina Virtual está programada para parar por alguns segundos. A CPU e a conectividade da rede podem ser suspensas, mas não há impacto na memória ou nos ficheiros abertos. <li>`Reboot`: A Máquina Virtual está programada para reiniciar (a memória não persistente é perdida). <li>`Redeploy`: A Máquina Virtual está programada para se mudar para outro nó (os discos efémeros estão perdidos). <li>`Preempt`: A Máquina Virtual spot está a ser eliminada (os discos efémeros estão perdidos). <li> `Terminate`: A Máquina Virtual está programada para ser eliminada. |
| ResourceType | Tipo de recurso que este evento tem impacto. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que este evento impacta. Isto é garantido que contenha máquinas de, no máximo, um Domínio de [Atualização,](manage-availability.md)mas pode não conter todas as máquinas da UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Estado do Evento | Estado deste evento. <br><br> Valores: <ul><li>`Scheduled`: Este evento está programado para começar após o tempo especificado na propriedade `NotBefore`.<li>`Started`: Este evento já começou.</ul> Nunca é concedido qualquer `Completed` ou estatuto semelhante; o evento deixará de ser devolvido quando o evento estiver concluído.
| Não antes| Tempo após o qual este evento pode começar. <br><br> Exemplo: <br><ul><li> Seg, 19 set 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Agendamento de eventos
Cada evento está agendado um tempo mínimo no futuro com base no tipo de evento. Desta vez reflete-se na propriedade `NotBefore` de um evento. 

|Tipo de evento  | Aviso Mínimo |
| - | - |
| Congelar| 15 minutos |
| Reiniciar | 15 minutos |
| Voltar a implementar | 10 minutos |
| Preempt | 30 segundos |
| Terminar | [Configurar o utilizador:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)5 a 15 minutos |

> [!NOTE] 
> Em alguns casos, o Azure é capaz de prever a falha do hospedeiro devido ao hardware degradado e tentará mitigar a perturbação do seu serviço, agendando uma migração. As máquinas virtuais afetadas receberão um evento agendado com uma `NotBefore` que normalmente é de alguns dias no futuro. O tempo real varia consoante a avaliação prevista do risco de falha. O Azure tenta dar 7 dias de antecedência quando possível, mas o tempo real varia e pode ser menor se a previsão for de que há uma alta probabilidade de o hardware falhar iminentemente. Para minimizar o risco para o seu serviço caso o hardware falhe antes de o sistema iniciar a migração, recomenda-se a reutilização da sua máquina virtual o mais rapidamente possível.

### <a name="event-scope"></a>Âmbito do evento     
Os eventos agendados são entregues a:
 - Máquinas virtuais autónomas
 - Todas as máquinas virtuais em um serviço de nuvem      
 - Todas as máquinas virtuais em um conjunto de disponibilidade      
 - Todas as máquinas virtuais num grupo de colocação de conjunto sinuoso.         

Como resultado, deve verificar o campo `Resources` no caso para identificar quais VMs vão ser impactados. 

### <a name="starting-an-event"></a>Iniciar um evento 

Depois de ter sabido de um evento próximo e ter completado a sua lógica para uma paragem graciosa, pode aprovar o evento em destaque fazendo uma chamada `POST` para o serviço de metadados com o `EventId`. Isto indica ao Azure que pode encurtar o tempo mínimo de notificação (quando possível). 

Segue-se o json esperado no `POST` órgão de pedido. O pedido deve conter uma lista de `StartRequests`. Cada `StartRequest` contém o `EventId` para o evento que pretende acelerar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Reconhecer um evento permite que o evento prossiga para todos os `Resources` no evento, e não apenas a máquina virtual que reconhece o evento. Pode, portanto, optar por eleger um líder para coordenar o reconhecimento, que pode ser tão simples como a primeira máquina no campo `Resources`.


## <a name="powershell-sample"></a>Exemplo do PowerShell 

A amostra seguinte consulta o serviço de metadados para eventos agendados e aprova cada evento em destaque.

```powershell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2019-01-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Passos Seguintes 

- Assista a uma [demonstração de eventos agendada](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) saqueada na sexta-feira do Azure. 
- Reveja as amostras de código de eventos agendados no [Azure Instance Metadata Eventos Agendados GitHub Repositório](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Leia mais sobre as APIs disponíveis no [serviço de Metadados de Exemplo](instance-metadata-service.md).
- Saiba mais sobre [a manutenção planeada para máquinas virtuais Windows em Azure](planned-maintenance.md).
