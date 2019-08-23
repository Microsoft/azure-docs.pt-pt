---
title: Configurar a recuperação de desastre de VMs VMware ou de servidores físicos em um site secundário com Azure Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação de desastre de VMs VMware ou servidores físicos Windows e Linux para um site secundário com Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: a87abfdd70db07e4310dc6a39a280e12f664d03b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972097"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Configurar a recuperação de desastre de máquinas virtuais VMware locais ou servidores físicos para um site secundário

O InMage Scout no [Azure site Recovery](site-recovery-overview.md) fornece replicação em tempo real entre sites VMware locais. O InMage Scout está incluído em Azure Site Recovery assinaturas de serviço.

## <a name="end-of-support-announcement"></a>Anúncio do fim do suporte

O cenário de Azure Site Recovery para replicação entre data centers físicos ou VMware locais está atingindo o fim do suporte.

-   De agosto de 2018, o cenário não pode ser configurado no cofre dos serviços de recuperação e o software InMage Scout não pode ser baixado do cofre. As implantações existentes terão suporte. 
-   De dezembro de 31 2020, o cenário não terá suporte.
- Os parceiros existentes podem integrar novos clientes ao cenário até que o suporte termine.

Durante 2018 e 2019, duas atualizações serão lançadas: 

-   Atualização 7: Corrige problemas de conformidade e configuração de rede e fornece suporte a TLS 1,2.
-   Atualização 8: Adiciona suporte para sistemas operacionais Linux RHEL/CentOS 7.3/7.4/7.5 e para SUSE 12

Após a atualização 8, nenhuma outra atualização será lançada. Haverá suporte limitado a hotfixes para os sistemas operacionais adicionados na atualização 8 e correções de bugs com base no melhor esforço.

Azure Site Recovery continua a inovar fornecendo aos clientes VMware e Hyper-V uma solução de DRaaS direta e de melhor classe com o Azure como um site de recuperação de desastre. A Microsoft recomenda que os clientes existentes do InMage/ASR Scout considerem o uso do Azure Site Recovery VMware para o cenário do Azure para suas necessidades de continuidade de negócios. O cenário de Azure Site Recovery do VMware para o Azure é uma solução de DR de classe empresarial para aplicativos VMware, que oferece RPO e RTO de minutos, suporte para replicação e recuperação de aplicativos de várias VMS, integração direta, monitoramento abrangente e vantagem significativa do TCO.

### <a name="scenario-migration"></a>Migração de cenário
Como alternativa, é recomendável configurar a recuperação de desastre para VMs VMware locais e máquinas físicas replicando-as no Azure. Faça isso da seguinte maneira:

