---
title: Eventos Agendados para VMs windows em Azure
description: Eventos regulares utilizando o Serviço de Metadados Azure para as suas máquinas virtuais Windows.
author: EricRadzikowskiMSFT
ms.service: virtual-machines
ms.subservice: scheduled-events
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: ericrad
ms.reviwer: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0dcfdfbd480cdc708fced38d421b7588c5224a52
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673319"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Serviço de Metadados Azure: Eventos agendados para VMs windows

Eventos Agendados é um Serviço de Metadados Azure que dá tempo à sua aplicação para se preparar para a manutenção da máquina virtual (VM). Fornece informações sobre os próximos eventos de manutenção (por exemplo, reiniciar) para que a sua aplicação possa preparar-se para eles e limitar a interrupção. Está disponível para todos os tipos de Máquinas Virtuais Azure, incluindo PaaS e IaaS tanto no Windows como no Linux. 

Para obter informações sobre eventos agendados em Linux, consulte [Eventos Agendados para VMs Linux](../linux/scheduled-events.md).

> [!Note] 
> Os Eventos Agendados estão geralmente disponíveis em todas as Regiões Azure. Consulte [a Versão e a Disponibilidade da Região](#version-and-region-availability) para obter informações mais recentes sobre a libertação.

## <a name="why-use-scheduled-events"></a>Porquê usar Eventos Agendados?

Muitas aplicações podem beneficiar do tempo de preparação para a manutenção de VM. O tempo pode ser usado para executar tarefas específicas de aplicações que melhorem a disponibilidade, fiabilidade e capacidade de serviço, incluindo: 

- Posto de controlo e restauro.
- Drenagem de ligação.
- Réplica primária falha.
- Remoção de uma piscina de balançadores de carga.
- Registo de eventos.
- Paralisação graciosa.

Com Eventos Agendados, a sua aplicação pode descobrir quando a manutenção ocorrerá e desencadear tarefas para limitar o seu impacto.  

Eventos Agendados proporcionam eventos nos seguintes casos de utilização:

- [Manutenção iniciada pela plataforma](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json) (por exemplo, reboot VM, migração ao vivo ou atualizações de preservação da memória para hospedeiro)
- Máquina virtual está a funcionar com [hardware de hospedeiro degradado](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) que se prevê falhar em breve
- Manutenção iniciada pelo utilizador (por exemplo, um utilizador reinicia ou reimplanta um VM)
- [Spot VM](../spot-vms.md) e [Spot escala definir](../../virtual-machine-scale-sets/use-spot.md) despejos de instância.

## <a name="the-basics"></a>Noções básicas  

  O Serviço de Metadados expõe informações sobre a execução de VMs utilizando um ponto final REST acessível a partir do VM. A informação está disponível através de um IP não-sensível para que não seja exposta fora do VM.

### <a name="scope"></a>Âmbito
Os eventos agendados são entregues para:

- Máquinas virtuais autónomas.
- Todos os VMs num serviço de nuvem.
- Todos os VMs num conjunto de disponibilidade.
- Todos os VMs em uma zona de disponibilidade.
- Todos os VMs de um grupo de colocação de conjunto de escala. 

> [!NOTE]
> Os Eventos Agendados para todas as máquinas virtuais (VMs) num inquilino do Controlador de Tecidos (FC) são entregues a todos os VMs de um inquilino do FC. O inquilino fc equivale a um VM autónomo, um serviço de nuvem inteiro, um conjunto completo de disponibilidade, e um Grupo de Colocação para um Conjunto de Escala VM (VMSS) independentemente do uso da Zona de Disponibilidade. 

Como resultado, verifique o `Resources` campo no caso de identificar quais os VMs afetados.

### <a name="endpoint-discovery"></a>Descoberta de ponto final
Para VMs ativados por VNET, o Serviço de Metadados está disponível a partir de um IP estático não sensível, `169.254.169.254` . O ponto final completo para a versão mais recente de Eventos Agendados é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

Se o VM não for criado dentro de uma Rede Virtual, os casos padrão para serviços na nuvem e VMs clássicos, a lógica adicional é necessária para descobrir o endereço IP para usar. Para aprender a [descobrir o ponto final do hospedeiro,](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)consulte esta amostra.

### <a name="version-and-region-availability"></a>Disponibilidade de versão e região
O serviço Eventos Agendados é versão. As versões são obrigatórias; a versão atual é `2019-01-01` .

| Versão | Tipo de libertação | Regiões | Release Notes (Notas de Lançamento) | 
| - | - | - | - | 
| 2019-08-01 | Disponibilidade Geral | Todos | <li> Suporte adicional para EventSource |
| 2019-04-01 | Disponibilidade Geral | Todos | <li> Apoio adicional para descrição do evento |
| 2019-01-01 | Disponibilidade Geral | Todos | <li> Suporte adicionado para conjuntos de escala de máquina virtual 'Terminate' |
| 2017-11-01 | Disponibilidade Geral | Todos | <li> Suporte adicional para o Spot VM despejo EventType 'Preempt'<br> | 
| 2017-08-01 | Disponibilidade Geral | Todos | <li> Removido sublinhado de nomes de recursos para IaaS VMs<br><li>Requisito do cabeçalho de metadados aplicado para todos os pedidos | 
| 2017-03-01 | Pré-visualizar | Todos | <li>Versão inicial |


> [!NOTE] 
> As versões anteriores de Eventos Agendados suportados {mais recente} como a versão api. Este formato já não é suportado e será depreciado no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Permitir e desativar eventos programados
Eventos Agendados estão habilitados para o seu serviço na primeira vez que faz um pedido de eventos. Deve esperar uma resposta atrasada na sua primeira chamada de até dois minutos.

Eventos Agendados são desativados para o seu serviço se não fizer um pedido de 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo utilizador
A manutenção VM iniciada pelo utilizador através do portal Azure, API, CLI ou PowerShell resulta num evento agendado. Em seguida, pode testar a lógica de preparação de manutenção na sua aplicação, e a sua aplicação pode preparar-se para a manutenção iniciada pelo utilizador.

Se reiniciar um VM, está agendado um evento com o `Reboot` tipo. Se recolocar um VM, está agendado um evento com o `Redeploy` tipo.

## <a name="use-the-api"></a>Utilizar a API

### <a name="headers"></a>Cabeçalhos
Ao consultar o Serviço de Metadados, deve fornecer o cabeçalho `Metadata:true` para garantir que o pedido não foi redirecionado involuntariamente. O `Metadata:true` cabeçalho é necessário para todos os pedidos de eventos agendados. A não inclusão do cabeçalho no pedido resulta numa resposta de "Mau Pedido" do Serviço de Metadados.

### <a name="query-for-events"></a>Consulta para eventos
Pode consultar os eventos agendados, fazendo a seguinte chamada:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Uma resposta contém uma série de eventos programados. Uma matriz vazia significa que, atualmente, não estão agendados eventos.
No caso de existirem eventos agendados, a resposta contém uma série de eventos. 
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

### <a name="event-properties"></a>Propriedades do evento
|Propriedade  |  Descrição |
| - | - |
| EventId | Identificador globalmente único para este evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto que este evento causa. <br><br> Valores: <br><ul><li> `Freeze`: A Máquina Virtual está programada para fazer uma pausa durante alguns segundos. A CPU e a conectividade da rede podem ser suspensas, mas não há impacto na memória ou nos ficheiros abertos.<li>`Reboot`: A Máquina Virtual está programada para reiniciar (perde-se a memória não persistente). <li>`Redeploy`: A Máquina Virtual está programada para se mover para outro nó (os discos efémeros estão perdidos). <li>`Preempt`: A Máquina Virtual Spot está a ser eliminada (os discos efémeros perdem-se). <li> `Terminate`: A máquina virtual está programada para ser eliminada. |
| ResourceType | Tipo de recurso que este evento afeta. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que este evento afeta. A lista é garantida para conter máquinas de um domínio de [atualização](../manage-availability.md)no máximo , mas pode não conter todas as máquinas na UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Estado deste evento. <br><br> Valores: <ul><li>`Scheduled`: Este evento está agendado para começar após o tempo especificado na `NotBefore` propriedade.<li>`Started`: Este evento já começou.</ul> Nenhum `Completed` estatuto ou estatuto semelhante é fornecido. O evento já não é devolvido quando o evento estiver terminado.
| NotBefore| Tempo após o qual este evento pode começar. <br><br> Exemplo: <br><ul><li> Seg, 19 set 2016 18:29:47 GMT  |
| Descrição | Descrição deste evento. <br><br> Exemplo: <br><ul><li> O servidor anfitrião está a ser submetido a manutenção. |
| Fonte de Eventos | Iniciador do evento. <br><br> Exemplo: <br><ul><li> `Platform`: Este evento é iniciado por plataforma. <li>`User`: Este evento é iniciado pelo utilizador. |

### <a name="event-scheduling"></a>Agendamento de eventos
Cada evento está programado um período mínimo de tempo no futuro com base no tipo de evento. Desta vez reflete-se na propriedade de um `NotBefore` evento. 

|EventType  | Aviso mínimo |
| - | - |
| Congelar| 15 minutos |
| Reiniciar | 15 minutos |
| Voltar a implementar | 10 minutos |
| Preempt | 30 segundos |
| Terminate | [Configurar do utilizador:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)5 a 15 minutos |

> [!NOTE] 
> Em alguns casos, o Azure é capaz de prever a falha do hospedeiro devido a hardware degradado e tentará mitigar a perturbação do seu serviço agendando uma migração. As máquinas virtuais afetadas receberão um evento agendado com um `NotBefore` que normalmente é de alguns dias no futuro. O tempo real varia em função da avaliação prevista do risco de falha. O Azure tenta dar um pré-aviso de 7 dias quando possível, mas o tempo real varia e pode ser menor se a previsão for que há uma alta probabilidade de o hardware falhar iminentemente. Para minimizar o risco para o seu serviço no caso de o hardware falhar antes da migração iniciada pelo sistema, recomendamos que se auto-reimplante a sua máquina virtual o mais rapidamente possível.

### <a name="polling-frequency"></a>Frequência de sondagens

Pode sondar o ponto final para atualizações com a frequência ou com pouca frequência que quiser. No entanto, quanto mais tempo demorar entre pedidos, mais tempo poderá perder para reagir a um evento que se avizinha. A maioria dos eventos tem 5 a 15 minutos de antecedência, embora em alguns casos o aviso prévio possa ser de apenas 30 segundos. Para garantir que tem o máximo de tempo possível para tomar medidas mitigadoras, recomendamos que faça uma sondagem do serviço uma vez por segundo.

### <a name="start-an-event"></a>Inicie um evento 

Depois de conhecer um evento que se avizinha e terminar a sua lógica para uma paragem graciosa, pode aprovar o evento pendente fazendo uma `POST` chamada para o Serviço de Metadados com `EventId` . Esta chamada indica ao Azure que pode encurtar o tempo mínimo de notificação (quando possível). 

A amostra JSON seguinte é esperada no `POST` organismo de pedido. O pedido deve conter uma lista de `StartRequests` . Cada um `StartRequest` contém para o evento que pretende `EventId` acelerar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Amostra de bash
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Reconhecer um evento permite que o evento prossiga para todos `Resources` no evento, e não apenas para o VM que reconhece o evento. Portanto, pode optar por eleger um líder para coordenar o reconhecimento, que pode ser tão simples como a primeira máquina no `Resources` campo.

## <a name="python-sample"></a>Amostra de Python 

As seguintes consultas de amostra Metadata Service para eventos agendados e aprova cada evento pendente:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01"
this_host = socket.gethostname()


def get_scheduled_events():
    req = urllib2.Request(metadata_url)
    req.add_header('Metadata', 'true')
    resp = urllib2.urlopen(req)
    data = json.loads(resp.read())
    return data


def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ", "_")
    description = evt['Description']
    eventSource = evt['EventSource']
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + 
        " by " + eventSource + 
        " with description " + description +
        " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Passos seguintes 
- Assista [a Eventos Agendados na Sexta-feira](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) azul para ver uma demonstração. 
- Reveja as amostras de código de eventos programados no [repositório de metados de casos Azure Instance GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Leia mais sobre as APIs que estão disponíveis no [Serviço de Metadados de Exemplo.](instance-metadata-service.md)
- Saiba mais sobre [a manutenção planeada para máquinas virtuais do Windows em Azure.](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)
