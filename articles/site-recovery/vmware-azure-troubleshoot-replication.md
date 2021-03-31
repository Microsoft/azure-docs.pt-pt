---
title: Problemas de replicação para recuperação de desastres de VMware VMs e servidores físicos para Azure, utilizando a Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações de resolução de problemas para problemas comuns de replicação durante a recuperação de VMware vMs e servidores físicos para Azure, utilizando a Recuperação do Site Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 8b44a1d6119cc658b9460e0a52fa0629f759964a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336210"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Resolver problemas de replicação para VMs VMware e servidores físicos

Este artigo descreve algumas questões comuns e erros específicos que poderá encontrar quando replicar VMware no local e servidores físicos para a Azure usando [a Recuperação do Site](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Passo 1: Monitorizar a saúde do servidor do processo

A Recuperação do Site utiliza o [servidor de processo](vmware-physical-azure-config-process-server-overview.md#process-server) para receber e otimizar dados replicados e enviá-lo para a Azure.

Recomendamos que monitorize a saúde dos servidores de processos no portal, para garantir que estão conectados e funcionando corretamente, e que a replicação está a progredir para as máquinas de origem associadas ao servidor de processos.

- [Saiba mais sobre](vmware-physical-azure-monitor-process-server.md) os servidores de processos de monitorização.
- [Consultar as melhores práticas](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Saúde do](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) servidor do processo de resolução de problemas.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Passo 2: Problemas de conectividade e replicação de resolução de problemas

Falhas de replicação iniciais e em curso são muitas vezes causadas por problemas de conectividade entre o servidor de origem e o servidor de processo ou entre o servidor de processo e O Azure.

Para resolver estes problemas, [resolver problemas de conectividade e replicação](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Passo 3: Máquinas de origem de resolução de problemas que não estão disponíveis para replicação

Quando tentar selecionar a máquina de origem para permitir a replicação utilizando a Recuperação do Local, a máquina pode não estar disponível por uma das seguintes razões:

* **Duas máquinas virtuais com o mesmo exemplo UUID**: Se duas máquinas virtuais sob o vCenter tiverem o mesmo caso UUID, a primeira máquina virtual descoberta pelo servidor de configuração é mostrada no portal Azure. Para resolver este problema, certifique-se de que nenhuma duas máquinas virtuais têm o mesmo caso UUID. Este cenário é geralmente visto em casos em que um VM de backup torna-se ativo e é registado nos nossos registos de descoberta. Consulte a [Azure Site Recovery VMware-to-Azure: Como limpar entradas duplicadas ou velhas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) para resolver.
* **Credenciais de utilizador incorretas do vCenter**: Certifique-se de que adicionou as credenciais corretas do vCenter quando configurar o servidor de configuração utilizando o modelo OVF ou configuração unificada. Para verificar as credenciais que adicionou durante a configuração, consulte [alterar credenciais para a descoberta automática](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **vCenter privilégios insuficientes**: Se as permissões fornecidas para aceder ao vCenter não tiverem as permissões necessárias, poderá ocorrer uma falha na descoberta de máquinas virtuais. Certifique-se de que as permissões descritas no [Prepare uma conta para a descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) são adicionadas à conta de utilizador do vCenter.
* **Servidores de gestão de recuperação do site Azure**: Se a máquina virtual for utilizada como servidor de gestão sob uma ou mais das seguintes funções - Servidor de processo de configuração /servidor-alvo principal, então não será capaz de escolher a máquina virtual a partir do portal. Os servidores de gestão não podem ser replicados.
* **Já protegido/falhado através dos serviços de Recuperação do Sítio Azure**: Se a máquina virtual já estiver protegida ou falhada através da Recuperação do Site, a máquina virtual não está disponível para selecionar para proteção no portal. Certifique-se de que a máquina virtual que procura no portal ainda não está protegida por nenhum outro utilizador ou por uma subscrição diferente.
* **vCenter não ligado**: Verifique se o vCenter está em estado ligado. Para verificar, vá ao cofre dos Serviços de Recuperação > Infraestrutura de Recuperação de Locais > Servidores de Configuração > Clique no respetivo servidor de configuração > uma lâmina abre à sua direita com detalhes dos servidores associados. Verifique se o vCenter está ligado. Se estiver num estado "Não Conectado", resolva o problema e, em seguida, [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, a máquina virtual será listada no portal.
* **ESXi desligado**: Se o hospedeiro ESXi sob o qual a máquina virtual reside estiver desligado do estado, então a máquina virtual não será listada ou não será selecionada no portal Azure. Ligue no anfitrião ESXi, [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, a máquina virtual será listada no portal.
* **Reinicialização pendente**: Se houver um reinício pendente na máquina virtual, então não poderá selecionar a máquina no portal Azure. Certifique-se de que completa as atividades pendentes de reinicialização, [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.
* **IP não encontrado**: Se a máquina virtual não tiver um endereço IP válido associado a ele, então não poderá selecionar a máquina no portal Azure. Certifique-se de atribuir um endereço IP válido à máquina virtual, [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Máquinas virtuais protegidas de resolução de problemas acinzentados no portal

As máquinas virtuais que são replicadas no âmbito da Recuperação do Site não estão disponíveis no portal Azure se houver entradas duplicadas no sistema. Para saber como eliminar entradas velhas e resolver o problema, consulte a [Azure Site Recovery VMware-to-Azure: Como limpar entradas duplicadas ou velhas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Nenhum ponto de recuperação consistente de colisão disponível para o VM nos últimos minutos 'XXX'

Algumas das questões mais comuns estão listadas abaixo

### <a name="initial-replication-issues-error-78169"></a>Problemas iniciais de replicação [erro 78169]

Acima, garantindo que não existem questões relacionadas com a conectividade, largura de banda ou sincronização de tempo, certifique-se de que:

- Nenhum software antivírus está a bloquear a recuperação do local de Azure. Saiba [mais](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sobre as exclusões de pastas necessárias para a Recuperação do Local de Azure.

### <a name="source-machines-with-high-churn-error-78188"></a>Máquinas de origem com alta agitação [erro 78188]

Possíveis Causas:
- A taxa de alteração de dados (write bytes/sec) nos discos listados da máquina virtual é mais do que os limites suportados pela Recuperação do [Local Azure](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) para o tipo de conta de armazenamento alvo de replicação.
- Há um aumento repentino na taxa de churn devido à qual a elevada quantidade de dados está pendente para o upload.

Para resolver o problema:
- Certifique-se de que o tipo de conta de armazenamento alvo (Standard ou Premium) é a provisionado de acordo com o requisito da taxa de churn na fonte.
- Se já estiver a replicar um disco gerido Premium (tipo assédisco), certifique-se de que o tamanho do disco suporta a taxa de churn observada de acordo com os limites de Recuperação do Site. Pode aumentar o tamanho do assídisco, se necessário. Siga os passos abaixo:
    - Navegue para a lâmina discos da máquina replicada impactada e copie o nome do disco de réplica
    - Navegue para este disco gerido por réplica
    - Pode ver um banner na lâmina do visão geral dizendo que foi gerado um URL SAS. Clique neste banner e cancele a exportação. Ignore este passo se não vir o estandarte.
    - Assim que o URL SAS for revogado, vá à lâmina de configuração do disco gerido e aumente o tamanho para que a Recuperação do Local de Azure suporte a taxa de churn observada no disco de origem
- Se o churn observado for temporário, aguarde algumas horas para que o upload de dados pendentes se apanhe e crie pontos de recuperação.
- Se o disco contiver dados não críticos como registos temporários, dados de teste, etc., considere mover estes dados para outro lugar ou excluir completamente este disco da replicação
- Se o problema continuar a persistir, utilize o planejador de implementação de [recuperação](site-recovery-deployment-planner.md#overview) do local para ajudar a planear a replicação.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Máquinas de origem sem batimentocardíaco [erro 78174]

Isto acontece quando o agente de mobilidade do local de Azure na Máquina de Origem não está a comunicar com o Servidor de Configuração (CS).

Para resolver o problema, utilize as seguintes etapas para verificar a conectividade da rede desde a origem VM até ao Servidor Config:

1. Verifique se a Máquina de Origem está a funcionar.
2. Inscreva-se na Máquina de Origem utilizando uma conta que tenha privilégios de administrador.
3. Verifique se os seguintes serviços estão em funcionamento e se não reiniciar os serviços:
   - Svagents (Agente InMage Scout VX)
   - Serviço de Aplicação InMage Scout
4. Na Máquina de Origem, examine os registos no local para obter detalhes de erro:

    *C:\Ficheiros de programas (X86)\Microsoft Azure Site Recovery\agent\svagents \* .log*

### <a name="process-server-with-no-heartbeat-error-806"></a>Servidor de processo sem batimento cardíaco [erro 806]
Caso não haja batimentos cardíacos do Servidor de Processo (PS), verifique se:
1. PS VM está a funcionar
2. Consulte os seguintes registos no PS para obter detalhes de erro:

    *C:\ProgramData\ASR\home\svsystems\eventmanager \* .log*\
    e\
    *C:\ProgramData\ASR\home\home\svsystems\monitor_protection \* .log*

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Servidor de alvo principal sem batimento cardíaco [erro 78022]

Isto acontece quando o agente de mobilidade do local de Azure no Alvo Principal não está a comunicar com o Servidor de Configuração.

Para resolver o problema, utilize as seguintes etapas para verificar o estado do serviço:

1. Verifique se o VM do alvo principal está a funcionar.
2. Inscreva-se no Master Target VM usando uma conta que tenha privilégios de administrador.
    - Verifique se o serviço de svagents está a funcionar. Se estiver em funcionamento, reinicie o serviço
    - Verifique os registos no local para obter detalhes de erro:

        *C:\Ficheiros de programas (X86)\Microsoft Azure Site Recovery\agent\svagents \* .log*
3. Para registar o alvo principal com o servidor de configuração, navegue para a pasta **%PROGRAMDATA%\ASR\Agente**, e execute o seguinte na 2.00da de comando:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Error ID 78144 - Não há nenhum ponto de recuperação consistente disponível para o VM nos últimos minutos 'XXX'

Foram feitas melhorias nas versões do agente de mobilidade [9.23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher)  &  [9.27](site-recovery-whats-new.md#update-rollup-39) para lidar com os comportamentos de falha de instalação vss. Certifique-se de que está nas versões mais recentes para obter uma melhor orientação sobre falhas de VSS na resolução de problemas.

Algumas das questões mais comuns estão listadas abaixo

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Causa 1: Emissão conhecida no servidor SQL 2008/2008 R2
**Como corrigir** : Existe um problema conhecido com o servidor SQL 2008/2008 R2. Consulte este agente de [recuperação do site KB Azure ou outra falha de backup VSS não componente para um servidor que hospeda SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Causa 2: Os trabalhos de recuperação do site Azure falham nos servidores que hospedam qualquer versão de instâncias do SQL Server com DBs AUTO_CLOSE
**Como corrigir** : Consulte [o artigo](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) do Kb


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Causa 3: Emissão conhecida no SQL Server 2016 e 2017
**Como corrigir** : Consulte [o artigo](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) do Kb

#### <a name="cause-4-app-consistency-not-enabled-on-linux-servers"></a>Causa 4: App-Consistency não ativados nos servidores Linux
**Como corrigir** : A Azure Site Recovery for Linux Operation System suporta scripts personalizados de aplicações para consistência de aplicações. O script personalizado com opções pré e post será usado pelo Agente de Mobilidade de Recuperação do Site Azure para obter consistência de aplicações. [Aqui](./site-recovery-faq.md#replication) estão os passos para o permitir.

### <a name="more-causes-due-to-vss-related-issues"></a>Mais causas devido a problemas relacionados com VSS:

Para resolver mais problemas, verifique os ficheiros da máquina de origem para obter o código de erro exato para avaria:

*C:\Ficheiros do programa (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log*

Como localizar os erros no ficheiro?
Procure a cadeia "vacpError" abrindo o ficheiro vacp.log num editor

`Ex: `**`vacpError`**`:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|`

No exemplo acima **2147754994** está o código de erro que lhe diz sobre a falha como mostrado abaixo

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>O escritor VSS não está instalado - Erro 2147221164

*Como corrigir*: Para gerar a etiqueta de consistência da aplicação, a Azure Site Recovery utiliza o Microsoft Volume Shadow copy Service (VSS). Instala um VSS Provider para o seu funcionamento para tirar fotografias de consistência da aplicação. Este VSS Provider está instalado como um serviço. Caso o serviço VSS Provider não esteja instalado, a criação de instantâneo de consistência da aplicação falha com o erro de ID 0x80040154 "Classe não registada". </br>
Consulte [o artigo para a resolução de problemas de instalação de escritores vss](./vmware-azure-troubleshoot-push-install.md#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>O escritor vss está desativado - Erro 2147943458

**Como corrigir**: Para gerar a etiqueta de consistência da aplicação, a Azure Site Recovery utiliza o Microsoft Volume Shadow copy Service (VSS). Instala um VSS Provider para o seu funcionamento para tirar fotografias de consistência da aplicação. Este VSS Provider está instalado como um serviço. Caso o serviço VSS Provider seja desativado, a criação de instantâneo de consistência da aplicação falha com o ID de erro "O serviço especificado está desativado e não pode ser iniciado (0x80070422)". </br>

- Se o VSS estiver desativado,
    - Verifique se o tipo de arranque do serviço VSS Provider está definido como **Automático**.
    - Reiniciar os seguintes serviços:
        - Serviço VSS
        - Fornecedor vss de recuperação do site Azure
        - Serviço VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - Erro 2147754756

**Como corrigir**: Para gerar a etiqueta de consistência da aplicação, a Azure Site Recovery utiliza o Microsoft Volume Shadow copy Service (VSS).
Verifique se o serviço Azure Site Recovery VSS Provider está instalado ou não. </br>

- Recandidutar a instalação do Fornecedor utilizando os seguintes comandos:
- Desinstalar o fornecedor existente: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Reinstalar: C:\Ficheiros de programas (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Verifique se o tipo de arranque do serviço VSS Provider está definido como **Automático**.
    - Reiniciar os seguintes serviços:
        - Serviço VSS
        - Fornecedor vss de recuperação do site Azure
        - Serviço VDS

## <a name="error-id-95001---insufficient-permissions-found"></a>ID de erro 95001 - Permissões insuficientes encontradas

Este erro ocorre quando se tenta ativar a replicação e as pastas de aplicação não têm permissões suficientes.

**Como corrigir:** Para resolver este problema, certifique-se de que o utilizador IUSR tem a função de proprietário para todas as pastas abaixo mencionadas -

- *C\ProgramData\Microsoft Azure Site Recovery\private*
- O diretório de instalação. Por exemplo, se o diretório de instalação for unidade F, em seguida, fornecer as permissões corretas para -
    - *F:\Ficheiros de programa (x86)\Microsoft Azure Site Recovery\home\svsystems*
- A *pasta \pushinstallsvc* no diretório de instalação. Por exemplo, se o diretório de instalação for unidade F, forneça as permissões corretas para -
    - *F:\Ficheiros de programa (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc*
- A *pasta \etc* no diretório de instalação. Por exemplo, se o diretório de instalação for unidade F, forneça as permissões corretas para -
    - *F:\Ficheiros de programa (x86)\Microsoft Azure Site Recovery\home\svsystems\etc*
- *C:\Temperatura*
- *C:\terceiroparty\php5nts*
- Todos os itens sob o caminho abaixo -
    - *C:\terceiraparty\rrdtool-1.2.15-win32-perl58\rrdtool\Lançamento\**

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda, publique a sua pergunta na [página de perguntas do Microsoft Q&Uma página de perguntas para a recuperação do site Azure](/answers/topics/azure-site-recovery.html). Temos uma comunidade ativa, e um dos nossos engenheiros pode ajudá-lo.
