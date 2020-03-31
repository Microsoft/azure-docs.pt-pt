---
title: Recuperação de desastres de VMware VMs/servidores físicos para um site secundário com recuperação do site Azure
description: Saiba como configurar a recuperação de desastres de VMware VMs, ou servidores físicos Windows e Linux, para um site secundário com Recuperação do Site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256812"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Configurar recuperação após desastre de máquinas virtuais do VMware ou servidores físicos no local para um site secundário

O InMage Scout in [Azure Site Recovery](site-recovery-overview.md) fornece replicação em tempo real entre sites VMware no local. O InMage Scout está incluído nas subscrições do serviço azure site recovery.

## <a name="end-of-support-announcement"></a>Anúncio do fim do suporte

O cenário de recuperação do site Azure para replicação entre VMware no local ou datacenters físicos está a atingir o fim do suporte.

-   A partir de agosto de 2018, o cenário não pode ser configurado no cofre dos Serviços de Recuperação, e o software InMage Scout não pode ser descarregado do cofre. As missões existentes serão apoiadas. 
-   A partir de 31 de dezembro de 2020, o cenário não será apoiado.
- Os parceiros existentes podem embarcar novos clientes para o cenário até que o suporte termine.

Durante 2018 e 2019, serão lançadas duas atualizações: 

-   Atualização 7: Corrige problemas de configuração e conformidade da rede e fornece suporte TLS 1.2.
-   Atualização 8: Adiciona suporte aos sistemas operativos Linux RHEL/CentOS 7.3/7.4/7.5 e para SUSE 12

Após a Atualização 8, não serão lançadas mais atualizações. Haverá um suporte de fixação de fixação limitado para os sistemas operativos adicionados no Update 8, e correções de bugs com base no melhor esforço.

A Azure Site Recovery continua a inovar fornecendo aos clientes VMware e Hyper-V uma solução DRaaS perfeita e de melhor classe com o Azure como local de recuperação de desastres. A Microsoft recomenda que os clientes existentes da InMage /ASR Scout considerem usar o cenário VMware da Azure Site Recovery para o Azure para as suas necessidades de continuidade de negócio. O cenário VMware da Azure Site Recovery para Azure é uma solução DR de classe empresarial para aplicações VMware, que oferece RPO e RTO de minutos, suporte para replicação e recuperação de aplicações multi-VM, embarque sem emenda, monitorização abrangente, e vantagem significativa da TCO.

### <a name="scenario-migration"></a>Migração de cenário
Como alternativa, recomendamos a criação de uma recuperação de desastres para VMware VMware no local e máquinas físicas replicando-as para Azure. Proceda da seguinte forma:

