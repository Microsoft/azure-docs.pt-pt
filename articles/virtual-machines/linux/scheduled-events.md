---
title: Eventos agendados para VMs do Linux no Azure | Documentos da Microsoft
description: Agendar eventos com o serviço de metadados do Azure para as suas máquinas virtuais do Linux.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 6337477b55addefb7579d6f328473428ba72ba24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799264"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Serviço de metadados do Azure: Eventos agendados para VMs do Linux

Eventos agendados é um serviço de metadados do Azure que fornece a hora de aplicação para se preparar para a manutenção de máquina virtual (VM). Fornece informações sobre eventos de manutenção futura (por exemplo, reinícios), para que seu aplicativo pode se preparar e limitar a interrupção. Está disponível para todos os tipos de máquinas virtuais do Azure, incluindo PaaS e IaaS no Windows e Linux. 

Para obter informações sobre eventos agendados no Windows, consulte [agendada de eventos para Windows VMs](../windows/scheduled-events.md).

> [!Note] 
> Eventos agendados está disponível em geral em todas as regiões do Azure. Ver [versão e disponibilidade das regiões](#version-and-region-availability) para informações de versão mais recente.

## <a name="why-use-scheduled-events"></a>Por que usar eventos agendados?

Muitos aplicativos podem beneficiar de tempo para se preparar para a manutenção da VM. O tempo pode ser utilizado para executar tarefas específicas do aplicativo que melhorar a disponibilidade, fiabilidade e capacidade de serviço, incluindo: 

- Ponto de verificação e restauro.
- Ligação a ser drenado.
- Ativação pós-falha de réplica primária.
- Remoção de um conjunto de Balanceador de carga.
- Log de eventos.
- Encerramento correto.

Com eventos agendados, seu aplicativo pode detetar quando manutenção irá ocorrer e acionar tarefas para limitar seu impacto.  

Eventos agendados fornece eventos nos seguintes casos de utilização:

- Manutenção iniciada pelo plataforma (por exemplo, uma atualização do sistema operacional host)
- Hardware degradado
- Manutenção iniciada pelo utilizador (por exemplo, um utilizador for reiniciado ou reimplementa uma VM)
- [Expulsão de baixa prioridade VM](https://azure.microsoft.com/en-us/blog/low-priority-scale-sets) conjuntos de dimensionamento

## <a name="the-basics"></a>As noções básicas  

  Serviço de metadados expõe informações sobre como executar as VMs ao utilizar um ponto final REST, que é acessível a partir de dentro da VM. As informações estão disponíveis por meio de um IP nonroutable para que não está exposta fora da VM.

### <a name="scope"></a>Âmbito
Eventos agendados são entregues para:

- Todas as VMs num serviço cloud.
- Todas as VMs num conjunto de disponibilidade.
- Todas as VMs num grupo de colocação do conjunto de dimensionamento. 

Como resultado, verifique o `Resources` campo no evento para identificar qual as VMs são afetadas.

### <a name="endpoint-discovery"></a>Deteção de pontos finais
Para VNET ativada VMs, serviço de metadados está disponível de um IP estático nonroutable, `169.254.169.254`. É o ponto final completo para a versão mais recente do eventos agendados: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Se a VM não é criada numa rede Virtual, os casos de padrão para serviços cloud e VMs clássicas, lógica adicional é necessário para detetar o endereço IP a utilizar. Para saber como [detetar o ponto final de anfitrião](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), veja este exemplo.

### <a name="version-and-region-availability"></a>Versão e a disponibilidade de região
O serviço de eventos agendados tem a mesma versão. Versões são obrigatórias; a versão atual é `2017-11-01`.

| Version | Tipo de versão | Regiões | Notas de Versão | 
| - | - | - | - | 
| 2017-11-01 | Disponibilidade Geral | Todos | <li> Foi adicionado suporte para expulsão de baixa prioridade VM EventType 'Preempt'<br> | 
| 2017-08-01 | Disponibilidade Geral | Todos | <li> Removido o caráter de sublinhado antecedendo nomes de recursos para IaaS VMs<br><li>Requisito de cabeçalho de metadados imposto a todos os pedidos | 
| 2017-03-01 | Pré-visualização | Todos | <li>Versão inicial


> [!NOTE] 
> Versões anteriores de pré-visualização de eventos agendados suportado {mais recente} como a api-version. Este formato já não é suportado e será preterido no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Ativar e desativar eventos agendados
Eventos agendados está ativada para o tempo de serviço a primeira fizer um pedido para eventos. Deve esperar uma resposta atrasada em sua primeira chamada de até dois minutos.

Eventos agendados está desativada para o seu serviço, se não faz um pedido para 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo utilizador
Manutenção da VM iniciado pelo utilizador através do portal do Azure, a API, a CLI ou o PowerShell resulta num evento agendado. Em seguida, pode testar a lógica de preparação de manutenção em seu aplicativo e seu aplicativo pode se preparar para a manutenção iniciada pelo utilizador.

Se reiniciar uma VM, um evento com o tipo de `Reboot` está agendada. Se voltar a implementar uma VM, um evento com o tipo de `Redeploy` está agendada.

## <a name="use-the-api"></a>Utilize a API

### <a name="headers"></a>Cabeçalhos
Ao consultar o serviço de metadados, tem de fornecer o cabeçalho `Metadata:true` para garantir que o pedido não foi redirecionado inadvertidamente. O `Metadata:true` cabeçalho é obrigatório para todos os pedidos de eventos agendados. Falha ao incluir o cabeçalho no pedido resulta numa resposta de "Solicitação incorreta" do serviço de metadados.

### <a name="query-for-events"></a>Consulta para eventos
Pode consultar para eventos agendados, fazendo a seguinte chamada:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que atualmente não existem eventos estão agendados.
No caso em que há eventos agendados, a resposta contém uma matriz de eventos. 
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

### <a name="event-properties"></a>Propriedades do evento
|Propriedade  |  Descrição |
| - | - |
| EventId | Identificador exclusivo global para este evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto faz com que o este evento. <br><br> Valores: <br><ul><li> `Freeze`: A Máquina Virtual está agendada para pausar durante alguns segundos. A CPU está suspenso, mas não há nenhum impacto na memória, ficheiros abertos ou ligações de rede. <li>`Reboot`: A Máquina Virtual está agendada para reinicialização (a memória não persistentes é perdida). <li>`Redeploy`: A Máquina Virtual está agendada para mover para outro nó (discos efémeros são perdidos). <li>`Preempt`: A Máquina Virtual de baixa prioridade está a ser eliminado (discos efémeros são perdidos).|
| ResourceType | Tipo de recurso que afeta a este evento. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que afeta a este evento. A lista é garantida que contêm máquinas a partir de um [domínio de atualização](manage-availability.md), mas não poderá conter todas as máquinas a UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Estado deste evento. <br><br> Valores: <ul><li>`Scheduled`: Este evento está agendado para iniciar após o período de tempo especificado no `NotBefore` propriedade.<li>`Started`: Este evento foi iniciado.</ul> Não `Completed` ou estado semelhante nunca seja fornecido. O evento já não é retornado quando o evento estiver concluído.
| NotBefore| Tempo após o qual pode iniciar este evento. <br><br> Exemplo: <br><ul><li> Segunda-feira, 19 de Setembro de 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Agendamento do evento
Cada evento está agendado uma quantidade mínima de tempo no futuro, com base no tipo de evento. Neste momento é refletido num evento `NotBefore` propriedade. 

|EventType  | Tenha em atenção mínimo |
| - | - |
| Congelamento| 15 minutos |
| Reiniciar | 15 minutos |
| Voltar a implementar | 10 minutos |
| Tomar o lugar | 30 segundos |

### <a name="start-an-event"></a>Iniciar um evento 

Depois de saber de um evento futuro e concluir a sua lógica de encerramento correto, pode aprovar o evento pendente, fazendo uma `POST` chamar para o serviço de metadados com `EventId`. Essa chamada indica para o Azure, que ele pode reduzir a notificação mínima de tempo (quando possível). 

O seguinte exemplo JSON é esperado no `POST` corpo do pedido. O pedido deve conter uma lista de `StartRequests`. Cada `StartRequest` contém `EventId` para o evento que pretende acelerar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Exemplo de bash
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Ter consciência de um evento permite que o evento continuar para todos os `Resources` no caso, não apenas a VM que reconhece o evento. Por conseguinte, pode optar por eleger um coordenador para coordenar a confirmação, que pode ser tão simples como a primeira máquina no `Resources` campo.

## <a name="python-sample"></a>Exemplo de Python 

O exemplo a seguir consulta o serviço de metadados para eventos agendados e aprova a cada evento pendentes:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01"
headers = "{Metadata:true}"
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
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Passos Seguintes 
- Assista [eventos agendados no Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) para ver uma demonstração. 
- Reveja os exemplos de código de eventos agendados na [repositório do GitHub de eventos agendados Azure instância metadados](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Saiba mais sobre as APIs que estão disponíveis no [serviço de metadados de instância](instance-metadata-service.md).
- Saiba mais sobre [planejado de manutenção de máquinas virtuais do Linux no Azure](planned-maintenance.md).
