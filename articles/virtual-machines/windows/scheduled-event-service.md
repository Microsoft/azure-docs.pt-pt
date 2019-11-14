---
title: Monitorar eventos agendados para suas VMs do Windows no Azure
description: Saiba como monitorar suas máquinas virtuais do Azure para eventos agendados.
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
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073321"
---
# <a name="monitoring-scheduled-events"></a>Eventos Agendados de monitoramento

As atualizações são aplicadas a diferentes partes do Azure todos os dias, para manter os serviços em execução neles seguros e atualizados. Além das atualizações planejadas, eventos não planejados também podem ocorrer. Por exemplo, se alguma degradação ou falha de hardware for detectada, os serviços do Azure talvez precisem executar a manutenção não planejada. Usando a migração dinâmica, preservando as atualizações de memória e geralmente mantendo uma barra estrita sobre o impacto das atualizações, na maioria dos casos esses eventos são quase transparentes para os clientes e não têm impacto ou, no máximo, causam alguns segundos de congelamento da máquina virtual. No entanto, para alguns aplicativos, até alguns segundos de congelamento da máquina virtual podem causar um impacto. Saber com antecedência sobre a futura manutenção do Azure é importante, para garantir a melhor experiência para esses aplicativos. [Eventos agendados serviço](scheduled-events.md) fornece uma interface programática para ser notificada sobre a manutenção futura e permite que você manipule a manutenção normalmente. 

Neste artigo, mostraremos como você pode usar eventos agendados para ser notificado sobre eventos de manutenção que poderiam estar afetando suas VMs e criar uma automação básica que pode ajudar no monitoramento e na análise.


## <a name="routing-scheduled-events-to-log-analytics"></a>Roteamento de eventos agendados para Log Analytics

Eventos Agendados está disponível como parte do [serviço de metadados de instância do Azure](instance-metadata-service.md), que está disponível em todas as máquinas virtuais do Azure. Os clientes podem escrever automação para consultar o ponto de extremidade de suas máquinas virtuais a fim de encontrar notificações de manutenção agendadas e realizar atenuações, como salvar o estado e levar a máquina virtual fora de rotação. É recomendável criar a automação para registrar o Eventos Agendados para que você possa ter um log de auditoria dos eventos de manutenção do Azure. 

Neste artigo, descreveremos como capturar Eventos Agendados de manutenção para Log Analytics. Em seguida, dispararemos algumas ações básicas de notificação, como enviar um email para sua equipe e obter uma exibição histórica de todos os eventos que afetaram suas máquinas virtuais. Para a agregação e a automação de eventos, usaremos [log Analytics](/azure/azure-monitor/learn/quick-create-workspace), mas você pode usar qualquer solução de monitoramento para coletar esses logs e disparar a automação.

![Diagrama mostrando o ciclo de vida do evento](./media/notifications/events.png)

## <a name="prerequisites"></a>Pré-requisitos