1.  Reveja a comparação rápida abaixo. Antes de poder replicar as máquinas no local, precisa de verificar se cumprem [os requisitos](./vmware-physical-azure-support-matrix.md#replicated-machines) de replicação ao Azure. Se estiver a replicar VMs VMware, recomendamos que reveja [as diretrizes](./site-recovery-plan-capacity-vmware.md)de planeamento da capacidade e execute a [ferramenta de Planificador](./site-recovery-deployment-planner.md) de Implementação para requisitos de capacidade de identidade e verifique a conformidade.
2.  Depois de executar o Planificador de Implantação, pode configurar a replicação: o VMware VMware VMs, seguir estes tutoriais para [preparar o Azure,](./tutorial-prepare-azure.md) [preparar o seu ambiente VMware no local](./vmware-azure-tutorial-prepare-on-premises.md)e [configurar a recuperação de desastres.](./vmware-azure-tutorial-prepare-on-premises.md)
o Para máquinas físicas, siga este [tutorial.](./physical-azure-disaster-recovery.md)
3.  Depois de as máquinas se replicarem para o Azure, podes fazer um exercício de recuperação de [desastres](./site-recovery-test-failover-to-azure.md) para garantir que está tudo a funcionar como esperado.

### <a name="quick-comparison"></a>Comparação rápida

**Funcionalidade** | **Replicação para o Azure** |**Replicação entre centros de dados VMware**
--|--|--
**Componentes necessários** |Serviço de mobilidade em máquinas replicadas. Servidor de configuração no local, servidor de processos, servidor de alvo principal. Servidor de processo temporário em Azure para falha.|Serviço de mobilidade, servidor de processos, servidor de configuração e alvo principal
**Configuração e orquestração** |Cofre de Serviços de Recuperação no portal Azure | Usando vContinuum 
**Replicada** |Disco (Windows e Linux) |Janelas de volume<br> Disco-Linux
**Cluster de disco partilhado** |Não suportado|Suportado
**Limites de churn de dados (média)** |10 dados MB/s por disco<br> Dados de 25MB/s por VM<br> [Mais informações](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > dados de 10 MB/s por disco  <br> > dados de 25 MB/s por VM
**Monitorização** |Do portal Azure|A partir de CX (Servidor de Configuração)
**Matriz de Suporte** | [Clique aqui para mais detalhes](./vmware-physical-azure-support-matrix.md)|[Descarregue a matriz compatível com a ASR Scout](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial:

- [Reveja](vmware-physical-secondary-support-matrix.md) os requisitos de suporte para todos os componentes.
- Certifique-se de que as máquinas que pretende replicar cumprem o [suporte da máquina replicado](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Descarregue e instale atualizações de componentes

 Reveja e instale as [últimas atualizações.](#updates) As atualizações devem ser instaladas nos servidores na seguinte ordem:

1. Servidor RX (se aplicável)
2. Servidores de configuração
3. Servidores de processos
4. Servidores Master Target
5. servidores vContinuum
6. Servidor fonte (tanto windows como Linux Servers)

Instale as atualizações da seguinte forma:

> [!NOTE]
>A versão de atualização de ficheiros de todos os componentes do Scout pode não ser a mesma no ficheiro .zip da atualização. A versão mais antiga indica que não há alteração no componente desde a atualização anterior desta atualização.

Descarregue o ficheiro de [atualização](https://aka.ms/asr-scout-update7) .zip e os ficheiros de configuração de [atualização MySQL e PHP.](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) O ficheiro atualização .zip contém todos os binários base e binários de atualização cumulativo dos seguintes componentes: 
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
  1. Extraios ficheiros .zip.
  2. **Servidor RX**: Copie **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** para o servidor RX e extrai-o. Na pasta extraída, executar **/Instalar**.
  3. **Servidor de configuração e servidor de processos**: **Copie CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** para o servidor de configuração e servidor de processo. Clique duas vezes para executá-lo.<br>
  4. **Servidor Do Windows Master Target**: Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** para o servidor. Clique duas vezes para executá-lo. O mesmo ficheiro também pode ser utilizado para instalação fresca. A mesma atualização de agente unificada também é aplicável para o servidor de origem.
  A atualização não precisa de ser aplicada no alvo Master preparado com **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe,** uma vez que se trata de um novo instalador de GA com todas as alterações mais recentes.
  5. **vContinuum servidor**: Copiar **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** para o servidor.  Certifique-se de que fechou o assistente vContinuum. Clique duas vezes no ficheiro para o executar.
  6. **Servidor de alvo principal do Linux**: Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** para o servidor Linux Master Target e extrai-lo. Na pasta extraída, executar **/Instalar**.
  7. **Servidor**de origem do Windows : Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** para o servidor de origem. Clique duas vezes no ficheiro para o executar. 
  8. **Servidor de origem Linux**: Para atualizar o agente unificado, copie a versão correspondente do ficheiro do agente unificado para o servidor Linux e extrai-o. Na pasta extraída, executar **/Instalar**.  Exemplo: Para o servidor RHEL 6.7 64 bits, copie **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** para o servidor e extrai-o. Na pasta extraída, executar **/Instalar**.
  9. Depois de atualizar o Servidor de Configuração, o Servidor de Processos e o servidor RX com os instaladores acima mencionados, as bibliotecas PHP e MySQL precisam de ser atualizadas manualmente com passos mencionados na secção 7.4 do guia de [instalação rápida](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Ativar a replicação

1. Instale a replicação entre os sites de origem e de destino VMware.
2. Consulte os seguintes documentos para saber mais sobre instalação, proteção e recuperação:

   * [Notas de versão](https://aka.ms/asr-scout-release-notes)
   * [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
   * [Guia do utilizador](https://aka.ms/asr-scout-user-guide)
   * [Guia de utilizador RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)
   * [Atualizar bibliotecas MYSQL e PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Atualizações

### <a name="site-recovery-scout-801-update-7"></a>Scout de recuperação do site 8.0.1 Atualização 7 
Atualizado: 31 de dezembro de 2018 Download [Scout update 7](https://aka.ms/asr-scout-update7).
Scout Update 7 é um instalador completo que pode ser usado para instalação fresca, bem como para atualizar agentes/MT existentes que estão em atualizações anteriores (de Update 1 a Update 6). Contém todas as correções do Update 1 para o Update 6 mais as novas correções e melhorias descritas abaixo.
 
#### <a name="new-features"></a>Novas funcionalidades
* Conformidade com o PCI
* Suporte TLS v1.2

#### <a name="bug-and-security-fixes"></a>Correções de Bugs e Segurança
* Fixo: O Windows Cluster/Máquinas Autónomas têm uma configuração IP incorreta após a recuperação/broca DR.
* Fixo: Às vezes adicione falhas na operação do disco para o cluster V2V.
* Fixo: vContinuum Wizard fica preso durante a fase de recuperação se o Alvo Principal for Windows Server 2016
* Fixo: Problemas de segurança mySQL são atenuados através da atualização do MySQL para a versão 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Upgrade manual para PHP e MySQL em CS,PS e RX
A plataforma de scripts PHP deve ser atualizada para a versão 7.2.10 no Servidor de Configuração, Servidor de Processoe e Servidor RX.
O sistema de gestão da base de dados MySQL deve ser atualizado para a versão 5.7.23 no Servidor de Configuração, Servidor de Processos e Servidor RX.
Siga os passos manuais dados no guia de [instalação Quick](https://aka.ms/asr-scout-quick-install-guide) para atualizar as versões PHP e MySQL.

### <a name="site-recovery-scout-801-update-6"></a>Scout de recuperação do site 8.0.1 Atualização 6 
Atualizado: 12 de outubro de 2017

Baixar [a tualização de scout 6](https://aka.ms/asr-scout-update6).

Scout Update 6 é uma atualização cumulativa. Contém todas as correções do Update 1 para o Update 5 mais as novas correções e melhorias descritas abaixo. 

#### <a name="new-platform-support"></a>Suporte de nova plataforma
* Suporte foi adicionado para Source Windows Server 2016
* Foi adicionado suporte para seguir os sistemas operativos Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - Centos 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Foi adicionado suporte para VMware Center 6.5

Instale as atualizações da seguinte forma:

> [!NOTE]
>A versão de atualização de ficheiros de todos os componentes do Scout pode não ser a mesma no ficheiro .zip da atualização. A versão mais antiga indica que não há alteração no componente desde a atualização anterior desta atualização.

Descarregue o ficheiro [.zip.](https://aka.ms/asr-scout-update6) O ficheiro contém os seguintes componentes: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA atualiza4 bits para RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_\<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Extraios ficheiros .zip.
  2. **Servidor RX**: Copie **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** para o servidor RX e extrai-lo. Na pasta extraída, executar **/Instalar**.
  3. **Servidor de configuração e servidor de processos**: Copie **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** para o servidor de configuração e servidor de processo. Clique duas vezes para executá-lo.<br>
  4. **Servidor do Windows Master Target**: Para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor. Clique duas vezes para executá-lo. A mesma atualização de agente unificada também é aplicável para o servidor de origem. Se a fonte não tiver sido atualizada para o Update 4, deverá atualizar o agente unificado.
  A atualização não precisa de ser aplicada no alvo Master preparado com **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe,** uma vez que se trata de um novo instalador de GA com todas as alterações mais recentes.
  5. **vContinuum servidor**: Copiar **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** para o servidor.  Certifique-se de que fechou o assistente vContinuum. Clique duas vezes no ficheiro para o executar.
  A atualização não precisa de ser aplicada no Alvo Principal preparado com **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe,** uma vez que se trata de um novo instalador de GA com todas as alterações mais recentes.
  6. Servidor de **alvo principal do Linux**: Para atualizar o agente unificado, copie **UA_RHEL6-64_8.0.4.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor alvo principal e extraí-lo. Na pasta extraída, executar **/Instalar**.
  7. **Servidor**de origem do Windows : Para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor de origem. Clique duas vezes no ficheiro para o executar. 
  Não é necessário instalar o agente Update 5 no servidor de origem se já foi atualizado para o Update 4 ou se o agente de origem estiver instalado com o mais recente instalador de base **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Servidor de origem Linux**: Para atualizar o agente unificado, copie a versão correspondente do ficheiro do agente unificado para o servidor Linux e extrai-o. Na pasta extraída, executar **/Instalar**.  Exemplo: Para o servidor RHEL 6.7 64 bits, copie **UA_RHEL6-64_8.0.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor e extrai-o. Na pasta extraída, executar **/Instalar**.


> [!NOTE]
> * O instalador de Agente Unificado base (UA) para windows foi atualizado para suportar o Windows Server 2016. O novo instalador **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** é embalado com o pacote base Scout GA **(InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O mesmo instalador será utilizado para toda a versão suportada do Windows. 
> * O central do Windows vContinuum & Master Target foi atualizado para suportar o Windows Server 2016. O novo instalador **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** é embalado com o pacote base Scout GA **(InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O mesmo instalador será utilizado para implementar o Windows 2016 Master Target e o Windows 2012R2 Master Target.
> * O servidor windows 2016 no servidor físico não é suportado pelo Scout ASR. Suporta apenas o VMware VMdo Do Windows Server 2016. 
>

#### <a name="bug-fixes-and-enhancements"></a>Correções e melhorias de bugs
- A proteção de failback falha para o VM Linux com a lista de discos a replicar está vazia no final do config.

### <a name="site-recovery-scout-801-update-5"></a>Scout de recuperação do site 8.0.1 Atualização 5
Scout Update 5 é uma atualização cumulativa. Contém todas as correções do Update 1 para o Update 4, e as novas correções descritas abaixo.
- As correções da Atualização do Scout de Recuperação do Local 4 para a Atualização 5 são especificamente para o alvo principal e componentes vContinuum.
- Se os servidores de origem, o alvo principal, a configuração, o processo e os servidores RX já estiverem a executar o Update 4, então aplique-o apenas no servidor de alvo principal. 

#### <a name="new-platform-support"></a>Suporte de nova plataforma
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* SLES 11 SP4 64 bit **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** é embalado com o pacote base Scout GA **(InMage_Scout_Standard_8.0.1 GA.zip**). Descarregue o pacote GA do portal, como descrito na criação de um cofre.


#### <a name="bug-fixes-and-enhancements"></a>Correções e melhorias de bugs

* Correções para maior fiabilidade do suporte do cluster Windows:
    * Fixo- Alguns dos discos de cluster P2V MSCS tornam-se RAW após a recuperação.
    * Falha a recuperação do cluster MSCS De P2V, devido a uma incompatibilidade de ordem do disco.
    * Fixo- A operação do cluster MSCS para adicionar discos falha com um erro de desajuste do tamanho do disco.
    * Fixo - A verificação de prontidão para o cluster MSCS de origem com mapeamento RDM LUNs falha na verificação de tamanho.
    * A proteção fixa e única do cluster do nó falha devido a um problema de desajuste scsi. 
    * Falha a proteção fixa do servidor de cluster Do Windows P2V se estiverem presentes os discos de cluster do alvo. 
    
* Fixo: Durante a proteção de reinicializador, se o servidor-alvo principal selecionado não estiver no mesmo servidor ESXi que a máquina de origem protegida (durante a proteção para a frente), então o vContinuum apanha o servidor-alvo principal errado durante a recuperação do failback e a recuperação operação falha.

> [!NOTE]
> * As correções do cluster P2V aplicam-se apenas aos clusters físicos de MSCS que estão recentemente protegidos com a Atualização 5 do Scout de Recuperação do Local. Para instalar as correções do cluster em clusters P2V MSCS protegidos com atualizações mais antigas, siga as etapas de atualização mencionadas na secção 12 das Notas de Lançamento do Escuteiro de [Recuperação](https://aka.ms/asr-scout-release-notes)do Local .
> * se no momento da reprotecção, o mesmo conjunto de discos estiver ativo em cada um dos nós do cluster como estavam quando inicialmente protegidos, então a reprotecção de um cluster físico de MSCS só pode reutilizar os discos-alvo existentes. Caso contrário, utilize os passos manuais na secção 12 das Notas de Lançamento do Escuteiro de Recuperação do [Local,](https://aka.ms/asr-scout-release-notes)para mover os discos laterais-alvo para o caminho correto da loja de dados, para reutilizar durante a reproteção. Se reproteger o cluster MSCS no modo P2V sem seguir os passos de atualização, cria um novo disco no servidor ESXi alvo. Terá de eliminar manualmente os discos antigos da loja de dados.
> * Quando um servidor SLES11 ou SLES11 (com qualquer pacote de serviço) for reiniciado graciosamente, marque manualmente os pares de replicação do disco **radicular** para a resincronização. Não há notificação na interface CX.Se não marcar o disco de raiz para ressincronização, poderá notar problemas de integridade de dados.


### <a name="azure-site-recovery-scout-801-update-4"></a>Scout de recuperação do site Azure 8.0.1 Atualização 4
Scout Update 4 é uma atualização cumulativa. Inclui todas as correções do Update 1 para o Update 3, e as novas correções descritas abaixo.

#### <a name="new-platform-support"></a>Suporte de nova plataforma

* Foi adicionado suporte para vCenter/vSphere 6.0, 6.1 e 6.2
* Foi adicionado apoio para estes sistemas operativos Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 e 7.2
  * CentOS 7.0, 7.1 e 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * Centos 6.8

> [!NOTE]
> RHEL/CentOS 7 64 **bitInMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** é embalado com o pacote base Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Descarregue o pacote Scout GA do portal como descrito em criar um cofre.

#### <a name="bug-fixes-and-enhancements"></a>Correções e melhorias de bugs

* Melhor manuseamento de encerramento para os seguintes sistemas operativos Linux e clones, para evitar problemas de ressincronização indesejados:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Para o Linux, todas as permissões de acesso a pastas no diretório de instalação de agentes unificados estão agora restritas apenas ao utilizador local.
* No Windows, uma correção para um problema de tempo que ocorreu ao emitir marcadores comuns de consistência distribuída, em aplicações distribuídas fortemente carregadas, como clusters SQL Server e Share Point.
* Uma correção relacionada com o registo no instalador de base do servidor de configuração.
* Foi adicionado ao instalador de base de alvo do Windows um link para VMware vCLI 6.0.
* Foram adicionados controlos e registos adicionais para alterações na configuração da rede durante exercícios de failover e recuperação de desastres.
* Uma correção para um problema que fez com que as informações de retenção não fossem reportadas ao servidor de configuração.  
* Para os clusters físicos, uma correção para um problema que fez com que o volume redimensionado falhasse no assistente vContinuum, ao diminuir o volume de origem.
* Uma correção para um problema de proteção de cluster que falhou com o erro: "Não consegui encontrar a assinatura do disco", quando o disco de cluster é um disco PRDM.
* Uma correção para uma falha no servidor de transporte de cxps, causada por uma exceção fora de alcance.
* O nome do servidor e as colunas de endereçoIP são agora resizáveis na página **de instalação push** do assistente vContinuum.
* Melhorias da API RX:
  * Os cinco últimos pontos de consistência comum disponíveis já disponíveis (apenas etiquetas garantidas).
  * A capacidade e os detalhes do espaço gratuito são apresentados para todos os dispositivos protegidos.
  * O estado do condutor de escuteiros no servidor de origem está disponível.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** base pacote tem:
>     * Um instalador de base de servidor de configuração atualizado **(InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Um instalador de base de alvo principal do Windows **(InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * Para todas as novas instalações, utilize o novo servidor de configuração e os bits GA do alvo principal do Windows.
> * A atualização 4 pode ser aplicada diretamente em 8.0.1 GA.
> * O servidor de configuração e as atualizações RX não podem ser relançados depois de aplicados.


### <a name="azure-site-recovery-scout-801-update-3"></a>Scout de recuperação do site Azure 8.0.1 Atualização 3

Todas as atualizações de Recuperação do Site são cumulativas. A atualização 3 contém todas as correções da Atualização 1 e atualização 2. A atualização 3 pode ser aplicada diretamente em 8.0.1 GA. O servidor de configuração e as atualizações RX não podem ser relançados depois de aplicados.

#### <a name="bug-fixes-and-enhancements"></a>Correções e melhorias de bugs
A atualização 3 corrige os seguintes problemas:

* O servidor de configuração e o RX não estão registados no cofre quando estão atrás do representante.
* O número de horas em que o objetivo do ponto de recuperação (RPO) não foi atingido não é atualizado no relatório de saúde.
* O servidor de configuração não está a sincronizar com o RX quando os detalhes do hardware ESX, ou detalhes da rede, contêm quaisquer caracteres UTF-8.
* Os controladores de domínio R2 do Windows Server 2008 não começam após a recuperação.
* A sincronização offline não está a funcionar como esperado.
* Após a falha do VM, a eliminação do par de replicação não progride na consola do servidor de configuração por muito tempo. Os utilizadores não podem completar as operações de failback ou retomar as operações.
* Foram otimizadas as operações de instantâneo global pelo trabalho de consistência, para ajudar a reduzir as desconexões de aplicações, como os clientes do SQL Server.
* O desempenho da ferramenta de consistência (VACP.exe) foi melhorado. O uso da memória necessário para a criação de instantâneos no Windows foi reduzido.
* O serviço de instalação push falha quando a palavra-passe é maior que 16 caracteres.
* vContinuum não verifica e solicita novas credenciais vCenter, quando as credenciais são modificadas.
* No Linux, o diretor de cache-alvo principal (cachemgr) não está a descarregar ficheiros do servidor de processos. Isto resulta em estrangulamento do par de replicação.
* Quando a ordem do disco de failover físico (MSCS) não é a mesma em todos os nós, a replicação não está definida para alguns dos volumes de cluster. O cluster deve ser reprotegido para tirar partido desta correção.  
* A funcionalidade SMTP não está a funcionar como esperado, depois de o RX ser atualizado de Scout 7.1 para Scout 8.0.1.
* Foram acrescentadas mais estatísticas no registo para a operação de reversão, para acompanhar o tempo que demorou a terminá-la.
* Foi adicionado suporte para sistemas operativos Linux no servidor de origem:
  * Red Hat Enterprise Linux (RHEL) 6 atualização 7
  * Atualização CentOS 6 7
* O servidor de configuração e as consolas RX mostram agora notificações para o par, que entra em modo bitmap.
* As seguintes correções de segurança foram adicionadas no RX:
    * Bypass de autorização através de adulteração de parâmetros: Acesso restrito a utilizadores não aplicáveis.
    * Falsificação de pedido transversal: O conceito de token de página foi implementado, e gera aleatoriamente para cada página. Isto significa que há apenas um único sinal de inscrição para o mesmo utilizador, e a atualização da página não funciona. Em vez disso, redireciona para o tablier.
    * Upload de ficheiro malicioso: Os ficheiros estão restritos a extensões específicas: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmif, rmiv, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmiv, rmiv, rmif, rmiv, rmif, rmif, rmif, rmif, rmif, rmiv, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmif, rmi , sdc, sitd, swf, sxc, sxw, alcatrão, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml, e zip.
    * Escrita inter-site persistente: Validações de entrada foram adicionadas.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Scout de recuperação do site Azure 8.0.1 Atualização 2 (Atualização 03Dec15)

As correções na Atualização 2 incluem:

* **Servidor de configuração**: Problemas que impediram que a função de medição gratuita de 31 dias funcionasse como esperado, quando o servidor de configuração estava registado no cofre de recuperação do site Azure.
* **Agente unificado**: Corrija para um problema na Atualização 1 que resultou na não instalação da atualização no servidor alvo principal, durante a atualização da versão 8.0 para 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Scout de recuperação do site Azure 8.0.1 Atualização 1
A atualização 1 inclui as seguintes correções de bugs e novas funcionalidades:

* 31 dias de proteção gratuita por instância do servidor. Isto permite-lhe testar a funcionalidade ou configurar uma prova de conceito.
* Todas as operações no servidor, incluindo o failover e o failback, são gratuitas nos primeiros 31 dias. O tempo começa quando um servidor é protegido pela primeira vez com o Scout de Recuperação do Local. A partir do 32º dia, cada servidor protegido é cobrado à taxa padrão de proteção de recuperação do site para um site propriedade do cliente.
* A qualquer momento, o número de servidores protegidos atualmente a ser carregados está disponível no **Dashboard** no cofre.
* O suporte foi adicionado para a interface vSphere Command-Line (vCLI) 5.5 Update 2.
* Foi adicionado suporte para estes sistemas operativos Linux no servidor de origem:
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * Centos 6 Update 6
    * Centos 5 Update 11
* Correções de bugs para resolver os seguintes problemas:
  * O registo do cofre falha no servidor de configuração ou no servidor RX.
  * Os volumes de cluster não aparecem como esperado quando os VMs agrupados são reprotegidos à medida que retomam.
  * O failback falha quando o servidor alvo principal é hospedado num servidor ESXi diferente dos VMs de produção no local.
  * As permissões de ficheirode configuração são alteradas quando atualiza para 8.0.1. Esta alteração afeta a proteção e as operações.
  * O limiar de ressincronização não é aplicado como esperado, causando comportamento de replicação inconsistente.
  * As definições de RPO não aparecem corretamente na consola do servidor de configuração. O valor dos dados não comprimidos mostra incorretamente o valor comprimido.
  * A operação Remover não é eliminada como esperado no assistente vContinuum, e a replicação não é eliminada da consola do servidor de configuração.
  * No assistente vContinuum, o disco é automaticamente desseleccionado quando clica em **Detalhes** na vista do disco, durante a proteção dos VMs MSCS.
  * No cenário físico-a-virtual (P2V), os serviços de HP necessários (como CIMnotify e CqMgHost) não são movidos para manual na recuperação de VM. Esta emissão resulta em tempo adicional de arranque.
  * A proteção VM linux falha quando existem mais de 26 discos no servidor alvo principal.

