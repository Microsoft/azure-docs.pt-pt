---
title: Problemas de replicação de problemas para recuperação de desastres de VMware VMs e servidores físicos para OAzure usando a Recuperação do Site Azure [ Microsoft Docs
description: Este artigo fornece informações de resolução de problemas para problemas comuns de replicação durante a recuperação de vMware vMs e servidores físicos para O Azure usando a Recuperação do Site Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 7237bb7e0538ba1a9b6333ccb6589efe657a247d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74423957"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Problemas de replicação de problemas para VMware VMs e servidores físicos

Este artigo descreve alguns problemas comuns e erros específicos que pode encontrar quando replica VMware VMs no local e servidores físicos para Azure usando a [Recuperação](site-recovery-overview.md)do Site .

## <a name="step-1-monitor-process-server-health"></a>Passo 1: Monitorizar a saúde do servidor de processos

A Recovery do Site utiliza o servidor de [processopara](vmware-physical-azure-config-process-server-overview.md#process-server) receber e otimizar dados replicados e enviá-lo para o Azure.

Recomendamos que monitorize a saúde dos servidores de processos no portal, para garantir que estão conectados e funcionando corretamente, e que a replicação está a progredir para as máquinas de origem associadas ao servidor de processos.

- [Saiba mais sobre](vmware-physical-azure-monitor-process-server.md) a monitorização dos servidores de processos.
- [Consultar as melhores práticas](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Problemas de saúde](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) do servidor do processo.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Passo 2: Problemas de conectividade e replicação

Falhas de replicação iniciais e em curso são frequentemente causadas por problemas de conectividade entre o servidor de origem e o servidor de processo sintetizado si mesmos e entre o servidor de processo e o Azure. 

Para resolver estes problemas, [problemas de conectividade e replicação.](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Passo 3: Máquinas de origem de resolução de problemas que não estão disponíveis para replicação

Quando tentar selecionar a máquina de origem para ativar a replicação utilizando a Recuperação do Local, a máquina pode não estar disponível por uma das seguintes razões:

* **Duas máquinas virtuais com a mesma instância UUID**: Se duas máquinas virtuais sob o vCenter tiverem a mesma instância UUID, a primeira máquina virtual descoberta pelo servidor de configuração é mostrada no portal Azure. Para resolver este problema, certifique-se de que nenhuma máquina virtual tem a mesma instância UUID. Este cenário é comumente visto em casos em que um VM de reserva torna-se ativo e está registado nos nossos registos de descobertas. Consulte o [Azure Site Recovery VMware-to-Azure: Como limpar entradas duplicadas ou velhas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) para resolver.
* **Credenciais de utilizador vCenter incorretas**: Certifique-se de que adicionou as credenciais vCenter corretas quando configurar o servidor de configuração utilizando o modelo OVF ou configuração unificada. Para verificar as credenciais que adicionou durante a configuração, consulte [Modificar as credenciais para a descoberta automática](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **vCentro privilégios insuficientes**: Se as permissões fornecidas para aceder ao vCenter não tiverem as permissões necessárias, pode ocorrer a não descoberta de máquinas virtuais. Certifique-se de que as permissões descritas no [Prepare uma conta para a descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) são adicionadas à conta de utilizador vCenter.
* **Servidores**de gestão de recuperação de sites Azure : Se a máquina virtual for utilizada como servidor de gestão sob uma ou mais das seguintes funções - Servidor de configuração /servidor de processo scale-out / servidor de alvo master, então não poderá escolher a máquina virtual a partir do portal. Os servidores de gestão não podem ser replicados.
* **Já protegida/falhada através**dos serviços de recuperação do site Azure : Se a máquina virtual já estiver protegida ou falhada através da Recuperação do Local, a máquina virtual não está disponível para selecionar para proteção no portal. Certifique-se de que a máquina virtual que procura no portal não está já protegida por qualquer outro utilizador ou sob uma subscrição diferente.
* **vCenter não ligado**: Verifique se o vCenter está em estado de ligação. Para verificar, vá ao cofre dos Serviços de Recuperação > Infraestrutura de Recuperação do Site > Servidores de Configuração > Clique no respetivo servidor de configuração > uma lâmina abre à sua direita com detalhes dos servidores associados. Verifique se o vCenter está ligado. Se estiver num estado "Não Conectado", resolva o problema e, em seguida, [refresque o servidor](vmware-azure-manage-configuration-server.md#refresh-configuration-server) de configuração no portal. Depois disso, a máquina virtual será listada no portal.
* **ESXi desligado**: Se o hospedeiro ESXi em que a máquina virtual reside estiver desligado, então a máquina virtual não será listada ou não será selecionável no portal Azure. Energia no hospedeiro ESXi, [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, a máquina virtual será listada no portal.
* **Reiniciar pendente**: Se houver um reboot pendente na máquina virtual, então não poderá selecionar a máquina no portal Azure. Certifique-se de completar as atividades de reinicialização pendentes, [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.
* **IP não encontrado**: Se a máquina virtual não tiver um endereço IP válido associado ao mesmo, então não poderá selecionar a máquina no portal Azure. Certifique-se de atribuir um endereço IP válido à máquina virtual, [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Máquinas virtuais protegidas de resolução de problemas cinzentas no portal

As máquinas virtuais que são replicadas no âmbito da Recuperação do Site não estão disponíveis no portal Azure se houver entradas duplicadas no sistema. Para saber como eliminar entradas velhas e resolver o problema, consulte o [Azure Site Recovery VMware-to-Azure: Como limpar entradas duplicadas ou velhas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Sem ponto de recuperação consistente de acidente disponível para o VM nos últimos minutos 'XXX'

Algumas das questões mais comuns estão listadas abaixo

### <a name="initial-replication-issues-error-78169"></a>Problemas de replicação iniciais [erro 78169]

Ao longo de um acima, garantindo que não existem problemas relacionados com conectividade, largura de banda ou sincronização de tempo, certifique-se de que:

- Nenhum software antivírus está a bloquear a recuperação do site azure. Saiba [mais](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sobre as exclusões de pastas necessárias para a Recuperação do Site Azure.

### <a name="source-machines-with-high-churn-error-78188"></a>Máquinas de origem com alta agitação [erro 78188]

Possíveis causas:
- A taxa de alteração de dados (escrever bytes/seg) nos discos listados da máquina virtual é mais do que os [limites suportados](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) pela Recuperação do Site Azure para o tipo de conta de armazenamento alvo de replicação.
- Há um aumento repentino na taxa de churn devido ao qual está pendente uma elevada quantidade de dados para o upload.

Para resolver a questão:
- Certifique-se de que o tipo de conta de armazenamento alvo (Standard ou Premium) é provisionado de acordo com o requisito da taxa de churn na fonte.
- Se já estiver a replicar-se a um disco gerido premium (tipo asrseeddisk), certifique-se de que o tamanho do disco suporta a taxa de churn observada de acordo com os limites de Recuperação do Site. Pode aumentar o tamanho do disco asrseedd, se necessário. Siga os passos abaixo:
    - Navegue para a lâmina de Discos da máquina replicada impactada e copie o nome do disco de réplica
    - Navegue para esta réplica do disco gerido
    - Pode ver um banner na lâmina de visão geral dizendo que foi gerado um URL SAS. Clique neste banner e cancele a exportação. Ignore este passo se não vir o estandarte.
    - Assim que o URL SAS for revogado, vá para a lâmina de configuração do Disco Gerido e aumente o tamanho de modo a que a ASR suporte a taxa de churn observada no disco de origem
- Se o churn observado for temporário, aguarde algumas horas para que o upload de dados pendentes apanhe e crie pontos de recuperação.
- Se o disco contiver dados não críticos como registos temporários, dados de teste, etc., considere mover estes dados para outro local ou excluir completamente este disco da replicação
- Se o problema continuar a persistir, utilize o planejador de [implantação](site-recovery-deployment-planner.md#overview) de recuperação do site para ajudar a planear a replicação.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Máquinas de origem sem batimentocardíaco [erro 78174]

Isto acontece quando o agente de mobilidade de recuperação do site Azure na Máquina Fonte não está a comunicar com o Servidor de Configuração (CS).

Para resolver o problema, utilize os seguintes passos para verificar a conectividade da rede desde a fonte VM até ao Servidor Config:

1. Verifique se a Máquina Fonte está a funcionar.
2. Inscreva-se na Máquina Fonte utilizando uma conta que tenha privilégios de administrador.
3. Verifique se os seguintes serviços estão em funcionamento e se não reiniciar os serviços:
   - Svagents (Agente InMage Scout VX)
   - Serviço de Aplicação InMage Scout
4. Na Máquina Fonte, examine os registos no local para obter detalhes de erro:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Processe servidor sem batimentocardíaco [erro 806]
No caso de não existir batimentocardíaco do Servidor de Processos (PS), verifique se:
1. Ps VM está em funcionamento
2. Verifique os seguintes registos no PS para obter detalhes de erro:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Servidor alvo principal sem batimento cardíaco [erro 78022]

Isto acontece quando o agente de mobilidade de recuperação do site Azure no Alvo Principal não está a comunicar com o Servidor de Configuração.

Para resolver o problema, utilize os seguintes passos para verificar o estado do serviço:

1. Verifique se o VM do alvo principal está a funcionar.
2. Inscreva-se no Master Target VM usando uma conta que tenha privilégios de administrador.
    - Verifique se o serviço de svagents está em funcionamento. Se estiver a funcionar, reinicie o serviço
    - Verifique os registos no local para obter detalhes de erro:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. Para registar o alvo principal com o servidor de configuração, navegue para pasta **%PROGRAMDATA%\ASR\Agent,** e execute o seguinte no pedido de comando:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Error ID 78144 - Não há ponto de recuperação consistente com aplicações disponíveis para o VM nos últimos minutos 'XXX'

Foram feitas melhorias nas versões [9.23](vmware-physical-mobility-service-overview.md#from-923-version-onwards) & [9.27](site-recovery-whats-new.md#update-rollup-39) do agente de mobilidade para lidar com comportamentos de falha de instalação VSS. Certifique-se de que está nas versões mais recentes para obter uma melhor orientação sobre falhas vss de resolução de problemas.

Algumas das questões mais comuns estão listadas abaixo

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Causa 1: Problema conhecido no servidor SQL 2008/2008 R2 
**Como corrigir** : Existe um problema conhecido com o servidor SQL 2008/2008 R2. Por favor, consulte este Agente de Recuperação do Site Do artigo KB [Azure ou outra cópia de segurança VSS não componente falha para um servidor que hospeda o SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Causa 2: Os trabalhos de recuperação do site azure falham nos servidores que hospedam qualquer versão de instâncias do SQL Server com DBs AUTO_CLOSE 
**Como corrigir** : Consulte o [artigo](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) Kb 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Causa 3: Edição conhecida no SQL Server 2016 e 2017
**Como corrigir** : Consulte o [artigo](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) Kb 


### <a name="more-causes-due-to-vss-related-issues"></a>Mais causas devido a questões relacionadas com o VSS:

Para resolver ainda mais os problemas, verifique os ficheiros na máquina de origem para obter o código de erro exato para falha:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Como localizar os erros no ficheiro?
Procure a cadeia "vacpError" abrindo o ficheiro vacp.log num editor
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

No exemplo acima **2147754994** é o código de erro que lhe diz sobre a falha como mostrado abaixo

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>O escritor VSS não está instalado - Error 2147221164 

*Como corrigir*: Para gerar uma etiqueta de consistência da aplicação, a Recuperação do Site Azure utiliza o Microsoft Volume Shadow Copy Service (VSS). Instala um Fornecedor VSS para a sua operação para tirar fotografias de consistência da aplicação. Este Fornecedor VSS está instalado como um serviço. Caso o serviço VSS Provider não esteja instalado, a criação de instantâneos de consistência da aplicação falha com o erro 0x80040154 "Classe não registada". </br>
Consulte o artigo para a instalação de problemas de [instalação de escritores VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Escritor VSS está incapacitado - Error 2147943458

**Como corrigir**: Para gerar uma etiqueta de consistência da aplicação, a Recuperação do Site Azure utiliza o Microsoft Volume Shadow Copy Service (VSS). Instala um Fornecedor VSS para a sua operação para tirar fotografias de consistência da aplicação. Este Fornecedor VSS está instalado como um serviço. Caso o serviço VSS Provider seja desativado, a criação de instantâneos de consistência da aplicação falha com o id de erro "O serviço especificado está desativado e não pode ser iniciado (0x80070422)". </br>

- Se o VSS estiver desativado,
    - Verifique se o tipo de arranque do serviço VSS Provider está definido para **Automático**.
    - Reiniciar os seguintes serviços:
        - Serviço VSS
        - Fornecedor VSS de Recuperação do Site Azure
        - Serviço VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - Erro 2147754756

**Como corrigir**: Para gerar uma etiqueta de consistência da aplicação, a Recuperação do Site Azure utiliza o Microsoft Volume Shadow Copy Service (VSS). Verifique se o serviço de recuperação do site azure VSS Provider está ou não instalado. </br>

- Voltar a tentar a instalação do Fornecedor utilizando os seguintes comandos:
- Desinstalar o fornecedor existente: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Reinstalação: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Verifique se o tipo de arranque do serviço VSS Provider está definido para **Automático**.
    - Reiniciar os seguintes serviços:
        - Serviço VSS
        - Fornecedor VSS de Recuperação do Site Azure
        - Serviço VDS

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda, publique a sua pergunta no fórum de recuperação do [site Azure.](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) Temos uma comunidade ativa, e um dos nossos engenheiros pode ajudá-lo.
