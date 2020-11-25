---
title: Monitorar eventos agendados para os seus VMs windows em Azure
description: Saiba como monitorizar as suas máquinas virtuais Azure para eventos agendados.
author: mysarn
ms.service: virtual-machines-windows
ms.subservice: monitoring
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: 0806c6e0ed89c2c0f4712ec985599810119fcf89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015524"
---
# <a name="monitoring-scheduled-events"></a>Monitorização de Eventos Agendados

As atualizações são aplicadas a diferentes partes do Azure todos os dias, para manter os serviços em funcionamento sobre eles seguros e atualizados. Além das atualizações planeadas, podem também ocorrer eventos não planeados. Por exemplo, se for detetada qualquer degradação ou falha de hardware, os serviços da Azure poderão necessitar de efetuar uma manutenção não planeada. Utilizando a migração ao vivo, preservando a memória atualizações e mantendo geralmente uma barra rígida sobre o impacto das atualizações, na maioria dos casos estes eventos são quase transparentes para os clientes, e não têm impacto ou, no máximo, causam alguns segundos de congelamento de máquinas virtuais. No entanto, para algumas aplicações, mesmo alguns segundos de congelamento de máquinas virtuais podem causar um impacto. Conhecer antecipadamente a próxima manutenção do Azure é importante, para garantir a melhor experiência para essas aplicações. [O serviço Eventos Agendados](scheduled-events.md) fornece-lhe uma interface programática para ser notificado sobre a próxima manutenção, e permite-lhe lidar graciosamente com a manutenção. 

Neste artigo, mostraremos como você pode usar eventos programados para ser notificado sobre eventos de manutenção que podem estar afetando seus VMs e construir alguma automatização básica que pode ajudar na monitorização e análise.


## <a name="routing-scheduled-events-to-log-analytics"></a>Eventos programados de encaminhamento para Log Analytics

Eventos Agendados estão disponíveis como parte do Serviço de [Metadados Azure Instance,](instance-metadata-service.md)que está disponível em todas as máquinas virtuais Azure. Os clientes podem escrever automatização para consultar o ponto final das suas máquinas virtuais para encontrar notificações de manutenção programadas e realizar mitigações, como salvar o estado e tirar a máquina virtual da rotação. Recomendamos a automatização de edifícios para registar os Eventos Agendados para que possa ter um registo de auditoria de eventos de manutenção da Azure. 

Neste artigo, vamos acompanhá-lo como capturar eventos programados de manutenção para registar analítica. Em seguida, vamos desencadear algumas ações básicas de notificação, como o envio de um e-mail para a sua equipa e obter uma visão histórica de todos os eventos que afetaram as suas máquinas virtuais. Para a agregação e automatização do evento utilizaremos [o Log Analytics,](../../azure-monitor/learn/quick-create-workspace.md)mas pode utilizar qualquer solução de monitorização para recolher estes registos e desencadear a automatização.

![Diagrama mostrando o ciclo de vida do evento](./media/notifications/events.png)

## <a name="prerequisites"></a>Pré-requisitos

