---
title: Eventos agendados para VMs Linux em Azure
description: Agendar eventos utilizando o Serviço de Metadados Azure para as suas máquinas virtuais Linux.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: ee600d7524af27a0e9e2ce0176e7bd4d1f60bc3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758554"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Serviço de Metadados Azure: Eventos agendados para VMs Linux

Eventos Agendados é um Serviço de Metadados Azure que dá tempo à sua aplicação para se preparar para a manutenção da máquina virtual (VM). Fornece informações sobre os próximos eventos de manutenção (por exemplo, reiniciar) para que a sua aplicação possa preparar-se para eles e limitar a perturbação. Está disponível para todos os tipos de Máquinas Virtuais Azure, incluindo PaaS e IaaS tanto no Windows como no Linux. 

Para obter informações sobre eventos agendados no Windows, consulte [Eventos Agendados para VMs do Windows](../windows/scheduled-events.md).

> [!Note] 
> Os Eventos Agendados estão geralmente disponíveis em todas as regiões do Azure. Consulte a [Versão e a Disponibilidade da Região](#version-and-region-availability) para obter informações mais recentes sobre o lançamento.

## <a name="why-use-scheduled-events"></a>Porquê usar eventos agendados?

Muitas aplicações podem beneficiar do tempo para se preparar para a manutenção vm. O tempo pode ser usado para executar tarefas específicas da aplicação que melhorem a disponibilidade, fiabilidade e serviço, incluindo: 

- Posto de controlo e restauro.
- Drenagem de ligação.
- Falha na réplica primária.
- Remoção de uma piscina de equilibradores de carga.
- Registo de eventos.
- Encerramento gracioso.

Com eventos agendados, a sua aplicação pode descobrir quando a manutenção ocorrerá e desencadear tarefas para limitar o seu impacto.  

Eventos Agendados fornece eventos nos seguintes casos de utilização:

- [Plataforma iniciada manutenção](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (por exemplo, VM reboot, migração ao vivo ou atualizações de preservação de memória para hospedeiro)
- A máquina virtual está a funcionar com hardware de [anfitrião degradado](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) que se prevê que falhe em breve
- Manutenção iniciada pelo utilizador (por exemplo, um utilizador reinicia ou reimplanta um VM)
- [Spot VM](spot-vms.md) e [Spot escala definir](../../virtual-machine-scale-sets/use-spot.md) despejos de instância.

## <a name="the-basics"></a>O Básico  

  O Serviço de Metadados expõe informações sobre a execução de VMs utilizando um ponto final REST acessível a partir do VM. A informação está disponível através de um IP não-encaminhamento para que não seja exposta fora do VM.

### <a name="scope"></a>Âmbito
Os eventos agendados são entregues a:

- Máquinas virtuais autónomas.
- Todos os VMs num serviço de nuvem.
- Todos os VMs em um conjunto de disponibilidade.
- Todos os VMs em um grupo de colocação de conjunto de escala. 

Como resultado, verifique `Resources` o campo no caso para identificar quais vMs são afetados.

### <a name="endpoint-discovery"></a>Descoberta endpoint
Para VNET ativado VMs, o Serviço de Metadados `169.254.169.254`está disponível a partir de um IP estático não redirecionável, . O ponto final completo para a versão mais recente dos Eventos Agendados é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Se o VM não for criado dentro de uma Rede Virtual, os casos padrão para serviços na nuvem e VMs clássicos, é necessária uma lógica adicional para descobrir o endereço IP para usar. Para aprender a [descobrir o ponto final do hospedeiro,](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)consulte esta amostra.

### <a name="version-and-region-availability"></a>Versão e Disponibilidade da Região
O serviço de Eventos Agendados é versão. As versões são obrigatórias; a versão `2019-01-01`atual é .

| Versão | Tipo de lançamento | Regiões | Notas de Versão | 
| - | - | - | - | 
| 2019-01-01 | Disponibilidade Geral | Todos | <li> Suporte adicional para conjuntos de escala de máquina virtual 'Terminate' |
| 2017-11-01 | Disponibilidade Geral | Todos | <li> Suporte adicional para o evento de despejo Spot VM 'Preempt'<br> | 
| 2017-08-01 | Disponibilidade Geral | Todos | <li> Sublinhado de recursos para VMs IaaS<br><li>Requisito de cabeçalho de metadados imposto para todos os pedidos | 
| 2017-03-01 | Pré-visualização | Todos | <li>Versão inicial |


> [!NOTE] 
> Os lançamentos anteriores de pré-visualização de Eventos Agendados suportavam {mais recente} como a versão api. Este formato já não é suportado e será depreciado no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Ativar e Desativar Eventos Agendados
Os Eventos Agendados estão programados para o seu serviço na primeira vez que faz um pedido de eventos. Deve esperar uma resposta atrasada na sua primeira chamada de até dois minutos.

Os Eventos Agendados são desativados para o seu serviço se não fizer um pedido por 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo utilizador
A manutenção vM iniciada pelo utilizador através do portal Azure, API, CLI ou PowerShell resulta num evento agendado. Em seguida, pode testar a lógica de preparação da manutenção na sua aplicação, e a sua aplicação pode preparar-se para a manutenção iniciada pelo utilizador.

Se reiniciar um VM, está `Reboot` agendado um evento com o tipo. Se reimplantar um VM, está `Redeploy` agendado um evento com o tipo.

## <a name="use-the-api"></a>Utilizar a API

### <a name="headers"></a>Cabeçalhos
Ao consultar o Serviço de Metadados, `Metadata:true` deve fornecer o cabeçalho para garantir que o pedido não foi redirecionado involuntariamente. O `Metadata:true` cabeçalho é necessário para todos os pedidos de eventos agendados. A não intenção de incluir o cabeçalho no pedido resulta numa resposta "Bad Request" do Serviço de Metadados.

### <a name="query-for-events"></a>Consulta para eventos
Você pode consultar para eventos agendados fazendo a seguinte chamada:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

Uma resposta contém uma série de eventos programados. Uma matriz vazia significa que, atualmente, não estão agendados eventos.
No caso de eventos agendados, a resposta contém uma série de eventos. 
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

### <a name="event-properties"></a>Propriedades do Evento
|Propriedade  |  Descrição |
| - | - |
| Eventid | Identificador globalmente único para este evento. <br><br> Exemplo: <br><ul><li>602d944-d2cd-49c7-8624-8643e71297  |
| Tipo de evento | Impacto que este evento causa. <br><br> Valores: <br><ul><li> `Freeze`: A Máquina Virtual está programada para parar durante alguns segundos. A CPU e a conectividade da rede podem ser suspensas, mas não há impacto na memória ou nos ficheiros abertos.<li>`Reboot`: A Máquina Virtual está programada para reiniciar (perde-se a memória não persistente). <li>`Redeploy`: A Máquina Virtual está programada para se deslocar para outro nó (os discos efémeros estão perdidos). <li>`Preempt`: A Máquina Virtual spot está a ser eliminada (os discos efémeros perdem-se). <li> `Terminate`: A máquina virtual está programada para ser eliminada. |
| ResourceType | Tipo de recurso que este evento afeta. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que este evento afeta. A lista é garantida para conter máquinas de, no máximo, um domínio de [atualização,](manage-availability.md)mas pode não conter todas as máquinas da UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Estado deste evento. <br><br> Valores: <ul><li>`Scheduled`: Este evento está programado para começar `NotBefore` após o tempo especificado na propriedade.<li>`Started`: Este evento já começou.</ul> Nunca `Completed` é concedido nenhum ou estatuto semelhante. O evento já não é devolvido quando o evento estiver terminado.
| NotBefore| Tempo após o qual este evento pode começar. <br><br> Exemplo: <br><ul><li> Seg, 19 set 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Agendamento de eventos
Cada evento está agendado um tempo mínimo no futuro com base no tipo de evento. Desta vez reflete-se na `NotBefore` propriedade de um evento. 

|Tipo de evento  | Aviso mínimo |
| - | - |
| Congelar| 15 minutos |
| Reiniciar | 15 minutos |
| Voltar a implementar | 10 minutos |
| Preempt | 30 segundos |
| Terminar | [Configurar o utilizador:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)5 a 15 minutos |

> [!NOTE] 
> Em alguns casos, o Azure é capaz de prever a falha do hospedeiro devido ao hardware degradado e tentará mitigar a perturbação do seu serviço, agendando uma migração. As máquinas virtuais afetadas receberão `NotBefore` um evento agendado com um que é normalmente alguns dias no futuro. O tempo real varia consoante a avaliação prevista do risco de falha. O Azure tenta dar 7 dias de antecedência quando possível, mas o tempo real varia e pode ser menor se a previsão for de que há uma alta probabilidade de o hardware falhar iminentemente. Para minimizar o risco para o seu serviço caso o hardware falhe antes da migração iniciada pelo sistema, recomendamos que se recoloque a sua máquina virtual o mais rapidamente possível.

### <a name="start-an-event"></a>Iniciar um evento 

Depois de conhecer um evento próximo e terminar a sua lógica para uma `POST` paragem graciosa, `EventId`pode aprovar o evento em destaque fazendo uma chamada para o Serviço de Metadados com . Esta chamada indica ao Azure que pode encurtar o tempo mínimo de notificação (quando possível). 

Espera-se a seguinte amostra `POST` JSON no organismo de pedido. O pedido deve conter `StartRequests`uma lista de . Cada `StartRequest` `EventId` um contém para o evento que pretende acelerar:
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
> Reconhecer um evento permite que o `Resources` evento prossiga para todos no evento, e não apenas para a VM que reconhece o evento. Portanto, pode escolher um líder para coordenar o reconhecimento, que pode ser tão `Resources` simples como a primeira máquina no terreno.

## <a name="python-sample"></a>Amostra de python 

A amostra seguinte consulta o Serviço de Metadados para eventos agendados e aprova cada evento pendente:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01"
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
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Passos seguintes 
- Assista aos [eventos agendados na Sexta-feira do Azure](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) para ver uma demonstração. 
- Reveja as amostras de código de eventos agendados no [repositório de eventos agendados](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)de metadados de instância seletiva do Azure.
- Leia mais sobre as APIs que estão disponíveis no [Serviço de Metadados de Instância](instance-metadata-service.md).
- Saiba mais sobre [a manutenção planeada para máquinas virtuais Linux em Azure.](planned-maintenance.md)