Para este exemplo, será necessário criar uma [máquina virtual do Windows em um conjunto de disponibilidade](tutorial-availability-sets.md). Eventos Agendados fornecer notificações sobre as alterações que podem afetar qualquer uma das máquinas virtuais em seu conjunto de disponibilidade, serviço de nuvem, conjunto de dimensionamento de máquinas virtuais ou VMs autônomas. Executaremos um [serviço](https://github.com/microsoft/AzureScheduledEventsService) que sonda eventos agendados em uma das VMs que atuarão como coletor, para obter eventos para todas as outras VMs no conjunto de disponibilidade.    

Não exclua o grupo de recursos de grupo no final do tutorial.

Você também precisará [criar um log Analytics espaço de trabalho](/azure/azure-monitor/learn/quick-create-workspace) que usaremos para agregar informações das VMs no conjunto de disponibilidade.

## <a name="set-up-the-environment"></a>Configurar o ambiente

Agora você deve ter duas VMs iniciais em um conjunto de disponibilidade. Agora, precisamos criar uma 3ª VM, chamada myCollectorVM, no mesmo conjunto de disponibilidade. 

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
 

Baixe o arquivo. zip de instalação do projeto do [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Conecte-se ao **myCollectorVM** e copie o arquivo. zip para a máquina virtual e Extraia todos os arquivos. Em sua VM, abra um prompt do PowerShell. Mova o prompt para a pasta que contém `SchService.ps1`, por exemplo: `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`e configure o serviço.

```powershell
.\SchService.ps1 -Setup
```

Inicie o serviço.

```powershell
.\SchService.ps1 -Start
```

Agora, o serviço iniciará a sondagem a cada 10 segundos para todos os eventos agendados e aprovar os eventos para agilizar a manutenção.  Congelar, reinicializar, reimplantar e preempção são os eventos capturados por eventos de agendamento.   Observe que você pode estender o script para disparar algumas mitigações antes de aprovar o evento.

Valide o status do serviço e verifique se ele está em execução.

```powershell
.\SchService.ps1 -status  
```

Isso deve retornar `Running`.

Agora, o serviço iniciará a sondagem a cada 10 segundos para todos os eventos agendados e aprovar os eventos para agilizar a manutenção.  Congelar, reinicializar, reimplantar e preempção são os eventos capturados por eventos de agendamento. Você pode estender o script para disparar algumas mitigações antes de aprovar o evento.

Quando qualquer um dos eventos acima for capturado pelo serviço de eventos de agendamento, ele será registrado no status do evento do log de eventos do aplicativo, tipo de evento, recursos (nomes de máquina virtual) e não antes (período de aviso mínimo). Você pode localizar os eventos com a ID 1234 no log de eventos do aplicativo.

Depois que o serviço for configurado e iniciado, ele registrará eventos nos logs de aplicativos do Windows.   Para verificar se isso funciona, reinicie uma das máquinas virtuais no conjunto de disponibilidade e você deverá ver um evento sendo registrado no Visualizador de eventos nos logs do Windows > log de aplicativo mostrando a VM reiniciada. 

![Captura de tela do Visualizador de eventos.](./media/notifications/event-viewer.png)

Quando os eventos são capturados pelo serviço de eventos de agendamento, eles serão registrados no aplicativo até mesmo log com status do evento, tipo de evento, recursos (nome da VM) e não antes (período de aviso mínimo). Você pode localizar os eventos com a ID 1234 no log de eventos do aplicativo.

> [!NOTE] 
> Neste exemplo, as máquinas virtuais estavam em um conjunto de disponibilidade, o que nos permitiu designar uma única máquina virtual como o coletor para escutar e rotear eventos agendados para nosso espaço de trabalhos do log Analytics. Se você tiver máquinas virtuais autônomas, poderá executar o serviço em cada máquina virtual e, em seguida, conectá-las individualmente ao seu espaço de trabalho do log Analytics.
>
> Para nossa configuração, escolhemos o Windows, mas você pode criar uma solução semelhante no Linux.

A qualquer momento, você pode parar/remover o serviço de evento agendado usando as opções `–stop` e `–remove`.

## <a name="connect-to-the-workspace"></a>Conectar-se ao espaço de trabalho


Agora queremos conectar um espaço de trabalho Log Analytics à VM do coletor. O espaço de trabalho Log Analytics atua como um repositório e configuraremos a coleta de log de eventos para capturar os logs de aplicativo da VM do coletor. 

 Para rotear o Eventos Agendados para o log de eventos, que será salvo como log do aplicativo pelo nosso serviço, você precisará conectar sua máquina virtual ao seu espaço de trabalho do Log Analytics.  
 
1. Abra a página do espaço de trabalho que você criou.
1. Em **conectar a uma fonte de dados** , selecione **VMS (máquinas virtuais) do Azure**.

    ![Conectar-se a uma VM como uma fonte de dados](./media/notifications/connect-to-data-source.png)

1. Procure e selecione **myCollectorVM**. 
1. Na página novo para **myCollectorVM**, selecione **conectar**.

Isso instalará o [Microsoft Monitoring Agent](/azure/virtual-machines/extensions/oms-windows) em sua máquina virtual. Levará alguns minutos para conectar sua VM ao espaço de trabalho e instalar a extensão. 

## <a name="configure-the-workspace"></a>Configurar o espaço de trabalho

1. Abra a página do seu espaço de trabalho e selecione **Configurações avançadas**.
1. Selecione **dados** no menu à esquerda e, em seguida, selecione **logs de eventos do Windows**.
1. Em **coletar dos seguintes logs de eventos**, comece a digitar *aplicativo* e, em seguida, selecione **aplicativo** na lista.

    ![Selecionar configurações avançadas](./media/notifications/advanced.png)

1. Deixe **erro**, **aviso**e **informações** selecionadas e, em seguida, selecione **salvar** para salvar as configurações.


> [!NOTE]
> Haverá algum atraso e poderá levar até 10 minutos para que o log esteja disponível. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Criando uma regra de alerta com Azure Monitor 


Depois que os eventos forem enviados para Log Analytics, você poderá executar a [consulta](/azure/azure-monitor/log-query/get-started-portal) a seguir para procurar os eventos de agendamento.

1. Na parte superior da página, selecione **logs** e cole o seguinte na caixa de texto:

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

1. Selecione **salvar**e, em seguida, digite *logQuery* para o nome, deixe **consulta** como o tipo, digite *VMLogs* como a **categoria**e, em seguida, selecione **salvar**. 

    ![Salvar a consulta](./media/notifications/save-query.png)

1. Selecione **Nova regra de alerta**. 
1. Na página **criar regra** , deixe `collectorworkspace` como o **recurso**.
1. Em **condição**, selecione a entrada *sempre que a pesquisa de logs do cliente for <login undefined>* . A página **Configurar lógica de sinal** será aberta.
1. Em **valor do limite**, insira *0* e, em seguida, selecione **concluído**.
1. Em **ações**, selecione **Criar grupo de ações**. A página **Adicionar grupo de ações** será aberta.
1. Em **nome do grupo de ações**, digite *myaction*.
1. Em **nome curto**, digite **myaction**.
1. Em **grupo de recursos**, selecione **myResourceGroupAvailability**.
1. Em ações, em **nome da ação** , digite **email**e, em seguida, selecione **email/SMS/Push/voz**. A página **email/SMS/Push/voz** será aberta.
1. Selecione **email**, digite seu endereço de email e, em seguida, selecione **OK**.
1. Na página **Adicionar grupo de ações** , selecione **OK**. 
1. Na página **criar regra** , em **detalhes do alerta**, digite *MyALERT* para o **nome da regra de alerta**e digite *regra de alerta de email* para a **Descrição**.
1. Quando tiver terminado, selecione **criar regra de alerta**.
1. Reinicie uma das VMs no conjunto de disponibilidade. Em alguns minutos, você deve receber um email de que o alerta foi disparado.

Para gerenciar suas regras de alerta, vá para o grupo de recursos, selecione **alertas** no menu à esquerda e, em seguida, selecione **gerenciar regras de alerta** na parte superior da página.

     
## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte a página de [serviço de eventos agendados](https://github.com/microsoft/AzureScheduledEventsService) no github.
