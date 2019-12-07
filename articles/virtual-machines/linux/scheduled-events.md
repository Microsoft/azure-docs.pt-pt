---
title: Eventos Agendados para VMs do Linux no Azure
description: Agende eventos usando o serviço de metadados do Azure para suas máquinas virtuais do Linux.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: c0b30ecb9bc2b029141e528139f2b8a308c3a8dd
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892843"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Serviço de metadados do Azure: Eventos Agendados para VMs Linux

Eventos Agendados é um serviço de metadados do Azure que dá ao seu aplicativo tempo para se preparar para a manutenção de máquinas virtuais (VM). Ele fornece informações sobre eventos de manutenção futuros (por exemplo, reinicialização) para que seu aplicativo possa se preparar para eles e limitar a interrupção. Ele está disponível para todos os tipos de máquinas virtuais do Azure, incluindo PaaS e IaaS no Windows e no Linux. 

Para obter informações sobre Eventos Agendados no Windows, consulte [eventos agendados para VMs do Windows](../windows/scheduled-events.md).

> [!Note] 
> O Eventos Agendados está geralmente disponível em todas as regiões do Azure. Consulte [disponibilidade de versão e região](#version-and-region-availability) para obter informações de versão mais recentes.

## <a name="why-use-scheduled-events"></a>Por que usar Eventos Agendados?

Muitos aplicativos podem se beneficiar do tempo para se preparar para a manutenção da VM. O tempo pode ser usado para executar tarefas específicas do aplicativo que melhoram a disponibilidade, a confiabilidade e a manutenção, incluindo: 

- Ponto de verificação e restauração.
- Descarga de conexão.
- Failover de réplica primária.
- Remoção de um pool de balanceador de carga.
- Log de eventos.
- Desligamento normal.

Com o Eventos Agendados, seu aplicativo pode descobrir quando a manutenção ocorrerá e disparará tarefas para limitar seu impacto.  

Eventos Agendados fornece eventos nos seguintes casos de uso:

- [Manutenção iniciada pela plataforma](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (por exemplo, reinicialização de VM, migração ao vivo ou atualizações de preservação de memória para host)
- Hardware degradado
- Manutenção iniciada pelo usuário (por exemplo, um usuário reinicia ou reimplanta uma VM)
- [Detectar a VM](spot-vms.md) e as remoções de instância do [conjunto de escala de spot](../../virtual-machine-scale-sets/use-spot.md) .

## <a name="the-basics"></a>As noções básicas  

  O serviço de metadados expõe informações sobre a execução de VMs usando um ponto de extremidade REST que é acessível de dentro da VM. As informações estão disponíveis por meio de um IP não roteável para que não seja exposta fora da VM.

### <a name="scope"></a>Âmbito
Os eventos agendados são entregues a:

- Máquinas virtuais autônomas.
- Todas as VMs em um serviço de nuvem.
- Todas as VMs em um conjunto de disponibilidade.
- Todas as VMs em um grupo de posicionamento do conjunto de dimensionamento. 

Como resultado, verifique o campo `Resources` no evento para identificar quais VMs são afetadas.

### <a name="endpoint-discovery"></a>Descoberta de ponto de extremidade
Para VMs habilitadas para VNET, o serviço de metadados está disponível em um IP não roteável estático, `169.254.169.254`. O ponto de extremidade completo para a versão mais recente do Eventos Agendados é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Se a VM não for criada em uma rede virtual, os casos padrão para serviços de nuvem e VMs clássicas, será necessária lógica adicional para descobrir o endereço IP a ser usado. Para saber como [descobrir o ponto de extremidade do host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), consulte este exemplo.

### <a name="version-and-region-availability"></a>Disponibilidade de versão e região
O serviço de Eventos Agendados tem controle de versão. As versões são obrigatórias; a versão atual é `2017-11-01`.

| Versão | Tipo de Versão | Regiões | Notas de Lançamento | 
| - | - | - | - | 
| 2017-11-01 | Disponibilidade Geral | Tudo | <li> Suporte adicionado para o EventType de remoção de VM spot ' preempt '<br> | 
| 2017-08-01 | Disponibilidade Geral | Tudo | <li> Foi removido o sublinhado precedido dos nomes de recursos para VMs IaaS<br><li>Requisito de cabeçalho de metadados imposto para todas as solicitações | 
| 2017-03-01 | Pré-visualização | Tudo | <li>Versão inicial


> [!NOTE] 
> Versões prévias anteriores do Eventos Agendados com suporte {Latest} como a versão de API. Esse formato não é mais suportado e será preterido no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Habilitando e desabilitando Eventos Agendados
O Eventos Agendados está habilitado para o serviço na primeira vez que você faz uma solicitação de eventos. Você deve esperar uma resposta atrasada em sua primeira chamada de até dois minutos.

O Eventos Agendados será desabilitado para seu serviço se ele não fizer uma solicitação por 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo usuário
A manutenção de VM iniciada pelo usuário por meio do portal do Azure, API, CLI ou PowerShell resulta em um evento agendado. Em seguida, você pode testar a lógica de preparação de manutenção em seu aplicativo e seu aplicativo pode se preparar para a manutenção iniciada pelo usuário.

Se você reiniciar uma VM, um evento com o tipo `Reboot` será agendado. Se você reimplantar uma VM, um evento com o tipo `Redeploy` será agendado.

## <a name="use-the-api"></a>Utilizar a API

### <a name="headers"></a>Cabeçalhos
Ao consultar o serviço de metadados, você deve fornecer o `Metadata:true` de cabeçalho para garantir que a solicitação não tenha sido redirecionada de forma não intencional. O cabeçalho de `Metadata:true` é necessário para todas as solicitações de eventos agendados. Falha ao incluir o cabeçalho na solicitação resulta em uma resposta de "solicitação inadequada" do serviço de metadados.

### <a name="query-for-events"></a>Consulta de eventos
Você pode consultar eventos agendados fazendo a seguinte chamada:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que, no momento, nenhum evento está agendado.
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
| EventId | Identificador global exclusivo para este evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto causado por esse evento. <br><br> Valores: <br><ul><li> `Freeze`: a máquina virtual está agendada para pausar por alguns segundos. A conectividade de CPU e de rede pode ser suspensa, mas não há nenhum impacto na memória ou em arquivos abertos.<li>`Reboot`: a máquina virtual está agendada para reinicialização (a memória não persistente é perdida). <li>`Redeploy`: a máquina virtual está agendada para ser movida para outro nó (discos efêmeros são perdidos). <li>`Preempt`: a máquina virtual Spot está sendo excluída (discos efêmeros são perdidos).|
| ResourceType | Tipo de recurso que esse evento afeta. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que esse evento afeta. A lista tem a garantia de conter computadores de no máximo um [domínio de atualização](manage-availability.md), mas pode não conter todos os computadores no UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Status deste evento. <br><br> Valores: <ul><li>`Scheduled`: esse evento está agendado para iniciar após o tempo especificado na propriedade `NotBefore`.<li>`Started`: este evento foi iniciado.</ul> Nenhum `Completed` ou status semelhante já foi fornecido. O evento não é mais retornado quando o evento é concluído.
| NotBefore| Tempo após o qual esse evento pode ser iniciado. <br><br> Exemplo: <br><ul><li> Segunda-feira, 19 de setembro de 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Agendamento de eventos
Cada evento é agendado uma quantidade mínima de tempo no futuro com base no tipo de evento. Esse tempo é refletido na propriedade de `NotBefore` de um evento. 

|EventType  | Aviso mínimo |
| - | - |
| Trave| 15 minutos |
| Reiniciar | 15 minutos |
| Voltar a implementar | 10 minutos |
| Ocupa | 30 segundos |

### <a name="start-an-event"></a>Iniciar um evento 

Depois de aprender sobre um evento futuro e concluir sua lógica para desligamento normal, você pode aprovar o evento pendente fazendo uma chamada de `POST` para o serviço de metadados com `EventId`. Essa chamada indica ao Azure que ele pode reduzir o tempo mínimo de notificação (quando possível). 

O exemplo de JSON a seguir é esperado no corpo da solicitação `POST`. A solicitação deve conter uma lista de `StartRequests`. Cada `StartRequest` contém `EventId` para o evento que você deseja acelerar:
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
> A confirmação de um evento permite que o evento prossiga para todos os `Resources` no evento, não apenas a VM que reconhece o evento. Portanto, você pode optar por escolher um líder para coordenar a confirmação, que pode ser tão simples quanto a primeira máquina no campo `Resources`.

## <a name="python-sample"></a>Exemplo de Python 

O exemplo a seguir consulta o serviço de metadados para eventos agendados e aprova cada evento pendente:

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

## <a name="next-steps"></a>Passos seguintes 
- Assista [eventos agendados na sexta-feira do Azure](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) para ver uma demonstração. 
- Examine os exemplos de código de Eventos Agendados no [repositório de metadados de instância do Azure eventos agendados GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Leia mais sobre as APIs que estão disponíveis no [serviço de metadados de instância](instance-metadata-service.md).
- Saiba mais sobre [a manutenção planejada para máquinas virtuais do Linux no Azure](planned-maintenance.md).
