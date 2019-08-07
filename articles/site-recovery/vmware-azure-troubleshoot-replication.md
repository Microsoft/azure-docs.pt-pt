---
title: Solucionar problemas de replicação para recuperação de desastre de VMs VMware e servidores físicos no Azure usando Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações de solução de problemas comuns de replicação durante a recuperação de desastre de VMs VMware e servidores físicos no Azure usando Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 54686a96385532e17fe0ac6e59058b91b40c1342
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742570"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Solucionar problemas de replicação para VMs VMware e servidores físicos

Este artigo descreve alguns problemas comuns e erros específicos que você pode encontrar ao replicar VMs VMware locais e servidores físicos para o Azure usando [site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Passo 1: Monitorar a integridade do servidor de processo

Site Recovery usa o [servidor de processo](vmware-physical-azure-config-process-server-overview.md#process-server) para receber e otimizar os dados replicados e enviá-los para o Azure.

Recomendamos que você monitore a integridade dos servidores de processo no portal, para garantir que eles estejam conectados e funcionando corretamente, e que a replicação esteja progredindo para os computadores de origem associados ao servidor de processo.

- [Saiba mais sobre](vmware-physical-azure-monitor-process-server.md) o monitoramento de servidores de processo.
- [Consultar as melhores práticas](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Solucionar problemas](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) de integridade do servidor de processo.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Passo 2: Solucionar problemas de conectividade e replicação

As falhas de replicação inicial e contínua geralmente são causadas por problemas de conectividade entre o servidor de origem e o servidor de processo ou entre o servidor de processo e o Azure. 

Para resolver esses problemas, [solucione problemas de conectividade e replicação](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Passo 3: Solucionar problemas de computadores de origem que não estão disponíveis para replicação

Quando você tenta selecionar o computador de origem para habilitar a replicação usando Site Recovery, o computador pode não estar disponível por um dos seguintes motivos:

* **Duas máquinas virtuais com o mesmo UUID de instância**: Se duas máquinas virtuais no vCenter tiverem o mesmo UUID de instância, a primeira máquina virtual descoberta pelo servidor de configuração será mostrada na portal do Azure. Para resolver esse problema, certifique-se de que duas máquinas virtuais tenham o mesmo UUID de instância. Esse cenário geralmente é visto em instâncias em que uma VM de backup se torna ativa e registrada em nossos registros de descoberta. [Consulte Azure site Recovery VMware para o Azure: Como limpar entradas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) duplicadas ou obsoletas para resolver.
* **Credenciais de usuário do vCenter**incorretas: Certifique-se de ter adicionado as credenciais do vCenter corretas ao configurar o servidor de configuração usando o modelo OVF ou a configuração unificada. Para verificar as credenciais que você adicionou durante a instalação, consulte [Modificar credenciais para descoberta automática](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **privilégios**insuficientes do vCenter: Se as permissões fornecidas para acessar o vCenter não tiverem as permissões necessárias, a falha na descoberta de máquinas virtuais poderá ocorrer. Certifique-se de que as permissões descritas em [preparar uma conta para a descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) sejam adicionadas à conta de usuário do vCenter.
* **Azure site Recovery servidores de gerenciamento**: Se a máquina virtual for usada como servidor de gerenciamento em uma ou mais das funções a seguir: servidor de processo/scale-out de servidor de configuração/servidor de destino mestre, não será possível escolher a máquina virtual no Portal. Os servidores de gerenciamento não podem ser replicados.
* **Já protegido/com failover por meio de serviços de Azure site Recovery**: Se a máquina virtual já estiver protegida ou com failover por meio de Site Recovery, a máquina virtual não estará disponível para selecionar para proteção no Portal. Verifique se a máquina virtual que você está procurando no portal ainda não está protegida por nenhum outro usuário ou em uma assinatura diferente.
* **vCenter não conectado**: Verifique se o vCenter está no estado conectado. Para verificar, acesse cofre dos serviços de recuperação > Site Recovery infraestrutura > servidores de configuração > clique no respectivo servidor de configuração > uma folha é aberta à direita com detalhes dos servidores associados. Verifique se o vCenter está conectado. Se ele estiver em um estado "não conectado", resolva o problema e, em seguida, [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no Portal. Depois disso, a máquina virtual será listada no Portal.
* **ESXi**desligado: Se o host ESXi no qual a máquina virtual reside estiver no estado desligado, a máquina virtual não será listada ou não poderá ser selecionada na portal do Azure. Ligue o host ESXi, [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no Portal. Depois disso, a máquina virtual será listada no Portal.
* **Reinicialização pendente**: Se houver uma reinicialização pendente na máquina virtual, você não poderá selecionar a máquina em portal do Azure. Certifique-se de concluir as atividades de reinicialização pendentes, [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no Portal.
* **IP não encontrado**: Se a máquina virtual não tiver um endereço IP válido associado a ela, você não poderá selecionar a máquina em portal do Azure. Certifique-se de atribuir um endereço IP válido à máquina virtual, [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no Portal.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Solucionar problemas de máquinas virtuais protegidas esmaecidas no portal

As máquinas virtuais replicadas em Site Recovery não estarão disponíveis no portal do Azure se houver entradas duplicadas no sistema. Para saber como excluir entradas obsoletas e resolver o problema, consulte [Azure site Recovery VMware-to-Azure: Como limpar entradas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)duplicadas ou obsoletas.

## <a name="common-errors-and-solutions"></a>Erros e soluções comuns

### <a name="initial-replication-issues-error-78169"></a>Problemas de replicação inicial [erro 78169]

Acima de verificar se não há problemas relacionados à conectividade, largura de banda ou sincronização de tempo, verifique se:

- Nenhum software antivírus está bloqueando Azure Site Recovery. Saiba [mais](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sobre as exclusões de pasta necessárias para Azure site Recovery.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Pontos de recuperação consistentes com o aplicativo ausentes [erro 78144]

 Isso ocorre devido a problemas com o VSS (serviço de cópias de sombra de volume). Para resolver este problema: 
 
- Verifique se a versão instalada do agente de Azure Site Recovery é pelo menos 9.22.2. 
- Verifique se o provedor VSS está instalado como um serviço nos serviços do Windows e verifique também o MMC do serviço de componente para verificar se Azure Site Recovery provedor VSS está listado.
- Se o provedor VSS não estiver instalado, consulte o [artigo solução de problemas de falha na instalação](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Se o VSS estiver desabilitado,
    - Verifique se o tipo de inicialização do serviço do provedor do VSS está definido como **automático**.
    - Reinicie os seguintes serviços:
        - Serviço VSS
        - Azure Site Recovery o provedor de VSS
        - Serviço VDS

- Se você estiver executando cargas de trabalho do SQL ou do Exchange, verifique se há falhas nos logs desses gravadores de aplicativos. Erros frequentes e sua resolução são capturados nos seguintes artigos:
    -  [A opção de fechamento automático do banco de dados SQL Server está definida como TRUE](https://support.microsoft.com/help/4504104)
    - [SQL Server 2008 R2 gerando um erro sem nova tentativa](https://support.microsoft.com/help/4504103)
    - [Problema conhecido no SQL Server 2016 e 2017](https://support.microsoft.com/help/4493364)
    - [Problema comum com os Exchange Servers 2013 e 2016](https://support.microsoft.com/help/4037535)


### <a name="source-machines-with-high-churn-error-78188"></a>Computadores de origem com variação alta [erro 78188]

Possíveis causas:
- A taxa de alteração de dados (bytes de gravação/s) nos discos listados da máquina virtual é maior que o [Azure site Recovery limites com suporte](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) para o tipo de conta de armazenamento de destino de replicação.
- Há um pico repentino na taxa de rotatividade devido a qual grande quantidade de dados está pendente para carregamento.

Para resolver o problema:
- Certifique-se de que o tipo de conta de armazenamento de destino (Standard ou Premium) seja provisionado de acordo com o requisito de taxa de rotatividade na origem.
- Se você já estiver replicando para um disco gerenciado Premium (tipo asrseeddisk), verifique se o tamanho do disco dá suporte à taxa de rotatividade observada de acordo com os limites de Site Recovery. Você pode aumentar o tamanho do asrseeddisk, se necessário. Siga as etapas abaixo:
    - Navegue até a folha discos da máquina replicada afetada e copie o nome do disco de réplica
    - Navegar até este disco gerenciado de réplica
    - Você pode ver uma faixa na folha de visão geral dizendo que uma URL SAS foi gerada. Clique nessa faixa e cancele a exportação. Ignore esta etapa se você não vir a faixa.
    - Assim que a URL SAS for revogada, vá para a folha de configuração do disco gerenciado e aumente o tamanho para que a ASR dê suporte à taxa de rotatividade observada no disco de origem
- Se a rotatividade observada for temporária, aguarde algumas horas para que o carregamento de dados pendente seja atualizado e crie pontos de recuperação.
- Se o disco contiver dados não críticos, como logs temporários, dados de teste, etc., considere mover esses dados em outro lugar ou excluir completamente este disco da replicação
- Se o problema continuar a persistir, use o Site Recovery planejador de [implantação](site-recovery-deployment-planner.md#overview) para ajudar a planejar a replicação.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Computadores de origem sem pulsação [erro 78174]

Isso acontece quando Azure Site Recovery agente de mobilidade no computador de origem não está se comunicando com o servidor de configuração (CS).

Para resolver o problema, use as seguintes etapas para verificar a conectividade de rede da VM de origem para o servidor de configuração:

1. Verifique se o computador de origem está em execução.
2. Entre no computador de origem usando uma conta que tenha privilégios de administrador.
3. Verifique se os seguintes serviços estão em execução e se não reinicie os serviços:
   - Svagents (agente do InMage Scout VX)
   - Serviço de aplicativo InMage Scout
4. No computador de origem, examine os logs no local para obter os detalhes do erro:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Servidor de processo sem pulsação [erro 806]
Caso não haja nenhuma pulsação do servidor de processo (PS), verifique se:
1. A VM PS está em execução
2. Verifique os seguintes logs no PS para obter detalhes do erro:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Servidor de destino mestre sem pulsação [erro 78022]

Isso acontece quando Azure Site Recovery agente de mobilidade no destino mestre não está se comunicando com o servidor de configuração.

Para resolver o problema, use as seguintes etapas para verificar o status do serviço:

1. Verifique se a VM de destino mestre está em execução.
2. Entre na VM de destino mestre usando uma conta que tenha privilégios de administrador.
    - Verifique se o serviço svagents está em execução. Se estiver em execução, reinicie o serviço
    - Verifique os logs no local para obter detalhes do erro:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID do erro 78144-nenhum ponto de recuperação consistente com o aplicativo disponível para a VM nos últimos ' XXX ' minutos

Alguns dos problemas mais comuns estão listados abaixo

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Causa 1: Problema conhecido no SQL Server 2008/2008 R2 
**Como corrigir** : Há um problema conhecido com o SQL Server 2008/2008 R2. Consulte este artigo da base [de conhecimento Azure site Recovery agente ou outro backup de VSS que não seja de componente falha para um servidor que hospeda SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Causa 2: Azure Site Recovery trabalhos falham em servidores que hospedam qualquer versão do SQL Server instâncias com bancos de AUTO_CLOSE 
**Como corrigir** : Consulte o [artigo](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) da base de conhecimento 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Causa 3: Problema conhecido no SQL Server 2016 e 2017
**Como corrigir** : Consulte o [artigo](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) da base de conhecimento 


### <a name="more-causes-due-to-vss-related-issues"></a>Mais causas devido a problemas relacionados ao VSS:

Para solucionar os problemas, verifique os arquivos no computador de origem para obter o código de erro exato para a falha:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Como localizar os erros no arquivo?
Procure a cadeia de caracteres "vacpError" abrindo o arquivo vacp. log em um editor
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

No exemplo acima, **2147754994** é o código de erro que informa sobre a falha, conforme mostrado abaixo

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>O gravador VSS não está instalado-erro 2147221164 

*Como corrigir*: Para gerar a marca de consistência do aplicativo, Azure Site Recovery usa o VSS (serviço de cópias de sombra de volume) da Microsoft. Ele instala um provedor VSS para sua operação para obter instantâneos de consistência do aplicativo. Este provedor VSS é instalado como um serviço. Caso o serviço do provedor do VSS não esteja instalado, a criação do instantâneo de consistência do aplicativo falha com a ID do erro 0x80040154 "classe não registrada". </br>
Consulte o [artigo para solução de problemas de instalação do gravador VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>O gravador VSS está desabilitado-erro 2147943458

**Como corrigir**: Para gerar a marca de consistência do aplicativo, Azure Site Recovery usa o VSS (serviço de cópias de sombra de volume) da Microsoft. Ele instala um provedor VSS para sua operação para obter instantâneos de consistência do aplicativo. Este provedor VSS é instalado como um serviço. Caso o serviço do provedor do VSS esteja desabilitado, a criação do instantâneo de consistência do aplicativo falha com a ID do erro "o serviço especificado está desabilitado e não pode ser iniciado (0x80070422)". </br>

- Se o VSS estiver desabilitado,
    - Verifique se o tipo de inicialização do serviço do provedor do VSS está definido como **automático**.
    - Reinicie os seguintes serviços:
        - Serviço VSS
        - Azure Site Recovery o provedor de VSS
        - Serviço VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>PROVEDOR VSS NOT_REGISTERED-erro 2147754756

**Como corrigir**: Para gerar a marca de consistência do aplicativo, Azure Site Recovery usa o VSS (serviço de cópias de sombra de volume) da Microsoft. Verifique se o serviço do provedor VSS Azure Site Recovery está instalado ou não. </br>

- Repita a instalação do provedor usando os seguintes comandos:
- Desinstalar o provedor existente: C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Install C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent\InMageVSSProvider_Install.cmd
 
Verifique se o tipo de inicialização do serviço do provedor do VSS está definido como **automático**.
    - Reinicie os seguintes serviços:
        - Serviço VSS
        - Azure Site Recovery o provedor de VSS
        - Serviço VDS

## <a name="next-steps"></a>Passos Seguintes

Se precisar de mais ajuda, poste sua pergunta no [Fórum de Azure site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Temos uma comunidade ativa, e um de nossos engenheiros pode ajudá-lo.
