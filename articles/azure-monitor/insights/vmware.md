---
title: Solução de monitorização VMware em Azure Monitor | Microsoft Docs
description: Saiba como a solução de Monitorização VMware pode ajudar a gerir registos e monitorizar os anfitriões ESXi.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: 9dc31cd4f492a4e95ce8232a8df28f07206e23b1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587176"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Solução de Monitorização VMware (Preprecada) no Azure Monitor

![Símbolo VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> A solução VMware Monitoring foi depreciada.  Os clientes que já instalaram a solução podem continuar a usá-la, mas a VMware Monitoring não pode ser adicionada a quaisquer novos espaços de trabalho.

A solução de monitorização VMware no Azure Monitor é uma solução que o ajuda a criar uma abordagem centralizada de registo e monitorização para grandes registos VMware. Este artigo descreve como pode resolver problemas, capturar e gerir os anfitriões ESXi num único local utilizando a solução. Com a solução, pode ver dados detalhados para todos os seus anfitriões ESXi num único local. Pode ver as principais contagens de eventos, estado e tendências dos anfitriões VM e ESXi fornecidos através dos registos de anfitriões ESXi. Pode resolver problemas visualizando e pesquisando registos de anfitriões ESXi centralizados. E, pode criar alertas baseados em consultas de pesquisa de registo.

A solução utiliza a funcionalidade syslog nativa do anfitrião ESXi para empurrar dados para um VM alvo, que tem o agente Log Analytics. No entanto, a solução não escreve ficheiros em syslog dentro do VM alvo. O agente Log Analytics abre a porta 1514 e ouve isto. Assim que recebe os dados, o agente Log Analytics empurra os dados para o Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Instale e configuure a solução
Utilize as seguintes informações para instalar e configurar a solução.

* Adicione a solução de monitorização VMware à sua subscrição utilizando o processo descrito na [Instalação de uma solução de monitorização](./solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Anfitriões VMware ESXi apoiados
vSphere ESXi Host 5.5, 6.0 e 6.5

#### <a name="prepare-a-linux-server"></a>Preparar um servidor Linux
Crie um VM do sistema operativo Linux para receber todos os dados syslog dos anfitriões ESXi. O [agente Log Analytics Linux](../vm/quick-collect-linux-computer.md) é o ponto de recolha de todos os dados do syslog do anfitrião ESXi. Pode utilizar vários anfitriões ESXi para encaminhar registos para um único servidor Linux, como no exemplo seguinte.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![fluxo de syslog](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Coleção de syslog configurar
1. Configurar o reencaminhamento do syslog para a VSphere. Para obter informações detalhadas que ajudem a configurar o encaminhamento do syslog, consulte [o sysloging de configuração no ESXi 5.0 e superior (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Vá ao software de **configuração do anfitrião ESXi**  >    >  **Advanced Settings**  >  **Syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. No campo *Syslog.global.logHost,* adicione o seu servidor Linux e o número de porta *1514*. Por exemplo, `tcp://hostname:1514` ou `tcp://123.456.789.101:1514`
1. Abra a firewall do anfitrião ESXi para syslog. Configuração do **anfitrião ESXi**  >  **Software**  >  Perfil de **Segurança**  >  **Firewall** e **open Properties**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Verifique a consola vSphere para verificar se o syslog está corretamente configurado. Confirme no anfitrião ESXI que a porta **1514** está configurada.
1. Faça o download e instale o agente Log Analytics para Linux no servidor Linux. Para obter mais informações, consulte a [Documentação do Agente Log Analytics para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Depois de instalado o agente Log Analytics para o Linux, vá ao diretório /etc/opt/microsoft/omsagent/sysconf/omsagent.d e copie o ficheiro vmware_esxi.conf para o diretório /etc/opt/microsoft/omsagent/conf/omsagent.d e a alteração do proprietário/grupo e permissões do ficheiro. Por exemplo:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Reinicie o agente Log Analytics para o Linux executando `sudo /opt/microsoft/omsagent/bin/service_control restart` .
1. Teste a conectividade entre o servidor Linux e o anfitrião ESXi utilizando o `nc` comando no anfitrião ESXi. Por exemplo:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. No portal Azure, realize uma consulta de registo para `VMware_CL` . Quando o Azure Monitor recolhe os dados do syslog, mantém o formato syslog. No portal, alguns campos específicos são capturados, tais como *o Nome anfitrião* e *o Nome de Processo.*  

    ![A screenshot mostra uma consulta de registo para Tipo = VMware_CL com um resultado com um tempotado.](./media/vmware/type.png)  

    Se os resultados da pesquisa de registo de visualização forem semelhantes à imagem acima, está definido para utilizar o painel de solução de monitorização VMware.  

## <a name="vmware-data-collection-details"></a>Detalhes da recolha de dados da VMware
A solução VMware Monitoring recolhe várias métricas de desempenho e regista dados dos anfitriões ESXi utilizando os agentes de Log Analytics para o Linux que ativou.

A tabela seguinte mostra métodos de recolha de dados e outros detalhes sobre a forma como os dados são recolhidos.

| plataforma | Agente de Log Analytics para Linux | Agente gestor de operações do Centro de Sistema | Storage do Azure | Diretor de Operações necessário? | Dados de agente do Gestor de Operações enviados através do grupo de gestão | frequência de coleção |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |a cada 3 minutos |

A tabela que se segue mostra exemplos de campos de dados recolhidos pela solução de monitorização VMware:

| nome de campo | descrição |
| --- | --- |
| Device_s |Dispositivos de armazenamento VMware |
| ESXIFailure_s |tipos de falha |
| EventTime_t |tempo em que o evento ocorreu |
| HostName_s |Nome do anfitrião ESXi |
| Operation_s |criar VM ou eliminar VM |
| ProcessName_s |nome do evento |
| ResourceId_s |nome do anfitrião VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Estado do SCSI da VMware |
| SyslogMessage_s |Dados do Syslog |
| UserName_s |utilizador que criou ou apagou o VM |
| VMName_s |o nome da VM |
| Computador |computador anfitrião |
| TimeGenerated |tempo que os dados foram gerados |
| DataCenter_s |Datacenter VMware |
| StorageLatency_s |latência de armazenamento (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Visão geral da solução de monitorização VMware
O azulejo VMware aparece no seu espaço de trabalho Log Analytics. Proporciona uma visão de alto nível de quaisquer falhas. Quando clica no azulejo, entra-se numa vista de painel.

![A screenshot mostra o azulejo VMware, exibindo nove falhas.](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navegue pela vista do painel
Na vista do painel **VMware,** as lâminas são organizadas por:

* Contagem de Estado de Falha
* Anfitrião superior por contagens de eventos
* Contagem de eventos de topo
* Atividades de máquinas virtuais
* Eventos de disco anfitrião ESXi

![solução1](./media/vmware/solutionview1-1.png)

![solução2](./media/vmware/solutionview1-2.png)

Clique em qualquer lâmina para abrir o painel de pesquisa do Log Analytics que mostre informações detalhadas específicas para a lâmina.

A partir daqui, pode editar a consulta de registo para modificá-la para algo específico. Para obter detalhes sobre a criação de consultas de registo, consulte [encontrar dados utilizando consultas de registo no Azure Monitor](../logs/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Encontre eventos de anfitrião ESXi
Um único anfitrião ESXi gera vários registos, com base nos seus processos. A solução VMware Monitoring centraliza-os e resume a contagem do evento. Esta vista centralizada ajuda-o a entender qual o anfitrião ESXi que tem um grande volume de eventos e quais os eventos que ocorrem mais frequentemente no seu ambiente.

![evento](./media/vmware/events.png)

Pode perfurar mais clicando num anfitrião ESXi ou num tipo de evento.

Quando clicar num nome de anfitrião ESXi, vê as informações do anfitrião ESXi. Se quiser reduzir os resultados com o tipo de evento, adicione `“ProcessName_s=EVENT TYPE”` a sua consulta de pesquisa. Pode selecionar **o Nome de Processo** no filtro de pesquisa. Isso limita a informação para si.

![Screenshot do anfitrião ESXi per Event Count e Breakdown Per Event Type blades na vista do painel de monitorização VMware.](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Encontre atividades de VM elevadas
Uma máquina virtual pode ser criada e eliminada em qualquer anfitrião ESXi. É útil para um administrador identificar quantos VMs um hospedeiro ESXi cria. Isso, por sua vez, ajuda a compreender o desempenho e o planeamento de capacidades. Acompanhar os eventos de atividade em VM é crucial para gerir o seu ambiente.

![Screenshot da lâmina de atividades da máquina virtual no painel de monitorização VMware, mostrando um gráfico de criação e eliminação de VM pelo anfitrião ESXi.](./media/vmware/vmactivities1.png)

Se quiser ver dados adicionais de criação de VM do anfitrião ESXi, clique num nome de anfitrião ESXi.

![Screenshot de um painel do painel de monitorização VMware mostrando uma tabela com uma linha de dados para cada criação de máquina virtual por um anfitrião ESXi.](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Consultas de log comuns
A solução inclui outras consultas úteis que podem ajudá-lo a gerir os seus anfitriões ESXi, tais como espaço de armazenamento elevado, latência de armazenamento e falha de caminho.

![A screenshot mostra pesquisas recomendadas, que são consultas armazenadas úteis.](./media/vmware/queries.png)


#### <a name="save-queries"></a>Guardar consultas
Guardar consultas de registo é uma funcionalidade padrão no Azure Monitor e pode ajudá-lo a manter quaisquer consultas que tenha achado úteis. Depois de criar uma consulta que ache útil, guarde-a clicando nos **Favoritos.** Uma consulta guardada permite-lhe reutilizá-la facilmente mais tarde a partir da página [My Dashboard,](../visualize/tutorial-logs-dashboards.md) onde pode criar os seus próprios dashboards personalizados.

![O screenshot mostra parte de um dashboard personalizado com a marca De registo com ícones para Undo, Exportação, Alerta, Salvar, Favoritos e História.](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Criar alertas de consultas
Depois de criar as suas consultas, talvez queira usar as consultas para alertá-lo quando ocorrerem eventos específicos. Consulte [alertas no Log Analytics](../alerts/alerts-overview.md) para obter informações sobre como criar alertas. Para exemplos de consultas de alerta e outros exemplos de consultas, consulte o [Monitor VMware utilizando](/archive/blogs/msoms/monitor-vmware-using-oms-log-analytics) o post de blog do Log Analytics.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>O que preciso fazer na configuração do anfitrião ESXi? Que impacto terá no meu ambiente atual?
A solução utiliza o mecanismo de reencaminhamento Syslog host Syslog nativo. Não precisa de nenhum software adicional da Microsoft no ESXi Host para capturar os registos. Deve ter um baixo impacto no seu ambiente existente. No entanto, é necessário definir o encaminhamento do syslog, que é a funcionalidade ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Preciso de reiniciar o meu anfitrião ESXi?
N.º Este processo não requer um reinício. Às vezes, a vSphere não atualiza corretamente o syslog. Nesse caso, inicie sessão no anfitrião ESXi e recarregue o syslog. Mais uma vez, não tens de reiniciar o hospedeiro, por isso este processo não é perturbador para o teu ambiente.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Posso aumentar ou diminuir o volume de dados de registo enviados para o Log Analytics?
Sim, tu podes. Pode utilizar as definições de nível de registo do anfitrião ESXi em vSphere. A recolha de registos baseia-se no nível de *informação.* Por isso, se pretender auditar a criação ou eliminação de VM, tem de manter o nível de *informação* no Hostd. Para mais informações, consulte a Base de [Conhecimentos VMware.](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658)

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Porque é que o Hostd não fornece dados para registar análises? A minha definição de registo está definida para informação.
Havia um bug de hospedeiro ESXi para o slogan do syslog. Para mais informações, consulte a Base de [Conhecimentos VMware.](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202) Depois de aplicar a solução alternativa, o Hostd deve funcionar normalmente.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Posso ter vários anfitriões ESXi a encaminhar dados de syslog para um único VM com omsagent?
Sim. Pode ter vários anfitriões ESXi a encaminhar-se para um único VM com omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Por que não vejo dados a fluir para o Log Analytics?
Pode haver várias razões:

* O anfitrião ESXi não está a empurrar corretamente os dados para o VM que executa o omsagent. Para testar, execute os seguintes passos:

  1. Para confirmar, inicie sessão no anfitrião ESXi utilizando ssh e execute o seguinte comando: `nc -z ipaddressofVM 1514`

      Se isto não for bem sucedido, as definições vSphere na Configuração Avançada provavelmente não estão corretas. Consulte [a coleção de syslog Configure](#configure-syslog-collection) para obter informações sobre como configurar o anfitrião ESXi para o encaminhamento de syslog.
  1. Se a conectividade da porta de syslog for bem sucedida, mas ainda não ver nenhum dado, então recarregue o syslog no anfitrião ESXi utilizando o ssh para executar o seguinte comando: `esxcli system syslog reload`
* O VM com o agente Log Analytics não está corretamente definido. Para testar isto, execute os seguintes passos:

  1. Log Analytics ouve a porta 1514. Para verificar se está aberto, executar o seguinte comando: `netstat -a | grep 1514`
  1. Devia ver o porto `1514/tcp` aberto. Caso contrário, verifique se o omsagent está instalado corretamente. Se não vir a informação da porta, a porta de syslog não está aberta no VM.

    a. Verifique se o agente Log Analytics está a funcionar utilizando `ps -ef | grep oms` . Se não estiver a funcionar, inicie o processo executando o comando `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Abra o ficheiro `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Verifique se a definição adequada do utilizador e do grupo é válida, semelhante a: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Se o ficheiro não existir ou a definição do utilizador e do grupo estiver errada, tome medidas corretivas [preparando um servidor Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Passos seguintes
* Utilize [consultas de registo](../logs/log-query-overview.md) no Log Analytics para ver dados detalhados do anfitrião VMware.
* [Crie os seus próprios dashboards](../visualize/tutorial-logs-dashboards.md) mostrando dados do anfitrião VMware.
* [Crie alertas](../alerts/alerts-overview.md) quando ocorrerem eventos específicos de anfitrião VMware.

