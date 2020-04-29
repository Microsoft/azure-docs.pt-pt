---
title: Solução de Monitorização VMware no Monitor Azure [ Monitor] Microsoft Docs
description: Saiba como a solução de Monitorização VMware pode ajudar a gerir os registos e monitorizar os anfitriões ESXi.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: c1622ef16155206d779c6d703fc7da568d233e7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77664784"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Solução de Monitorização VMware (Depreciada) no Monitor Azure

![Símbolo VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> A solução de monitorização VMware foi depreciada.  Os clientes que já instalaram a solução podem continuar a utilizá-la, mas a VMware Monitoring não pode ser adicionada a quaisquer novos espaços de trabalho.

A solução de monitorização VMware no Monitor Azure é uma solução que o ajuda a criar uma abordagem centralizada de exploração e monitorização de grandes registos VMware. Este artigo descreve como você pode resolver problemas, capturar e gerir os anfitriões ESXi em um único local usando a solução. Com a solução, pode ver dados detalhados para todos os seus anfitriões ESXi num único local. Você pode ver as principais contagens de eventos, estado e tendências dos anfitriões VM e ESXi fornecidos através dos registos de anfitriões ESXi. Você pode resolver problemas visualizando e pesquisando registos de anfitriões ESXi centralizados. E pode criar alertas com base em consultas de pesquisa de registo.

A solução utiliza a funcionalidade de slog nativo do hospedeiro ESXi para empurrar dados para um VM alvo, que tem o agente Log Analytics. No entanto, a solução não escreve ficheiros em syslog dentro do VM alvo. O agente Log Analytics abre a porta 1514 e ouve isto. Uma vez que recebe os dados, o agente Log Analytics empurra os dados para o Monitor Azure.

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

* Adicione a solução de monitorização VMware à sua subscrição utilizando o processo descrito na [Instalação de uma solução de monitorização](../insights/solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Anfitriões VMware ESXi suportados
vSphere ESXi Host 5.5, 6.0 e 6.5

#### <a name="prepare-a-linux-server"></a>Prepare um servidor Linux
Crie um VM do sistema operativo Linux para receber todos os dados syslog dos anfitriões ESXi. O [agente Log Analytics Linux](../learn/quick-collect-linux-computer.md) é o ponto de recolha de todos os dados de syslog esXi. Pode utilizar vários anfitriões ESXi para encaminhar registos para um único servidor Linux, como no exemplo seguinte.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![fluxo syslog](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configure coleção de syslog
1. Configurar o encaminhamento de sislog para a VSphere. Para obter informações detalhadas para ajudar a configurar o encaminhamento de syslog, consulte [o syslog de configuração em ESXi 5.0 e superior (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Vá ao**Syslog****de Configurações Avançadas** > de Configurações avançadas do **SOFTWARE DE Configuração** > **Software** > ESXi .
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. No campo *Syslog.global.logHost,* adicione o seu servidor Linux e o número de porta *1514*. Por exemplo, `tcp://hostname:1514` ou `tcp://123.456.789.101:1514`
1. Abra a firewall de hospedeiro ESXi para syslog. **ESXi Anfitrião Configuração** > **Software** > Software**Security Profile** > **Firewall** e open **Properties**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Verifique a consola vSphere para verificar se o syslog está corretamente configurado. Confirme no hospedeiro ESXI que a porta **1514** está configurada.
1. Descarregue e instale o agente Log Analytics para O Linux no servidor Linux. Para mais informações, consulte a [Documentação para o agente Delog Analytics para linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Depois de instalado o agente Log Analytics para o Linux, aceda ao diretório /etc/opt/microsoft/omsagent/sysconf/omsagent.d e copie o ficheiro vmware_esxi.conf para o ficheiro /etc/opt/microsoft/omsagent/conf/omsagent.d e a alteração do proprietário/grupo e permissões do ficheiro. Por exemplo:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Reinicie o agente Log Analytics `sudo /opt/microsoft/omsagent/bin/service_control restart`para o Linux executando .
1. Teste a conectividade entre o servidor Linux e `nc` o hospedeiro ESXi utilizando o comando no hospedeiro ESXi. Por exemplo:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. No portal Azure, faça uma `VMware_CL`consulta de log para . Quando o Monitor Azure recolhe os dados do syslog, mantém o formato syslog. No portal, alguns campos específicos são capturados, tais como *Hostname* e *ProcessName*.  

    ![tipo](./media/vmware/type.png)  

    Se os resultados de pesquisa de registo suporem semelhantes à imagem acima, está preparado para utilizar o painel de instrumentos de monitorização VMware.  

## <a name="vmware-data-collection-details"></a>Detalhes da recolha de dados vMware
A solução de Monitorização VMware recolhe várias métricas de desempenho e dados de registo dos anfitriões ESXi utilizando os agentes Log Analytics para o Linux que ativou.

O quadro seguinte mostra métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos.

| 
    da Microsoft
   | Agente de Log Analytics para Linux | Agente SCOM | Storage do Azure | SCOM necessário? | Dados do agente SCOM enviados através de grupo de gestão | frequência de recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |a cada 3 minutos |

A tabela que se segue mostra exemplos de campos de dados recolhidos pela solução de monitorização VMware:

| nome de campo | descrição |
| --- | --- |
| Device_s |Dispositivos de armazenamento VMware |
| ESXIFailure_s |tipos de falha |
| EventTime_t |tempo em que o evento ocorreu |
| HostName_s |Nome anfitrião ESXi |
| Operation_s |criar VM ou excluir VM |
| ProcessName_s |nome do evento |
| ResourceId_s |nome do anfitrião VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Estatuto VMware SCSI |
| SyslogMessage_s |Dados syslog |
| UserName_s |utilizador que criou ou apagou VM |
| VMName_s |o nome da VM |
| Computador |computador anfitrião |
| TimeGenerated |tempo que os dados foram gerados |
| DataCenter_s |Centro de dados VMware |
| StorageLatency_s |latência de armazenamento (Ms) |

## <a name="vmware-monitoring-solution-overview"></a>Visão geral da solução de monitorização VMware
O azulejo VMware aparece no seu espaço de trabalho Log Analytics. Proporciona uma visão de alto nível de quaisquer falhas. Quando clicar no azulejo, entra numa vista do painel de instrumentos.

![mosaicos](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navegue na vista do painel de instrumentos
Na vista do painel de instrumentos **VMware,** as lâminas são organizadas por:

* Contagem de estado de falha
* Melhor anfitrião por contagens de eventos
* Contagem de eventos
* Atividades de Máquina Virtual
* Eventos de disco de anfitriões ESXi

![solução1](./media/vmware/solutionview1-1.png)

![solução2](./media/vmware/solutionview1-2.png)

Clique em qualquer lâmina para abrir painel de pesquisa de Log Analytics que mostre informações detalhadas específicas para a lâmina.

A partir daqui, pode editar a consulta de registo para modificá-la para algo específico. Para mais detalhes sobre a criação de consultas de registo, consulte localizar dados utilizando consultas de [registo no Monitor Azure](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Encontre eventos anfitriões ESXi
Um único hospedeiro ESXi gera vários troncos, com base nos seus processos. A solução de monitorização VMware centraliza-os e resume as contagens do evento. Esta visão centralizada ajuda-o a entender qual o hospedeiro ESXi tem um grande volume de eventos e quais os eventos que ocorrem mais frequentemente no seu ambiente.

![evento](./media/vmware/events.png)

Pode perfurar mais clicando num hospedeiro ESXi ou num tipo de evento.

Quando clica num nome de anfitrião ESXi, vê informações do anfitrião ESXi. Se quiser reduzir os resultados com `“ProcessName_s=EVENT TYPE”` o tipo de evento, adicione a sua consulta de pesquisa. Pode selecionar o **Nome do Processo** no filtro de pesquisa. Isso limita a informação para si.

![broca](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Encontre atividades vm elevadas
Uma máquina virtual pode ser criada e eliminada em qualquer hospedeiro ESXi. É útil para um administrador identificar quantos VMs um hospedeiro ESXi cria. Por sua vez, ajuda a compreender o desempenho e o planeamento da capacidade. Acompanhar os eventos de atividade da VM é crucial na gestão do seu ambiente.

![broca](./media/vmware/vmactivities1.png)

Se quiser ver dados adicionais de criação de VM do esxi, clique num nome de anfitrião ESXi.

![broca](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Consultas comuns de registo
A solução inclui outras consultas úteis que podem ajudá-lo a gerir os seus anfitriões ESXi, tais como espaço de armazenamento elevado, latência de armazenamento e falha no caminho.

![consultas](./media/vmware/queries.png)


#### <a name="save-queries"></a>Guardar consultas
Guardar consultas de registo é uma funcionalidade padrão no Monitor Azure e pode ajudá-lo a manter quaisquer consultas que tenha achado úteis. Depois de criar uma consulta que acha útil, guarde-a clicando nos **Favoritos**. Uma consulta guardada permite-lhe reutilizá-la facilmente mais tarde a partir da página [My Dashboard](../learn/tutorial-logs-dashboards.md) onde pode criar os seus próprios dashboards personalizados.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Criar alertas a partir de consultas
Depois de ter criado as suas consultas, talvez queira usar as consultas para alertá-lo quando ocorrerem eventos específicos. Consulte [alertas em Log Analytics](../platform/alerts-overview.md) para obter informações sobre como criar alertas. Por exemplo, em consultas de alerta e outros exemplos de consultas, consulte o [Monitor VMware utilizando](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) a publicação de blog Log Analytics.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>O que preciso fazer no cenário de hospedeiro ESXi? Que impacto terá no meu ambiente atual?
A solução utiliza o mecanismo de encaminhamento syslog de anfitriões ESXi nativo. Não precisa de nenhum software adicional da Microsoft no EsXi Host para capturar os registos. Deve ter um baixo impacto no seu ambiente existente. No entanto, é necessário definir o encaminhamento de syslog, que é a funcionalidade ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Preciso reiniciar o meu anfitrião ESXi?
Não. Este processo não requer um recomeço. Por vezes, a vSphere não atualiza corretamente o syslog. Nesse caso, aceda ao hospedeiro ESXi e recarregue o syslog. Mais uma vez, não tens de reiniciar o hospedeiro, por isso este processo não é perturbador para o teu ambiente.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Posso aumentar ou diminuir o volume de dados de registo enviados para o Log Analytics?
Sim, tu podes. Pode utilizar as definições de nível de registo do hospedeiro ESXi na vSphere. A recolha de registos baseia-se no nível de *informação.* Por isso, se quiser auditar a criação ou eliminação de VM, tem de manter o nível de *informação* no Hostd. Para mais informações, consulte a Base de [Conhecimento vMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Porque é que o Hostd não fornece dados ao Log Analytics? A minha definição de registo está definida para informações.
Havia um inseto hospedeiro ESXi para o carimbo de tempo do syslog. Para mais informações, consulte a Base de [Conhecimento vMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Depois de aplicar a suver, o Hospedeiro deve funcionar normalmente.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Posso ter vários anfitriões ESXi a encaminhar dados de syslog para um único VM com omsagent?
Sim. Você pode ter vários anfitriões ESXi reencaminhando para um único VM com omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Por que não vejo dados a fluir para o Log Analytics?
Pode haver múltiplas razões:

* O hospedeiro ESXi não está corretamente a empurrar dados para o agente de execução vm. Para testar, efetue os seguintes passos:

  1. Para confirmar, inicie sessão no hospedeiro ESXi utilizando o SSH e execute o seguinte comando:`nc -z ipaddressofVM 1514`

      Se isto não for bem sucedido, as definições vSphere na Configuração Avançada provavelmente não estão corretas. Consulte a recolha de [syslog configure](#configure-syslog-collection) para obter informações sobre como configurar o hospedeiro ESXi para encaminhamento de syslog.
  1. Se a conectividade da porta syslog for bem sucedida, mas ainda não vê nenhum dado, em seguida, recarregue o slog no hospedeiro ESXi usando ssh para executar o seguinte comando:`esxcli system syslog reload`
* O VM com o agente Log Analytics não está corretamente definido. Para testar isto, execute os seguintes passos:

  1. Log Analytics ouve a porta 1514. Para verificar se está aberto, execute o seguinte comando:`netstat -a | grep 1514`
  1. Devia ver `1514/tcp` o porto aberto. Se não o fizer, verifique se o agente omsagent está instalado corretamente. Se não vir as informações da porta, então a porta syslog não está aberta no VM.

    a. Verifique se o agente Log `ps -ef | grep oms`Analytics está a funcionar utilizando . Se não estiver a funcionar, inicie o processo executando o comando`sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Abra o ficheiro `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Verifique se a definição adequada do utilizador e do grupo é válida, semelhante a:`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Se o ficheiro não existir ou se a definição do utilizador e do grupo estiver errada, tome medidas corretivas [preparando um servidor Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Passos seguintes
* Utilize consultas de [registo](../log-query/log-query-overview.md) no Log Analytics para visualizar dados detalhados do anfitrião vMware.
* [Crie os seus próprios dashboards](../learn/tutorial-logs-dashboards.md) mostrando dados do anfitrião VMware.
* [Crie alertas](../platform/alerts-overview.md) quando ocorrerem eventos específicos de anfitriões vMware.
