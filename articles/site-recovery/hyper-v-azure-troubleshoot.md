---
title: Recuperação de desastres hiper-V com recuperação do site Azure
description: Descreve como resolver problemas de recuperação de desastres com replicação Hyper-V a Azure usando a Recuperação do Site Azure
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: rajanaki
ms.openlocfilehash: 0a3e5c922009353e4ba9ccab12cf70ea2b5992da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73961487"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Resolver problemas de replicação e ativação pós-falha do Hyper-V para o Azure

Este artigo descreve questões comuns que pode encontrar ao replicar no local Hiper-V V VMs para Azure, usando a Recuperação do [Site Azure](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Ativar questões de proteção

Se tiver problemas ao ativar a proteção para VMs Hiper-V, consulte as seguintes recomendações:

1. Verifique se os anfitriões e VMs hiper-V satisfazem todos os [requisitos e pré-requisitos.](hyper-v-azure-support-matrix.md)
2. Se os servidores Hyper-V estiverem localizados em nuvens de Gestor de Máquinas Virtuais (VMM) do System Center, verifique se preparou o [servidor VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Verifique se o serviço de Gestão de Máquinas Virtuais Hyper-V está a funcionar nos anfitriões hyper-V.
4. Verifique se existem problemas que aparecem no sinal de hiper-V-VMMS\Admin no VM. Este registo está localizado em **Registos de Aplicações e Serviços** > **Microsoft** > **Windows**.
5. No VM de hóspedes, verifique se o WMI está ativado e acessível.
   - [Saiba mais sobre](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) os testes básicos de WMI.
   - [Resolução de problemas](https://aka.ms/WMiTshooting) O WMI.
   - [Problemas](https://technet.microsoft.com/library/ff406382.aspx#H22) de resolução de problemas com scripts e serviços wMI.
6. No VM convidado, certifique-se de que a versão mais recente dos Serviços de Integração está em execução.
    - [Verifique](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) se tem a versão mais recente.
    - [Manter](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Serviços de Integração atualizados.
    
## <a name="replication-issues"></a>Problemas de replicação

Resolva os problemas com replicações iniciais e contínuas da seguinte forma:

1. Certifique-se de que está a executar a [versão mais recente](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) dos serviços de Recuperação do Site.
2. Verifique se a replicação é interrompida:
   - Verifique o estado de saúde da VM na consola Hyper-V Manager.
   - Se for crítico, clique à direita no VM > **Replication** > **View Replication Health**.
   - Se a replicação for interrompida, clique na **replicação do currículo**.
3. Verifique se os serviços necessários estão a funcionar. Se não estiverem, reiniciem-nos.
    - Se estiver a replicar o Hyper-V sem VMM, verifique se estes serviços estão a funcionar no hospedeiro Hyper-V:
        - Serviço de Gestão de Máquinas Virtuais
        - Serviço do Agente dos Serviços de Recuperação do Microsoft Azure
        - Serviço Microsoft Azure Site Recovery
        - Serviço de Anfitrião do Fornecedor de WMI
    - Se estiver a replicar-se com VMM no ambiente, verifique se estes serviços estão em funcionamento:
        - No hospedeiro Hyper-V, verifique se o serviço de Gestão de Máquinas Virtuais, o Agente de Serviços de Recuperação do Microsoft Azure e o serviço de hospedagem do fornecedor WMI estão em funcionamento.
        - No servidor VMM, certifique-se de que o Serviço de Gestor de Máquinas Virtuais do System Center está em funcionamento.
4. Verifique a conectividade entre o servidor Hyper-V e o Azure. Para verificar a conectividade, abra o Task Manager no hospedeiro Hyper V. No separador **Performance,** clique no **Monitor de Recursos Abertos**. No separador **rede** > Processo com Atividade de **Rede,** verifique se o cbengine.exe está a enviar ativamente grandes volumes (Mbs) de dados.
5. Verifique se os anfitriões Hyper-V podem ligar-se ao URL de armazenamento Azure. Para verificar se os anfitriões podem ligar, selecionar e verificar **cbengine.exe**. Consulte **as Ligações TCP** para verificar a conectividade do hospedeiro até à bolha de armazenamento Azure.
6. Verifique os problemas de desempenho, como descrito abaixo.
    
### <a name="performance-issues"></a>Problemas de desempenho

As limitações de largura de banda da rede podem ter impacto na replicação. Problemas de resolução de problemas da seguinte forma:

1. [Verifique](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) se existem restrições de largura de banda ou estrangulamento no seu ambiente.
2. Executar o [perfil de Planejador](hyper-v-deployment-planner-run.md)de Implantação .
3. Depois de executar o perfil, siga as recomendações de [largura de banda](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) e [armazenamento.](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
4. Verifique [as limitações](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)de recolha de dados . Se vir dados elevados num VM, faça o seguinte:
   - Verifique se o seu VM está marcado para ressincronização.
   - Siga [estes passos](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) para investigar a origem do churn.
   - O churn pode ocorrer quando os ficheiros de registo hrl excederem 50% do espaço de disco disponível. Se for esta a questão, disponibilize mais espaço de armazenamento para todos os VMs sobre os quais o problema ocorre.
   - Verifique se a replicação não está interrompida. Se for, continua a escrever as alterações ao ficheiro HRL, o que pode contribuir para o seu tamanho aumentado.
 

## <a name="critical-replication-state-issues"></a>Questões críticas do estado de replicação

1. Para verificar a saúde da replicação, ligue-se à consola Hyper-V Manager no local, selecione o VM e verifique a saúde.

    ![Saúde de replicação](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Clique em **Ver Replication Health** para ver os detalhes:

    - Se a replicação for interrompida, clique à direita na **replicação** > do Currículo de**Replicação**VM > .
    - Se um VM num hospedeiro Hyper-V configurado na Recuperação do Site migra para um hospedeiro hiper-V diferente no mesmo cluster, ou para uma máquina autónoma, a replicação para o VM não é afetada. Basta verificar se o novo hospedeiro Hyper-V cumpre todos os pré-requisitos e está configurado na Recuperação do Site.

## <a name="app-consistent-snapshot-issues"></a>Problemas instantâneos consistentes com aplicativos

Um instantâneo consistente com aplicações é uma imagem pontual dos dados da aplicação dentro do VM. O Serviço de Cópia sinuosa de Volume Sombra (VSS) garante que as aplicações no VM estão num estado consistente quando o instantâneo é tirado.  Esta secção detalha algumas questões comuns que poderá experimentar.

### <a name="vss-failing-inside-the-vm"></a>VsS falhando dentro do VM

1. Verifique se a versão mais recente dos serviços de Integração está instalada e em funcionamento.  Verifique se existe uma atualização executando o seguinte comando a partir de um pedido elevado da PowerShell no hospedeiro Hyper-V: **get-vm / selecione Name, State, IntegrationServicesState**.
2. Verifique se os serviços VSS estão em funcionamento e saudáveis:
   - Para consultar os serviços, inscreva-se no VM convidado. Em seguida, abra um pedido de comando administrativo, e executar os seguintes comandos para verificar se todos os escritores VSS são saudáveis.
       - **Escritores da lista vssadmin**
       - **Sombras da lista de Vssadmin**
       - **Fornecedores de listas de Vssadmin**
   - Verifique a saída. Se os escritores estiverem num estado falhado, faça o seguinte:
       - Verifique o registo do evento de aplicação no VM para obter erros de funcionamento VSS.
   - Tente reiniciar estes serviços associados ao escritor falhado:
     - Cópia sombra do volume
       - Fornecedor VSS de Recuperação do Site Azure
   - Depois de fazê-lo, espere por algumas horas para ver se as imagens consistentes com aplicações são geradas com sucesso.
   - Como último recurso, tente reiniciar o VM. Isto pode resolver serviços que estão em estado sem resposta.
3. Verifique se não tem discos dinâmicos no VM. Não é suportado por fotos consistentes com aplicações. Pode fazer o check-in em Disk Management (diskmgmt.msc).

    ![Disco dinâmico](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Verifique se não tem um disco iSCSI ligado ao VM. Tal não é suportado.
5. Verifique se o serviço de backup está ativado. Verifique se está ativado em serviços de > **integração**de **configurações Hiper-V**.
6. Certifique-se de que não existem conflitos com aplicações que tiram fotos VSS. Se várias aplicações estiverem a tentar tirar fotos vss ao mesmo tempo, podem ocorrer conflitos. Por exemplo, se uma aplicação de backup estiver a tirar fotos vsS quando a Recuperação do Site está programada pela sua política de replicação para tirar uma fotografia.   
7. Verifique se o VM está a passar por uma elevada taxa de agitação:
    - Pode medir a taxa diária de alteração de dados para os VMs dos hóspedes, utilizando contadores de desempenho no hospedeiro Hyper-V. Para medir a taxa de alteração de dados, ative o contador seguinte. Agregar uma amostra deste valor através dos discos VM durante 5-15 minutos, para obter o vm churn.
        - Categoria: "Dispositivo de Armazenamento Virtual Hiper-V"
        - Contador: "Write Bytes / Sec"</br>
        - Esta taxa de recolha de dados aumentará ou permanecerá a um nível elevado, dependendo do quão ocupado o VM ou as suas aplicações estão.
        - O churn de dados de disco de origem média é de 2 MB/s para armazenamento padrão para recuperação do site. [Mais informações](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Além disso, pode verificar os alvos de [escalabilidade](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)do armazenamento .
8. Executar o [Planificador](hyper-v-deployment-planner-run.md)de Implantação .
9. Reveja as recomendações relativas à [rede](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) e [armazenamento.](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS falhando dentro do hospedeiro Hiper-V

1. Verifique os registos de eventos para erros e recomendações vss:
    - No servidor de anfitriões Hyper-V, abra o registo do evento Hyper-V Admin em**Aplicações e Serviços** > do Espectador > de **Eventos**Regista o**Microsoft** > **Windows** > **Hyper-V** > **Admin**.
    - Verifique se existem eventos que indiquem falhas de instantâneo consistentes com aplicações.
    - Um erro típico é: "O Hiper-V não conseguiu gerar um conjunto de instantâneos VSS para a máquina virtual 'XYZ': O escritor experimentou um erro não transitório. Reiniciar o serviço VSS pode resolver problemas se o serviço não responder."

2. Para gerar instantâneos VSS para o VM, verifique se os Serviços de Integração Hyper-V estão instalados no VM e se o Serviço de Integração de Backup (VSS) está ativado.
    - Certifique-se de que os serviços de integração VSS/daemons estão em execução no hóspede, e estão em estado **ok.**
    - Pode verificar isto a partir de uma sessão elevada da PowerShell no anfitrião Hyper-V com comando **Get-VMIntegrationService -VMName VMName\<>-Name VSS** Também pode obter esta informação sessão de login no VM do hóspede. [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Certifique-se de que os Serviços de Integração Backup/VSS no VM estão em funcionamento e em estado saudável. Caso contrário, reinicie estes serviços e o serviço de solicitação Hyper-V Volume Shadow Copy no servidor de anfitriões Hyper-V.

### <a name="common-errors"></a>Erros comuns

**Código de erro** | **Mensagem** | **Detalhes**
--- | --- | ---
**0x800700EA** | "O Hyper-V não conseguiu gerar um conjunto de instantâneos VSS para máquina virtual: Mais dados estão disponíveis. (0x800700EA). A geração de conjuntos de instantâneovs ssnapshot pode falhar se a operação de backup estiver em andamento.<br/><br/> O funcionamento da replicação para máquina virtual falhou: Mais dados estão disponíveis." | Verifique se o seu VM tem um disco dinâmico ativado. Tal não é suportado.
**0x80070032** | "O Hyper-V Volume Shadow Copy Requestor não conseguiu ligar-se à máquina virtual <./VMname> porque a versão não corresponde à versão esperada pelo Hyper-V | Verifique se as últimas atualizações do Windows estão instaladas.<br/><br/> [Upgrade](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) para a versão mais recente dos Serviços de Integração.



## <a name="collect-replication-logs"></a>Recolher registos de replicação

Todos os eventos de replicação Hyper-V estão registados no registo Hyper-V-VMMS\Admin, localizado em Registos de **Aplicações e Serviços** > **Microsoft** > **Windows**. Além disso, pode ativar um registo Analytic para o Serviço de Gestão de Máquinas Virtuais Hyper-V, da seguinte forma:

1. Torne os registos Analíticos e Debug visualizados no Espectador de Eventos. Para disponibilizar os registos, no Visualizador de Eventos, clique em **Ver** > **Mostrar Registos Analíticos e Debug.** O registo analítico aparece em **Hiper-V-VMMS**.
2. No painel **Deações,** clique em **Ativar O Log**. 

    ![Ativar o log](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Depois de ativado, aparece no Monitor de **Desempenho,** como uma Sessão de Rastreio de **Eventos** em Conjuntos de **Colecionadores**de Dados . 
4. Para visualizar as informações recolhidas, pare a sessão de rastreio desativando o registo. Em seguida, guarde o registo e abra-o novamente no Espectador de Eventos, ou use outras ferramentas para convertê-lo conforme necessário.


### <a name="event-log-locations"></a>Localizações de registo de eventos

**Registo de eventos** | **Detalhes** |
--- | ---
**Aplicações e registos de serviço/Microsoft/VirtualMachineManager/Server/Admin** (servidor VMM) | Regista problemas com problemas de VMM.
**Aplicações e registos de serviço/MicrosoftAzureRecoveryServices/Replication** (anfitrião Hyper-V) | Regista problemas para resolver problemas problemas com os problemas do Agente de Serviços de Recuperação do Microsoft Azure. 
**Aplicações e registos de serviço/Recuperação/Fornecedor/Operacional do Site Microsoft/Azure**| Regista problemas para resolver problemas no Serviço de Recuperação de Sites do Microsoft Azure.
**Aplicações e registos de serviço/Microsoft/Windows/Hyper-V-VMMS/Admin** (anfitrião Hyper-V) | Regista problemas de gestão de VM hiper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Coleção de log para resolução avançada de problemas

Estas ferramentas podem ajudar na resolução avançada de problemas:

-   Para vMM, execute a recolha de registos de recuperação do site utilizando a ferramenta Plataforma de [Diagnóstico de Suporte (SDP).](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)
-   Para hyper-V sem VMM, [descarregue esta ferramenta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)e execute-a no hospedeiro Hyper-V para recolher os registos.

