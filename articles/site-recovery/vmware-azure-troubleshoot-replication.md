---
title: Resolver problemas de replicação para a recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece informações de resolução de problemas de replicação comuns durante a recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 1aaf13f01c7e7197001f3099fabd4b8be8545f0d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094706"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Resolver problemas de replicação de VMs de VMware e servidores físicos

Poderá ver uma mensagem de erro específico quando protege as máquinas virtuais VMware ou servidores físicos com o Azure Site Recovery. Este artigo descreve alguns problemas comuns que poderá encontrar ao replicar VMs de VMware no local e servidores físicos para o Azure, utilizando [recuperação de Site](site-recovery-overview.md).

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>Monitorizar estado de funcionamento do servidor de processos para evitar problemas de replicação

Recomenda-se para monitorar a integridade do servidor de processo (PS) no portal para se certificar de que a replicação está em curso para as suas máquinas de origem associada. No cofre, vá para gerir > infraestrutura do Site Recovery > servidores de configuração. No painel do servidor de configuração, clique no servidor de processos em servidores associados. Painel do servidor de processo abre-se com suas estatísticas de estado de funcionamento. Pode controlar a utilização da CPU, utilização da memória, status dos serviços de PS necessário para a replicação, a data de expiração do certificado e espaço livre. O estado de todas as estatísticas deve ser verde. 

