---
title: Monitorize eventos agendados para os seus VMs Windows em Azure
description: Saiba como monitorizar as suas máquinas virtuais Azure para eventos agendados.
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073321"
---
# <a name="monitoring-scheduled-events"></a>Monitorização de Eventos Agendados

As atualizações são aplicadas a diferentes partes do Azure todos os dias, para manter os serviços em funcionamento seguros e atualizados. Além das atualizações planeadas, podem também ocorrer eventos não planeados. Por exemplo, se for detetada qualquer degradação ou falha de hardware, os serviços do Azure poderão ter de realizar uma manutenção não planeada. Utilizando a migração ao vivo, a memória preservando atualizações e geralmente mantendo uma barra rígida no impacto das atualizações, na maioria dos casos estes eventos são quase transparentes para os clientes, e não têm qualquer impacto ou, no máximo, causam alguns segundos de congelamento virtual da máquina. No entanto, para algumas aplicações, mesmo alguns segundos de congelamento virtual da máquina podem causar um impacto. É importante conhecer antecipadamente a próxima manutenção do Azure, para garantir a melhor experiência para essas aplicações. [O serviço de Eventos Agendados](scheduled-events.md) fornece-lhe uma interface programática para ser notificado sobre a manutenção futura, e permite-lhe lidar graciosamente com a manutenção. 

Neste artigo, vamos mostrar como você pode usar eventos programados para ser notificado sobre eventos de manutenção que podem estar afetando seus VMs e construir alguma automatização básica que pode ajudar na monitorização e análise.


## <a name="routing-scheduled-events-to-log-analytics"></a>Encaminhamento de eventos agendados para Log Analytics

Os Eventos Agendados estão disponíveis no âmbito do Serviço de [Metadados de Instâncias Azure,](instance-metadata-service.md)que está disponível em todas as máquinas virtuais do Azure. Os clientes podem escrever automação para consultar o ponto final das suas máquinas virtuais para encontrar notificações de manutenção programadas e realizar mitigações, como salvar o estado e tirar a máquina virtual da rotação. Recomendamos que a automação de edifícios registe os Eventos Agendados para que possa ter um registo de auditoria de eventos de manutenção do Azure. 

Neste artigo, vamos acompanhá-lo através de como capturar eventos agendados de manutenção para Registar Analytics. Em seguida, vamos desencadear algumas ações básicas de notificação, como enviar um e-mail para a sua equipa e obter uma visão histórica de todos os eventos que afetaram as suas máquinas virtuais. Para a agregação e automatização do evento usaremos [log Analytics,](/azure/azure-monitor/learn/quick-create-workspace)mas você pode usar qualquer solução de monitorização para recolher estes registos e automatização de gatilhos.

![Diagrama mostrando o ciclo de vida do evento](./media/notifications/events.png)

## <a name="prerequisites"></a>Pré-requisitos

