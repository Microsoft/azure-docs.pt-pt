---
title: Configurar a recuperação após desastre de VMs de VMware ou servidores físicos para um site secundário com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como configurar a recuperação após desastre de VMs de VMware, ou Windows e Linux servidores físicos para um site secundário com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 29ced587022bc15e82c756cb5f1b80554cfcceb2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009419"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Configurar a recuperação após desastre de máquinas de virtuais de VMware no local ou servidores físicos para um site secundário

InMage Scout na [do Azure Site Recovery](site-recovery-overview.md) fornece replicação em tempo real entre locais sites de VMware. InMage Scout está incluído nas assinaturas de serviço do Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Anúncio de fim do suporte

O cenário de recuperação de sites do Azure para a replicação entre datacenters físicos ou o VMware no local está a atingir o fim do suporte.

-   A partir de Agosto de 2018, o cenário não pode ser configurado no cofre dos serviços de recuperação e, o software de InMage Scout não pode ser transferido a partir do cofre. As implementações existentes serão suportadas. 
-   De 2020 de 31 de Dezembro, o cenário de não ser suportado.
- Parceiros existentes podem integrar novos clientes para o cenário até as extremidades de suporte.

Durante a 2018 e 2019, serão lançadas duas atualizações: 

-   Atualização 7: Corrige problemas de configuração e de conformidade de rede e fornece suporte de TLS 1.2.
-   Atualização 8: Adiciona suporte para Linux sistemas operativos RHEL/CentOS 7.3/7.4/7.5 e para a 12 de SUSE

Após a atualização 8, não são necessárias mais atualizações serão lançadas. Haverá suporte limitado de correção para os sistemas de operativos adicionados em 8 de atualização e correções de erros com base na melhor esforço.

O Azure Site Recovery continua a inovar ao fornecer uma solução de DRaaS totalmente integrada e melhor na classe de clientes do VMware e Hyper-V com o Azure como um site de recuperação após desastre. A Microsoft recomenda que existentes InMage / clientes de ASR Scout considere a utilização de VMware do Azure Site Recovery para o cenário do Azure para necessidades de continuidade do negócio. VMware do Azure Site Recovery para o cenário do Azure é uma solução de DR de nível empresarial para aplicações de VMware, que oferece RPO e RTO de minutos, suportam para a replicação de aplicativo de várias VMS e recuperação, a integração totalmente integrada, monitoramento abrangente, e vantagem do TCO significativa.

### <a name="scenario-migration"></a>Migração do cenário
Como alternativa, recomendamos a configuração da recuperação após desastre para VMs de VMware no local e máquinas físicas ao replicá-los para o Azure. Fazer isso da seguinte forma:

