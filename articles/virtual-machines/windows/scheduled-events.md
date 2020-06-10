---
title: Eventos Agendados para VMs windows em Azure
description: Eventos regulares utilizando o serviço Azure Metadata para as suas máquinas virtuais Windows.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: mimckitt
ms.openlocfilehash: dda71869411cbb37a24c2d39ef1d78563cfe6cab
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84604104"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Serviço de Metadados Azure: Eventos agendados para VMs windows

Eventos Agendados é um Serviço de Metadados Azure que dá tempo à sua aplicação para se preparar para a manutenção de máquinas virtuais. Fornece informações sobre os próximos eventos de manutenção (por exemplo, reboot) para que a sua aplicação possa preparar-se para eles e limitar a interrupção. Está disponível para todos os tipos de Máquina Virtual Azure, incluindo PaaS e IaaS tanto no Windows como no Linux. 

Para obter informações sobre eventos agendados em Linux, consulte [Eventos Agendados para VMs Linux](../linux/scheduled-events.md).

> [!Note] 
> Os Eventos Agendados estão geralmente disponíveis em todas as Regiões Azure. Consulte [a Versão e a Disponibilidade da Região](#version-and-region-availability) para obter informações mais recentes sobre a libertação.

## <a name="why-scheduled-events"></a>Por que eventos agendados?

Muitas aplicações podem beneficiar do tempo para se prepararem para a manutenção virtual da máquina. O tempo pode ser usado para executar tarefas específicas da aplicação que melhorem a disponibilidade, fiabilidade e capacidade de serviço, incluindo: 

- Ponto de verificação e restauro
- Drenagem de ligação
- Falha na réplica primária 
- Remoção da piscina do balançador de carga
- Registo de eventos
- Encerramento gracioso 

Utilizando Eventos Agendados, a sua aplicação pode descobrir quando a manutenção ocorrerá e desencadear tarefas para limitar o seu impacto. Permitir eventos programados dá à sua máquina virtual um mínimo de tempo antes da atividade de manutenção ser realizada. Consulte a secção de Agendamento de Eventos abaixo para mais detalhes.

Eventos Agendados proporcionam eventos nos seguintes casos de utilização:
- [Manutenção iniciada pela plataforma](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (por exemplo, reboot VM, migração ao vivo ou atualizações de preservação da memória para hospedeiro)
- Máquina virtual está a funcionar com [hardware de hospedeiro degradado](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) que se prevê falhar em breve
- Manutenção iniciada pelo utilizador (por exemplo, o utilizador reinicia ou reimplanta um VM)
- [Spot VM](spot-vms.md) e [Spot escala definir](../../virtual-machine-scale-sets/use-spot.md) despejos de instância

## <a name="the-basics"></a>O Básico  

O serviço Azure Metadata expõe informações sobre a execução de Máquinas Virtuais utilizando um ponto final REST acessível a partir do VM. A informação está disponível através de um IP não roteável para que não seja exposta fora do VM.

### <a name="endpoint-discovery"></a>Descoberta de Ponto Final
Para VMs ativados por VNET, o serviço de metadados está disponível a partir de um IP estático não roteado, `169.254.169.254` . O ponto final completo para a versão mais recente de Eventos Agendados é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

Se a Máquina Virtual não for criada dentro de uma Rede Virtual, os casos padrão para serviços na nuvem e VMs clássicos, a lógica adicional é necessária para descobrir o endereço IP para usar. Consulte esta amostra para saber como [descobrir o ponto final do hospedeiro](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Disponibilidade de versão e região
O Serviço de Eventos Agendados é versão. As versões são obrigatórias e a versão atual é `2019-01-01` .

| Versão | Tipo de libertação | Regiões | Notas de Versão | 
| - | - | - | - |
| 2019-08-01 | Disponibilidade Geral | Todos | <li> Suporte adicional para EventSource |
| 2019-04-01 | Disponibilidade Geral | Todos | <li> Apoio adicional para descrição do evento |
| 2019-01-01 | Disponibilidade Geral | Todos | <li> Suporte adicionado para conjuntos de escala de máquina virtual 'Terminate' |
| 2017-11-01 | Disponibilidade Geral | Todos | <li> Suporte adicional para o Spot VM despejo EventType 'Preempt'<br> | 
| 2017-08-01 | Disponibilidade Geral | Todos | <li> Removido sublinhado de nomes de recursos para IaaS VMs<br><li>Requisito do cabeçalho de metadados aplicado para todos os pedidos | 
| 2017-03-01 | Pré-visualizar | Todos |<li>Versão inicial |

> [!NOTE] 
> Lançamentos anteriores de pré-visualização de eventos programados suportados {mais recente} como a versão api. Este formato já não é suportado e será depreciado no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Ativar e Desativar Eventos Agendados
Eventos Agendados estão habilitados para o seu serviço na primeira vez que faz um pedido de eventos. Deve esperar uma resposta atrasada na sua primeira chamada de até dois minutos. Deve consultar periodicamente o ponto final para detetar os próximos eventos de manutenção, bem como o estado das atividades de manutenção que estão a ser realizadas.

Eventos Agendados são desativados para o seu serviço se não fizer um pedido de 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção Iniciada pelo Utilizador
O utilizador iniciou a manutenção de máquinas virtuais através do portal Azure, API, CLI ou PowerShell resulta num evento agendado. Isto permite-lhe testar a lógica de preparação de manutenção na sua aplicação e permite que a sua aplicação se prepare para a manutenção iniciada pelo utilizador.

Reiniciar uma máquina virtual programa um evento com tipo `Reboot` . A recolocação de uma máquina virtual programa um evento com tipo `Redeploy` .

## <a name="using-the-api"></a>Utilizar a API

### <a name="headers"></a>Cabeçalhos
Ao consultar o Serviço de Metadados, deve fornecer o cabeçalho `Metadata:true` para garantir que o pedido não foi redirecionado involuntariamente. O `Metadata:true` cabeçalho é necessário para todos os pedidos de eventos agendados. A não inclusão do cabeçalho no pedido resultará numa resposta de Mau Pedido do Serviço de Metadados.

### <a name="query-for-events"></a>Consulta para eventos
Você pode consultar para Eventos Agendados simplesmente fazendo a seguinte chamada:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01 -H @{"Metadata"="true"}
```

Uma resposta contém uma série de eventos programados. Uma matriz vazia significa que não há eventos agendados.
No caso de existirem eventos agendados, a resposta contém uma série de eventos: 
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
            "Description": {eventDescription},
            "EventSource" : "Platform" | "User",
        }
    ]
}
```
O DocumentIncarnation é um ETag e fornece uma maneira fácil de inspecionar se a carga útil de Eventos mudou desde a última consulta.

### <a name="event-properties"></a>Propriedades do evento
|Propriedade  |  Descrição |
| - | - |
| EventId | Identificador globalmente único para este evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto que este evento causa. <br><br> Valores: <br><ul><li> `Freeze`: A Máquina Virtual está programada para fazer uma pausa durante alguns segundos. A CPU e a conectividade da rede podem ser suspensas, mas não há impacto na memória ou nos ficheiros abertos. <li>`Reboot`: A Máquina Virtual está programada para reiniciar (perde-se a memória não persistente). <li>`Redeploy`: A Máquina Virtual está programada para se mover para outro nó (os discos efémeros estão perdidos). <li>`Preempt`: A Máquina Virtual Spot está a ser eliminada (os discos efémeros perdem-se). <li> `Terminate`: A Máquina Virtual está programada para ser eliminada. |
| ResourceType | Tipo de recurso que este evento impacta. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que este evento impacta. Isto é garantido para conter máquinas de no máximo um [Domínio de Atualização](manage-availability.md), mas pode não conter todas as máquinas na UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Estado do evento | Estado deste evento. <br><br> Valores: <ul><li>`Scheduled`: Este evento está agendado para começar após o tempo especificado na `NotBefore` propriedade.<li>`Started`: Este evento já começou.</ul> Nenhum `Completed` estatuto ou estatuto semelhante é fornecido; o evento deixará de ser devolvido quando o evento estiver concluído.
| NotBefore| Tempo após o qual este evento pode começar. <br><br> Exemplo: <br><ul><li> Seg, 19 set 2016 18:29:47 GMT  |
| Descrição | Descrição deste evento. <br><br> Exemplo: <br><ul><li> O servidor anfitrião está a ser submetido a manutenção. |
| Fonte de Eventos | Iniciador do evento. <br><br> Exemplo: <br><ul><li> `Platform`: Este evento é iniciado por platfrom. <li>`User`: Este evento é iniciado pelo utilizador. |

### <a name="event-scheduling"></a>Agendamento de eventos
Cada evento está programado um período mínimo de tempo no futuro com base no tipo de evento. Desta vez reflete-se na propriedade de um `NotBefore` evento. 

|EventType  | Aviso Mínimo |
| - | - |
| Congelar| 15 minutos |
| Reiniciar | 15 minutos |
| Voltar a implementar | 10 minutos |
| Preempt | 30 segundos |
| Terminar | [Configurar do utilizador:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)5 a 15 minutos |

> [!NOTE] 
> Em alguns casos, o Azure é capaz de prever a falha do hospedeiro devido a hardware degradado e tentará mitigar a perturbação do seu serviço agendando uma migração. As máquinas virtuais afetadas receberão um evento agendado com um `NotBefore` que normalmente é de alguns dias no futuro. O tempo real varia em função da avaliação prevista do risco de falha. O Azure tenta dar um pré-aviso de 7 dias quando possível, mas o tempo real varia e pode ser menor se a previsão for que há uma alta probabilidade de o hardware falhar iminentemente. Para minimizar o risco para o seu serviço no caso de o hardware falhar antes do sistema iniciar a migração, recomenda-se a auto-redistribuição da sua máquina virtual o mais rapidamente possível.

### <a name="event-scope"></a>Âmbito do evento     
Os eventos agendados são entregues para:
 - Máquinas virtuais autónomas.
 - Todas as máquinas virtuais num serviço de cloud.     
 - Todas as máquinas virtuais num conjunto de disponibilidade. 
 - Todas as máquinas virtuais numa Zona de Disponibilidade.
 - Todas as máquinas virtuais num grupo de colocação de conjunto de escala (incluindo lote).       

> [!NOTE]
> Numa zona de disponibilidade, os eventos programados vão apenas para VMs únicos e afetados na zona de disponibilidade.
> 
> Por exemplo, num conjunto de disponibilidade, se tiver 100 VMs no conjunto e houver uma atualização para um dos VMs, o evento agendado vai para todos os 100 VMs no conjunto de disponibilidade.
>
> Numa zona de disponibilidade, se tiver 100 VMs na zona de disponibilidade, o evento vai apenas para o VM que é afetado.
>
> Como resultado, deverá verificar o `Resources` campo no caso de identificar quais VMs serão afetados. 

### <a name="starting-an-event"></a>Iniciar um evento 

Assim que tiver conhecimento de um evento que se avizinha e tiver completado a sua lógica de paragem graciosa, pode aprovar o evento pendente fazendo uma `POST` chamada para o serviço de metadados com o `EventId` . Isto indica ao Azure que pode encurtar o tempo mínimo de notificação (quando possível). 

Segue-se a json esperada no `POST` corpo de pedido. O pedido deve conter uma lista de `StartRequests` . Cada um `StartRequest` contém o evento que pretende `EventId` acelerar:
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
> Reconhecer um evento permite que o evento prossiga para todos `Resources` no evento, e não apenas para a máquina virtual que reconhece o evento. Pode, portanto, optar por eleger um líder para coordenar o reconhecimento, que pode ser tão simples como a primeira máquina no `Resources` campo.


## <a name="powershell-sample"></a>Exemplo do PowerShell 

As seguintes consultas de amostra do serviço de metadados para eventos agendados e aprova cada evento pendente.

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

## <a name="next-steps"></a>Próximos passos 

- Assista a uma [demonstração de eventos agendados](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) na sexta-feira de Azure. 
- Reveja as amostras de código de eventos programados no [Azure Instance Metadatas Eventos Agendados GitHub Repository](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Leia mais sobre as APIs disponíveis no [serviço de Metadados de Exemplo](instance-metadata-service.md).
- Saiba mais sobre [a manutenção planeada para máquinas virtuais do Windows em Azure.](planned-maintenance.md)
