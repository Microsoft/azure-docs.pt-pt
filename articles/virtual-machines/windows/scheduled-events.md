---
title: Eventos Agendados para VMs do Windows no Azure
description: Eventos agendados usando o serviço de metadados do Azure para em suas máquinas virtuais do Windows.
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
ms.openlocfilehash: f6e3e370201b49da149c09d87ed7cec63fef8ebf
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792245"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Serviço de metadados do Azure: Eventos Agendados para VMs do Windows

Eventos Agendados é um serviço de metadados do Azure que dá ao seu aplicativo tempo para se preparar para a manutenção da máquina virtual. Ele fornece informações sobre eventos de manutenção futuros (por exemplo, reinicialização) para que seu aplicativo possa se preparar para eles e limitar a interrupção. Ele está disponível para todos os tipos de máquina virtual do Azure, incluindo PaaS e IaaS no Windows e no Linux. 

Para obter informações sobre Eventos Agendados no Linux, consulte [eventos agendados para VMs do Linux](../linux/scheduled-events.md).

> [!Note] 
> O Eventos Agendados está geralmente disponível em todas as regiões do Azure. Consulte [disponibilidade de versão e região](#version-and-region-availability) para obter informações de versão mais recentes.

## <a name="why-scheduled-events"></a>Por que Eventos Agendados?

Muitos aplicativos podem se beneficiar do tempo para se preparar para a manutenção da máquina virtual. O tempo pode ser usado para executar tarefas específicas do aplicativo que melhoram a disponibilidade, a confiabilidade e a manutenção, incluindo: 

- Ponto de verificação e restauração
- Drenagem de ligação
- Failover de réplica primária 
- Remoção do pool do balanceador de carga
- Log de eventos
- Desligamento normal 

Usando Eventos Agendados seu aplicativo pode descobrir quando a manutenção ocorrerá e disparar tarefas para limitar seu impacto. A habilitação de eventos agendados dá ao seu computador virtual um período mínimo de tempo antes que a atividade de manutenção seja executada. Consulte a seção agendamento de eventos abaixo para obter detalhes.

Eventos Agendados fornece eventos nos seguintes casos de uso:
- [Manutenção iniciada pela plataforma](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (por exemplo, reinicialização de VM, migração ao vivo ou atualizações de preservação de memória para host)
- Hardware degradado
- Manutenção iniciada pelo usuário (por exemplo, o usuário reinicia ou reimplanta uma VM)
- [Detectar](spot-vms.md) as remoções de instância de VM e [conjunto de escala de spot](../../virtual-machine-scale-sets/use-spot.md)

## <a name="the-basics"></a>Noções básicas  

O serviço de metadados do Azure expõe informações sobre a execução de máquinas virtuais usando um ponto de extremidade REST acessível de dentro da VM. As informações estão disponíveis por meio de um IP não roteável para que não seja exposta fora da VM.

### <a name="endpoint-discovery"></a>Descoberta de ponto de extremidade
Para VMs habilitadas para VNET, o serviço de metadados está disponível de um IP não roteável estático, `169.254.169.254`. O ponto de extremidade completo para a versão mais recente do Eventos Agendados é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Se a máquina virtual não for criada em uma rede virtual, os casos padrão para serviços de nuvem e VMs clássicas, será necessária lógica adicional para descobrir o endereço IP a ser usado. Consulte este exemplo para saber como [descobrir o ponto de extremidade do host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Disponibilidade de versão e região
O serviço de Eventos Agendados tem controle de versão. As versões são obrigatórias e a versão atual é `2017-11-01`.

| Versão | Tipo de liberação | Regiões | Notas de Lançamento | 
| - | - | - | - |
| 2017-11-01 | Disponibilidade Geral | Tudo | <li> Suporte adicionado para a remoção de VM de baixa prioridade do EventType ' preempt '<br> | 
| 2017-08-01 | Disponibilidade Geral | Tudo | <li> Foi removido o sublinhado precedido dos nomes de recursos para VMs IaaS<br><li>Requisito de cabeçalho de metadados imposto para todas as solicitações | 
| 2017-03-01 | Pré-visualização | Tudo |<li>Versão inicial

> [!NOTE] 
> Versões prévias anteriores dos eventos agendados com suporte {Latest} como a versão de API. Esse formato não é mais suportado e será preterido no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Habilitando e desabilitando Eventos Agendados
O Eventos Agendados está habilitado para o serviço na primeira vez que você faz uma solicitação de eventos. Você deve esperar uma resposta atrasada em sua primeira chamada de até dois minutos. Você deve consultar o ponto de extremidade periodicamente para detectar futuros eventos de manutenção, bem como o status das atividades de manutenção que estão sendo executadas.

O Eventos Agendados será desabilitado para seu serviço se ele não fizer uma solicitação por 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo usuário
A manutenção de máquina virtual iniciada pelo usuário por meio do portal do Azure, API, CLI ou PowerShell resulta em um evento agendado. Isso permite que você teste a lógica de preparação de manutenção em seu aplicativo e permite que seu aplicativo se prepare para a manutenção iniciada pelo usuário.

Reiniciar uma máquina virtual agenda um evento com o tipo `Reboot`. Reimplantar uma máquina virtual agenda um evento com o tipo `Redeploy`.

## <a name="using-the-api"></a>Usando a API

### <a name="headers"></a>Cabeçalhos
Ao consultar o serviço de metadados, você deve fornecer o `Metadata:true` de cabeçalho para garantir que a solicitação não tenha sido redirecionada de forma não intencional. O cabeçalho de `Metadata:true` é necessário para todas as solicitações de eventos agendados. A falha ao incluir o cabeçalho na solicitação resultará em uma resposta de solicitação inadequada do serviço de metadados.

### <a name="query-for-events"></a>Consulta de eventos
Você pode consultar Eventos Agendados simplesmente fazendo a seguinte chamada:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01 -H @{"Metadata"="true"}
```

Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que não há eventos agendados no momento.
No caso em que há eventos agendados, a resposta contém uma matriz de eventos: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
O DocumentIncarnation é uma ETag e fornece uma maneira fácil de inspecionar se a carga de eventos foi alterada desde a última consulta.

### <a name="event-properties"></a>Propriedades do evento
|Propriedade  |  Descrição |
| - | - |
| 1008 | Identificador global exclusivo para este evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto causado por esse evento. <br><br> Valores: <br><ul><li> `Freeze`: a máquina virtual está agendada para pausar por alguns segundos. A conectividade de CPU e de rede pode ser suspensa, mas não há nenhum impacto na memória ou em arquivos abertos. <li>`Reboot`: a máquina virtual está agendada para reinicialização (a memória não persistente é perdida). <li>`Redeploy`: a máquina virtual está agendada para ser movida para outro nó (discos efêmeros são perdidos). <li>`Preempt`: a máquina virtual de baixa prioridade está sendo excluída (discos efêmeros são perdidos).|
| ResourceType | Tipo de recurso que esse evento afeta. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que esse evento afeta. Isso é garantido para conter máquinas de no máximo um [domínio de atualização](manage-availability.md), mas pode não conter todas as máquinas no UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Status do evento | Status deste evento. <br><br> Valores: <ul><li>`Scheduled`: esse evento está agendado para iniciar após o tempo especificado na propriedade `NotBefore`.<li>`Started`: este evento foi iniciado.</ul> Nenhum `Completed` ou status semelhante já foi fornecido; o evento não será mais retornado quando o evento for concluído.
| NotBefore| Tempo após o qual esse evento pode ser iniciado. <br><br> Exemplo: <br><ul><li> Segunda-feira, 19 de setembro de 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Agendamento de eventos
Cada evento é agendado uma quantidade mínima de tempo no futuro com base no tipo de evento. Esse tempo é refletido na propriedade de `NotBefore` de um evento. 

|EventType  | Aviso mínimo |
| - | - |
| Trave| 15 minutos |
| Reiniciar | 15 minutos |
| Voltar a implementar | 10 minutos |
| Ocupa | 30 segundos |

### <a name="event-scope"></a>Escopo do evento     
Os eventos agendados são entregues a:
 - Máquinas virtuais autônomas
 - Todas as máquinas virtuais em um serviço de nuvem      
 - Todas as máquinas virtuais em um conjunto de disponibilidade      
 - Todas as máquinas virtuais em um grupo de posicionamento do conjunto de dimensionamento.         

Como resultado, você deve verificar o campo `Resources` no evento para identificar quais VMs serão afetadas. 

### <a name="starting-an-event"></a>Iniciando um evento 

Depois de ter aprendido um evento futuro e concluído sua lógica para desligamento normal, você pode aprovar o evento pendente fazendo uma chamada de `POST` para o serviço de metadados com o `EventId`. Isso indica ao Azure que ele pode reduzir o tempo mínimo de notificação (quando possível). 

A seguir está o JSON esperado no corpo da solicitação `POST`. A solicitação deve conter uma lista de `StartRequests`. Cada `StartRequest` contém o `EventId` para o evento que você deseja acelerar:
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
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> A confirmação de um evento permite que o evento prossiga para todos os `Resources` no evento, não apenas a máquina virtual que reconhece o evento. Portanto, você pode optar por escolher um líder para coordenar a confirmação, o que pode ser tão simples quanto o primeiro computador no campo `Resources`.


## <a name="powershell-sample"></a>Exemplo do PowerShell 

O exemplo a seguir consulta o serviço de metadados para eventos agendados e aprova cada evento pendente.

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
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-11-01' -f $localHostIP 

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

## <a name="next-steps"></a>Passos seguintes 

- Assista a uma [demonstração de eventos agendados](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) na sexta-feira do Azure. 
- Examine os exemplos de código de Eventos Agendados no [repositório de metadados de instância do Azure eventos agendados GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Leia mais sobre as APIs disponíveis no [serviço de metadados de instância](instance-metadata-service.md).
- Saiba mais sobre [a manutenção planejada para máquinas virtuais do Windows no Azure](planned-maintenance.md).
