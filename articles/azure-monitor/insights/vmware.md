---
title: Solução de monitorização de VMware no Azure Monitor | Documentos da Microsoft
description: Saiba mais sobre como a solução de monitorização de VMware pode ajudar a gerir os registos e monitorizar anfitriões ESXi.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: magoedte
ms.openlocfilehash: 7d02f5b592600cf7efd0980c1284e8c724cd1f5b
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195159"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Solução de monitorização de VMware (preterido) no Azure Monitor

![Símbolo de VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> A solução de monitorização de VMware foi preterida.  Os clientes que já tenham instalado a solução podem continuar a utilizá-lo, mas a monitorização de VMware não podem ser adicionado a novas áreas de trabalho.

A solução de monitorização de VMware no Azure Monitor é uma solução que ajuda a criar um registo centralizado e a abordagem de monitorização para os registos de VMware grandes. Este artigo descreve como pode resolver problemas, capturar e gerir anfitriões ESXi numa única localização através da solução. Com a solução, pode ver dados detalhados de todos os seus anfitriões ESXi numa única localização. Pode ver contagens dos melhores eventos, o estado e as tendências de hosts VM e ESXi fornecidos através de registos de anfitrião ESXi. Pode solucionar ao visualizar e pesquisar registos de anfitrião do ESXi centralizados. E, pode criar alertas com base em consultas de pesquisa de registo.

A solução utiliza a funcionalidade de syslog nativo do anfitrião ESXi para enviar dados para um destino de VM, o que tem o agente do Log Analytics. No entanto, a solução não gravar arquivos no syslog dentro da VM de destino. O agente Log Analytics abre a porta 1514 e escutada por isto. Depois de receber os dados, o agente Log Analytics envia os dados no Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

* Adicionar a solução de monitorização de VMware para sua assinatura usando o processo descrito em [instalar uma solução de monitorização](../insights/solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Anfitriões do VMware ESXi suportados
vSphere anfitrião ESXi 5.5, 6.0 e 6.5

#### <a name="prepare-a-linux-server"></a>Preparar um servidor do Linux
Crie um sistema de operativo Linux VM receba todos os dados do syslog dos anfitriões ESXi. O [agente Linux do Log Analytics](../learn/quick-collect-linux-computer.md) é o ponto de coleção para todos os dados de syslog do anfitrião ESXi. Pode usar vários anfitriões ESXi para reencaminhar os registos para um único servidor do Linux, como no exemplo seguinte.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![fluxo de syslog](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurar a recolha de syslog
1. Configure o reencaminhamento do syslog para VSphere. Para obter informações detalhadas ajudar a configurar o reencaminhamento do syslog, consulte [configurar syslog no ESXi 5.0 e superior (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Aceda a **configuração do anfitrião ESXi** > **Software** > **definições avançadas** > **Syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. Na *Syslog.global.logHost* campo, adicione o servidor Linux e o número de porta *1514*. Por exemplo, `tcp://hostname:1514` ou `tcp://123.456.789.101:1514`
1. Abra a firewall do anfitrião ESXi para syslog. **Configuração do anfitrião ESXi** > **Software** > **perfil de segurança** > **Firewall** e abra **Propriedades**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Verifique o consola para verificar que essa syslog está devidamente configurado do vSphere. Confirme no anfitrião ESXI essa porta **1514** está configurado.
1. Baixe e instale o agente do Log Analytics para Linux no servidor do Linux. Para obter mais informações, consulte a [documentação para o agente do Log Analytics para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Depois do agente Log Analytics para Linux está instalado, vá para o diretório de /etc/opt/microsoft/omsagent/sysconf/omsagent.d e copie o vmware_esxi.conf ficheiro para o diretório de /etc/opt/microsoft/omsagent/conf/omsagent.d e a alteração, o proprietário/grupo e permissões do ficheiro. Por exemplo:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Reiniciar o agente do Log Analytics para Linux executando `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Testar a conectividade entre o servidor Linux e o anfitrião ESXi, utilizando o `nc` comando no anfitrião ESXi. Por exemplo:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. No portal do Azure, executar uma consulta de registo para `VMware_CL`. Quando o Monitor do Azure recolhe os dados do syslog, mantém o formato do syslog. No portal, alguns campos específicos são capturados, tal como *Hostname* e *ProcessName*.  

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
Na **VMware** vista do dashboard, os painéis são organizados por:

* Contagem de estado de falha
* Principais anfitriões por contagens de eventos
* Contagens dos melhores eventos
* Atividades de máquina virtual
* Eventos de disco do anfitrião ESXi

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Clique em qualquer painel para abrir o painel de pesquisa do Log Analytics que mostra informações detalhadas específico para o painel.

A partir daqui, pode editar a consulta de registo para modificá-lo para algo específico. Para obter detalhes sobre a criação de consultas de registo, consulte [encontrar os dados com consultas de registo no Azure Monitor](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Encontrar eventos de anfitrião ESXi
Um único anfitrião do ESXi gera vários registos, com base em seus processos. A solução de monitorização de VMware centraliza-los e resume as contagens de eventos. Esta vista centralizada ajuda a compreender qual anfitrião ESXi tem um grande volume de eventos e quais eventos com mais frequência ocorrem no seu ambiente.

![event](./media/vmware/events.png)

Pode explorar ainda mais ao clicar num anfitrião ESXi ou um tipo de evento.

Quando clica num nome de anfitrião do ESXi, exibir informações a partir desse anfitrião ESXi. Se quiser restringir os resultados com o tipo de evento, adicione `“ProcessName_s=EVENT TYPE”` na sua consulta de pesquisa. Pode selecionar **ProcessName** no filtro de pesquisa. Que restringe as informações para.

![desagregar](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Localizar atividades elevadas de VM
Uma máquina virtual podem ser criada e eliminada a qualquer anfitrião ESXi. É útil para um administrador identificar o número de VMs cria um anfitrião ESXi. No-por sua vez, ajuda a compreender o desempenho e de planeamento de capacidade. Manter o controle de eventos de atividade da VM é fundamental ao gerenciamento do ambiente.

![desagregar](./media/vmware/vmactivities1.png)

Se quiser ver mais dados de criação do VM de anfitrião do ESXi, clique num nome de anfitrião ESXi.

![desagregar](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Consultas de registo comuns
A solução inclui outras consultas útil que podem ajudar a gerir os seus anfitriões ESXi, como o espaço de armazenamento de elevada, a latência de armazenamento e a falha de caminho.

![consultas de](./media/vmware/queries.png)


#### <a name="save-queries"></a>Guardar consultas
A guardar consultas de registo é uma funcionalidade padrão do Azure Monitor e pode ajudar a manter todas as consultas que encontrar útil. Depois de criar uma consulta que ser úteis, guarde-o clicando a **Favoritos**. Uma consulta guardada permite-lhe facilmente reutilizá-lo mais tarde a partir da [meu painel](../learn/tutorial-logs-dashboards.md) página onde pode criar seus próprios dashboards personalizados.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Criar alertas a partir de consultas
Depois de criar as suas consultas, pode querer utilizar as consultas para o alertar quando ocorrem eventos específicos. Ver [alertas no Log Analytics](../platform/alerts-overview.md) para obter informações sobre como criar alertas. Para obter exemplos de consultas e outros exemplos de consulta de alerta, consulte a [VMware de Monitor de utilizar o Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) postagem de blog.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>O que é necessário no ESXi alojar definição? O impacto que ela terá no meu ambiente atual?
A solução utiliza o Syslog de anfitrião ESXi nativo mecanismo de reencaminhamento. Não é necessário qualquer software adicionais da Microsoft no anfitrião ESXi para capturar os registos. Ele deve ter um impacto baixo para seu ambiente existente. No entanto, terá de definir o reencaminhamento do syslog, que é a funcionalidade ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>É necessário reiniciar o anfitrião ESXi do meu?
Não. Este processo não requer um reinício. Às vezes, o vSphere não atualiza corretamente o syslog. Nesse caso, inicie sessão para o anfitrião ESXi e recarregar o syslog. Novamente, não tem de reiniciar o anfitrião, para que este processo não interrompa o seu ambiente.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Pode aumentar ou diminuir o volume de dados de registo enviados para o Log Analytics?
Sim, pode. Pode utilizar as definições de nível de registo de anfitrião do ESXi no vSphere. Recolha de registos se baseia a *informações* nível. Então, se pretender que a auditoria da VM é criada ou eliminada, precisa manter o *informações* nível no Hostd. Para obter mais informações, consulte a [Base de dados de conhecimento do VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Por que motivo é Hostd não fornecer dados para o Log Analytics? Minha definição de registo está definida como informações.
Ocorreu um erro de anfitrião ESXi para o carimbo de hora do syslog. Para obter mais informações, consulte a [Base de dados de conhecimento do VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Depois de aplicar a solução alternativa, Hostd deverá funcionar normalmente.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Pode ter vários anfitriões ESXi reencaminhamento de dados do syslog para uma única VM com omsagent?
Sim. Pode ter vários anfitriões ESXi reencaminhamento para uma única VM com omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Por que motivo não vejo dados que fluem para o Log Analytics?
Pode haver vários motivos:

* O anfitrião ESXi corretamente não é enviar dados para a VM em execução omsagent. Para testar, execute os seguintes passos:

  1. Para confirmar, inicie sessão no anfitrião ESXi utilizar ssh e execute o seguinte comando: `nc -z ipaddressofVM 1514`

      Se não for bem-sucedida, definições de vSphere na configuração do Advanced serão provável que não está correto. Ver [configurar a recolha de syslog](#configure-syslog-collection) para obter informações sobre como configurar o anfitrião ESXi para o reencaminhamento do syslog.
  1. Se a conectividade de porta de syslog é efetuada com êxito, mas ainda não vir quaisquer dados, recarregar o syslog no anfitrião ESXi ao utilizar o ssh para executar o seguinte comando: ` esxcli system syslog reload`
* A VM com o agente do Log Analytics não está definida corretamente. Para testar isto, execute os seguintes passos:

  1. O log Analytics escuta na porta 1514. Para verificar que está aberta, execute o seguinte comando: `netstat -a | grep 1514`
  1. Deverá ver porta `1514/tcp` abrir. Se não o fizer, certifique-se de que o omsagent está corretamente instalado. Se não vir as informações da porta, a porta de syslog não está aberta na VM.

    a. Certifique-se de que o agente Log Analytics está em execução usando `ps -ef | grep oms`. Se não estiver em execução, inicie o processo ao executar o comando ` sudo /opt/microsoft/omsagent/bin/service_control start`

    b. Abra o ficheiro `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

    c. Certifique-se de que o usuário adequado e a definição de grupo é válido, semelhante a: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

    d. Se o ficheiro não existe ou o utilizador e a definição de grupo é errado, tomar medidas corretivas pela [preparar o servidor Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Passos Seguintes
* Uso [registar as consultas](../log-query/log-query-overview.md) no Log Analytics para ver o VMware detalhada alojar os dados.
* [Criar seus próprios dashboards](../learn/tutorial-logs-dashboards.md) a mostrar dados de anfitrião do VMware.
* [Criar alertas](../platform/alerts-overview.md) quando ocorrem eventos específicos de anfitriões de VMware.