Para este exemplo, terá de criar uma [máquina virtual do Windows num Conjunto de Disponibilidade.](tutorial-availability-sets.md) Eventos Agendados fornecem notificações sobre alterações que podem afetar qualquer uma das máquinas virtuais no seu conjunto de disponibilidade, Cloud Service, Virtual Machine Scale set ou VMs autónomos. Vamos realizar um [serviço](https://github.com/microsoft/AzureScheduledEventsService) que sonda para eventos agendados num dos VMs que funcionarão como colecionador, para obter eventos para todos os outros VMs no conjunto de disponibilidade.    

Não apague o grupo de recursos no final do tutorial.

Também terá de [criar um espaço de trabalho Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) que iremos utilizar para agregar informações dos VMs no conjunto de disponibilidade.

## <a name="set-up-the-environment"></a>Configurar o ambiente

Deve agora ter 2 VMs iniciais num conjunto de disponibilidade. Agora precisamos criar um 3º VM, chamado myCollectorVM, no mesmo conjunto de disponibilidade. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Descarregue a instalação .zip ficheiro do projeto do [GitHub.](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip)

Ligue-se ao **myCollectorVM** e copie o ficheiro .zip à máquina virtual e extraia todos os ficheiros. No seu VM, abra um pedido powerShell. Mova a sua solicitação para a pasta `SchService.ps1` contendo, por `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>` exemplo, e instale o serviço.

```powershell
.\SchService.ps1 -Setup
```

Inicie o serviço.

```powershell
.\SchService.ps1 -Start
```

O serviço começará agora a sondar a cada 10 segundos para quaisquer eventos agendados e aprovará os eventos para acelerar a manutenção.  Freeze, Reboot, Reemploy e Preempt são os eventos capturados pelos eventos da Agenda.   Note que pode estender o script para desencadear algumas mitigações antes de aprovar o evento.

Valide o estado de serviço e certifique-se de que está em funcionamento.

```powershell
.\SchService.ps1 -status  
```

Isto deve `Running` voltar.

O serviço começará agora a sondar a cada 10 segundos para quaisquer eventos agendados e aprovará os eventos para acelerar a manutenção.  Freeze, Reboot, Reemploy e Preempt são os eventos capturados pelos eventos da Agenda. Pode estender o script para desencadear algumas mitigações antes de aprovar o evento.

Quando qualquer um dos eventos acima referidos for capturado pelo serviço Schedule Event, será registado no Estado do Evento de Registo de Eventos de Aplicação, Tipo de Evento, Recursos (nomes de máquinas virtuais) e NotBefore (período mínimo de aviso). Pode localizar os eventos com o ID 1234 no Registo de Eventos de Aplicação.

Uma vez que o serviço é configurado e iniciado, ele registará eventos nos registos da Aplicação do Windows.   Para verificar este funcionamento, reinicie uma das máquinas virtuais no conjunto de disponibilidades e deverá ver um evento a ser registado no visualizador do Evento em Registos do Windows > registo de aplicação mostrando que o VM foi reiniciado. 

![Screenshot do espectador do evento.](./media/notifications/event-viewer.png)

Quando os eventos forem capturados pelo serviço Evento de Horário, será registado na aplicação mesmo com o Estado do Evento, Tipo de Evento, Recursos (nome VM) e NotBefore (período mínimo de pré-aviso). Pode localizar os eventos com o ID 1234 no Registo de Eventos de Aplicação.

> [!NOTE] 
> Neste exemplo, as máquinas virtuais encontravam-se num conjunto de disponibilidade, o que nos permitiu designar uma única máquina virtual como colecionador para ouvir e encaminhar eventos programados para o nosso espaço de trabalhos de log analytics. Se tiver máquinas virtuais autónomas, pode executar o serviço em todas as máquinas virtuais e, em seguida, conectá-las individualmente ao seu espaço de trabalho de análise de registo.
>
> Para a nossa configuração, escolhemos o Windows, mas podes desenhar uma solução semelhante no Linux.

Em qualquer ponto pode parar/remover o Serviço de Eventos Programado utilizando os interruptores `–stop` e `–remove` .

## <a name="connect-to-the-workspace"></a>Ligar-se ao espaço de trabalho


Queremos agora ligar um espaço de trabalho log analytics ao VM colecionador. O espaço de trabalho Log Analytics funciona como um repositório e vamos configurar a recolha de registos de eventos para capturar os registos de aplicações do VM do colecionador. 

 Para encaminhar os Eventos Agendados para o Registo de Eventos, que será guardado como registo de aplicação pelo nosso serviço, terá de ligar a sua máquina virtual ao seu espaço de trabalho Log Analytics.  
 
1. Abra a página para o espaço de trabalho que criou.
1. Em **Ligação a uma fonte** de dados selecione **máquinas virtuais Azure (VMs)**.

    ![Ligue-se a um VM como fonte de dados](./media/notifications/connect-to-data-source.png)

1. Procure e selecione **myCollectorVM**. 
1. Na nova página para **o myCollectorVM,** selecione **Connect**.

Isto instalará o [agente microsoft monitor na](../extensions/oms-windows.md) sua máquina virtual. Levará alguns minutos para ligar o seu VM ao espaço de trabalho e instalar a extensão. 

## <a name="configure-the-workspace"></a>Configure o espaço de trabalho

1. Abra a página para o seu espaço de trabalho e selecione **definições avançadas**.
1. Selecione **Dados** do menu esquerdo e, em seguida, selecione **Registos de Eventos do Windows**.
1. Em **Recolher a partir dos seguintes registos de eventos,** comece a digitar a *aplicação* e, em seguida, selecione **Aplicação** da lista.

    ![Selecione configurações avançadas](./media/notifications/advanced.png)

1. Deixar **ERRO**, **AVISO** e **INFORMAÇÃO** selecionados e, em seguida, **selecionar Guardar** para guardar as definições.


> [!NOTE]
> Haverá algum atraso, e pode levar até 10 minutos antes do registo estar disponível. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Criar uma regra de alerta com o Azure Monitor 


Uma vez que os eventos são empurrados para Log Analytics, você pode executar a seguinte [consulta](../../azure-monitor/log-query/get-started-portal.md) para procurar o horário Eventos.

1. No topo da página, selecione **Logs** e cole o seguinte na caixa de texto:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. **Selecione Guardar**, e, em seguida, *digitar logQuery* para o nome, deixar **a Consulta** como o tipo, escrever *VMLogs* como **categoria**, e, em seguida, selecionar **Guardar**. 

    ![Salve a consulta](./media/notifications/save-query.png)

1. Selecione **Nova regra de alerta**. 
1. Na página **'Criar' regras,** deixe `collectorworkspace` como **recurso**.
1. Em **Condição**, selecione a entrada *Sempre que a pesquisa de registo do cliente estiver <login undefined>*. A página **lógica de sinal de configuração** abrir-se-á.
1. No **valor limiar**, insira *0* e, em seguida, selecione **Fazer**.
1. Em **Ações**, **selecione Criar grupo de ação**. A página **do grupo de ação Add** será aberta.
1. Em **Nome do grupo Action**, digite *myActionGroup*.
1. Em **nome curto,** escreva **myActionGroup**.
1. No **grupo de recursos,** selecione **myResourceGroupAvailability**.
1. Em Ações, em **ACTION NAME** tipo **Email**, e, em seguida, selecione **Email/SMS/Push/Voice**. A página **email/SMS/Push/Voice** abrirá.
1. Selecione **Email**, escreva no seu endereço de e-mail e, em seguida, selecione **OK**.
1. Na página do **grupo de ação Add,** selecione **OK**. 
1. Na página **'Criar' regra,** em **DETALHES ALERTA,** digite *myAlert* para o **nome da regra de alerta** e, em seguida, escreva a regra de alerta de *e-mail* para a **Descrição**.
1. Quando terminar, selecione **Criar a regra de alerta**.
1. Reinicie um dos VMs no conjunto de disponibilidade. Dentro de alguns minutos, deve receber um e-mail de que o alerta foi desencadeado.

Para gerir as suas regras de alerta, vá ao grupo de recursos, selecione **Alertas** do menu esquerdo e, em seguida, **selecione Gerir as regras** de alerta a partir do topo da página.

     
## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte a página [de serviço de eventos agendados](https://github.com/microsoft/AzureScheduledEventsService) no GitHub.