**Recomenda-se para ter memória e utilização da CPU em 70% e liberar espaço acima 25%**. Espaço livre refere-se para o espaço em disco de cache no servidor de processos, que é utilizada para armazenar os dados de replicação das máquinas de origem antes de carregar para o Azure. Se reduz para menos de 20%, a replicação será limitada para todas as máquinas de origem associada. Siga os [diretrizes de capacidade](./site-recovery-plan-capacity-vmware.md#capacity-considerations) para compreender a configuração necessária para replicar as máquinas de origem.

Certifique-se de que os seguintes serviços estão em execução na máquina de PS. Iniciar ou reiniciar qualquer serviço que não está em execução.

**Servidor de processos incorporado**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* Serviço InMage PushInstall
* Serviço de carregamento de registo (LogUpload)
* Serviço InMage Scout Application
* Agente de serviços de recuperação do Microsoft Azure (obengine)
* InMage Scout VX Agent - Sentinel/Outpost (svagents)
* tmansvc
* World Wide Web (W3SVC) do serviço de publicação
* MySQL
* Serviço do Microsoft Azure Site Recovery (dra)

**Servidor de processos de escalamento horizontal**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* Serviço InMage PushInstall
* Serviço de carregamento de registo (LogUpload)
* Serviço InMage Scout Application
* Agente de serviços de recuperação do Microsoft Azure (obengine)
* InMage Scout VX Agent - Sentinel/Outpost (svagents)
* tmansvc

**Servidor de processos no Azure para reativação pós-falha**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* Serviço InMage PushInstall
* Serviço de carregamento de registo (LogUpload)

Certifique-se de que o StartType de todos os serviços está definido como **automático ou automático (início atrasado)**. Serviço de agente de serviços de recuperação do Microsoft Azure (obengine) não precisa ter seu StartType definido conforme apresentado acima.

## <a name="replication-issues"></a>Problemas de replicação

Falhas de replicação iniciais e contínuas, muitas vezes, são causadas por problemas de conectividade entre o servidor de origem e o servidor de processos ou entre o servidor de processos e o Azure. Na maioria dos casos, pode solucionar esses problemas, concluindo os passos nas secções seguintes.

>[!Note]
>Certifique-se de que:
>1. O sistema data hora para o item protegido está em sincronização.
>2. Nenhum software antivírus está a bloquear o Azure Site Recovery. Saiba mais [mais](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) no exclusões de pastas necessárias para o Azure Site Recovery.

### <a name="check-the-source-machine-for-connectivity-issues"></a>Verifique a máquina de origem para problemas de conectividade

As seguintes lista mostra formas como pode verificar a máquina de origem.

*  Na linha de comandos no servidor de origem, use o Telnet para executar ping no servidor de processo através da porta HTTPS, executando o seguinte comando. HTTPS porta 9443 é o padrão usado pelo servidor de processos para enviar e receber tráfego de replicação. Pode modificar esta porta no momento do registo. O seguinte comando verifica para problemas de conectividade de rede e para problemas que bloquear a porta de firewall.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Utilize o Telnet para testar a conectividade. Não utilize `ping`. Se o Telnet não estiver instalado, conclua os passos listados em [instalar o cliente Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   Se o telnet é capaz de se ligar com êxito para a porta de PS, poderia ser visto um ecrã em branco.

   Se não conseguir ligar ao servidor de processos, permita a entrada de porta 9443 no servidor de processos. Por exemplo, poderá ter de permitir a entrada de porta 9443 no servidor de processos, se a rede tiver uma rede de perímetro ou sub-rede filtrada. Em seguida, verifique se o problema ainda ocorre.

*  Se o telnet é efetuada com êxito e ainda o computador de origem informa que o PS não está acessível, abra o browser no computador de origem e verifique se o endereço https://<PS_IP>:<PS_Data_Port>/ está acessível.

    Erro de certificado HTTPS é esperado no atingir este endereço. Ignorar erro de certificado e continuando devem acabar em 400-pedido incorreto, que significa que o servidor não pode servir o pedido do navegador e que a ligação HTTPS standard para o servidor está a funcionar bem e bom.

    Se não tiver êxito, detalhes sobre a mensagem de erro no navegador fornecerá orientações. Para por exemplo, se a autenticação de proxy está incorreta, o servidor de proxy devolve 407 – Proxy de autenticação necessários, juntamente com as ações necessárias na mensagem de erro. 

*  Verifique os seguintes registos na VM de origem para erros relacionados com a rede de falhas de carregamento:

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server-for-connectivity-issues"></a>Verificar o servidor de processos para problemas de conectividade

As seguintes lista mostra formas como pode verificar o servidor de processos:

> [!NOTE]
> Servidor de processos tem de ter um endereço IPv4 estático e não deve ter NAT IP configurado no mesmo.

* **Verifique a conectividade entre máquinas de origem e servidor de processos**
* No caso de é capazes de fazer um Telnet de máquina de origem e ainda o PS não está acessível a partir da origem, verifique a ligação de ponto a ponto com cxprocessserver da VM de origem ao executar a ferramenta de cxpsclient na VM de origem:

      <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

   Verifique os registos de gerado no PS nos diretórios seguintes para obter detalhes sobre erros correspondentes:

      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
      and
      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
* Verifique os seguintes registos no PS caso não haja nenhum heartbeat do PS. Isso é identificado pela **código de erro 806** no portal.

      C:\ProgramData\ASR\home\svsystems\eventmanager*.log
      and
      C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

* **Verifique se o servidor de processos está ativamente enviar dados por push para o Azure**.

  1. No servidor de processos, abra o Gestor de tarefas (prima Ctrl + Shift + Esc).
  2. Selecione o **desempenho** separador e, em seguida, selecione a **abra o Monitor de recursos** ligação. 
  3. Sobre o **Monitor de recursos** página, selecione a **rede** separador. Sob **processos com a atividade de rede**, verifique se **cbengine.exe** ativamente está enviando um grande volume de dados.

       ![Captura de ecrã que mostra os volumes em processos com a atividade de rede](./media/vmware-azure-troubleshoot-replication/cbengine.png)

  Se cbengine.exe não está a enviar um grande volume de dados, conclua os passos nas secções seguintes.

* **Verifique se o servidor de processos pode ligar ao armazenamento de Blobs do Azure**.

  Selecione **cbengine.exe**. Sob **conexões TCP**, verifique se existe conectividade entre o servidor de processos e o URL de armazenamento do blogue do Azure.

  ![Captura de ecrã que mostra a conectividade entre cbengine.exe e o URL de armazenamento de Blobs do Azure](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

  Se nenhuma conectividade do servidor de processos para o URL de armazenamento do blogue do Azure, no painel de controlo, selecione **serviços**. Verifique se os seguintes serviços estão em execução:

  *  cxprocessserver
  *  InMage Scout VX Agent – Sentinel/Outpost
  *  Agente dos Serviços de Recuperação do Microsoft Azure
  *  Serviço Microsoft Azure Site Recovery
  *  tmansvc

  Iniciar ou reiniciar qualquer serviço que não está em execução. Verifique se o problema ainda ocorre.

* **Verifique se o servidor de processos pode ligar-se para o endereço IP público do Azure através da porta 443**.

  Em %programfiles%\Microsoft Agent\Temp de serviços de recuperação do Azure, abra o ficheiro de CBEngineCurr.errlog mais recente. No ficheiro, procure **443** ou para a cadeia de caracteres **tentativa de ligação falhada**.

  ![Captura de ecrã que mostra o erro registos na pasta Temp](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

  Se são apresentados os problemas, na linha de comandos no servidor de processos, utilize o Telnet para enviar um ping de seu endereço IP público do Azure (o endereço IP está oculto na imagem anterior). Pode encontrar o endereço IP público do Azure no ficheiro CBEngineCurr.currLog através da porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

  Se não conseguir ligar, verifique se o problema de acesso é devido a definições de firewall ou proxy, tal como descrito no passo seguinte.

* **Verifique se o firewall baseadas no endereço IP no servidor de processos bloqueia o acesso**.

  Se utilizar regras de firewall baseadas no endereço IP no servidor, transferir a lista completa de [intervalos IP de datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Adicione os intervalos de endereços IP à sua configuração de firewall para se certificar de que a firewall permite a comunicação para o Azure (e a porta HTTPS predefinida, 443). Permita intervalos de endereços IP para a região do Azure da sua subscrição e para a região do Azure-oeste (utilizada para gestão de identidades e de controlo de acesso).

* **Verificar se um firewall baseado em URL no servidor de processos bloqueia o acesso**.

  Se utilizar uma regra de firewall baseado em URL no servidor, adicione os URLs indicados na tabela a seguir para a configuração de firewall:

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Verifique se as definições de proxy no servidor de processos bloquear o acesso**.

   Se utilizar um servidor proxy, certifique-se de que o nome do servidor proxy é resolvido pelo servidor DNS. Para verificar o valor que forneceu quando configurou o servidor de configuração, vá para a chave de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.

   Em seguida, certifique-se de que as mesmas definições são utilizadas pelo agente de recuperação de sites do Azure para enviar dados: 
      
   1. Procure **do Microsoft Azure Backup**. 
   2. Open **Microsoft Azure Backup**e, em seguida, selecione **ação** > **alterar propriedades**. 
   3. Sobre o **configuração do Proxy** separador, deverá ver o endereço de proxy. O endereço de proxy deve ser a mesmo que o endereço de proxy que é mostrado nas definições de registo. Caso contrário, alterá-lo para o mesmo endereço.

*  **Verifique se a largura de banda de limitação é restrita no servidor de processos**.

   Aumentar a largura de banda e, em seguida, verifique se o problema ainda ocorre.


## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>Máquina de origem não estiver listada no portal do Azure

Quando tenta selecionar a máquina de origem para ativar a replicação utilizando a recuperação de Site, a máquina pode não estar disponível para um dos seguintes motivos:

* **Duas máquinas virtuais com o mesmo UUID da instância**: Se duas máquinas virtuais sob o vCenter tiver a mesma instância UUID, a primeira máquina virtual detetada pelo servidor de configuração é apresentada no portal do Azure. Para resolver este problema, certifique-se de que não existem duas máquinas virtuais têm a mesma instância UUID. Este cenário é comum visto em casos em que uma VM de cópia de segurança é ativada e está registada na nossa registos de deteção. Consulte [do Azure Site Recovery VMware para o Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) para resolver.
* **As credenciais de utilizador do vCenter incorreto**: Certifique-se de que adicionou as credenciais do vCenter correto quando configurou o servidor de configuração com o modelo OVF ou a configuração unificada. Para verificar as credenciais que foi adicionado durante a configuração, consulte [modificar as credenciais para a deteção automática](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **privilégios insuficientes do vCenter**: Se as permissões fornecidas para aceder ao vCenter não tem as permissões necessárias, pode ocorrer Falha ao detetar máquinas virtuais. Certifique-se de que as permissões descritas [preparar uma conta para a deteção automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) são adicionados para a conta de utilizador do vCenter.
* **Servidores de gestão do Azure Site Recovery**: Se a máquina virtual for utilizada como servidor de gestão numa ou mais das seguintes funções - configuração /scale-out de servidor servidor de processos / principal do servidor de destino, em seguida, não poderá escolher a máquina virtual a partir do portal. Servidores de gestão não podem ser replicadas.
* **Já protegidos/ativação pós-falha através dos serviços do Azure Site Recovery**: Se a máquina virtual já está protegida ou a ativação pós-falha através do Site Recovery, a máquina virtual não está disponível para selecionar para a proteção no portal. Certifique-se de que a máquina virtual que está procurando no portal do já não está protegida por qualquer outro utilizador ou numa subscrição diferente.
* **vCenter não ligado**: Verifique se o vCenter está no estado ligado. Para verificar, aceda ao Cofre dos Recovery Services > infraestrutura do Site Recovery > servidores de configuração > clique no servidor de configuração respetivos > é aberto um painel de diretamente com os detalhes de servidores associados. Verifique se o vCenter está ligado. Se no estado "Não ligado", resolver o problema e, em seguida [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, máquina virtual será listada no portal.
* **ESXi está desligada**: Se o anfitrião ESXi em que reside a máquina virtual está num Estado, desligado, em seguida, máquina virtual não será apresentada ou não podem ser selecionada no portal do Azure. Ligar o anfitrião ESXi, [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, máquina virtual será listada no portal.
* **Reinício pendente**: Se existir um reinício pendente na máquina virtual, em seguida, não será possível selecionar a máquina no portal do Azure. Certifique-se para concluir as atividades de reinício pendente [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, máquina virtual será listada no portal.
* **IP não foi encontrado**: Se a máquina virtual não tiver um endereço IP válido associado ao mesmo, em seguida, não será possível selecionar a máquina no portal do Azure. Certifique-se de atribuir um endereço IP válido para a máquina virtual, [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, máquina virtual será listada no portal.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>Máquinas virtuais protegidas são cinzento out no portal

Máquinas virtuais que são replicadas na recuperação de sites não estão disponíveis no portal do Azure se existirem entradas duplicadas no sistema. Para saber como eliminar entradas obsoletas e resolver o problema, consulte [Azure Site Recovery VMware para o Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-recommended-steps-for-resolution"></a>Os passos de erros comuns e recomendados para a resolução

### <a name="initial-replication-issues-error-78169"></a>[Erro 78169] de problemas de replicação inicial

Ao longo de um garantir acima que existem sem conectividade, largura de banda ou tempo sincronizar problemas relacionados, certifique-se de que:

- Nenhum software antivírus está a bloquear o Azure Site Recovery. Saiba mais [mais](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) no exclusões de pastas necessárias para o Azure Site Recovery.

### <a name="application-consistency-recovery-point-missing-error-78144"></a>Ponto de recuperação de consistência de aplicação em falta [Erro 78144]

 Isto acontece devido a problemas com a cópia de sombra de volumes (VSS) do serviço. Para resolver este problema: 
 
- Certifique-se de que a versão instalada do agente do Azure Site Recovery é, pelo menos, 9.22.2. 
- Certifique-se de que o fornecedor do VSS está instalado como um serviço nos serviços do Windows e também verificar se o serviço de componentes do MMC para verificar se o fornecedor de VSS do Azure Site Recovery está listado.
- Se o fornecedor do VSS não está instalado, consulte a [artigo de resolução de problemas: Falha na instalação](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Se estiver desativado o VSS,
    - Certifique-se de que o tipo de arranque do serviço fornecedor do VSS está definido como **automática**.
    - Reinicie os seguintes serviços:
        - Serviço VSS
        - Fornecedor VSS do Azure Site Recovery
        - Serviço VDS

### <a name="high-churn-on-source-machine-error-78188"></a>Elevado volume de alterações na máquina de origem [Erro 78188]

Causas possíveis:
- A taxa de alteração de dados (bytes escritos/seg) nos discos listados da máquina virtual é mais do que o [limites de suporte do Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) para o tipo de conta de armazenamento de destino de replicação.
- Não existe um pico da taxa de abandono devido a qual elevada quantidade de dados está pendente para carregamento.

Para resolver o problema:
- Certifique-se de que o tipo de conta de armazenamento destino (Standard ou Premium) é aprovisionado de acordo com o requisito de taxa de alterações a dados na origem.
- Se o volume de alterações observado é temporário, aguarde algumas horas até que o carregamento de dados pendentes para acompanhar e criar pontos de recuperação.
- Se o problema persistir, utilize o ASR [Planeador de implementações](site-recovery-deployment-planner.md#overview) para ajudar a planear a replicação.

### <a name="no-heartbeat-from-source-machine-error-78174"></a>Não existem nenhu Heartbeat da máquina de origem [Erro 78174]

Isto acontece quando o agente de mobilidade do Azure Site Recovery na máquina de origem não está a comunicar com o servidor de configuração (CS).

Para resolver o problema, utilize os seguintes passos para verificar a conectividade de rede de VM de origem para o servidor de configuração:

1. Certifique-se de que a máquina de origem está em execução.
2. Inicie sessão no computador de origem com uma conta que tenha privilégios de administrador.
3. Certifique-se de que os seguintes serviços estão em execução e se não reiniciar os serviços:
   - Svagents (InMage Scout VX Agent)
   - Serviço InMage Scout Application
4. No computador de origem, examine os registos no local para obter detalhes de erro:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="no-heartbeat-from-process-server-error-806"></a>Nenhum heartbeat do servidor de processos [Erro 806]
Caso não haja nenhum heartbeat do processo de servidor (PS), verifique se a:
1. PS VM está em execução
2. Verifique o seguinte inicia sessão PS para detalhes do erro:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="no-heartbeat-from-master-target-error-78022"></a>Nenhum Heartbeat do destino principal [Erro 78022]

Isto acontece quando o agente de mobilidade de recuperação de Site do Azure no destino mestre não está a comunicar com o servidor de configuração.

Para resolver o problema, utilize os seguintes passos para verificar o estado do serviço:

1. Certifique-se de que a VM de destino mestre está em execução.
2. Inicie sessão para a VM de destino mestre usando uma conta que tenha privilégios de administrador.
    - Certifique-se de que o serviço de svagents está em execução. Se estiver em execução, reinicie o serviço
    - Verifique os registos no local para obter detalhes de erro:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-is-not-reachable-from-the-source-machine-error-78186"></a>Servidor de processos não está acessível a partir da máquina de origem [Erro 78186]

Este erro leva a falhas e de aplicação pontos consistentes, não a ser gerados se não está a ser resolvido. Para resolver o problema, siga o abaixo de resolução de problemas de ligações:
1. Certifique-se de que [PS serviços estão em execução](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Verifique os problemas de conectividade de máquina de origem](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Verifique os problemas de conectividade do servidor de processo](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) e siga as orientações fornecidas para:
    - A verificar a conectividade com a origem
    - Problemas de firewall e proxy

### <a name="data-upload-blocked-from-source-machine-to-process-server-error-78028"></a>Carregamento de dados impedido de máquina de origem para o servidor de processos [Erro 78028]

Este erro leva a falhas e de aplicação pontos consistentes, não a ser gerados se não está a ser resolvido. Para resolver o problema, siga o abaixo de resolução de problemas de ligações:

1. Certifique-se de que [PS serviços estão em execução](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Verifique os problemas de conectividade de máquina de origem](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Verifique os problemas de conectividade do servidor de processo](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) e siga as orientações fornecidas para:
    - A verificar a conectividade com a origem
    - Problemas de firewall e proxy

## <a name="next-steps"></a>Passos Seguintes

Se precisar de mais ajuda, publique sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Temos uma Comunidade ativa e um dos nossos engenheiros pode a ajudá-lo.
