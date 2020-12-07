---
title: Resolução de problemas Recuperação de desastres hiper-V com recuperação do local de Azure
description: Descreve como resolver problemas de recuperação de desastres com a replicação de Hyper-V a Azure usando a recuperação do site Azure
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: sharrai
ms.openlocfilehash: c804e13029dcec42a43885cbf0d9b227b3d0338f
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750807"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Resolver problemas de replicação e ativação pós-falha do Hyper-V para o Azure

Este artigo descreve questões comuns que poderá encontrar ao replicar no local Hiper-V VMs para Azure, utilizando [a recuperação do site Azure](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Permitir problemas de proteção

Se sentir problemas quando ativa a proteção de VMs hiper-V, verifique as seguintes recomendações:

1. Verifique se os seus anfitriões e VMs hiper-V satisfazem todos os [requisitos e requisitos.](hyper-v-azure-support-matrix.md)
2. Se os servidores Hiper-V estiverem localizados em nuvens do Gestor de Máquinas Virtuais do Centro do Sistema (VMM), verifique se preparou o [servidor VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Verifique se o serviço de Gestão de Máquinas Virtuais Hyper-V está a funcionar em anfitriões Hiper-V.
4. Verifique se há problemas que aparecem no hiper-VMMS\Admin para o VM. Este registo está localizado em **Aplicações e Serviços Regista**  >  **o Microsoft**  >  **Windows**.
5. No VM do hóspede, verifique se o WMI está ativado e acessível.
   - [Saiba mais sobre](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) os testes básicos de WMI.
   - [Resolução de problemas](/windows/win32/wmisdk/wmi-troubleshooting) O WMI.
   - [Problemas de resolução de problemas](/previous-versions/tn-archive/ff406382(v=msdn.10)#H22) com scripts e serviços do WMI.
6. No VM convidado, certifique-se de que a versão mais recente dos Serviços de Integração está em execução.
    - [Verifique](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) se tem a versão mais recente.
    - [Manter](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Serviços de Integração atualizados.

### <a name="cannot-enable-protection-as-the-virtual-machine-is-not-highly-available-error-code-70094"></a>Não é possível ativar a proteção, uma vez que a máquina virtual não está altamente disponível (código de erro 70094)

Quando está a permitir a replicação de uma máquina e encontra um erro indicando que a replicação não pode ser ativada, uma vez que a máquina não está altamente disponível, então para corrigir este problema tente os passos abaixo:

- Reinicie o serviço VMM no servidor VMM.
- Retire a máquina virtual do cluster e adicione-a novamente.

### <a name="the-vss-writer-ntds-failed-with-status-11-and-writer-specific-failure-code-0x800423f4"></a>O escritor vss NTDS falhou com o estatuto 11 e o código de falha específico do escritor 0x800423F4

Ao tentar ativar a replicação, pode enfrentar um erro informando que ativar a replicação falhada a NTDS falhou. Uma das possíveis causas para este problema é que o sistema operativo da máquina virtual no Windows Server 2012 e não no Windows Server 2012 R2. Para corrigir este problema, experimente os passos abaixo:

- Upgrade para Windows Server R2 com 4072650 aplicado.
- Certifique-se de que o Hyper-V Host também é windows 2016 ou superior.

## <a name="replication-issues"></a>Problemas de replicação

Resolva os problemas com replicações iniciais e contínuas da seguinte forma:

1. Certifique-se de que está a executar a [versão mais recente](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) dos serviços de Recuperação do Site.
2. Verifique se a replicação é interrompida:
   - Verifique o estado de saúde em VM na consola Hyper-V Manager.
   - Se for crítico, clique com o botão direito para ver a Saúde de **Replicação** da  >  **Replicação**> de Replicação em VM .
   - Se a replicação for interrompida, clique em **Retomar a Replicação**.
3. Verifique se os serviços necessários estão a funcionar. Se não estiverem, reinicie-os.
    - Se estiver a replicar o Hyper-V sem VMM, verifique se estes serviços estão a funcionar no anfitrião Hyper-V:
        - Serviço de Gestão de Máquinas Virtuais
        - Serviço do Agente dos Serviços de Recuperação do Microsoft Azure
        - Serviço Microsoft Azure Site Recovery
        - Serviço de Anfitrião do Fornecedor de WMI
    - Se estiver a replicar com VMM no ambiente, verifique se estes serviços estão a funcionar:
        - No anfitrião Hyper-V, verifique se o serviço de Gestão de Máquinas Virtuais, o Agente de Serviços de Recuperação do Microsoft Azure e o serviço WMI Provider Host estão em execução.
        - No servidor VMM, certifique-se de que o Serviço de Gestor de Máquinas Virtual do Centro de Sistema está em funcionamento.
4. Verifique a conectividade entre o servidor Hyper-V e o Azure. Para verificar a conectividade, abra o Gestor de Tarefas no anfitrião Hyper V. No **separador Desempenho,** clique em **'Monitor de Recursos Aberto'.** No separador **rede** > **Processo com Atividade da Rede,** verifique se cbengine.exe está a enviar ativamente grandes volumes (Mbs) de dados.
5. Verifique se os anfitriões Hiper-V podem ligar-se ao URL blob de armazenamento Azure. Para verificar se os anfitriões podem ligar, selecione e verifique **cbengine.exe**. Consulte **as ligações TCP** para verificar a conectividade do anfitrião até à bolha de armazenamento Azure.
6. Verifique os problemas de desempenho, como descrito abaixo.
    
### <a name="performance-issues"></a>Problemas de desempenho

As limitações da largura de banda da rede podem ter impacto na replicação. Problemas de resolução de problemas da seguinte forma:

1. [Verifique](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) se existem restrições de largura de banda ou de estrangulamento no seu ambiente.
2. Executar o [profiler de planeamento de implementação](hyper-v-deployment-planner-run.md).
3. Depois de executar o perfil, siga as recomendações [de largura de banda](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) e [armazenamento.](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
4. Verifique as [limitações do churn de dados](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Se vir dados elevados num VM, faça o seguinte:
   - Verifique se o seu VM está marcado para a ressincronização.
   - Siga [estes passos](https://techcommunity.microsoft.com/t5/virtualization/bg-p/Virtualization) para investigar a origem da agitação.
   - O churn pode ocorrer quando os ficheiros de registo HRL excedem 50% do espaço disponível do disco. Se este for o problema, fornece mais espaço de armazenamento para todos os VMs sobre os quais o problema ocorre.
   - Verifique se a replicação não está interrompida. Se for, continua a escrever as alterações ao ficheiro HRL, o que pode contribuir para o seu tamanho aumentado.
 

## <a name="critical-replication-state-issues"></a>Questões críticas do Estado de replicação

1. Para verificar a saúde da replicação, ligue-se à consola Hyper-V Manager no local, selecione o VM e verifique a saúde.

    ![Saúde de replicação](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Clique **em Ver Saúde de Replicação** para ver os detalhes:

    - Se a replicação for interrompida, clique com o botão direito na replicação do Currículo **de Replicação** de VM >  >  **Resume replication**.
    - Se um VM num hospedeiro Hiper-V configurado na Recuperação do Local migra para um hospedeiro Hiper-V diferente no mesmo cluster, ou para uma máquina autónoma, a replicação para o VM não é impactada. Verifique se o novo anfitrião Hyper-V cumpre todos os pré-requisitos e está configurado na Recuperação do Local.

## <a name="app-consistent-snapshot-issues"></a>Problemas de instantâneo consistentes com aplicativos

Um instantâneo consistente com aplicações é uma imagem pontual dos dados da aplicação dentro do VM. O Volume Shadow Copy Service (VSS) garante que as aplicações no VM estão num estado consistente quando o instantâneo é tirado.  Esta secção detalha algumas questões comuns que poderá experimentar.

### <a name="vss-failing-inside-the-vm"></a>VSS falhando dentro do VM

1. Verifique se a versão mais recente dos serviços de Integração está instalada e em execução.  Verifique se existe uma atualização executando o seguinte comando a partir de uma solicitação elevada de PowerShell no anfitrião Hyper-V: **get-vm / select Name, State, IntegrationServicesState**.
2. Verifique se os serviços vss estão funcionando e saudáveis:
   - Para verificar os serviços, inscreva-se no VM do hóspede. Em seguida, abra um pedido de comando administrativo, e executar os seguintes comandos para verificar se todos os escritores vss estão saudáveis.
       - **Escritores da lista vssadmin**
       - **Sombras da lista de Vssadmin**
       - **Fornecedores de listas vssadmin**
   - Verifique a saída. Se os escritores estiverem num estado de falha, faça o seguinte:
       - Verifique o registo do evento de aplicação no VM para obter erros de funcionamento vss.
   - Tente reiniciar estes serviços associados ao escritor falhado:
     - Cópia sombra do volume
       - Fornecedor vss de recuperação do site Azure
   - Depois de fazer isto, aguarde algumas horas para ver se as imagens consistentes com aplicações são geradas com sucesso.
   - Como último recurso tente reiniciar o VM. Isto pode resolver serviços que estão em estado sem resposta.
3. Verifique se não tem discos dinâmicos no VM. THis não é suportado para fotos consistentes com aplicações. Pode consultar em Disk Management (diskmgmt.msc).

    ![Disco dinâmico](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Verifique se não tem um disco iSCSI ligado ao VM. Tal não é suportado.
5. Verifique se o serviço de cópia de segurança está ativado. Verifique se está ativado nos Serviços de Integração **de Definições Hiper-V**  >  **Integration Services**.
6. Certifique-se de que não existem conflitos com apps que tiram fotos vss. Se várias aplicações estiverem a tentar tirar fotos vss ao mesmo tempo, podem ocorrer conflitos. Por exemplo, se uma aplicação de Backup estiver a tirar fotografias do VSS quando a Recuperação do Site é agendada pela sua política de replicação para tirar uma fotografia.   
7. Verifique se o VM está a passar por uma alta taxa de churn:
    - Pode medir a taxa diária de alteração de dados para os VMs do hóspede, utilizando contadores de desempenho no anfitrião Hyper-V. Para medir a taxa de alteração de dados, ative o contador seguinte. Agregar uma amostra deste valor através dos discos VM durante 5-15 minutos, para obter o churn VM.
        - Categoria: "Dispositivo de Armazenamento Virtual Hiper-V"
        - Contador: "Write Bytes / Sec"</br>
        - Esta taxa de churn de dados irá aumentar ou permanecer a um nível elevado, dependendo do quão ocupados estão os VM ou as suas aplicações.
        - O dado médio do disco de origem é de 2 MB/s para armazenamento padrão para recuperação do local. [Saiba mais](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Além disso, pode [verificar os objetivos de escalabilidade de armazenamento](../storage/common/scalability-targets-standard-account.md).
8. Certifique-se de que, se estiver a utilizar um servidor baseado em Linux, então ativou a consistência da aplicação nele. [Saiba mais](./site-recovery-faq.md#replication)
9. Executar o [Planejador de Implantação](hyper-v-deployment-planner-run.md).
10. Reveja as recomendações para [a rede](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) e [armazenamento.](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS falhando dentro do Anfitrião Hiper-V

1. Consulte os registos de eventos para obter erros e recomendações vss:
    - No servidor anfitrião Hyper-V, abra o registo de eventos de Admin Hyper-V em Aplicações e Serviços **do Espectador** de  >  **Eventos**  >  **Microsoft**  >  **Windows**  >  **Hyper-V**  >  **Admin**.
    - Verifique se existem eventos que indiquem falhas de instantâneo consistentes em aplicações.
    - Um erro típico é o seguinte: "O Hiper-V não conseguiu gerar o conjunto de instantâneos VSS para a máquina virtual 'XYZ': O escritor experimentou um erro não transitório. Reiniciar o serviço VSS pode resolver problemas se o serviço não responder."

2. Para gerar instantâneos VSS para o VM, verifique se os Serviços de Integração de Hiper-V estão instalados no VM e se o Serviço de Integração de Backup (VSS) está ativado.
    - Certifique-se de que o serviço vss/daemons dos Serviços de Integração está em funcionamento no hóspede, e está em **bom** estado.
    - Pode verificar isto a partir de uma sessão elevada de PowerShell no anfitrião Hyper-V com o comando **Get-VMIntegrationService -VMName \<VMName> -Name VSS** Também pode obter esta informação iniciando sessão no VM convidado. [Saiba mais](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Certifique-se de que os Serviços de Integração backup/VSS no VM estão em funcionamento e em estado saudável. Caso contrário, reinicie estes serviços e o serviço de pedidos de cópia de sombra de volume Hiper-V no servidor anfitrião Hyper-V.

### <a name="common-errors"></a>Erros comuns

**Código de erro** | **Mensagem** | **Detalhes**
--- | --- | ---
**0x800700EA** | "O Hiper-V não conseguiu gerar o conjunto de instantâneos VSS para a máquina virtual: Mais dados estão disponíveis. (0x800700EA). A geração de conjunto de instantâneos VSS pode falhar se a operação de backup estiver em andamento.<br/><br/> A operação de replicação da máquina virtual falhou: Mais dados estão disponíveis." | Verifique se o seu VM tem disco dinâmico ativado. Tal não é suportado.
**0x80070032** | "Hyper-V Volume Shadow Copy Requestor não conseguiu ligar-se à máquina virtual <./VMname> porque a versão não corresponde à versão esperada pelo Hyper-V | Verifique se as atualizações mais recentes do Windows estão instaladas.<br/><br/> [Upgrade](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) para a versão mais recente dos Serviços de Integração.



## <a name="collect-replication-logs"></a>Recolher registos de replicação

Todos os eventos de replicação do Hyper-V estão registados no registo Hyper-VMMS\Admin, localizado em **Aplicações e Registos de Serviços**  >  **Microsoft**  >  **Windows**. Além disso, pode ativar um registo analítico para o Serviço de Gestão de Máquinas Virtuais Hiper-V, da seguinte forma:

1. Tornar os registos Analíticos e Debug visualizadores no Visualizador de Eventos. Para disponibilizar os registos, no Visualizador de Eventos, clique em **Ver**  >  **Mostrar Registos Analíticos e Debug.** O registo analítico aparece em **Hiper-V-VMMS**.
2. No painel **de Ações,** clique em **Ativar o Registo**. 

    ![Ativar o registo](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Depois de ativado, aparece no Monitor de **Desempenho,** como uma **Sessão de Rastreio de Eventos** em **Conjuntos de Coletor de Dados**. 
4. Para visualizar as informações recolhidas, pare a sessão de rastreio desativando o registo. Em seguida, guarde o registo e abra-o novamente no Visualizador de Eventos, ou use outras ferramentas para convertê-lo conforme necessário.


### <a name="event-log-locations"></a>Locais de registo de eventos

**Registo de eventos** | **Detalhes** |
--- | ---
**Aplicações e Registos de Serviço/Microsoft/VirtualMachineManager/Server/Admin** (servidor VMM) | Registos para resolver problemas de VMM.
**Aplicações e Registos de Serviço/MicrosoftAzureRecoveryServices/Replication** (anfitrião Hiper-V) | Registos para resolver problemas com os problemas do Agente de Serviços de Recuperação do Microsoft Azure. 
**Aplicações e Registos de Serviço/Recuperação/Provedor/Fornecedor/Operacional do Site Microsoft/Azure** (anfitrião Hiper-V)| Registos para resolver problemas no Serviço de Recuperação do Site microsoft Azure.
**Aplicações e Registos de Serviço/Microsoft/Windows/Hyper-VMMS/Admin** (anfitrião Hiper-V) | Registos para resolver problemas de gestão de Hiper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Coleção de registos para resolução avançada de problemas

Estas ferramentas podem ajudar na resolução avançada de problemas:

-   Para o VMM, execute a recolha de registos de recuperação do local utilizando a [ferramenta Plataforma de Diagnóstico de Suporte (SDP).](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)
-   Para Hiper-V sem VMM, [descarregue esta ferramenta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)e execute-a no anfitrião Hyper-V para recolher os registos.