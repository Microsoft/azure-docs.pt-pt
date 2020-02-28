---
title: Solução de Monitorização VMware no Monitor Azure [ Monitor] Microsoft Docs
description: Saiba mais sobre como a solução de monitorização de VMware pode ajudar a gerir os registos e monitorizar anfitriões ESXi.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: c1622ef16155206d779c6d703fc7da568d233e7e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664784"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Solução de Monitorização VMware (Depreciada) no Monitor Azure

![Símbolo de VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> A solução de monitorização de VMware foi preterida.  Os clientes que já tenham instalado a solução podem continuar a utilizá-lo, mas a monitorização de VMware não podem ser adicionado a novas áreas de trabalho.

A solução de monitorização VMware no Monitor Azure é uma solução que o ajuda a criar uma abordagem centralizada de exploração e monitorização de grandes registos VMware. Este artigo descreve como pode resolver problemas, capturar e gerir anfitriões ESXi numa única localização através da solução. Com a solução, pode ver dados detalhados de todos os seus anfitriões ESXi numa única localização. Pode ver contagens dos melhores eventos, o estado e as tendências de hosts VM e ESXi fornecidos através de registos de anfitrião ESXi. Pode solucionar ao visualizar e pesquisar registos de anfitrião do ESXi centralizados. E, pode criar alertas com base em consultas de pesquisa de registo.

A solução utiliza a funcionalidade de syslog nativo do anfitrião ESXi para enviar dados para um destino de VM, o que tem o agente do Log Analytics. No entanto, a solução não gravar arquivos no syslog dentro da VM de destino. O agente Log Analytics abre a porta 1514 e escutada por isto. Uma vez que recebe os dados, o agente Log Analytics empurra os dados para o Monitor Azure.

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

* Adicione a solução de monitorização VMware à sua subscrição utilizando o processo descrito na [Instalação de uma solução de monitorização](../insights/solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Anfitriões do VMware ESXi suportados
vSphere anfitrião ESXi 5.5, 6.0 e 6.5

#### <a name="prepare-a-linux-server"></a>Preparar um servidor do Linux
Crie um sistema de operativo Linux VM receba todos os dados do syslog dos anfitriões ESXi. O [agente Log Analytics Linux](../learn/quick-collect-linux-computer.md) é o ponto de recolha de todos os dados de syslog esXi. Pode usar vários anfitriões ESXi para reencaminhar os registos para um único servidor do Linux, como no exemplo seguinte.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![fluxo de syslog](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurar a recolha de syslog
1. Configure o reencaminhamento do syslog para VSphere. Para obter informações detalhadas para ajudar a configurar o encaminhamento de syslog, consulte [o syslog de configuração em ESXi 5.0 e superior (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Vá à configuração > **software** **de configuração do hospedeiro ESXi** > **Definições Avançadas** > **Syslog**.
   ![](./media/vmware/vsphere1.png) vsphereconfig  
1. No campo *Syslog.global.logHost,* adicione o seu servidor Linux e o número de porta *1514*. Por exemplo, `tcp://hostname:1514` ou `tcp://123.456.789.101:1514`
1. Abra a firewall do anfitrião ESXi para syslog. **ESXi Configuração** de anfitriões > **software** > perfil de **segurança** > **Firewall** e propriedades **abertas**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Verifique o consola para verificar que essa syslog está devidamente configurado do vSphere. Confirme no hospedeiro ESXI que a porta **1514** está configurada.
1. Baixe e instale o agente do Log Analytics para Linux no servidor do Linux. Para mais informações, consulte a [Documentação para o agente Delog Analytics para linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Depois do agente Log Analytics para Linux está instalado, vá para o diretório de /etc/opt/microsoft/omsagent/sysconf/omsagent.d e copie o vmware_esxi.conf ficheiro para o diretório de /etc/opt/microsoft/omsagent/conf/omsagent.d e a alteração, o proprietário/grupo e permissões do ficheiro. Por exemplo:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Reinicie o agente Log Analytics para o Linux executando `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Teste a conectividade entre o servidor Linux e o hospedeiro ESXi utilizando o comando `nc` no hospedeiro ESXi. Por exemplo:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. No portal Azure, efetue uma consulta de registo para `VMware_CL`. Quando o Monitor Azure recolhe os dados do syslog, mantém o formato syslog. No portal, alguns campos específicos são capturados, tais como *Hostname* e *ProcessName*.  

    ![tipo](./media/vmware/type.png)  

    Se seus resultados de pesquisa de registo do modo de exibição são semelhantes à imagem acima, está pronto para utilizar o dashboard da solução de monitorização de VMware.  

## <a name="vmware-data-collection-details"></a>Detalhes de recolha de dados do VMware
A solução de monitorização de VMware recolhe diversos dados de registo e métricas de desempenho dos anfitriões ESXi com os agentes do Log Analytics para Linux que tiver ativado.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos.

| Plataforma | Agente do log Analytics para Linux | Agente do SCOM | Storage do Azure | SCOM necessário? | Dados de agente do SCOM enviados através do grupo de gestão | Frequência de recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |a cada 3 minutos |

A tabela seguinte mostra exemplos de campos de dados recolhidos pela solução de monitorização de VMware:

| nome do campo | descrição |
| --- | --- |
| Device_s |Dispositivos de armazenamento do VMware |
| ESXIFailure_s |tipos de falha |
| EventTime_t |Quando Ocorreu o evento de tempo |
| HostName_s |Nome de anfitrião ESXi |
| Operation_s |Criar VM ou eliminar VM |
| ProcessName_s |Nome do evento |
| ResourceId_s |nome do anfitrião do VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Estado de SCSI de VMware |
| SyslogMessage_s |Dados do syslog |
| UserName_s |utilizador que criou ou eliminou a VM |
| VMName_s |o nome da VM |
| Computador |computador anfitrião |
| TimeGenerated |que os dados que foi gerados |
| DataCenter_s |Centro de dados do VMware |
| StorageLatency_s |Latência de armazenamento (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Descrição geral da solução monitorização de VMware
É apresentado o mosaico do VMware na sua área de trabalho do Log Analytics. Ele fornece uma visão geral das falhas. Quando clica no mosaico, que entra numa vista de dashboard.

![mosaicos](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navegue até a vista do dashboard
Na vista do painel de instrumentos **VMware,** as lâminas são organizadas por:

* Contagem de estado de falha
* Principais anfitriões por contagens de eventos
* Contagens dos melhores eventos
* Atividades de máquina virtual
* Eventos de disco do anfitrião ESXi

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Clique em qualquer painel para abrir o painel de pesquisa do Log Analytics que mostra informações detalhadas específico para o painel.

A partir daqui, pode editar a consulta de registo para modificá-la para algo específico. Para mais detalhes sobre a criação de consultas de registo, consulte localizar dados utilizando consultas de [registo no Monitor Azure](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Encontrar eventos de anfitrião ESXi
Um único anfitrião do ESXi gera vários registos, com base em seus processos. A solução de monitorização de VMware centraliza-los e resume as contagens de eventos. Esta vista centralizada ajuda a compreender qual anfitrião ESXi tem um grande volume de eventos e quais eventos com mais frequência ocorrem no seu ambiente.

![evento](./media/vmware/events.png)

Pode explorar ainda mais ao clicar num anfitrião ESXi ou um tipo de evento.

Quando clica num nome de anfitrião do ESXi, exibir informações a partir desse anfitrião ESXi. Se pretender reduzir os resultados com o tipo de evento, adicione `“ProcessName_s=EVENT TYPE”` na sua consulta de pesquisa. Pode selecionar o **Nome do Processo** no filtro de pesquisa. Que restringe as informações para.

![desagregar](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Localizar atividades elevadas de VM
Uma máquina virtual podem ser criada e eliminada a qualquer anfitrião ESXi. É útil para um administrador identificar o número de VMs cria um anfitrião ESXi. No-por sua vez, ajuda a compreender o desempenho e de planeamento de capacidade. Manter o controle de eventos de atividade da VM é fundamental ao gerenciamento do ambiente.

![desagregar](./media/vmware/vmactivities1.png)

Se quiser ver mais dados de criação do VM de anfitrião do ESXi, clique num nome de anfitrião ESXi.

![desagregar](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Consultas comuns de registo
A solução inclui outras consultas útil que podem ajudar a gerir os seus anfitriões ESXi, como o espaço de armazenamento de elevada, a latência de armazenamento e a falha de caminho.

![consultas de](./media/vmware/queries.png)


#### <a name="save-queries"></a>Guardar consultas
Guardar consultas de registo é uma funcionalidade padrão no Monitor Azure e pode ajudá-lo a manter quaisquer consultas que tenha achado úteis. Depois de criar uma consulta que acha útil, guarde-a clicando nos **Favoritos**. Uma consulta guardada permite-lhe reutilizá-la facilmente mais tarde a partir da página [My Dashboard](../learn/tutorial-logs-dashboards.md) onde pode criar os seus próprios dashboards personalizados.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Criar alertas a partir de consultas
Depois de criar as suas consultas, pode querer utilizar as consultas para o alertar quando ocorrem eventos específicos. Consulte [alertas em Log Analytics](../platform/alerts-overview.md) para obter informações sobre como criar alertas. Por exemplo, em consultas de alerta e outros exemplos de consultas, consulte o [Monitor VMware utilizando](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) a publicação de blog Log Analytics.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>O que é necessário no ESXi alojar definição? O impacto que ela terá no meu ambiente atual?
A solução utiliza o Syslog de anfitrião ESXi nativo mecanismo de reencaminhamento. Não é necessário qualquer software adicionais da Microsoft no anfitrião ESXi para capturar os registos. Ele deve ter um impacto baixo para seu ambiente existente. No entanto, terá de definir o reencaminhamento do syslog, que é a funcionalidade ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>É necessário reiniciar o anfitrião ESXi do meu?
Não. Este processo não requer um reinício. Às vezes, o vSphere não atualiza corretamente o syslog. Nesse caso, inicie sessão para o anfitrião ESXi e recarregar o syslog. Novamente, não tem de reiniciar o anfitrião, para que este processo não interrompa o seu ambiente.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Pode aumentar ou diminuir o volume de dados de registo enviados para o Log Analytics?
Sim, pode. Pode utilizar as definições de nível de registo de anfitrião do ESXi no vSphere. A recolha de registos baseia-se no nível de *informação.* Por isso, se quiser auditar a criação ou eliminação de VM, tem de manter o nível de *informação* no Hostd. Para mais informações, consulte a Base de [Conhecimento vMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Por que motivo é Hostd não fornecer dados para o Log Analytics? Minha definição de registo está definida como informações.
Ocorreu um erro de anfitrião ESXi para o carimbo de hora do syslog. Para mais informações, consulte a Base de [Conhecimento vMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Depois de aplicar a solução alternativa, Hostd deverá funcionar normalmente.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Pode ter vários anfitriões ESXi reencaminhamento de dados do syslog para uma única VM com omsagent?
Sim. Pode ter vários anfitriões ESXi reencaminhamento para uma única VM com omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Por que motivo não vejo dados que fluem para o Log Analytics?
Pode haver vários motivos:

* O anfitrião ESXi corretamente não é enviar dados para a VM em execução omsagent. Para testar, execute os seguintes passos:

  1. Para confirmar, inicie sessão no hospedeiro ESXi utilizando o SSH e execute o seguinte comando: `nc -z ipaddressofVM 1514`

      Se não for bem-sucedida, definições de vSphere na configuração do Advanced serão provável que não está correto. Consulte a recolha de [syslog configure](#configure-syslog-collection) para obter informações sobre como configurar o hospedeiro ESXi para encaminhamento de syslog.
  1. Se a conectividade da porta syslog for bem sucedida, mas ainda não vê nenhum dado, em seguida, recarregue o slog no hospedeiro ESXi usando ssh para executar o seguinte comando: `esxcli system syslog reload`
* A VM com o agente do Log Analytics não está definida corretamente. Para testar isto, execute os seguintes passos:

  1. O log Analytics escuta na porta 1514. Para verificar se está aberto, execute o seguinte comando: `netstat -a | grep 1514`
  1. Devia ver o porto `1514/tcp` aberto. Se não o fizer, certifique-se de que o omsagent está corretamente instalado. Se não vir as informações da porta, a porta de syslog não está aberta na VM.

    a. Verifique se o agente Log Analytics está a funcionar utilizando `ps -ef | grep oms`. Se não estiver a funcionar, inicie o processo executando o comando `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Abra o ficheiro `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Verifique se a definição adequada do utilizador e do grupo é válida, semelhante a: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Se o ficheiro não existir ou se a definição do utilizador e do grupo estiver errada, tome medidas corretivas [preparando um servidor Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Passos seguintes
* Utilize consultas de [registo](../log-query/log-query-overview.md) no Log Analytics para visualizar dados detalhados do anfitrião vMware.
* [Crie os seus próprios dashboards](../learn/tutorial-logs-dashboards.md) mostrando dados do anfitrião VMware.
* [Crie alertas](../platform/alerts-overview.md) quando ocorrerem eventos específicos de anfitriões vMware.