1.  Reveja a comparação rápida abaixo. Antes, pode replicar máquinas no local, terá de verificação que cumprem [requisitos](./vmware-physical-azure-support-matrix.md#replicated-machines) para replicação no Azure. Se estiver a replicar VMs de VMware, recomendamos que reveja [diretrizes de planeamento de capacidade](./site-recovery-plan-capacity-vmware.md)e execute o [ferramenta Planeador de implementações](./site-recovery-deployment-planner.md) para os requisitos de capacidade de identidade e verificar a conformidade.
2.  Depois de executar o planeador de implementação, pode configurar a replicação: o para VMs de VMware, siga estes tutoriais para [preparar o Azure](./tutorial-prepare-azure.md), [preparar seu ambiente do VMware no local](./vmware-azure-tutorial-prepare-on-premises.md), e [configurar recuperação após desastre](./vmware-azure-tutorial-prepare-on-premises.md).
s para máquinas físicas, siga este [tutorial](./physical-azure-disaster-recovery.md).
3.  Depois das máquinas estão a replicar para o Azure, pode executar uma [teste de recuperação após desastre](./site-recovery-test-failover-to-azure.md) para se certificar de que está tudo a funcionar conforme esperado.

### <a name="quick-comparison"></a>Comparação rápida

**Funcionalidade** | **Replicação para o Azure** |**Replicação entre os datacenters de VMware**
--|--|--
**Componentes necessários** |Serviço de mobilidade nas máquinas replicadas. Local no servidor de configuração, o servidor de processos, o servidor de destino mestre. Servidor de processo temporário no Azure para reativação pós-falha.|Serviço de mobilidade, servidor de processos, o servidor de configuração e o destino principal
**Configuração e orquestração** |O Cofre dos serviços de recuperação no portal do Azure | Usando vContinuum 
**Replicados** |Disco (Windows e Linux) |Volume-Windows<br> Disk-Linux
**Cluster de disco partilhado** |Não suportado|Suportadas
**Alterações a dados limites (média)** |10 MB/s dados por disco<br> Dados de 25MB/s por VM<br> [Saiba mais](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | Dados > 10 MB/s por disco  <br> Dados > 25 MB/s por VM
**Monitorização** |No portal do Azure|No c++ /CX (servidor de configuração)
**Matriz de suporte** | [Clique aqui para obter detalhes](./vmware-physical-azure-support-matrix.md)|[Transferir a matriz de ASR Scout compatível](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial:

- [Revisão](vmware-physical-secondary-support-matrix.md) os requisitos de suporte para todos os componentes.
- Certifique-se de que estão em conformidade com as máquinas que pretende replicar [replicados suporte máquina](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Transferir e instalar atualizações de componentes

 Reveja e instale a versão mais recente [atualizações](#updates). Atualizações devem ser instaladas em servidores na seguinte ordem:

1. Servidor RX (se aplicável)
2. Servidores de configuração
3. Servidores de processos
4. Servidores de destino principal
5. servidores de vContinuum
6. Servidor de origem (Windows e servidores Linux)

Instale as atualizações da seguinte forma:

> [!NOTE]
>Versão de atualização de ficheiro dos todos os componentes do Scout pode não ser o mesmo para o ficheiro. zip de atualização. A versão mais antiga indicam que não há nenhuma alteração no componente desde a atualização anterior para esta atualização.

Transfira o [atualizar](https://aka.ms/asr-scout-update7) ficheiro. zip e a [MySQL e PHP atualizam](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) ficheiros de configuração. O ficheiro. zip de atualização contém todas as a base de binários e os binários de atualização cumulativos dos seguintes componentes: 
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
  1. Extraia os ficheiros. zip.
  2. **Servidor de RX**: Cópia **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** para o servidor de RX e extraia-o. A pasta extraída, execute **/Install**.
  3. **Servidor de configuração e o servidor de processos**: Cópia **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** para o servidor de configuração e o servidor de processos. Faça duplo clique para executá-lo.<br>
  4. **Servidor de destino principal de Windows**: Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** para o servidor. Clique duas vezes nele para executá-lo. O mesmo ficheiro também pode ser utilizado para instalação de raiz. A mesma atualização de agente unified também é aplicável para o servidor de origem.
  A atualização não tem de aplicar no mestre de destino preparado com o **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** como este é o novo instalador de disponibilidade geral em todas as alterações mais recentes.
  5. **servidor vContinuum**:  Cópia **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** para o servidor.  Certifique-se de que tenha fechado o Assistente do vContinuum. Faça duplo clique no ficheiro para executá-lo.
  6. **Servidor de destino principal do Linux**: Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** para o servidor de destino principal do Linux e extraia-o. A pasta extraída, execute **/Install**.
  7. **Servidor de origem do Windows**: Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** ao servidor de origem. Faça duplo clique no ficheiro para executá-lo. 
  8. **Servidor de origem do Linux**: Para atualizar o agente unificado, copie a versão correspondente do arquivo agente unificado para o servidor Linux e extraí-lo. A pasta extraída, execute **/Install**.  Exemplo: Para RHEL 6.7 server de 64 bits, copie **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** para o servidor e extraia-o. A pasta extraída, execute **/Install**.
  9. Depois de atualizar o servidor de configuração, o servidor de processos e o servidor RX com os programas de instalação acima mencionados, as bibliotecas PHP e MySQL precisa ser atualizado manualmente com os passos mencionados na secção 7.4 a [guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Ativar a replicação

1. Configurar a replicação entre a origem e sites de VMware de destino.
2. Consulte os seguintes documentos para saber mais sobre a instalação, proteção e recuperação:

   * [Notas de versão](https://aka.ms/asr-scout-release-notes)
   * [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
   * [Guia do utilizador](https://aka.ms/asr-scout-user-guide)
   * [Guia do usuário RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)
   * [Atualizar bibliotecas do MYSQL e PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Atualizações

### <a name="site-recovery-scout-801-update-7"></a>Atualização do Scout 8.0.1 de recuperação de sites 7 
Atualizado: Transferir a partir de 31 de Dezembro de 2018 [atualização do Scout 7](https://aka.ms/asr-scout-update7).
Atualização do Scout 7 é um instalador completo que pode ser utilizado para a instalação de raiz, bem como para atualizar existente agentes/MT que são as atualizações anteriores (a partir de 1 de atualização para atualização 6). Ele contém todas as correções de atualização 1 para atualização 6 plus as novas correções e aprimoramentos de descrito abaixo.
 
#### <a name="new-features"></a>Novos recursos
* Conformidade de PCI
* Suporte a TLS versão 1.2

#### <a name="bug-and-security-fixes"></a>Bugs e correções de segurança
* Corrigido: Máquinas de Cluster/autónomas do Windows tem uma configuração incorreta do IP após a recuperação/exploração de DR.
* Corrigido: Por vezes, não consegue adicionar operação de disco para o cluster de V2V.
* Corrigido: Assistente do vContinuum fica preso durante a fase de recuperação se o destino principal é o Windows Server 2016
* Corrigido: Problemas de segurança do MySQL são atenuados ao atualizar o MySQL para a versão 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Atualização manual para PHP e MySQL no CS, PS e RX
A plataforma de script PHP deve ser atualizada para a versão 7.2.10 no servidor de configuração, o servidor de processos e o servidor RX.
O sistema de gestão de base de dados MySQL deve ser atualizado para a versão 5.7.23 no servidor de configuração, o servidor de processos e o servidor RX.
Siga os passos manuais fornecidos na [guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide) para atualizar as versões do PHP e MySQL.

### <a name="site-recovery-scout-801-update-6"></a>Atualização do Scout 8.0.1 de recuperação de sites 6 
Atualizado: 12 de Outubro de 2017

Baixe [atualização do Scout 6](https://aka.ms/asr-scout-update6).

A atualização do Scout 6 é uma atualização cumulativa. Ele contém todas as correções de atualização 1 para Update 5 e as novas correções e aprimoramentos de descrito abaixo. 

#### <a name="new-platform-support"></a>Novo suporte de plataforma
* Foi adicionado suporte para a origem Windows Server 2016
* Foi adicionado suporte para os seguintes sistemas operativos Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Foi adicionado suporte para VMware Center 6.5

Instale as atualizações da seguinte forma:

> [!NOTE]
>Versão de atualização de ficheiro dos todos os componentes do Scout pode não ser o mesmo para o ficheiro. zip de atualização. A versão mais antiga indicam que não há nenhuma alteração no componente desde a atualização anterior para esta atualização.

Transfira o [atualizar](https://aka.ms/asr-scout-update6) ficheiro. zip. O ficheiro contém os seguintes componentes: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- Bits de update4 UA para RHEL5, OL5, OL6, SUSE 10, 11 de SUSE: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Extraia os ficheiros. zip.
  2. **Servidor de RX**: Cópia **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** para o servidor de RX e extraia-o. A pasta extraída, execute **/Install**.
  3. **Servidor de configuração e o servidor de processos**: Cópia **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** para o servidor de configuração e o servidor de processos. Faça duplo clique para executá-lo.<br>
  4. **Servidor de destino principal de Windows**: Para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor. Clique duas vezes nele para executá-lo. A mesma atualização de agente unified também é aplicável para o servidor de origem. Se a origem não foi atualizada para a atualização 4, deve atualizar o agente unificado.
  A atualização não tem de aplicar no mestre de destino preparado com o **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** como este é o novo instalador de disponibilidade geral em todas as alterações mais recentes.
  5. **servidor vContinuum**:  Cópia **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** para o servidor.  Certifique-se de que tenha fechado o Assistente do vContinuum. Faça duplo clique no ficheiro para executá-lo.
  A atualização não tem de aplicar no destino mestre preparado com o **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** como este é o novo instalador de disponibilidade geral em todas as alterações mais recentes.
  6. **Servidor de destino principal do Linux**: Para atualizar o agente unificado, copie **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** a mestre e extraia-o servidor de destino. A pasta extraída, execute **/Install**.
  7. **Servidor de origem do Windows**: Para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** ao servidor de origem. Faça duplo clique no ficheiro para executá-lo. 
  Não precisa de instalar o agente de atualização 5 no servidor de origem, se ele já foi atualizado para a atualização 4 ou agente de origem é instalado com o instalador de base mais recente **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Servidor de origem do Linux**: Para atualizar o agente unificado, copie a versão correspondente do arquivo agente unificado para o servidor Linux e extraí-lo. A pasta extraída, execute **/Install**.  Exemplo: Para RHEL 6.7 server de 64 bits, copie **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor e extraia-o. A pasta extraída, execute **/Install**.


> [!NOTE]
> * Base Unified Agent(UA) installer para Windows foi atualizado para suportar o Windows Server 2016. O novo instalador **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** é empacotado com o pacote básico do Scout GA (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O instalador mesmo será utilizado para todas as versões suportadas do Windows. 
> * Base do vContinuum do Windows e de destino principal instalador foi atualizado para suportar o Windows Server 2016. O novo instalador **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** é empacotado com o pacote básico do Scout GA (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O instalador mesmo será utilizado para implementar o destino de principal do Windows 2016 e Windows 2012R2 mestre de destino.
> * O servidor de Windows 2016 no servidor físico não é suportado pelo Scout de ASR. Ele oferece suporte apenas Windows Server 2016 VM de VMware. 
>

#### <a name="bug-fixes-and-enhancements"></a>Correções de erros e melhorias
- Falha de proteção de reativação pós-falha para VM do Linux com a lista de discos a serem replicados está vazia no final da configuração.

### <a name="site-recovery-scout-801-update-5"></a>Atualização do Scout 8.0.1 de recuperação de sites 5
Atualização do Scout 5 é uma atualização cumulativa. Contém todas as correções de atualização 1 para a atualização 4 e as novas correções descritas abaixo.
- Correções do Site Recovery Scout atualização 4 a 5 de atualização são especificamente para os componentes de vContinuum e de destino mestres.
- Se os servidores de origem, o destino mestre, configuração, processo e servidores de RX já estiver a executar a atualização 4, em seguida, aplicá-la apenas no servidor de destino mestre. 

#### <a name="new-platform-support"></a>Novo suporte de plataforma
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* SLES 11 SP4 64 bits **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** é empacotado com o pacote básico do Scout GA (**InMage_Scout_Standard_8.0.1 GA.zip**). Transferir o pacote de disponibilidade geral no portal, conforme descrito em criar um cofre.


#### <a name="bug-fixes-and-enhancements"></a>Correções de erros e melhorias

* Correções para o cluster do Windows uma maior fiabilidade de suporte:
    * Fixo – alguns do MSCS P2V tornam-se de discos de cluster RAW após a recuperação.
    * A recuperação de cluster do MSCS de P2V Fixed-falha devido a um erro de correspondência de encomenda do disco.
    * Fixo – o MSCS operação para adicionar discos falhar com um erro de incompatibilidade de tamanho de disco de cluster.
    * Verifique – a preparação para a origem de cluster do MSCS com o mapeamento de LUNs de RDM falha na verificação de tamanho.
    * Proteção de cluster de nó único do Fixed-falha devido a um problema de falta de correspondência de SCSI. 
    * A fixed-proteção de reavaliação de do servidor de cluster do Windows de P2V falha se existirem discos de cluster de destino. 
    
* Corrigido: Durante a proteção de reativação pós-falha, se o servidor de destino principais selecionados não está no mesmo servidor ESXi que a máquina de origem protegida (durante a proteção de encaminhamento), em seguida, vContinuum escolherá o servidor de destino mestre errado durante a recuperação de reativação pós-falha e a recuperação Falha da operação.

> [!NOTE]
> * As correções de cluster de P2V são aplicáveis apenas a clusters físicos MSCS recentemente protegidos com o Site Recovery Scout Update 5. Para instalar as correções de cluster em clusters de P2V MSCS protegidos com as atualizações mais antigas, siga os passos de atualização mencionados na secção 12 a [notas de versão do Site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * Se durante a reavaliação de proteção, o mesmo conjunto de discos está ativo em cada um de nós do cluster quando inicialmente protegidos, nova proteção de um cluster do MSCS físico só pode reutilizar discos de destino existente. Se não for, em seguida, utilize os passos manuais na seção 12 [notas de versão do Site Recovery Scout](https://aka.ms/asr-scout-release-notes), para mover os discos de lado de destino para o caminho de arquivo de dados correto, para reutilização durante a reavaliação de proteção. Se voltar a proteger o cluster do MSCS no modo de P2V sem seguir os passos de atualização, cria um novo disco no servidor de ESXi de destino. Terá de eliminar manualmente os discos antigos do arquivo de dados.
> * Quando um servidor de origem SLES11 ou SLES11 (com qualquer service pack) é reiniciado corretamente, em seguida, marcar manualmente os **raiz** pares de replicação para a nova sincronização do disco. Não haverá nenhuma notificação na interface do CX. Se não marcar o disco de raiz para a ressincronização, pode observar problemas de integridade de dados.


### <a name="azure-site-recovery-scout-801-update-4"></a>O Azure Site Recovery Scout 8.0.1 atualização 4
A atualização 4 do Scout é uma atualização cumulativa. Inclui todas as correções de atualização 1 para o Update 3 e as novas correções descritas abaixo.

#### <a name="new-platform-support"></a>Novo suporte de plataforma

* Foi adicionado suporte para o vCenter/vSphere 6.0, 6.1 e 6.2
* Foi adicionado suporte para estes sistemas de operativos Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, o 7.1 e 7.2
  * CentOS 7.0, o 7.1 e 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64bit **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** é empacotado com o pacote básico do Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Transferir o pacote de GA do Scout no portal, conforme descrito em criar um cofre.

#### <a name="bug-fixes-and-enhancements"></a>Correções de erros e melhorias

* Encerramento melhorado processamento para os seguintes sistemas operativos Linux e clones, para evitar problemas de ressincronização indesejável:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Para o Linux, todas as permissões de acesso de pasta no diretório de instalação do agente unificado agora são restritos apenas o utilizador local.
* No Windows, uma correção para um tempo limite problema ocorrido ao emitir indicadores de distribuídas de consistência comum, no bastante carregadas aplicações distribuídas, como os clusters do SQL Server e SharePoint.
* Um registo relacionadas com a correção no instalador de base de servidor de configuração.
* Uma ligação de transferência para o VMware vCLI 6.0 foi adicionada para o instalador de base de destino principal de Windows.
* Verificações adicionais e os registos foram adicionados para alterações de configuração de rede durante as explorações de recuperação após desastre e de ativação pós-falha.
* Uma solução para um problema que causou a informações de retenção para não será possível reportar para o servidor de configuração.  
* Para clusters físicos, uma solução para um problema que causou o redimensionamento de volume efetuar a ativação no Assistente do vContinuum, ao reduzir o volume de origem.
* Uma correção para um problema de proteção de cluster falhou com o erro: "Falha ao localizar a assinatura de disco", quando o disco de cluster é um disco PRDM.
* Uma correção para uma falha de servidor de transporte cxps, causada por uma exceção de fora do intervalo.
* Nome do servidor e de colunas de endereço IP estão agora redimensionáveis na **instalação Push do** página do Assistente do vContinuum.
* Aprimoramentos de API de RX:
  * Os cinco mais recente disponível comuns consistência aponta agora disponível (garantido apenas etiquetas).
  * Capacidade e os detalhes de espaço livre são apresentados para todos os dispositivos protegidos.
  * Estado de driver do Scout no servidor de origem está disponível.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** pacote básico tem:
>     * Um instalador de base de servidor de configuração atualizada (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Um instalador de base de destino principal de Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * Para instalações novas, utilize o novo servidor de configuração e bits de destino mestre GA do Windows.
> * A atualização 4 pode ser aplicada diretamente em 8.0.1 GA.
> * O servidor de configuração e atualizações de RX não serão apresentadas depois dos que foram aplicadas.


### <a name="azure-site-recovery-scout-801-update-3"></a>O Azure Site Recovery Scout 8.0.1 atualização 3

Todas as atualizações de recuperação de sites são cumulativas. Atualização 3 contém todas as correções Update 1 e 2 de atualização. A atualização 3 pode ser aplicada diretamente no 8.0.1 GA. O servidor de configuração e atualizações de RX não serão apresentadas depois dos que foram aplicadas.

#### <a name="bug-fixes-and-enhancements"></a>Correções de erros e melhorias
Atualização 3 corrige os problemas seguintes:

* O servidor de configuração e RX não estão registados no cofre quando estão por trás do proxy.
* O número de horas em que não foi atingido o objetivo de ponto de recuperação (RPO) não é atualizado no relatório de estado de funcionamento.
* O servidor de configuração não está a sincronizar com RX quando os detalhes de hardware do ESX, ou detalhes da rede, contenham quaisquer carateres de UTF-8.
* Controladores de domínio do Windows Server 2008 R2 não iniciada após a recuperação.
* Sincronização offline não está a funcionar conforme esperado.
* Após a ativação pós-falha da VM, a eliminação do par de replicação não progride na consola do servidor de configuração durante muito tempo. Os utilizadores não é possível concluir a reativação pós-falha ou retomar as operações.
* Em geral, operações de instantâneo pela tarefa de consistência foram otimizadas ajudar a reduzir o aplicativo se desliga tais como clientes do SQL Server.
* O desempenho de ferramenta (VACP.exe) de consistência foi aprimorado. Utilização de memória necessária para a criação de instantâneos no Windows foi reduzida.
* O push instalar falhas de serviço, quando a palavra-passe é superior a 16 carateres.
* vContinuum não verifique e pedir novas credenciais do vCenter, quando as credenciais são modificadas.
* No Linux, o Gerenciador de cache de destino mestre (cachemgr) não é transferir ficheiros do servidor de processos. Isso resulta na limitação do par de replicação.
* Quando a ordem de disco de cluster (MSCS) de ativação pós-falha física não é o mesmo em todos os nós, a replicação não está definida para alguns dos volumes de cluster. O cluster tem de ser protegida novamente para aproveitar esta correção.  
* Funcionalidade de SMTP não está a funcionar conforme esperado, após a RX é atualizada de Scout 7.1 para Scout 8.0.1.
* Foram adicionadas estatísticas mais no registo para a operação de reversão, para controlar o tempo necessário para concluí-la.
* Foi adicionado suporte para sistemas de operativos Linux no servidor de origem:
  * Atualização do Red Hat Enterprise Linux (RHEL) 6 7
  * CentOS 6 atualização 7
* O servidor de configuração e RX consolas agora mostrar notificações para o par, o que entra em modo de bitmap.
* Foram adicionadas as seguintes correções de segurança no RX:
    * Ignorar autorização por meio de violação de parâmetros: Acesso restrito a utilizadores não aplicáveis.
    * Falsificação de solicitação: O conceito de token de página foi implementado e gera aleatoriamente para todas as páginas. Isso significa que existe apenas uma única início de sessão instância para o mesmo utilizador, e a atualização de página não funciona. Em vez disso, redireciona para o dashboard.
    * Carregamento de ficheiros maliciosos: Ficheiros estão limitados a extensões específicas: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf , sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e zip.
    * Scripts entre sites persistente: Foram adicionadas validações de entrada.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (Update 03Dec15)

Correções na atualização 2 incluem:

* **Servidor de configuração**: Problemas que impediram a 31 dias medição à funcionalidade gratuita de trabalho conforme o esperado, quando o servidor de configuração foi registado para o cofre do Azure Site Recovery.
* **Agente unificado**: Correção para um problema em que resultaram na atualização não sendo instalada no servidor de destino mestre, durante a atualização da versão 8.0 para 8.0.1 Update 1.

### <a name="azure-site-recovery-scout-801-update-1"></a>O Azure Site Recovery Scout 8.0.1 atualização 1
Atualização 1 inclui as seguintes correções de erros e novas funcionalidades:

* 31 dias de proteção gratuito por instância de servidor. Isto permite-lhe testar a funcionalidade ou configurar uma prova de conceito.
* Todas as operações no servidor, incluindo a ativação pós-falha e reativação pós-falha, são gratuitas durante os primeiros 31 dias. O tempo é iniciado quando um servidor primeiro estiver protegido por Scout de recuperação de Site. Ao dia 32nd, cada servidor protegido é cobrado a taxa de instância padrão para a proteção de recuperação de sites num site de cliente.
* Em qualquer altura, está disponível no número de servidores protegidos atualmente está a ser cobrada a **Dashboard** no cofre.
* Foi adicionado suporte para vSphere a Interface de linha de comandos (vCLI) 5.5 atualização 2.
* Foi adicionado suporte para estes sistemas de operativos Linux no servidor de origem:
    * 6 de atualização do RHEL 6
    * RHEL 5 atualização 11
    * Atualização 6 do centOS 6
    * Atualização do centOS 5 11
* Correções de erros para resolver os problemas seguintes:
  * Falha do registo do cofre para o servidor de configuração ou o servidor de RX.
  * Volumes de cluster não são apresentados como esperados quando em cluster que VMS voltar a proteger como retomar a eles.
  * Reativação pós-falha falha quando o servidor de destino mestre está alojado num servidor ESXi diferente de VMs de produção no local.
  * Permissões de ficheiro de configuração são alteradas durante a atualização para 8.0.1. Esta alteração afeta a proteção e as operações.
  * O limiar de ressincronização não é imposto como esperado, fazendo com que o comportamento de replicação inconsistente.
  * As definições de RPO não são apresentados corretamente na consola do servidor de configuração. O valor de dados não comprimidos incorretamente mostra o valor comprimido.
  * A operação de remoção não elimina conforme esperado no Assistente do vContinuum e os replicação não é eliminada do console do servidor de configuração.
  * No Assistente do vContinuum, o disco é automaticamente não selecionado ao clicar **detalhes** na vista de disco, durante a proteção de VMs do MSCS.
  * No cenário físico-virtual (P2V), os serviços de HP necessários (como CIMnotify e CqMgHost) não são movidos para manual na recuperação VM. Este problema resulta em tempo de arranque adicionais.
  * A proteção de VM do Linux falha quando há mais de 26 discos no servidor de destino mestre.

