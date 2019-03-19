---
title: Resolver problemas de Hyper-V para recuperação após desastre do Azure com o Azure Site Recovery | Documentos da Microsoft
description: Descreve como resolver problemas de recuperação após desastre com o Hyper-V para replicação do Azure com o Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 14d763126cccadf8042da79b20d15e5c93a8ce62
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094182"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Resolver problemas de Hyper-V para replicação do Azure e de ativação pós-falha

Este artigo descreve problemas comuns que possam ser apresentados quando replicar VMs no local Hyper-V para o Azure, utilizando [do Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Ativar problemas de proteção

Se ocorrerem problemas ao ativar a proteção para VMs de Hyper-V, verifique as seguintes recomendações:

1. Verifique que os anfitriões Hyper-V e as VMs de corresponder a todos [requisitos e pré-requisitos](hyper-v-azure-support-matrix.md).
2. Se os servidores de Hyper-V estiverem localizados em clouds do System Center Virtual Machine Manager (VMM), certifique-se de que já preparou a [servidor do VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Verifique se o serviço de gestão de máquinas virtuais de Hyper-V está em execução em anfitriões Hyper-V.
4. Verificar a existência de problemas que são apresentados no Hyper-V-VMMS\Admin de início de sessão para a VM. Este registo está localizado em **Applications and Services Logs** > **Microsoft** > **Windows**.
5. Na VM do convidado, certifique-se de que WMI está ativado e acessível.
   - [Saiba mais sobre](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) teste básico de WMI.
   - [Resolver problemas de](https://aka.ms/WMiTshooting) WMI.
   - [Resolver problemas de](https://technet.microsoft.com/library/ff406382.aspx#H22) problemas com scripts do WMI e serviços.
6. Na VM do convidado, certifique-se de que a versão mais recente do Integration Services está em execução.
    - [Verificar](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) que tem a versão mais recente.
    - [Manter](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) serviços de integração atualizado.
    
## <a name="replication-issues"></a>Problemas de replicação

Resolva problemas com os replicação inicial e contínuo da seguinte forma:

1. Certifique-se de que está a executar o [versão mais recente](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) dos serviços de recuperação de sites.
2. Verifique se os replicação é colocada em pausa:
   - Verifique o estado de funcionamento da VM na consola do Gestor de Hyper-V.
   - Se for fundamental, com o botão direito a VM > **replicação** > **Ver estado de funcionamento de replicação**.
   - Se a replicação é colocada em pausa, clique em **retomar replicação**.
3. Verifique se os serviços necessários estão em execução. Se não forem, reiniciá-las.
    - Se estiver a replicar o Hyper-V sem o VMM, verifique se estes serviços são executados no anfitrião Hyper-V:
        - Serviço de gestão de máquinas virtuais
        - Serviço de agente de serviços de recuperação do Microsoft Azure
        - Serviço Microsoft Azure Site Recovery
        - Serviço de anfitrião do fornecedor de WMI
    - Se estiver a replicar com o VMM no ambiente, verifique se esses serviços estão em execução:
        - No anfitrião Hyper-V, verifique se o serviço de gestão de máquinas virtuais, o agente de serviços de recuperação do Microsoft Azure e o serviço de anfitrião do fornecedor de WMI estão em execução.
        - No servidor do VMM, certifique-se de que o serviço do System Center Virtual Machine Manager está em execução.
4. Verifique a conectividade entre o servidor de Hyper-V e o Azure. Para verificar a conectividade, abra o Gestor de tarefas no anfitrião de Hyper-V. Sobre o **desempenho** separador, clique em **abra o Monitor de recursos**. Sobre o **rede** separador > **processo com a atividade de rede**, verifique se o cbengine.exe está ativamente a enviar grandes (MB) de volumes de dados.
5. Verifique se os anfitriões de Hyper-V podem ligar-se para o URL do blob de armazenamento do Azure. Para verificar se os anfitriões podem ligar, selecione e verifique **cbengine.exe**. Modo de exibição **conexões TCP** para verificar a conectividade do anfitrião para o blob de armazenamento do Azure.
6. Verificar problemas de desempenho, conforme descrito abaixo.
    
### <a name="performance-issues"></a>Problemas de desempenho

Limitações de largura de banda de rede podem afetar a replicação. Resolva problemas da seguinte forma:

1. [Verificar](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) se houver largura de banda ou a limitação restrições no seu ambiente.
2. Executar o [criador de perfil do Planeador de implementações](hyper-v-deployment-planner-run.md).
3. Depois de executar o criador de perfil, siga os [largura de banda](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) e [armazenamento](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) recomendações.
4. Verifique [limitações de alterações a dados](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Se vir elevadas alterações numa VM a dados, faça o seguinte:
   - Verifique se a sua VM está marcada para ressincronização.
   - Siga [essas etapas](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) para investigar a origem do volume de alterações.
   - Alterações a dados pode ocorrer quando os ficheiros de registo HRL excederem 50% do espaço em disco disponível. Se este é o problema, Aprovisione mais espaço de armazenamento para todas as VMs em que o problema ocorre.
   - Verifique que a replicação não está em pausa. Se for, ele continua a escrever as alterações no ficheiro hrl, que pode contribuir para o seu tamanho maior.
 

## <a name="critical-replication-state-issues"></a>Problemas de estado de replicação crítica

1. Para verificar o estado de funcionamento da replicação, ligar para a consola do Gestor de Hyper-V no local, em seguida, selecione a VM e verificar o estado de funcionamento.

    ![Estado de funcionamento da replicação](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Clique em **Ver estado de funcionamento de replicação** para ver os detalhes:

    - Se a replicação é colocada em pausa, clique com botão direito a VM > **replicação** > **retomar replicação**.
    - Se uma VM num anfitrião Hyper-V configurado no Site Recovery é migrada para outro anfitrião Hyper-V no mesmo cluster ou para um computador autônomo, replicação para a VM não é afetada. Apenas verifique se o novo anfitrião de Hyper-V cumpre todos os pré-requisitos e se está configurado no Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problemas de instantâneos consistentes com a aplicação

Um instantâneo consistente com a aplicação é um instantâneo de ponto no tempo dos dados de aplicação no interior da VM. Serviço de cópia de sombra de volumes (VSS) garante que as aplicações na VM estão num estado consistente quando o instantâneo.  Esta secção fornece detalhes sobre alguns problemas comuns que podem ocorrer.

### <a name="vss-failing-inside-the-vm"></a>VSS falhar dentro da VM

1. Verifique se a versão mais recente do Integration services está instalado e em execução.  Verifique se está disponível uma atualização, executando o seguinte comando a partir de uma linha de comandos elevada do PowerShell no anfitrião Hyper-V: **get-vm | selecione o nome, estado, IntegrationServicesState**.
2. Verifique que os serviços VSS estão em execução e bom estado de funcionamento:
   - Para verificar os serviços, inicie sessão na VM do convidado. Em seguida, abra uma linha de comandos de administrador e execute os seguintes comandos para verificar se todos os escritores VSS estão em bom estado.
       - **Vssadmin gravadores de lista**
       - **Vssadmin sombras de lista**
       - **Provedores de lista vssadmin**
   - Verificar a saída. Se os escritores se encontram em estado de falha, faça o seguinte:
       - Verifique o registo de eventos do aplicativo na VM para erros de operação do VSS.
   - Experimente reiniciar estes serviços associados com o escritor com falhas:
     - Cópia de sombra de volume
       - Fornecedor VSS do Azure Site Recovery
   - Depois de fazer isso, aguarde duas horas para ver se os instantâneos consistentes com a aplicação são gerados com êxito.
   - Como último recurso, tente reiniciar a VM. Isso poderá resolver os serviços que estão num Estado não responde.
3. Certifique-se de que não tem discos dinâmicos na VM. Não é suportado para instantâneos consistentes com a aplicação. Pode verificar na gestão de discos (Diskmgmt. msc).

    ![Disco dinâmico](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Certifique-se de que não tem um disco iSCSI ligado à VM. Tal não é suportado.
5. Verifique que o serviço de cópia de segurança está ativado. Certifique-se de que está ativada no **definições de Hyper-V** > **Integration Services**.
6. Certifique-se de que não existam conflitos com as aplicações de obtenção de instantâneos do VSS. Pode ocorrer se várias aplicações estão a tentar tirar instantâneos do VSS à mesmo entra em conflito do tempo. Por exemplo, se uma aplicação de cópia de segurança é tirar instantâneos do VSS quando o Site Recovery está agendado pela sua política de replicação para tirar um instantâneo.   
7. Verifique se a VM está a ter uma elevada taxa de alteração:
    - Pode medir a taxa de alteração de dados diária para as VMs de convidado, utilizando os contadores de desempenho no anfitrião de Hyper-V. Para medir a taxa de alteração de dados, ative o seguinte contador. Agrega um exemplo deste valor em todos os discos VM para 5 a 15 minutos, para obter o volume de alterações VM.
        - Categoria: "Dispositivo de armazenamento Virtual do hyper-V"
        - Contador: "Escrever Bytes / seg"</br>
        - Essa taxa de alterações a dados irá aumentar ou permanecem num alto nível, consoante o estado de disponibilidade da VM ou de seus aplicativos estão.
        - O alterações de dados do disco de origem média é de 2 MB/s para o armazenamento standard para o Site Recovery. [Saiba mais](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Além disso, pode [Verifique se os destinos de escalabilidade de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).
8. Executar o [Planeador de implementações](hyper-v-deployment-planner-run.md).
9. Rever as recomendações relativas [rede](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) e [armazenamento](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS falhar dentro do anfitrião de Hyper-V

1. Verifique os registos de eventos para erros VSS e recomendações:
    - No servidor de anfitrião do Hyper-V, abra o registo de eventos do administrador do Hyper-V no **Visualizador de eventos** > **registos de serviços e aplicações** > **Microsoft**  >  **Windows** > **Hyper-V** > **administrador**.
    - Verifique se existem quaisquer eventos que indicam a falhas de instantâneo consistente da aplicação.
    - Um erro típico é: "Hyper-V não foi possível gerar o conjunto de instantâneos do VSS para a máquina virtual"XYZ": O escritor Ocorreu um erro de não transitórias. Reiniciar o serviço VSS poderá resolver problemas se o serviço não está a responder."

2. Para gerar instantâneos do VSS para a VM, verifique que os serviços de integração do Hyper-V estão instalados na VM e que o serviço de integração de cópia de segurança (VSS) está ativado.
    - Certifique-se de que o serviço de VSS de serviços de integração/daemons estão em execução no convidado e estão numa **OK** estado.
    - Pode verificar isto partir de uma sessão de PowerShell elevada no anfitrião Hyper-V com o comando **et-VMIntegrationService - VMName<VMName>-nome VSS** também pode obter estas informações ao iniciar sessão na VM do convidado. [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Certifique-se de que os serviços de integração de cópia de segurança/VSS na VM em execução e em bom estado de funcionamento. Caso contrário, reinicie esses serviços e o serviço de solicitante de cópia de sombra de volumes do Hyper-V no servidor de anfitrião do Hyper-V.

### <a name="common-errors"></a>Erros comuns

**Código de erro** | **mensagem** | **Detalhes**
--- | --- | ---
**0x800700EA** | "Não conseguiu gerar o conjunto de instantâneos do VSS para a máquina virtual de Hyper-V: Estão disponíveis mais dados. (0x800700EA). Geração de conjunto de instantâneos do VSS pode falhar se a operação de cópia de segurança está em curso.<br/><br/> Falha na operação de replicação para a máquina virtual: Estão disponíveis mais dados." | Verifique se a VM tem o disco dinâmico ativado. Tal não é suportado.
**0x80070032** | "Não conseguiu ligar à máquina virtual de solicitante de cópia de sombra de Volume de Hyper-V <. / VMname > porque a versão não corresponde a versão esperada pelo Hyper-V | Verifique se as atualizações mais recentes do Windows estão instaladas.<br/><br/> [Atualizar](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) para a versão mais recente do Integration Services.



## <a name="collect-replication-logs"></a>Os registos de replicação de recolha

Todos os eventos de replicação de Hyper-V são registadas no registo do Hyper-V-VMMS\Admin, localizado em **Applications and Services Logs** > **Microsoft** > **Windows**. Além disso, pode ativar um registo de análise para o serviço de gestão de Máquina Virtual de Hyper-V, da seguinte forma:

1. Verifique os registos analíticos e de depuração podem ser visualizados no Visualizador de eventos. Para disponibilizar os registos, no Visualizador de eventos, clique em **View** > **Mostrar depurar registos analíticos e.**. O registo de análise é apresentado em **Hyper-V-VMMS**.
2. Na **ações** painel, clique em **ativar registo**. 

    ![Ativar registo](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Depois de ser ativada, este aparece no **Monitor de desempenho**, como um **sessão de rastreio de eventos** sob **conjuntos de Recoletores de dados**. 
4. Para ver as informações coletadas, pare a sessão de rastreamento ao desativar o registo. Em seguida, guarde o registo e abra-o novamente no Visualizador de eventos ou utilizar outras ferramentas para convertê-la conforme necessário.


### <a name="event-log-locations"></a>Localizações de registo de eventos

**Registo de eventos** | **Detalhes** |
--- | ---
**Aplicações e serviço de registos/Microsoft/VirtualMachineManager/servidor/Admin** (servidor VMM) | Registos para resolver problemas do VMM.
**Aplicações e serviço de registos/MicrosoftAzureRecoveryServices/replicação** (anfitrião de Hyper-V) | Registos para resolver problemas de agente de serviços de recuperação do Microsoft Azure. 
**Aplicações e serviço de registos/Microsoft/Azure Site Recovery/fornecedor/Operational** (anfitrião de Hyper-V)| Registos para resolver problemas de serviço do Microsoft Azure Site Recovery.
**Aplicações e serviço de registos/Microsoft/Windows/Hyper-V-VMMS/Admin** (anfitrião de Hyper-V) | Registos para resolver problemas de gestão de VMS de Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Recolha de registos para a resolução de problemas avançada

Essas ferramentas podem ajudar na resolução de problemas avançada:

-   Para o VMM, realizar a coleção de registo do Site Recovery com o [ferramenta de plataforma de diagnóstico de suporte (SDP)](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   Para o Hyper-V sem o VMM, [baixe esta ferramenta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab), e execute-o no anfitrião Hyper-V para recolher os registos.

