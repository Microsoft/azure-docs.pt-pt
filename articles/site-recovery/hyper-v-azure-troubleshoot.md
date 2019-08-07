---
title: Solucionar problemas do Hyper-V para a recuperação de desastres do Azure com o Azure Site Recovery | Microsoft Docs
description: Descreve como solucionar problemas de recuperação de desastres com o Hyper-V para replicação do Azure usando o Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: rajanaki
ms.openlocfilehash: 2cf43f8a235b112cfcf1fc6c9dba626a5a0c9b7e
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828401"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Solucionar problemas de replicação e failover do Hyper-V para o Azure

Este artigo descreve os problemas comuns que você pode ter ao replicar VMs do Hyper-V locais para o Azure, usando [Azure site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Habilitar problemas de proteção

Se você tiver problemas ao habilitar a proteção para VMs do Hyper-V, verifique as seguintes recomendações:

1. Verifique se os hosts e as VMs do Hyper-V atendem a todos os [requisitos e pré-requisitos](hyper-v-azure-support-matrix.md).
2. Se os servidores Hyper-V estiverem localizados em nuvens System Center Virtual Machine Manager (VMM), verifique se você preparou o [servidor do VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Verifique se o serviço gerenciamento de máquinas virtuais do Hyper-V está em execução em hosts Hyper-V.
4. Verifique se há problemas que aparecem no Hyper-V-VMMS\Admin entrar na VM. Esse log está localizado > em **logs de aplicativos e serviços** **Microsoft** > **Windows**.
5. Na VM convidada, verifique se o WMI está habilitado e acessível.
   - [Saiba mais sobre](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) os testes básicos do WMI.
   - [Solucionar problemas](https://aka.ms/WMiTshooting) Esses.
   - [Solucionar](https://technet.microsoft.com/library/ff406382.aspx#H22) problemas com scripts e serviços WMI.
6. Na VM convidada, verifique se a versão mais recente do Integration Services está em execução.
    - [Verifique](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) se você tem a versão mais recente.
    - [Manter](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Integration Services atualizado.
    
## <a name="replication-issues"></a>Problemas de replicação

Resolva os problemas com replicações iniciais e contínuas da seguinte forma:

1. Verifique se você está executando a [versão mais recente](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) dos serviços de site Recovery.
2. Verifique se a replicação está em pausa:
   - Verifique o status de integridade da VM no console do Gerenciador do Hyper-V.
   - Se for crítico, clique com o botão direito do mouse na VM > **replicação** > **Exibir integridade da replicação**.
   - Se a replicação estiver em pausa, clique em **retomar replicação**.
3. Verifique se os serviços necessários estão em execução. Se não estiverem, reinicie-os.
    - Se você estiver replicando o Hyper-V sem o VMM, verifique se esses serviços estão em execução no host do Hyper-V:
        - Serviço de Gestão de Máquinas Virtuais
        - Serviço do Agente dos Serviços de Recuperação do Microsoft Azure
        - Serviço Microsoft Azure Site Recovery
        - Serviço de Anfitrião do Fornecedor de WMI
    - Se você estiver replicando com o VMM no ambiente, verifique se esses serviços estão em execução:
        - No host Hyper-V, verifique se o serviço de gerenciamento de máquinas virtuais, o agente de Serviços de Recuperação do Microsoft Azure e o serviço de host do provedor WMI estão em execução.
        - No servidor do VMM, verifique se o serviço System Center Virtual Machine Manager está em execução.
4. Verifique a conectividade entre o servidor Hyper-V e o Azure. Para verificar a conectividade, abra o Gerenciador de tarefas no host Hyper-V. Na guia **desempenho** , clique em **abrir monitor de recursos**. Na guia **rede** > **processo com a atividade de rede**, verifique se cbengine. exe está enviando ativamente grandes volumes (MBS) de dados.
5. Verifique se os hosts Hyper-V podem se conectar à URL de blob do armazenamento do Azure. Para verificar se os hosts podem se conectar, selecione e verifique **cbengine. exe**. Exiba **conexões TCP** para verificar a conectividade do host com o Azure Storage BLOB.
6. Verifique os problemas de desempenho, conforme descrito abaixo.
    
### <a name="performance-issues"></a>Problemas de desempenho

Limitações de largura de banda de rede podem afetar a replicação. Solucione problemas da seguinte maneira:

1. [Verifique](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) se há restrições de largura de banda ou limitação em seu ambiente.
2. Execute o [planejador de implantações](hyper-v-deployment-planner-run.md)Profiler.
3. Depois de executar o criador de perfil, siga as recomendações de [armazenamento](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) e [largura de banda](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) .
4. Verifique as [limitações de rotatividade de dados](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Se você vir variação de dados alta em uma VM, faça o seguinte:
   - Verifique se sua VM está marcada para ressincronização.
   - Siga [estas etapas](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) para investigar a origem da rotatividade.
   - A rotatividade pode ocorrer quando os arquivos de log HRL excedem 50% do espaço em disco disponível. Se esse for o problema, provisione mais espaço de armazenamento para todas as VMs nas quais o problema ocorre.
   - Verifique se a replicação não está em pausa. Se for, ele continuará gravando as alterações no arquivo HRL, o que pode contribuir para seu maior tamanho.
 

## <a name="critical-replication-state-issues"></a>Problemas críticos de estado de replicação

1. Para verificar a integridade da replicação, conecte-se ao console do Gerenciador do Hyper-V local, selecione a VM e verifique a integridade.

    ![Estado de funcionamento da replicação](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Clique em **Exibir integridade da replicação** para ver os detalhes:

    - Se a replicação estiver em pausa, clique com o botão direito do mouse na VM > **replicação** > **retomar replicação**.
    - Se uma VM em um host Hyper-V configurada em Site Recovery migrar para um host Hyper-V diferente no mesmo cluster ou para um computador autônomo, a replicação para a VM não será afetada. Basta verificar se o novo host Hyper-V atende a todos os pré-requisitos e está configurado em Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problemas de instantâneo consistente com o aplicativo

Um instantâneo consistente com o aplicativo é um instantâneo pontual dos dados do aplicativo dentro da VM. O Serviço de Cópias de Sombra de Volume (VSS) garante que os aplicativos na VM estejam em um estado consistente quando o instantâneo for tirado.  Esta seção detalha alguns problemas comuns que podem ocorrer.

### <a name="vss-failing-inside-the-vm"></a>Falha do VSS dentro da VM

1. Verifique se a versão mais recente do Integration Services está instalada e em execução.  Verifique se uma atualização está disponível executando o seguinte comando em um prompt do PowerShell com privilégios elevados no host Hyper-V: **Get-VM | selecione nome, estado, IntegrationServicesState**.
2. Verifique se os serviços VSS estão em execução e íntegros:
   - Para verificar os serviços, entre na VM convidada. Em seguida, abra um prompt de comando de administrador e execute os comandos a seguir para verificar se todos os gravadores VSS estão íntegros.
       - **VSSadmin list writers**
       - **Vssadmin da lista de sombras**
       - **VSSadmin list providers**
   - Verifique a saída. Se os gravadores estiverem em um estado de falha, faça o seguinte:
       - Verifique o log de eventos do aplicativo na VM para erros de operação do VSS.
   - Tente reiniciar esses serviços associados ao gravador com falha:
     - Cópia de sombra de volume
       - Azure Site Recovery o provedor de VSS
   - Depois de fazer isso, aguarde algumas horas para ver se os instantâneos consistentes com o aplicativo são gerados com êxito.
   - Como último recurso, tente reinicializar a VM. Isso pode resolver serviços que estão em estado sem resposta.
3. Verifique se você não tem discos dinâmicos na VM. Isso não tem suporte para instantâneos consistentes com o aplicativo. Você pode fazer check-in no gerenciamento de disco (diskmgmt. msc).

    ![Disco dinâmico](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Verifique se você não tem um disco iSCSI conectado à VM. Tal não é suportado.
5. Verifique se o serviço de backup está habilitado. Verifique se ele está habilitado > nas **configurações do Hyper-V** **Integration Services**.
6. Verifique se não há conflitos com aplicativos usando instantâneos VSS. Se vários aplicativos estiverem tentando obter instantâneos VSS ao mesmo tempo, poderão ocorrer conflitos. Por exemplo, se um aplicativo de backup estiver usando instantâneos VSS quando Site Recovery estiver agendado pela política de replicação para tirar um instantâneo.   
7. Verifique se a VM está apresentando uma alta taxa de rotatividade:
    - Você pode medir a taxa diária de alteração de dados para as VMs convidadas, usando contadores de desempenho no host Hyper-V. Para medir a taxa de alteração de dados, habilite o contador a seguir. Agregue um exemplo desse valor nos discos de VM por 5-15 minutos para obter a rotatividade de VM.
        - Categorias "Dispositivo de armazenamento virtual do Hyper-V"
        - Neutraliza "Bytes de gravação/s"</br>
        - Essa taxa de rotatividade de dados aumentará ou permanecerá em um alto nível, dependendo da ocupação da VM ou de seus aplicativos.
        - A variação média de dados do disco de origem é de 2 MB/s para o armazenamento Standard para Site Recovery. [Saiba mais](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Além disso, você pode [verificar as metas](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)de escalabilidade de armazenamento.
8. Execute o [planejador de implantações](hyper-v-deployment-planner-run.md).
9. Examine as recomendações para [rede](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) e [armazenamento](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>Falha do VSS dentro do host do Hyper-V

1. Verifique os logs de eventos para obter erros e recomendações do VSS:
    - No servidor de host do Hyper-v, abra o log de eventos do administrador do Hyper-v em **Visualizador de eventos** > logs > de**aplicativos e serviços** **Microsoft** > **Windows** > **Hyper-v**  >  **Administrador**.
    - Verifique se há algum evento que indique falhas de instantâneo consistentes com o aplicativo.
    - Um erro típico é: "O Hyper-V não pôde gerar o conjunto de instantâneos VSS para a máquina virtual ' XYZ ': O gravador sofreu um erro não transitório. Reiniciar o serviço VSS pode resolver problemas se o serviço não estiver respondendo. "

2. Para gerar instantâneos do VSS para a VM, verifique se os Integration Services do Hyper-V estão instalados na VM e se o serviço de integração de backup (VSS) está habilitado.
    - Verifique se os Integration Services Service/daemons do VSS estão em execução no convidado e estão em um estado **OK** .
    - Você pode verificar isso em uma sessão elevada do PowerShell no host Hyper-V com o comando **Get-VMIntegrationService-\<VMName VMName >-Name VSS** , você também pode obter essas informações fazendo logon na VM convidada. [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Verifique se os serviços de integração de backup/VSS na VM estão em execução e em estado íntegro. Caso contrário, reinicie esses serviços e o serviço do solicitante de cópia de sombra de volume do Hyper-V no servidor de host do Hyper-V.

### <a name="common-errors"></a>Erros comuns

**Código de erro** | **Mensagem** | **Detalhes**
--- | --- | ---
**0x800700EA** | "O Hyper-V não pôde gerar o conjunto de instantâneos VSS para a máquina virtual: Mais dados estão disponíveis. (0x800700EA). A geração do conjunto de instantâneos VSS poderá falhar se a operação de backup estiver em andamento.<br/><br/> Falha na operação de replicação para a máquina virtual: Mais dados estão disponíveis. " | Verifique se a VM tem o disco dinâmico habilitado. Tal não é suportado.
**0x80070032** | "O solicitante de cópia de sombra de volume do Hyper-V falhou ao se conectar à máquina virtual <./VMname > porque a versão não corresponde à versão esperada pelo Hyper-V | Verifique se as atualizações mais recentes do Windows estão instaladas.<br/><br/> [Atualize](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) para a versão mais recente do Integration Services.



## <a name="collect-replication-logs"></a>Coletar logs de replicação

Todos os eventos de replicação do Hyper-V são registrados no log do Hyper-V-VMMS\Admin, localizado em logs > de **aplicativos e serviços** **Microsoft** > **Windows**. Além disso, você pode habilitar um log analítico para o serviço de gerenciamento de máquinas virtuais do Hyper-V, da seguinte maneira:

1. Tornar os logs analíticos e de depuração visíveis no Visualizador de Eventos. Para disponibilizar os logs, na Visualizador de eventos, clique em **Exibir** > **Mostrar logs analíticos e de depuração.** O log analítico aparece em **Hyper-V-VMMS**.
2. No painel **ações** , clique em **habilitar log**. 

    ![Habilitar log](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Depois de habilitado, ele aparece no **Monitor de desempenho**, como uma **sessão de rastreamento de eventos** em conjuntos de coletores de **dados**. 
4. Para exibir as informações coletadas, interrompa a sessão de rastreamento desabilitando o log. Em seguida, salve o log e abra-o novamente no Visualizador de Eventos ou use outras ferramentas para convertê-lo conforme necessário.


### <a name="event-log-locations"></a>Locais do log de eventos

**Log de eventos** | **Detalhes** |
--- | ---
**Aplicativos e logs de serviço/Microsoft/VirtualMachineManager/servidor/administrador** (Servidor VMM) | Logs para solucionar problemas do VMM.
**Aplicativos e logs de serviço/MicrosoftAzureRecoveryServices/replicação** (Host Hyper-V) | Logs para solucionar problemas do Serviços de Recuperação do Microsoft Azure Agent. 
**Aplicativos e logs de serviço/Microsoft/Azure site Recovery/provedor/operacional** (Host Hyper-V)| Logs para solucionar problemas de Microsoft Azure Site Recovery de serviço.
**Aplicativos e logs de serviço/Microsoft/Windows/Hyper-V-VMMS/admin** (Host Hyper-V) | Logs para solucionar problemas de gerenciamento de VM do Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Coleta de log para solução de problemas avançada

Essas ferramentas podem ajudar com a solução de problemas avançada:

-   Para o VMM, execute Site Recovery coleta de log usando a [ferramenta SDP (plataforma de diagnóstico de suporte)](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   Para o Hyper-V sem o VMM, [Baixe essa ferramenta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)e execute-a no host Hyper-v para coletar os logs.