Para este exemplo, terá de criar uma [Máquina Virtual do Windows num Conjunto](tutorial-availability-sets.md)de Disponibilidade . Os Eventos Agendados fornecem notificações sobre alterações que podem afetar qualquer uma das máquinas virtuais no seu conjunto de disponibilidade, Serviço de Nuvem, Conjunto de Escala de Máquina Virtual ou VMs autónomos. Vamos executar um [serviço](https://github.com/microsoft/AzureScheduledEventsService) que faz sondagens para eventos agendados num dos VMs que funcionarão como colecionadores, para obter eventos para todos os outros VMs no conjunto de disponibilidade.    

Não apague o grupo de recursos de grupo no final do tutorial.

Também terá de criar um espaço de [trabalho de Log Analytics](/azure/azure-monitor/learn/quick-create-workspace) que utilizaremos para agregar informações dos VMs no conjunto de disponibilidade.

## <a name="set-up-the-environment"></a>Configurar o ambiente

Deverá agora ter 2 VMs iniciais num conjunto de disponibilidade. Agora precisamos criar um 3º VM, chamado myCollectorVM, no mesmo conjunto de disponibilidade. 

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
 

Descarregue a instalação .zip file do projeto a partir do [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Ligue-se ao **myCollectorVM** e copie o ficheiro .zip para a máquina virtual e extrai todos os ficheiros. No seu VM, abra um pedido powerShell. Mova o seu pedido `SchService.ps1`para a `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`pasta contendo, por exemplo: - e instale o serviço.

```powershell
.\SchService.ps1 -Setup
```

Inicie o serviço.

```powershell
.\SchService.ps1 -Start
```

O serviço começará agora a fazer sondagens a cada 10 segundos para quaisquer eventos agendados e aprovará os eventos para acelerar a manutenção.  Congelar, Reiniciar, Reimplantar e Preempt são os eventos capturados pelos eventos da Agenda.   Note que pode estender o script para desencadear algumas mitigações antes de aprovar o evento.

Valide o estado do serviço e certifique-se de que está em funcionamento.

```powershell
.\SchService.ps1 -status  
```

Isto deve `Running`voltar.

O serviço começará agora a fazer sondagens a cada 10 segundos para quaisquer eventos agendados e aprovará os eventos para acelerar a manutenção.  Congelar, Reiniciar, Reimplantar e Preempt são os eventos capturados pelos eventos da Agenda. Pode estender o guião para desencadear algumas mitigações antes de aprovar o evento.

Quando qualquer um dos eventos acima referidos for capturado pelo serviço Schedule Event, será registado no Status de Evento de Registo de Eventos de Eventos de Aplicação, Tipo de Evento, Recursos (nomes de máquinas virtuais) e NotBefore (período mínimo de aviso). Pode localizar os eventos com id 1234 no Registo de Eventos de Aplicação.

Assim que o serviço estiver configurado e iniciado, irá registar eventos nos registos da Aplicação do Windows.   Para verificar se isto funciona, reinicie uma das máquinas virtuais no conjunto de disponibilidade e deverá ver um evento a ser registado no Espectador do Evento em Registos do Windows > Registos de Aplicações mostrando o VM reiniciado. 

![Screenshot do espectador do evento.](./media/notifications/event-viewer.png)

Quando os eventos forem captados pelo serviço Schedule Event, será registado na aplicação até mesmo com o Estado do Evento, Tipo de Evento, Recursos (nome VM) e NotBefore (período mínimo de aviso). Pode localizar os eventos com id 1234 no Registo de Eventos de Aplicação.

> [!NOTE] 
> Neste exemplo, as máquinas virtuais estavam num conjunto de disponibilidade, o que nos permitiu designar uma única máquina virtual como colecionador para ouvir e encaminhar eventos programados para o nosso espaço de trabalho slog analytics. Se tiver máquinas virtuais autónomas, pode executar o serviço em todas as máquinas virtuais e, em seguida, conectá-las individualmente ao seu espaço de trabalho de análise de registo.
>
> Para a nossa configuração, escolhemos o Windows, mas pode desenhar uma solução semelhante no Linux.

Em qualquer momento pode parar/remover o Serviço de `–stop` Eventos Agendadoutilizando os interruptores e `–remove`.

## <a name="connect-to-the-workspace"></a>Ligue-se ao espaço de trabalho


Queremos agora ligar um Espaço de Trabalho de Log Analytics ao VM do colecionador. O espaço de trabalho Log Analytics funciona como um repositório e vamos configurar a recolha de registos de eventos para capturar os registos de aplicação do VM do colecionador. 

 Para encaminhar os Eventos Agendados para o Registo de Eventos, que será guardado como registo de aplicação pelo nosso serviço, terá de ligar a sua máquina virtual ao seu espaço de trabalho Log Analytics.  
 
1. Abra a página para o espaço de trabalho que criou.
1. Em **Connect a uma fonte de dados** selecione **máquinas virtuais Azure (VMs)**.

    ![Ligue-se a um VM como fonte de dados](./media/notifications/connect-to-data-source.png)

1. Procure e selecione **myCollectorVM**. 
1. Na nova página para **myCollectorVM,** selecione **Connect**.

Isto irá instalar o [agente de monitorização](/azure/virtual-machines/extensions/oms-windows) da Microsoft na sua máquina virtual. Levará alguns minutos para ligar o vM ao espaço de trabalho e instalar a extensão. 

## <a name="configure-the-workspace"></a>Configure o espaço de trabalho

1. Abra a página para o seu espaço de trabalho e selecione **Definições Avançadas**.
1. Selecione **Dados** do menu esquerdo e, em seguida, selecione Registos de **Eventos do Windows**.
1. Em **Recolher a partir dos registos de eventos seguintes,** comece a digitar a *aplicação* e, em seguida, selecione **A Aplicação** da lista.

    ![Selecione configurações avançadas](./media/notifications/advanced.png)

1. Deixe **ERRO,** **AVISO**e **INFORMAÇÃO** selecionada e, em seguida, selecione **Guardar** para guardar as definições.


> [!NOTE]
> Haverá algum atraso, e pode demorar até 10 minutos antes do registo estar disponível. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Criar uma regra de alerta com o Monitor Azure 


Uma vez que os eventos são empurrados para log Analytics, você pode executar a seguinte [consulta](/azure/azure-monitor/log-query/get-started-portal) para procurar os eventos de agenda.

1. Na parte superior da página, selecione **Registos** e cola o seguinte na caixa de texto:

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

1. Selecione **Guardar**, e, em seguida, *digitar logquery* para o nome, deixe **a Consulta** como o tipo, escreva *VMLogs* como **categoria**, e, em seguida, selecione **Guardar**. 

    ![Salve a consulta](./media/notifications/save-query.png)

1. Selecione **Nova regra de alerta**. 
1. Na página de regra `collectorworkspace` **Criar,** deixe como **Recurso**.
1. Em **Condições,** selecione a entrada Sempre que a procura de *registo do cliente for <login undefined> *. A página lógica do **sinal Configurar** será aberta.
1. Sob **o valor limiar,** insira *0* e, em seguida, selecione **Done**.
1. Under **Actions**, selecione **Create action group**. A página do **grupo de ação Add** será aberta.
1. Em **nome de grupo action**, *digite myActionGroup*.
1. Em **nome curto,** escreva **myActionGroup**.
1. No **grupo Recursos,** selecione **myResourceGroupAvailability**.
1. Em Ações, no tipo DE **E-mail**DE **NOME AÇÃO,** e, em seguida, selecione **Email/SMS/Push/Voice**. A página **de e-mail/SMS/Push/Voice** será aberta.
1. Selecione **Email,** escreva no seu endereço de e-mail e, em seguida, selecione **OK**.
1. Na página do **grupo de ação Adicionar,** selecione **OK**. 
1. Na página de **regra Criar,** em DETALHES DE **ALERTA,** digite *myAlert* para o nome da **regra alerta,** e, em seguida, digite a regra de alerta de *e-mail* para a **Descrição**.
1. Quando terminar, selecione **Criar a regra**de alerta .
1. Reinicie um dos VMs no conjunto de disponibilidade. Dentro de alguns minutos, deve receber um e-mail que o alerta foi desencadeado.

Para gerir as suas regras de alerta, dirija-se ao grupo de recursos, selecione **Alertas** do menu esquerdo e, em seguida, **selecione Gerir regras** de alerta a partir do topo da página.

     
## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte a página de serviço de [eventos agendados](https://github.com/microsoft/AzureScheduledEventsService) no GitHub.
