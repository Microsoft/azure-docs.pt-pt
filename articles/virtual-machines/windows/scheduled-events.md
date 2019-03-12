---
title: Eventos agendados para VMs do Windows no Azure | Documentos da Microsoft
description: Agendada eventos com o serviço de metadados do Azure nas suas máquinas virtuais do Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: c9bd14128a6874f06983aa99ebb5a8a9a85843a2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550691"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Serviço de metadados do Azure: Eventos agendados para VMs do Windows

Eventos agendados é um serviço de metadados do Azure que dá o tempo de aplicação para se preparar para a manutenção de máquina virtual. Fornece informações sobre eventos de manutenção futura (por exemplo, reinícios), de modo a seu aplicativo pode se preparar e limitar a interrupção. Está disponível para todos os tipos de Máquina Virtual do Azure, incluindo PaaS e IaaS no Windows e Linux. 

Para obter informações sobre eventos agendados no Linux, consulte [eventos agendados para VMs do Linux](../linux/scheduled-events.md).

> [!Note] 
> Eventos agendados está disponível em geral em todas as regiões do Azure. Ver [versão e disponibilidade das regiões](#version-and-region-availability) para informações de versão mais recente.

## <a name="why-scheduled-events"></a>Por que eventos agendados?

Muitos aplicativos podem beneficiar de tempo para se preparar para a manutenção de máquina virtual. O tempo pode ser utilizado para executar tarefas específicas de aplicação que melhorar a disponibilidade, fiabilidade e capacidade de serviço incluindo: 

- Ponto de verificação e de restauro
- Drenagem de ligação
- Ativação pós-falha de réplica primária 
- Remoção do conjunto de Balanceador de carga
- Registo de eventos
- Encerramento correto 

Através dos eventos agendados seu aplicativo podem detetar quando manutenção irá ocorrer e acionar tarefas para limitar seu impacto. Ativar eventos agendados dá a sua máquina virtual uma quantidade mínima de tempo antes que seja efetuada a atividade de manutenção. Consulte a secção de agendamento eventos abaixo para obter detalhes.

Eventos agendados fornece eventos nos seguintes casos de utilização:
- Manutenção de plataforma iniciada (por exemplo, atualização de SO de Host)
- (Por exemplo, usuário será reiniciado e reimplementa uma VM) de manutenção iniciada pelo utilizador

## <a name="the-basics"></a>As noções básicas  

O serviço de metadados do Azure expõe informações sobre como executar máquinas virtuais com um ponto final REST acessível a partir de dentro da VM. As informações estão disponíveis por meio de um IP não encaminháveis internos para que não está exposta fora da VM.

### <a name="endpoint-discovery"></a>Deteção de pontos finais
Para VNET ativada VMs, o serviço de metadados está disponível a partir de um IP estático não encaminhável, `169.254.169.254`. É o ponto final completo para a versão mais recente do eventos agendados: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Se a Máquina Virtual não for criada dentro de uma rede Virtual, os casos de padrão para serviços cloud e VMs clássicas, lógica adicional é necessário para detetar o endereço IP a utilizar. Veja este exemplo para saber como [detetar o ponto final de anfitrião](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Versão e a disponibilidade de região
O serviço de eventos agendados tem a mesma versão. Versões são obrigatórias e a versão atual é `2017-08-01`.

| Versão | Tipo de versão | Regiões | Notas de Lançamento | 
| - | - | - | - |
| 2017-08-01 | Disponibilidade Geral | Tudo | <li> Removido o caráter de sublinhado antecedendo nomes de recursos para IaaS VMs<br><li>Requisito de cabeçalho de metadados imposto a todos os pedidos | 
| 2017-03-01 | Pré-visualização | Tudo |<li>Versão inicial

> [!NOTE] 
> Versões anteriores de pré-visualização de eventos agendados suportados {mais recente} como a api-version. Este formato já não é suportado e será preterido no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Ativar e desativar eventos agendados
Eventos agendados está ativada para o tempo de serviço a primeira fizer um pedido para eventos. Deve esperar uma resposta atrasada em sua primeira chamada de até dois minutos. Deve consultar o ponto de extremidade periodicamente para detetar eventos de manutenção futura, bem como o estado das atividades de manutenção que estão a ser efetuadas.

Eventos agendados está desativada para o seu serviço, se não faz um pedido para 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo utilizador
Manutenção de máquina virtual através do portal do Azure, API, da CLI, iniciada pelo utilizador ou PowerShell resulta num evento agendado. Isto permite-lhe testar a lógica de preparação de manutenção em seu aplicativo e permite que seu aplicativo para se preparar para a manutenção iniciada pelo utilizador.

Reiniciar uma máquina virtual agenda um evento com o tipo `Reboot`. Voltar a implementar uma máquina virtual agenda um evento com o tipo `Redeploy`.

## <a name="using-the-api"></a>Com a API

### <a name="headers"></a>Cabeçalhos
Quando consulta o serviço de metadados, tem de fornecer o cabeçalho `Metadata:true` para garantir que o pedido não foi redirecionado inadvertidamente. O `Metadata:true` cabeçalho é obrigatório para todos os pedidos de eventos agendados. Não incluir o cabeçalho no pedido irá resultar numa resposta de pedido incorreto do serviço de metadados.

### <a name="query-for-events"></a>Consulta para eventos
Pode consultar para eventos agendados simplesmente fazendo a seguinte chamada:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
```

Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que atualmente não há eventos agendados.
No caso em que há eventos agendados, a resposta contém uma matriz de eventos: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
O DocumentIncarnation é uma ETag e fornece uma forma fácil de inspecionar se o payload de eventos foi alterado desde a última consulta.

### <a name="event-properties"></a>Propriedades do evento
|Propriedade  |  Descrição |
| - | - |
| EventId | Identificador exclusivo global para este evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto faz com que o este evento. <br><br> Valores: <br><ul><li> `Freeze`: A Máquina Virtual está agendada para pausar durante alguns segundos. A CPU está suspenso, mas não há nenhum impacto na memória, ficheiros abertos ou ligações de rede. <li>`Reboot`: A Máquina Virtual está agendada para reinicialização (a memória não persistentes é perdida). <li>`Redeploy`: A Máquina Virtual está agendada para mover para outro nó (discos efémeros são perdidos). |
| ResourceType | Tipo de recurso que tem impacto sobre a este evento. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que tem impacto sobre a este evento. Isso é garantido que contêm máquinas a partir de um [domínio de atualização](manage-availability.md), mas não pode conter todas as máquinas a UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Estado do evento | Estado deste evento. <br><br> Valores: <ul><li>`Scheduled`: Este evento está agendado para iniciar após o período de tempo especificado no `NotBefore` propriedade.<li>`Started`: Este evento foi iniciado.</ul> Não `Completed` ou estado semelhante já é fornecido; o evento já não vai ser devolvido quando o evento for concluído.
| NotBefore| Tempo após o qual este evento pode começar. <br><br> Exemplo: <br><ul><li> Segunda-feira, 19 de Setembro de 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Agendamento do evento
Cada evento está agendado uma quantidade mínima de tempo no futuro, com base no tipo de evento. Neste momento é refletido num evento `NotBefore` propriedade. 

|EventType  | Tenha em atenção mínimo |
| - | - |
| Congelamento| 15 minutos |
| Reiniciar | 15 minutos |
| Implementar novamente | 10 minutos |

### <a name="event-scope"></a>Âmbito de eventos     
Eventos agendados são entregues para:        
 - Todas as máquinas virtuais num serviço Cloud      
 - Todas as máquinas virtuais num conjunto de disponibilidade      
 - Todas as máquinas virtuais num grupo de colocação do conjunto de dimensionamento.         

Como resultado, deve verificar o `Resources` campo no evento para identificar que VMs vão ser afetadas. 

### <a name="starting-an-event"></a>A partir de um evento 

Depois de ter aprendido de um evento futuro e concluir a sua lógica de encerramento correto, pode aprovar o evento pendente, fazendo uma `POST` chamar para o serviço de metadados com o `EventId`. Isso indica para o Azure que ele pode reduzir a notificação mínima de tempo (quando possível). 

Segue-se o json esperado para o `POST` corpo do pedido. O pedido deve conter uma lista de `StartRequests`. Cada `StartRequest` contém o `EventId` para o evento que pretende acelerar:
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
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Ter consciência de um evento permite que o evento continuar para todos os `Resources` no caso, não apenas a máquina virtual que reconhece o evento. , Por conseguinte, pode optar por eleger um coordenador para coordenar a confirmação, que pode ser tão simples como a primeira máquina no `Resources` campo.


## <a name="powershell-sample"></a>Exemplo do PowerShell 

O exemplo a seguir consulta o serviço de metadados para eventos agendados e aprova a cada evento pendentes.

```PowerShell
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
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-08-01' -f $localHostIP 

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

- Veja uma [agendados eventos demonstração](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) no Azure Friday. 
- Reveja os exemplos de código de eventos agendados no [Azure instância metadados agendados eventos de repositório do GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Saiba mais sobre as APIs disponíveis na [serviço de metadados de instância](instance-metadata-service.md).
- Saiba mais sobre [planejado de manutenção para máquinas de virtuais do Windows no Azure](planned-maintenance.md).
