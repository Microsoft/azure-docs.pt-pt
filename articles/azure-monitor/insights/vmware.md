---
title: Monitoramento do VMware solução no Azure Monitor | Microsoft Docs
description: Saiba mais sobre como a solução de Monitoramento do VMware pode ajudar a gerenciar logs e monitorar hosts ESXi.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 05/04/2018
ms.openlocfilehash: dc453ad42312bb096aed1356d376b0906870a7b0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900614"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Monitoramento do VMware (preterido) solução no Azure Monitor

![Símbolo do VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> A solução Monitoramento do VMware foi preterida.  Os clientes que já instalaram a solução podem continuar a usá-la, mas não é possível adicionar Monitoramento do VMware a nenhum espaço de trabalho novo.

A solução Monitoramento do VMware no Azure Monitor é uma solução que ajuda a criar uma abordagem centralizada de registro em log e monitoramento para grandes logs do VMware. Este artigo descreve como você pode solucionar problemas, capturar e gerenciar os hosts ESXi em um único local usando a solução. Com a solução, você pode ver dados detalhados para todos os seus hosts ESXi em um único local. Você pode ver as principais contagens de eventos, status e tendências de hosts de VM e ESXi fornecidos por meio dos logs de host ESXi. Você pode solucionar problemas exibindo e pesquisando logs de host ESXi centralizados. E você pode criar alertas com base em consultas de pesquisa de log.

A solução usa a funcionalidade de syslog nativo do host ESXi para enviar dados por push a uma VM de destino, que tem o agente de Log Analytics. No entanto, a solução não grava arquivos no syslog na VM de destino. O agente de Log Analytics abre a porta 1514 e ouve isso. Depois de receber os dados, o agente de Log Analytics envia os dados por push para Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

* Adicione a solução Monitoramento do VMware à sua assinatura usando o processo descrito em [instalar uma solução de monitoramento](../insights/solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Hosts VMware ESXi com suporte
Host vSphere ESXi 5,5, 6,0 e 6,5

#### <a name="prepare-a-linux-server"></a>Preparar um servidor Linux
Crie uma VM do sistema operacional Linux para receber todos os dados de syslog dos hosts ESXi. O [agente Linux log Analytics](../learn/quick-collect-linux-computer.md) é o ponto de coleta para todos os dados syslog de host ESXi. Você pode usar vários hosts ESXi para encaminhar logs para um único servidor Linux, como no exemplo a seguir.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![fluxo de syslog](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurar coleção de syslog
1. Configure o encaminhamento de syslog para VSphere. Para obter informações detalhadas para ajudar a configurar o encaminhamento do syslog, consulte [Configurando o syslog no ESXi 5,0 e superior (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Vá para **configuração do host ESXi** > **software** > **Configurações avançadas** > **syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. No campo *syslog. global. logHost* , adicione seu servidor Linux e o número da porta *1514*. Por exemplo, `tcp://hostname:1514` ou `tcp://123.456.789.101:1514`
1. Abra o Firewall do host ESXi para syslog. **Configuração do host ESXi** > **software** > **Security Profile** > **Firewall** e abra **Propriedades**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Verifique o console do vSphere para verificar se o syslog está configurado corretamente. Confirme no host ESXI que a porta **1514** está configurada.
1. Baixe e instale o agente de Log Analytics para Linux no servidor Linux. Para obter mais informações, consulte a [documentação do log Analytics Agent para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Após a instalação do agente de Log Analytics para Linux, vá para o diretório/etc/opt/Microsoft/omsagent/sysconf/omsagent.d e copie o arquivo vmware_esxi. conf para o diretório/etc/opt/Microsoft/omsagent/conf/omsagent.d e altere o proprietário/grupo e permissões do arquivo. Por exemplo:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Reinicie o agente de Log Analytics para Linux executando `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Teste a conectividade entre o servidor Linux e o host ESXi usando o comando `nc` no host ESXi. Por exemplo:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. No portal do Azure, execute uma consulta de log para `VMware_CL`. Quando Azure Monitor coleta os dados do syslog, ele retém o formato syslog. No portal, alguns campos específicos são capturados, como *hostname* e *ProcessName*.  

    ![tipo](./media/vmware/type.png)  

    Se os resultados da pesquisa de log de exibição forem semelhantes à imagem acima, você será configurado para usar o painel de solução Monitoramento do VMware.  

## <a name="vmware-data-collection-details"></a>Detalhes da coleta de dados do VMware
A solução Monitoramento do VMware coleta várias métricas de desempenho e dados de log de hosts ESXi usando os agentes de Log Analytics para Linux que você habilitou.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados.

| Plataforma | Agente de Log Analytics para Linux | Agente do SCOM | Armazenamento do Azure | O SCOM é necessário? | Dados do agente do SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |a cada 3 minutos |

A tabela a seguir mostra exemplos de campos de dados coletados pela solução Monitoramento do VMware:

| nome do campo | descrição |
| --- | --- |
| Device_s |Dispositivos de armazenamento do VMware |
| ESXIFailure_s |tipos de falha |
| EventTime_t |hora em que o evento ocorreu |
| HostName_s |Nome do host ESXi |
| Operation_s |criar VM ou excluir VM |
| ProcessName_s |Nome do evento |
| ResourceId_s |nome do host VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Status do VMware SCSI |
| SyslogMessage_s |Dados do syslog |
| UserName_s |usuário que criou ou excluiu a VM |
| VMName_s |o nome da VM |
| Computador |computador host |
| TimeGenerated |hora em que os dados foram gerados |
| DataCenter_s |Datacenter do VMware |
| StorageLatency_s |latência de armazenamento (MS) |

## <a name="vmware-monitoring-solution-overview"></a>Visão geral da solução de Monitoramento do VMware
O bloco VMware é exibido em seu espaço de trabalho Log Analytics. Ele fornece uma exibição de alto nível de quaisquer falhas. Ao clicar no bloco, você entra em um modo de exibição de painel.

![mosaicos](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navegar no modo de exibição de painel
Na exibição de painel do **VMware** , as folhas são organizadas por:

* Contagem de status de falha
* Principais hosts por contagens de eventos
* Principais contagens de eventos
* Atividades da máquina virtual
* Eventos de disco do host ESXi

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Clique em qualquer folha para abrir Log Analytics painel de pesquisa que mostra informações detalhadas específicas para a folha.

A partir daqui, você pode editar a consulta de log para modificá-la para algo específico. Para obter detalhes sobre como criar consultas de log, consulte [Localizar dados usando consultas de log em Azure monitor](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Localizar eventos de host ESXi
Um único host ESXi gera vários logs, com base em seus processos. A solução de Monitoramento do VMware os centraliza e resume as contagens de eventos. Essa exibição centralizada ajuda você a entender qual host ESXi tem um alto volume de eventos e quais eventos ocorrem com mais frequência em seu ambiente.

![circunstância](./media/vmware/events.png)

Você pode fazer uma busca detalhada clicando em um host ESXi ou um tipo de evento.

Ao clicar em um nome de host ESXi, você exibirá informações desse host ESXi. Se você quiser restringir os resultados com o tipo de evento, adicione `“ProcessName_s=EVENT TYPE”` em sua consulta de pesquisa. Você pode selecionar **ProcessName** no filtro de pesquisa. Isso restringe as informações para você.

![análise](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Localizar atividades de VM altas
Uma máquina virtual pode ser criada e excluída em qualquer host ESXi. É útil para um administrador identificar quantas VMs um host ESXi cria. Por sua vez, o ajuda a entender o planejamento de desempenho e capacidade. Manter o controle dos eventos de atividade da VM é crucial ao gerenciar seu ambiente.

![análise](./media/vmware/vmactivities1.png)

Se você quiser ver dados adicionais de criação de VM do host ESXi, clique em um nome de host ESXi.

![análise](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Consultas de log comuns
A solução inclui outras consultas úteis que podem ajudá-lo a gerenciar seus hosts ESXi, como espaço de armazenamento alto, latência de armazenamento e falha de caminho.

![consultas de](./media/vmware/queries.png)


#### <a name="save-queries"></a>Salvar consultas
Salvar consultas de log é um recurso padrão no Azure Monitor e pode ajudá-lo a manter todas as consultas que você encontrou úteis. Depois de criar uma consulta que você acha útil, salve-a clicando nos **favoritos**. Uma consulta salva permite reutilizá-la facilmente mais tarde na página [meu painel](../learn/tutorial-logs-dashboards.md) , na qual você pode criar seus próprios painéis personalizados.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Criar alertas de consultas
Depois de criar suas consultas, talvez você queira usar as consultas para alertá-lo quando eventos específicos ocorrerem. Consulte [alertas no log Analytics](../platform/alerts-overview.md) para obter informações sobre como criar alertas. Para obter exemplos de consultas de alerta e outros exemplos de consulta, consulte a postagem de blog [monitorar o VMware usando log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) .

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>O que preciso fazer na configuração do host ESXi? Que impacto terá no meu ambiente atual?
A solução usa o mecanismo de encaminhamento do syslog de host do ESXi nativo. Você não precisa de nenhum software adicional da Microsoft no host ESXi para capturar os logs. Ele deve ter um baixo impacto em seu ambiente existente. No entanto, você precisa definir o encaminhamento de syslog, que é a funcionalidade ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>É necessário reiniciar meu host ESXi?
Não. Esse processo não requer uma reinicialização. Às vezes, o vSphere não atualiza corretamente o syslog. Nesse caso, faça logon no host ESXi e recarregue o syslog. Novamente, você não precisa reiniciar o host, portanto, esse processo não é prejudicial para o seu ambiente.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Posso aumentar ou diminuir o volume de dados de log enviados para Log Analytics?
Sim, você pode. Você pode usar as configurações de nível de log do host ESXi em vSphere. A coleta de log baseia-se no nível de *informações* . Portanto, se você quiser auditar a criação ou exclusão de VM, será necessário manter o nível de *informações* em host. Para obter mais informações, consulte a [base de dados de conhecimento do VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Por que o hostd não está fornecendo dados para Log Analytics? Minha configuração de log está definida como info.
Houve um bug de host ESXi para o carimbo de data/hora do syslog. Para obter mais informações, consulte a [base de dados de conhecimento do VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Depois de aplicar a solução alternativa, o hostd deve funcionar normalmente.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Posso ter vários hosts ESXi encaminhando dados de syslog para uma única VM com omsagent?
Sim. Você pode ter vários hosts ESXi encaminhando para uma única VM com omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Por que não vejo dados que fluem para Log Analytics?
Pode haver vários motivos:

* O host ESXi não está enviando dados corretamente para a VM que executa o omsagent. Para testar, execute as seguintes etapas:

  1. Para confirmar, faça logon no host ESXi usando SSH e execute o seguinte comando: `nc -z ipaddressofVM 1514`

      Se isso não for bem-sucedido, as configurações de vSphere na configuração avançada provavelmente não estarão corretas. Consulte [Configurar coleção de syslog](#configure-syslog-collection) para obter informações sobre como configurar o host ESXi para encaminhamento de syslog.
  1. Se a conectividade da porta do syslog for bem-sucedida, mas você ainda não vir nenhum dado, recarregue o syslog no host ESXi usando o ssh para executar o seguinte comando: `esxcli system syslog reload`
* A VM com o agente de Log Analytics não está definida corretamente. Para testar isso, execute as seguintes etapas:

  1. Log Analytics escuta a porta 1514. Para verificar se ele está aberto, execute o seguinte comando: `netstat -a | grep 1514`
  1. Você deve ver a porta `1514/tcp` aberta. Se você não fizer isso, verifique se o omsagent está instalado corretamente. Se você não vir as informações de porta, a porta do syslog não será aberta na VM.

    a. Verifique se o agente de Log Analytics está em execução usando `ps -ef | grep oms`. Se não estiver em execução, inicie o processo executando o comando `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Abra o ficheiro `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Verifique se a configuração apropriada de usuário e grupo é válida, semelhante a: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Se o arquivo não existir ou se a configuração de usuário e grupo estiver errada, execute uma ação corretiva [preparando um servidor Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Passos seguintes
* Use [consultas de log](../log-query/log-query-overview.md) em log Analytics para exibir dados de host do VMware detalhados.
* [Crie seus próprios painéis](../learn/tutorial-logs-dashboards.md) mostrando dados de host do VMware.
* [Crie alertas](../platform/alerts-overview.md) quando ocorrerem eventos de host VMware específicos.