1.  Examine a comparação rápida abaixo. Antes de poder replicar computadores locais, você precisa verificar se eles atendem [aos requisitos](./vmware-physical-azure-support-matrix.md#replicated-machines) de replicação para o Azure. Se você estiver replicando VMs VMware, recomendamos que revise as [diretrizes de planejamento de capacidade](./site-recovery-plan-capacity-vmware.md)e execute a ferramenta de [planejador de implantações](./site-recovery-deployment-planner.md) para identificar os requisitos de capacidade e verificar a conformidade.
2.  Depois de executar o Planejador de Implantações, você pode configurar a replicação: o para VMs VMware, siga estes tutoriais para [preparar o Azure](./tutorial-prepare-azure.md), [preparar seu ambiente VMware local](./vmware-azure-tutorial-prepare-on-premises.md)e [Configurar a recuperação](./vmware-azure-tutorial-prepare-on-premises.md)de desastres.
o para computadores físicos, siga este [tutorial](./physical-azure-disaster-recovery.md).
3.  Depois que os computadores estiverem replicando para o Azure, você poderá executar uma [análise de recuperação de desastre](./site-recovery-test-failover-to-azure.md) para verificar se tudo está funcionando conforme o esperado.

### <a name="quick-comparison"></a>Comparação rápida

**Funcionalidade** | **Replicação para o Azure** |**Replicação entre data centers VMware**
--|--|--
**Componentes necessários** |Serviço de mobilidade em máquinas replicadas. Servidor de configuração local, servidor de processo, servidor de destino mestre. Servidor de processo temporário no Azure para failback.|Serviço de mobilidade, servidor de processo, servidor de configuração e destino mestre
**Configuração e orquestração** |Cofre dos serviços de recuperação no portal do Azure | Usando vContinuum 
**Replicados** |Disco (Windows e Linux) |Volume-Windows<br> Disk-Linux
**Cluster de disco compartilhado** |Não suportado|Suportadas
**Limites de rotatividade de dados (média)** |dados de 10 MB/s por disco<br> dados de 25 MB/s por VM<br> [Saiba mais](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > dados de 10 MB/s por disco  <br> > 25 MB/s de dados por VM
**Monitorização** |De portal do Azure|Da CX (servidor de configuração)
**Matriz de suporte** | [Clique aqui para obter detalhes](./vmware-physical-azure-support-matrix.md)|[Baixar matriz compatível com o ASR Scout](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial:

- [Examine](vmware-physical-secondary-support-matrix.md) os requisitos de suporte para todos os componentes.
- Verifique se os computadores que você deseja replicar estão em conformidade com o [suporte ao computador replicado](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Baixar e instalar atualizações de componentes

 Examine e instale as [atualizações](#updates)mais recentes. As atualizações devem ser instaladas nos servidores na seguinte ordem:

1. Servidor RX (se aplicável)
2. Servidores de configuração
3. Servidores de processo
4. Servidores de destino mestre
5. servidores vContinuum
6. Servidor de origem (servidores Windows e Linux)

Instale as atualizações da seguinte maneira:

> [!NOTE]
>A versão de atualização de arquivo de todos os componentes do Scout pode não ser a mesma no arquivo. zip de atualização. A versão mais antiga indica que não há nenhuma alteração no componente desde a atualização anterior para essa atualização.

Baixe o arquivo. zip de [atualização](https://aka.ms/asr-scout-update7) e os arquivos de configuração de [atualização do MySQL e php](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) . O arquivo Update. zip contém todos os binários base e binários de atualização cumulativa dos seguintes componentes: 
- InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015.tar.gz
- RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release.tar.gz
  1. Extraia os arquivos. zip.
  2. **Servidor RX**: Copie **rx_ 8.0.7.0 _ga_update_7_2965621_28dec18. tar. gz** para o servidor RX e extraia-o. Na pasta extraída, execute **/install**.
  3. **Servidor de configuração e servidor de processo**: Copie **cx_windows_ 8.0.7.0 _ga_update_7_2965621_28dec18. exe** para o servidor de configuração e o servidor de processo. Clique duas vezes para executá-lo.<br>
  4. **Servidor de destino mestre do Windows**: Para atualizar o agente unificado, copie **inmage_ua_ 8.0.7.0 _windows_ga_27dec2018_release. exe** para o servidor. Clique duas vezes nele para executá-lo. O mesmo arquivo também pode ser usado para nova instalação. A mesma atualização de agente unificado também se aplica ao servidor de origem.
  A atualização não precisa ser aplicada ao destino mestre preparado com **inmage_scout_vcontinuum_mt_ 8.0.7.0 _windows_ga_27dec2018_release. exe** , pois esse é um novo instalador de GA com todas as alterações mais recentes.
  5. **servidor vContinuum**:  Copie **inmage_scout_vcontinuum_mt_ 8.0.7.0 _windows_ga_27dec2018_release. exe** para o servidor.  Verifique se você fechou o assistente de vContinuum. Clique duas vezes no arquivo para executá-lo.
  6. **Servidor de destino mestre do Linux**: Para atualizar o agente unificado, copie **inmage_ua_ 8.0.7.0 _rhel6-64_ga_03dec2018_release. tar. gz** para o servidor de destino mestre do Linux e extraia-o. Na pasta extraída, execute **/install**.
  7. **Servidor de origem do Windows**: Para atualizar o agente unificado, copie **inmage_ua_ 8.0.7.0 _windows_ga_27dec2018_release. exe** para o servidor de origem. Clique duas vezes no arquivo para executá-lo. 
  8. **Servidor de origem do Linux**: Para atualizar o agente unificado, copie a versão correspondente do arquivo do agente unificado para o servidor Linux e extraia-a. Na pasta extraída, execute **/install**.  Exemplo: Para o servidor RHEL 6,7 64-bit, copie **inmage_ua_ 8.0.7.0 _rhel6-64_ga_03dec2018_release. tar. gz** para o servidor e extraia-o. Na pasta extraída, execute **/install**.
  9. Depois de atualizar o servidor de configuração, o servidor de processo e o servidor RX com os instaladores mencionados acima, as bibliotecas PHP e MySQL precisam ser atualizadas manualmente com as etapas mencionadas na seção 7,4 do [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Ativar replicação

1. Configure a replicação entre os sites VMware de origem e de destino.
2. Consulte os documentos a seguir para saber mais sobre a instalação, proteção e recuperação:

   * [Notas de versão](https://aka.ms/asr-scout-release-notes)
   * [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
   * [Guia do usuário](https://aka.ms/asr-scout-user-guide)
   * [Guia do usuário do RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)
   * [Atualizando bibliotecas MYSQL e PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Atualizações

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 atualização 7 
Atualização: 31 de dezembro de 2018 baixar a [atualização do Scout 7](https://aka.ms/asr-scout-update7).
O Scout Update 7 é um instalador completo que pode ser usado para a nova instalação, bem como para atualizar os agentes/MT existentes que estão nas atualizações anteriores (da atualização 1 para a atualização 6). Ele contém todas as correções da atualização 1 para a atualização 6, além das novas correções e aprimoramentos descritos abaixo.
 
#### <a name="new-features"></a>Novos recursos
* Conformidade com PCI
* Suporte a TLS v 1.2

#### <a name="bug-and-security-fixes"></a>Correções de bugs e de segurança
* Fixado O cluster do Windows/computadores autônomos têm configuração de IP incorreta após recuperação/DR-Drill.
* Fixado Às vezes, a operação de adição de disco falha para o cluster V2V.
* Corrigido: o assistente de vContinuum fica preso durante a fase de recuperação se o destino mestre for o Windows Server 2016
* Fixado Os problemas de segurança do MySQL são mitigados atualizando o MySQL para a versão 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Atualização manual para PHP e MySQL em CS, PS e RX
A plataforma de script PHP deve ser atualizada para a versão 7.2.10 no servidor de configuração, no servidor de processo e no servidor RX.
O sistema de gerenciamento de banco de dados MySQL deve ser atualizado para a versão 5.7.23 no servidor de configuração, servidor de processo e servidor RX.
Siga as etapas manuais fornecidas no guia de [instalação rápida](https://aka.ms/asr-scout-quick-install-guide) para atualizar as versões do PHP e do MySQL.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 atualização 6 
Atualização: 12 de outubro de 2017

Baixe a [atualização do Scout 6](https://aka.ms/asr-scout-update6).

O Scout Update 6 é uma atualização cumulativa. Ele contém todas as correções da atualização 1 para a atualização 5, além das novas correções e aprimoramentos descritos abaixo. 

#### <a name="new-platform-support"></a>Novo suporte de plataforma
* Foi adicionado suporte para o Windows Server 2016 de origem
* Foi adicionado suporte para os seguintes sistemas operacionais Linux:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6,9
    - Oracle Linux 5,11
    - Oracle Linux 6,8
* Foi adicionado suporte para o VMware Center 6,5

Instale as atualizações da seguinte maneira:

> [!NOTE]
>A versão de atualização de arquivo de todos os componentes do Scout pode não ser a mesma no arquivo. zip de atualização. A versão mais antiga indica que não há nenhuma alteração no componente desde a atualização anterior para essa atualização.

Baixe o arquivo. zip de [atualização](https://aka.ms/asr-scout-update6) . O arquivo contém os seguintes componentes: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA atualização 4 bits para RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_\<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Extraia os arquivos. zip.
  2. **Servidor RX**: Copie **rx_ 8.0.4.0 _ga_update_4_8725872_16sep16. tar. gz** para o servidor RX e extraia-o. Na pasta extraída, execute **/install**.
  3. **Servidor de configuração e servidor de processo**: Copie **cx_windows_ 8.0.6.0 _ga_update_6_13746667_18sep17. exe** para o servidor de configuração e o servidor de processo. Clique duas vezes para executá-lo.<br>
  4. **Servidor de destino mestre do Windows**: Para atualizar o agente unificado, copie **ua_windows_ 8.0.5.0 _ga_update_5_11525802_20apr17. exe** para o servidor. Clique duas vezes nele para executá-lo. A mesma atualização de agente unificado também se aplica ao servidor de origem. Se a origem não tiver sido atualizada para a atualização 4, você deverá atualizar o agente unificado.
  A atualização não precisa ser aplicada ao destino mestre preparado com **inmage_scout_vcontinuum_mt_ 8.0.1.0 _windows_ga_10oct2017_release. exe** , pois esse é um novo instalador de GA com todas as alterações mais recentes.
  5. **servidor vContinuum**:  Copie **vcon_windows_ 8.0.6.0 _ga_update_6_11525767_21sep17. exe** para o servidor.  Verifique se você fechou o assistente de vContinuum. Clique duas vezes no arquivo para executá-lo.
  A atualização não precisa ser aplicada ao destino mestre preparado com **inmage_scout_vcontinuum_mt_ 8.0.1.0 _windows_ga_10oct2017_release. exe** , pois esse é um novo instalador de GA com todas as alterações mais recentes.
  6. **Servidor de destino mestre do Linux**: Para atualizar o agente unificado, copie **ua_rhel6-64_ 8.0.4.0 _ga_update_4_9035261_26sep16. tar. gz** para o servidor de destino mestre e extraia-o. Na pasta extraída, execute **/install**.
  7. **Servidor de origem do Windows**: Para atualizar o agente unificado, copie **ua_windows_ 8.0.5.0 _ga_update_5_11525802_20apr17. exe** para o servidor de origem. Clique duas vezes no arquivo para executá-lo. 
  Você não precisa instalar o agente da atualização 5 no servidor de origem se ele já tiver sido atualizado para a atualização 4 ou se o agente de origem estiver instalado com o instalador base mais recente **inmage_ua_ 8.0.1.0 _windows_ga_28sep2017_release. exe**.
  8. **Servidor de origem do Linux**: Para atualizar o agente unificado, copie a versão correspondente do arquivo do agente unificado para o servidor Linux e extraia-a. Na pasta extraída, execute **/install**.  Exemplo: Para o servidor RHEL 6,7 64-bit, copie **ua_rhel6-64_ 8.0.4.0 _ga_update_4_9035261_26sep16. tar. gz** para o servidor e extraia-o. Na pasta extraída, execute **/install**.


> [!NOTE]
> * O instalador de UA (agente unificado) base para Windows foi atualizado para dar suporte ao Windows Server 2016. O novo instalador **inmage_ua_ 8.0.1.0 _windows_ga_28sep2017_release. exe** é empacotado com o pacote base scout GA (**inmage_scout_standard_ 8.0.1 ga-Oct17. zip**). O mesmo instalador será usado para todas as versões do Windows com suporte. 
> * Base Windows vContinuum & o instalador mestre de destino foi atualizado para dar suporte ao Windows Server 2016. O novo instalador **inmage_scout_vcontinuum_mt_ 8.0.1.0 _windows_ga_10oct2017_release. exe** é empacotado com o pacote base scout GA (**inmage_scout_standard_ 8.0.1 ga-Oct17. zip**). O mesmo instalador será usado para implantar o destino mestre do Windows 2016 e o destino mestre do Windows 2012R2.
> * O Windows Server 2016 no servidor físico não é suportado pelo ASR Scout. Ele dá suporte apenas à VM do VMware do Windows Server 2016. 
>

#### <a name="bug-fixes-and-enhancements"></a>Correções de bugs e aprimoramentos
- Falha na proteção de failback para que a VM do Linux com a lista de discos a serem replicados esteja vazia no final da configuração.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 atualização 5
O Scout Update 5 é uma atualização cumulativa. Ele contém todas as correções da atualização 1 para a atualização 4 e as novas correções descritas abaixo.
- As correções do Site Recovery Scout Update 4 para a atualização 5 são especificamente para os componentes de destino mestre e vContinuum.
- Se os servidores de origem, o destino mestre, a configuração, o processo e os servidores RX já estiverem executando a atualização 4, aplique-os somente no servidor de destino mestre. 

#### <a name="new-platform-support"></a>Novo suporte de plataforma
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* SLES 11 SP4 64 bit **inmage_ua_ 8.0.1.0 _sles11-SP4-64_ga_13apr2017_release. tar. gz** é empacotado com o pacote base scout GA (**inmage_scout_standard_ 8.0.1 ga. zip**). Baixe o pacote GA do portal, conforme descrito em criar um cofre.


#### <a name="bug-fixes-and-enhancements"></a>Correções de bugs e aprimoramentos

* Correções para maior confiabilidade de suporte de cluster do Windows:
    * Corrigido-alguns dos discos do cluster do MSCS P2V se tornam BRUTOs após a recuperação.
    * Corrigido-a recuperação de cluster do MSCS do P2V falha devido a uma incompatibilidade de ordem de disco.
    * Corrigido-a operação de cluster do MSCS para adicionar discos falha com um erro de incompatibilidade de tamanho de disco.
    * Corrigido-a verificação de preparação para o cluster do MSCS de origem com mapeamento de LUNs RDM falha na verificação de tamanho.
    * Fixo-a proteção de cluster de nó único falha devido a um problema de incompatibilidade de SCSI. 
    * Corrigido – a nova proteção do servidor de cluster do Windows P2V falhará se os discos de cluster de destino estiverem presentes. 
    
* Fixado Durante a proteção de failback, se o servidor de destino mestre selecionado não estiver no mesmo servidor ESXi que o computador de origem protegido (durante a proteção de encaminhamento), o vContinuum selecionará o servidor de destino mestre errado durante a recuperação de failback e a recuperação falha na operação.

> [!NOTE]
> * As correções de cluster P2V são aplicáveis somente a clusters do MSCS físicos que são protegidos recentemente com o Site Recovery Scout Update 5. Para instalar as correções de cluster em clusters do MSCS do P2V protegidos com atualizações mais antigas, siga as etapas de atualização mencionadas na seção 12 das [notas de versão do site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * se, no momento da nova proteção, o mesmo conjunto de discos estiver ativo em cada um dos nós de cluster como estavam quando protegidos inicialmente, a nova proteção de um cluster do MSCS físico poderá reutilizar os discos de destino existentes. Caso contrário, use as etapas manuais na seção 12 de [notas de versão do site Recovery Scout](https://aka.ms/asr-scout-release-notes)para mover os discos do lado de destino para o caminho de repositório de armazenamento correto, para reutilização durante a nova proteção. Se você proteger novamente o cluster do MSCS no modo FV sem seguir as etapas de atualização, ele criará um novo disco no servidor ESXi de destino. Você precisará excluir manualmente os discos antigos do repositório de armazenamento.
> * Quando um servidor de origem SLES11 ou SLES11 (com qualquer service pack) for reinicializado normalmente, marque manualmente os pares de replicação de disco **raiz** para nova sincronização. Não há nenhuma notificação na interface CX. Se você não marcar o disco raiz para ressincronização, poderá notar problemas de integridade de dados.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 atualização 4
O Scout Update 4 é uma atualização cumulativa. Ele inclui todas as correções da atualização 1 para a atualização 3 e as novas correções descritas abaixo.

#### <a name="new-platform-support"></a>Novo suporte de plataforma

* Foi adicionado suporte para vCenter/vSphere 6,0, 6,1 e 6,2
* Foi adicionado suporte para esses sistemas operacionais Linux:
  * Red Hat Enterprise Linux (RHEL) 7,0, 7,1 e 7,2
  * CentOS 7,0, 7,1 e 7,2
  * Red Hat Enterprise Linux (RHEL) 6,8
  * CentOS 6,8

> [!NOTE]
> RHEL/CentOS 7 64 bit **inmage_ua_ 8.0.1.0 _rhel7-64_ga_06oct2016_release. tar. gz** é empacotado com o pacote base scout GA **inmage_scout_standard_ 8.0.1 ga. zip**. Baixe o pacote do Scout GA no portal, conforme descrito em criar um cofre.

#### <a name="bug-fixes-and-enhancements"></a>Correções de bugs e aprimoramentos

* Melhor manipulação de desligamento para os seguintes sistemas operacionais Linux e clones, para evitar problemas de ressincronização indesejados:
    * Red Hat Enterprise Linux (RHEL) 6. x
    * Oracle Linux (OL) 6. x
* Para o Linux, todas as permissões de acesso à pasta no diretório de instalação do agente unificado agora são restritas somente ao usuário local.
* No Windows, uma correção para um problema de tempo limite que ocorreu durante a emissão de indicadores de consistência distribuída comuns, em aplicativos distribuídos altamente carregados, como SQL Server e clusters de ponto de compartilhamento.
* Uma correção relacionada ao log no instalador base do servidor de configuração.
* Um link de download para o VMware vCLI 6,0 foi adicionado ao instalador base de destino mestre do Windows.
* Logs e verificações adicionais foram adicionados, para alterações de configuração de rede durante a análise de failover e recuperação de desastre.
* Uma correção para um problema que fazia com que as informações de retenção não sejam relatadas ao servidor de configuração.  
* Para clusters físicos, uma correção para um problema que causou a falha no redimensionamento de volume no assistente de vContinuum, ao reduzir o volume de origem.
* Uma correção para um problema de proteção de cluster que falhou com o erro: "Falha ao localizar a assinatura de disco", quando o disco de cluster é um disco PRDM.
* Uma correção para uma falha do servidor de transporte cxps, causada por uma exceção fora do intervalo.
* As colunas nome do servidor e endereço IP agora são redimensionáveis na página **instalação por push** do assistente de vContinuum.
* Aprimoramentos da API RX:
  * Os cinco pontos de consistência comuns mais recentes disponíveis agora estão disponíveis (somente marcas Garantidas).
  * Os detalhes de capacidade e espaço livre são exibidos para todos os dispositivos protegidos.
  * O estado do driver Scout no servidor de origem está disponível.

> [!NOTE]
> * O pacote base **inmage_scout_standard_ 8.0.1 _ga. zip** tem:
>     * Um instalador base do servidor de configuração atualizado (**inmage_cx_ 8.0.1.0 _windows_ga_26feb2015_release. exe**)
>     * Um instalador base de destino mestre do Windows (**inmage_scout_vcontinuum_mt_ 8.0.1.0 _windows_ga_26feb2015_release. exe**).
>     * Para todas as novas instalações, use o novo servidor de configuração e os bits de GA de destino mestre do Windows.
> * A atualização 4 pode ser aplicada diretamente no 8.0.1 GA.
> * O servidor de configuração e as atualizações de RX não podem ser revertidas depois de serem aplicadas.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 atualização 3

Todas as atualizações de Site Recovery são cumulativas. A atualização 3 contém todas as correções da atualização 1 e da atualização 2. A atualização 3 pode ser aplicada diretamente no 8.0.1 GA. O servidor de configuração e as atualizações de RX não podem ser revertidas depois de serem aplicadas.

#### <a name="bug-fixes-and-enhancements"></a>Correções de bugs e aprimoramentos
A atualização 3 corrige os seguintes problemas:

* O servidor de configuração e o RX não são registrados no cofre quando estão atrás do proxy.
* O número de horas em que o RPO (objetivo de ponto de recuperação) não foi atingido não é atualizado no relatório de integridade.
* O servidor de configuração não está sincronizando com RX quando os detalhes de hardware do ESX ou detalhes de rede contêm qualquer caractere UTF-8.
* Os controladores de domínio do Windows Server 2008 R2 não são iniciados após a recuperação.
* A sincronização offline não está funcionando conforme o esperado.
* Após o failover da VM, a exclusão do par de replicação não progride no console do servidor de configuração por um longo tempo. Os usuários não podem concluir as operações de failback ou retomada.
* As operações gerais de instantâneo pelo trabalho de consistência foram otimizadas para ajudar a reduzir desconexões de aplicativos, como clientes SQL Server.
* O desempenho da ferramenta de consistência (VACP. exe) foi aprimorado. O uso de memória necessário para a criação de instantâneos no Windows foi reduzido.
* O serviço de instalação por push falha quando a senha tem mais de 16 caracteres.
* vContinuum não verifica e solicita novas credenciais do vCenter, quando as credenciais são modificadas.
* No Linux, o Gerenciador de cache de destino mestre (cachemgr) não está baixando arquivos do servidor de processo. Isso resulta na limitação do par de replicação.
* Quando a ordem de disco do MSCS (cluster de failover físico) não é a mesma em todos os nós, a replicação não é definida para alguns dos volumes de cluster. O cluster deve ser protegido novamente para aproveitar essa correção.  
* A funcionalidade SMTP não está funcionando conforme o esperado, após o RX ser atualizado do Scout 7,1 para o Scout 8.0.1.
* Mais estatísticas foram adicionadas ao log para a operação de reversão, para controlar o tempo necessário para concluí-la.
* Foi adicionado suporte para sistemas operacionais Linux no servidor de origem:
  * Red Hat Enterprise Linux (RHEL) 6 atualização 7
  * CentOS 6 atualização 7
* O servidor de configuração e os consoles RX agora mostram notificações para o par, que entra no modo Bitmap.
* As seguintes correções de segurança foram adicionadas em RX:
    * Bypass de autorização por meio de violação de parâmetro: Acesso restrito a usuários não aplicáveis.
    * Falsificação de solicitação entre sites: O conceito de token de página foi implementado e gera aleatoriamente para cada página. Isso significa que há apenas uma instância de entrada única para o mesmo usuário e a atualização de página não funciona. Em vez disso, ele redireciona para o painel.
    * Upload de arquivo mal-intencionado: Os arquivos são restritos a extensões específicas: z, AIFF, ASF, AVI, BMP, CSV, Doc, docx, FLA, FLV, GIF, gz, GZIP, JPEG, jpg, log, mid, MOV, mp3, MP4, MPC, MPEG, MPG, ODS, odt, PDF, png, ppt, pptx, PXD, QT, RAM, rar, RM, RMI, RMVB, RTF, SDC, sitd, SWF , sxc, sxw, tar, TGZ, TIF, TIFF, txt, vsd, WAV, WMA, WMV, xls, xlsx, XML e zip.
    * Script persistente entre sites: As validações de entrada foram adicionadas.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 atualização 2 (atualização 03Dec15)

As correções na atualização 2 incluem:

* **Servidor de configuração**: Problemas que impediram que o recurso de medição gratuita de 31 dias funcione conforme o esperado, quando o servidor de configuração foi registrado para Azure Site Recovery cofre.
* **Agente**unificado: Correção de um problema na atualização 1 que fez com que a atualização não fosse instalada no servidor de destino mestre, durante a atualização da versão 8,0 para 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 atualização 1
A atualização 1 inclui as seguintes correções de bugs e novos recursos:

* 31 dias de proteção gratuita por instância de servidor. Isso permite que você teste a funcionalidade ou configure uma prova de conceito.
* Todas as operações no servidor, incluindo failover e failback, são gratuitas durante os primeiros 31 dias. O tempo começa quando um servidor é protegido pela primeira vez com o Site Recovery Scout. Do dia 32 º, cada servidor protegido é cobrado com a taxa de instância padrão para proteção de Site Recovery para um site de Propriedade do cliente.
* A qualquer momento, o número de servidores protegidos atualmente sendo cobrados está disponível no **painel** no cofre.
* Foi adicionado suporte para a interface de linha de comando vSphere (vCLI) 5,5 atualização 2.
* Foi adicionado suporte para esses sistemas operacionais Linux no servidor de origem:
    * RHEL 6 Atualização 6
    * RHEL 5 atualização 11
    * CentOS 6 Atualização 6
    * CentOS 5 atualização 11
* Correções de bugs para resolver os seguintes problemas:
  * Falha no registro do cofre para o servidor de configuração ou servidor RX.
  * Os volumes de cluster não aparecem conforme o esperado quando as VMs clusterizadas são protegidas novamente à medida que eles são retomados.
  * O failback falha quando o servidor de destino mestre é hospedado em um servidor ESXi diferente das VMs de produção locais.
  * As permissões do arquivo de configuração são alteradas quando você atualiza para o 8.0.1. Essa alteração afeta a proteção e as operações.
  * O limite de ressincronização não é imposto conforme o esperado, causando um comportamento de replicação inconsistente.
  * As configurações de RPO não aparecem corretamente no console do servidor de configuração. O valor de dados não compactados mostra incorretamente o valor compactado.
  * A operação de remoção não exclui conforme esperado no assistente de vContinuum e a replicação não é excluída do console do servidor de configuração.
  * No assistente de vContinuum, o disco é desmarcado automaticamente quando você clica em **detalhes** na exibição de disco, durante a proteção de VMs do MSCS.
  * No cenário FV (Physical-to-virtual), os serviços HP necessários (como CIMnotify e CqMgHost) não são movidos para manual na recuperação de VM. Esse problema resulta em tempo de inicialização adicional.
  * A proteção da VM do Linux falha quando há mais de 26 discos no servidor de destino mestre.

