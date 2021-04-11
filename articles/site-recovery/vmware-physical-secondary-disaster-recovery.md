---
title: Recuperação de desastres de VMware VMs/servidores físicos para um site secundário com recuperação do site Azure
description: Saiba como configurar a recuperação de desastres de VMware VMs, ou servidores físicos Windows e Linux, para um site secundário com a Recuperação do Site Azure.
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: c2456fcfffd9645f94856998142f262298b5eef9
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581469"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Configurar recuperação após desastre de máquinas virtuais do VMware ou servidores físicos no local para um site secundário

InMage Scout in [Azure Site Recovery](site-recovery-overview.md) fornece replicação em tempo real entre sites VMware no local. O InMage Scout está incluído nas subscrições do serviço de recuperação do site Azure.

## <a name="end-of-support-announcement"></a>Anúncio do fim do suporte

O cenário de recuperação do local de Azure para a replicação entre vMware ou centros de dados físicos está a atingir o fim do suporte.

-   A partir de agosto de 2018, o cenário não pode ser configurado no cofre dos Serviços de Recuperação, e o software InMage Scout não pode ser descarregado do cofre. As implementações existentes serão apoiadas. 
-   A partir de 31 de dezembro de 2020, o cenário não será apoiado.
- Os parceiros existentes podem embarcar novos clientes para o cenário até que o suporte termine.

Durante 2018 e 2019, serão lançadas duas atualizações: 

-   Atualização 7: Corrige problemas de configuração e conformidade da rede e fornece suporte TLS 1.2.
-   Atualização 8: Adiciona suporte aos sistemas operativos Linux RHEL/CentOS 7.3/7.4/7.5 e ao SUSE 12

Depois da Atualização 8, não serão lançadas mais atualizações. Haverá um suporte limitado de hotfix para os sistemas operativos adicionados no Update 8, e correções de bugs com base no melhor esforço.

A Azure Site Recovery continua a inovar fornecendo aos clientes VMware e Hiper-V uma solução DRaaS sem emenda e melhor classe com a Azure como um local de recuperação de desastres. A Microsoft recomenda que os clientes existentes do InMage /ASR Scout considerem utilizar o VMware vMware da Azure Site Recovery para o cenário Azure para as suas necessidades de continuidade de negócio. O vMware para o azure site Recovery's VMware to Azure é uma solução DR de classe empresarial para aplicações VMware, que oferece RPO e RTO de minutos, suporte para replicação e recuperação de aplicações multi-VM, embarque sem emenda, monitorização abrangente e vantagem significativa de TCO.

### <a name="scenario-migration"></a>Migração de cenário
Como alternativa, recomendamos a criação de recuperação de desastres para VMware VMware e máquinas físicas no local, replicando-os em Azure. Proceda da seguinte forma:

1.  Reveja a comparação rápida abaixo. Antes de poder replicar máquinas no local, é necessário verificar se cumprem [os requisitos](./vmware-physical-azure-support-matrix.md#replicated-machines) de replicação ao Azure. Se estiver a replicar VMware VMs, recomendamos que reveja [as diretrizes de planeamento de capacidades](./site-recovery-plan-capacity-vmware.md)e execute a [ferramenta De planeamento de implementação](./site-recovery-deployment-planner.md) para os requisitos de capacidade de identidade e verifique a conformidade.
2.  Depois de executar o Deployment Planner, pode configurar a replicação: o Para VMware VMs, siga estes tutoriais para [preparar o Azure,](./tutorial-prepare-azure.md) [prepare o seu ambiente VMware no local](./vmware-azure-tutorial-prepare-on-premises.md)e [confiem de recuperação de desastres](./vmware-azure-tutorial-prepare-on-premises.md).
o Para máquinas físicas, siga este [tutorial.](./physical-azure-disaster-recovery.md)
3.  Depois de as máquinas estarem a replicar-se para o Azure, podes fazer um [exercício de recuperação](./site-recovery-test-failover-to-azure.md) de desastres para garantir que está tudo a funcionar como esperado.

### <a name="quick-comparison"></a>Comparação rápida

**Funcionalidade** | **Replicação para o Azure** |**Replicação entre centros de dados VMware**
--|--|--
**Componentes necessários** |Serviço de mobilidade em máquinas replicadas. Servidor de configuração no local, servidor de processo, servidor de alvo principal. Servidor de processo temporário em Azure para falha.|Serviço de mobilidade, Servidor de Processo, Servidor de Configuração e Alvo Principal
**Configuração e orquestração** |Cofre dos Serviços de Recuperação no portal Azure | Usando vContinuo 
**Replicada** |Disco (Windows e Linux) |Volume-Windows<br> Disk-Linux
**Aglomerado de disco compartilhado** |Não suportado|Suportado
**Limites de churn de dados (média)** |10 dados de MB/s por disco<br> 25MB/s dados por VM<br> [Saiba mais](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 dados de MB/s por disco  <br> > 25 dados de MB/s por VM
**Monitorização** |Do portal Azure|A partir de CX (Servidor de Configuração)
**Matriz de Apoio** | [Clique aqui para mais detalhes](./vmware-physical-azure-support-matrix.md)|[Baixe a matriz compatível com asr Scout](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial:

- [Reveja](vmware-physical-secondary-support-matrix.md) os requisitos de suporte para todos os componentes.
- Certifique-se de que as máquinas que pretende replicar cumprem o [suporte da máquina replicada](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Descarregue e instale atualizações de componentes

 Reveja e instale as [últimas atualizações.](#updates) As atualizações devem ser instaladas nos servidores na seguinte ordem:

1. Servidor RX (se aplicável)
2. Servidores de configuração
3. Servidores de processo
4. Servidores Master Target
5. v Servidores deContinuo
6. Servidor de origem (servidor windows e Linux)

Instale as atualizações da seguinte forma:

> [!NOTE]
>A versão de atualização de ficheiros de todos os componentes scout pode não ser a mesma na atualização .zip ficheiro. A versão mais antiga indica que não existe qualquer alteração no componente desde a atualização anterior a esta atualização.

Descarregue o ficheiro .zip [de atualização](https://aka.ms/asr-scout-update7) e os ficheiros de configuração [de atualização MySQL e PHP.](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) O ficheiro .zip de atualização contém todos os binários de base e binários de upgrade cumulativo dos seguintes componentes: 
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
  1. Extrair os ficheiros .zip.
  2. **Servidor RX**: Copiar **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** para o servidor RX e extraí-lo. Na pasta extraída, corra **/Instale**.
  3. **Servidor de configuração e servidor de processo**: Copiar **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** para o servidor de configuração e servidor de processo. Clique duas vezes para executá-lo.<br>
  4. **Servidor Alvo Do Windows**: Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** para o servidor. Clique duas vezes para executá-lo. O mesmo ficheiro também pode ser utilizado para instalação fresca. A mesma atualização unificada do agente também é aplicável para o servidor de origem.
  A atualização não necessita de ser aplicada no alvo Master preparado com **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe**  uma vez que este é o novo instalador de GA com todas as alterações mais recentes.
  5. **v Servidor deContinuo**: Copiar **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** para o servidor.  Certifique-se de que fechou o assistente vContinuum. Clique duas vezes no ficheiro para executá-lo.
  6. **Linux principal servidor-alvo**: Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** para o servidor Linux Master Target e extrai-o. Na pasta extraída, corra **/Instale**.
  7. **Servidor de origem do Windows**: Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** para o servidor de origem. Clique duas vezes no ficheiro para executá-lo. 
  8. **Servidor de origem Linux**: Para atualizar o agente unificado, copie a versão correspondente do ficheiro de agente unificado para o servidor Linux e extraa-o. Na pasta extraída, corra **/Instale**.  Exemplo: Para o servidor RHEL 6.7 64-bits, copie **InMage_UA_8.0.0.0_RHEL6-64_GA_03Dec2018_release.tar.gz** para o servidor e extraia-o. Na pasta extraída, corra **/Instale**.
  9. Após a atualização do servidor de configuração, do servidor processuais e do servidor RX com os instaladores acima mencionados, as bibliotecas PHP e MySQL precisam de ser atualizadas manualmente com os passos mencionados na secção 7.4 do guia de [instalação rápida](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Ativar a replicação

1. Configurar a replicação entre os sites VMware de origem e alvo.
2. Consulte os seguintes documentos para saber mais sobre instalação, proteção e recuperação:

   * [Notas de versão](https://aka.ms/asr-scout-release-notes)
   * [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
   * [Guia do utilizador](https://aka.ms/asr-scout-user-guide)
   * [Guia de utilizador RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)
   * [Modernização das bibliotecas MYSQL e PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Atualizações

### <a name="site-recovery-scout-801-update-7"></a>Escuteiro de recuperação do local 8.0.1 Atualização 7 
Atualizado: 31 de dezembro de 2018 Download [Scout update 7](https://aka.ms/asr-scout-update7).
Scout Update 7 é um instalador completo que pode ser usado para instalação nova, bem como para atualizar os agentes/MT existentes que estão em atualizações anteriores (da Atualização 1 a Atualização 6). Contém todas as correções da Atualização 1 para a Atualização 6 mais as novas correções e melhorias descritas abaixo.
 
#### <a name="new-features"></a>Novas funcionalidades
* Conformidade com o PCI
* Suporte TLS v1.2

#### <a name="bug-and-security-fixes"></a>Correções de Bug e Segurança
* Fixo: As máquinas Windows Cluster/Autónoma têm uma configuração IP incorreta após a recuperação/perfuração DR.
* Fixo: Por vezes, a operação do disco de adicionar falha no cluster V2V.
* Fixo: vRouse Do assistente fica preso durante a fase de recuperação se o alvo principal for o Windows Server 2016
* Fixo: Os problemas de segurança do MySQL são atenuados através da atualização do MySQL para a versão 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Atualização manual para PHP e MySQL em CS,PS e RX
A plataforma de scripts PHP deve ser atualizada para a versão 7.2.10 no Servidor de Configuração, Servidor de Processos e Servidor RX.
O sistema de gestão da base de dados MySQL deve ser atualizado para a versão 5.7.23 no Servidor de Configuração, Servidor de Processos e Servidor RX.
Siga os passos manuais indicados no [guia de instalação Quick](https://aka.ms/asr-scout-quick-install-guide) para atualizar as versões PHP e MySQL.

### <a name="site-recovery-scout-801-update-6"></a>Scout de recuperação do local 8.0.1 Atualização 6 
Atualizado: 12 de outubro de 2017

Baixar [atualização scout 6](https://aka.ms/asr-scout-update6).

Scout Update 6 é uma atualização cumulativa. Contém todas as correções da Atualização 1 para a Atualização 5 mais as novas correções e melhorias descritas abaixo. 

#### <a name="new-platform-support"></a>Suporte de nova plataforma
* O suporte foi adicionado para Source Windows Server 2016
* Foi adicionado suporte para os seguintes sistemas operativos Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oráculo Linux 6.8
* Foi adicionado suporte para VMware Center 6.5

Instale as atualizações da seguinte forma:

> [!NOTE]
>A versão de atualização de ficheiros de todos os componentes scout pode não ser a mesma na atualização .zip ficheiro. A versão mais antiga indica que não existe qualquer alteração no componente desde a atualização anterior a esta atualização.

Descarregue o ficheiro [.zip de atualização.](https://aka.ms/asr-scout-update6) O ficheiro contém os seguintes componentes: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA atualiza4 bits para RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_ \<Linux OS> _8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Extrair os ficheiros .zip.
  2. **Servidor RX**: Copiar **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** para o servidor RX e extraí-lo. Na pasta extraída, corra **/Instale**.
  3. **Servidor de configuração e servidor de processo**: Copiar **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** para o servidor de configuração e servidor de processo. Clique duas vezes para executá-lo.<br>
  4. **Servidor Alvo Do Windows**: Para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor. Clique duas vezes para executá-lo. A mesma atualização unificada do agente também é aplicável para o servidor de origem. Se a fonte não tiver sido atualizada para a Atualização 4, deverá atualizar o agente unificado.
  A atualização não necessita de ser aplicada no alvo Master preparado com **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**  uma vez que este é o novo instalador de GA com todas as alterações mais recentes.
  5. **v Servidor deContinuo**: Copiar **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** para o servidor.  Certifique-se de que fechou o assistente vContinuum. Clique duas vezes no ficheiro para executá-lo.
  A atualização não precisa de ser aplicada no Master Target preparado com **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** uma vez que este é o novo instalador de GA com todas as alterações mais recentes.
  6. **Linux master target server**: Para atualizar o agente unificado, copie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor alvo principal e extraia-o. Na pasta extraída, corra **/Instale**.
  7. **Servidor de origem do Windows**: Para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor de origem. Clique duas vezes no ficheiro para executá-lo. 
  Não precisa de instalar o agente Update 5 no servidor de origem se já tiver sido atualizado para o Update 4 ou se o agente de origem estiver instalado com o instalador de base mais recente **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Servidor de origem Linux**: Para atualizar o agente unificado, copie a versão correspondente do ficheiro de agente unificado para o servidor Linux e extraa-o. Na pasta extraída, corra **/Instale**.  Exemplo: Para o servidor RHEL 6.7 64-bits, copie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor e extraia-o. Na pasta extraída, corra **/Instale**.


> [!NOTE]
> * O instalador de agente unificado base (UA) para windows foi atualizado para suportar o Windows Server 2016. O novo **instaladorInMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** é embalado com o pacote base scout GA **(InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O mesmo instalador será utilizado para toda a versão suportada do Windows. 
> * O instalador Base Windows vContinuum & Master Target foi atualizado para suportar o Windows Server 2016. O novo **instaladorInMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** é embalado com o pacote base scout GA **(InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O mesmo instalador será utilizado para implementar o Windows 2016 Master Target e o Windows 2012R2 Master Target.
> * O servidor windows 2016 no servidor físico não é suportado pelo ASR Scout. Suporta apenas o VMware VM do Windows Server 2016. 
>

#### <a name="bug-fixes-and-enhancements"></a>Correções e melhorias de bugs
- A proteção contra falhas falha no Linux VM com a lista de discos a replicar está vazia no final da config.

### <a name="site-recovery-scout-801-update-5"></a>Scout de recuperação do local 8.0.1 Atualização 5
Scout Update 5 é uma atualização cumulativa. Contém todas as correções da Atualização 1 para a Atualização 4 e as novas correções descritas abaixo.
- As correções da Atualização de Scout de Recuperação do Site 4 para a Atualização 5 são especificamente para os componentes principal alvo e vContinuum.
- Se os servidores de origem, o alvo principal, a configuração, o processo e os servidores RX já estiverem a executar o Update 4, então aplique-o apenas no servidor alvo principal. 

#### <a name="new-platform-support"></a>Suporte de nova plataforma
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* SLES 11 SP4 64 bit **InMage_UA_8.0.0.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** é embalado com o pacote base Scout GA **(InMage_Scout_Standard_8.0.1 GA.zip).** Descarregue o pacote de GA do portal, como descrito na criação de um cofre.


#### <a name="bug-fixes-and-enhancements"></a>Correções e melhorias de bugs

* Correções para uma maior fiabilidade do suporte ao cluster do Windows:
    * Fixo- Alguns dos discos de cluster P2V MSCS tornam-se RAW após a recuperação.
    * A recuperação do cluster MSCS P2V fixo falha devido a uma incompatibilidade da ordem do disco.
    * Fixo- A operação de cluster MSCS para adicionar discos falha com um erro de incompatibilidade do tamanho do disco.
    * Fixo- A verificação de prontidão para o cluster MSCS de origem com mapeamento RDM LUNs falha na verificação de tamanho.
    * A proteção fixa do cluster de nó único falha devido a um problema de incompatibilidade scsi. 
    * Re-protecção fixa do servidor de cluster do Windows P2V falha se os discos de cluster alvo estiverem presentes. 
    
* Fixo: Durante a proteção contra falhas, se o servidor-alvo principal selecionado não estiver no mesmo servidor ESXi que a máquina de origem protegida (durante a proteção para a frente), então vContinuum apanha o servidor alvo principal errado durante a recuperação do failback e a operação de recuperação falha.

> [!NOTE]
> * As correções do cluster P2V são aplicáveis apenas aos clusters físicos de MSCS que são recentemente protegidos com a Atualização de Scout de Recuperação do Local 5. Para instalar as correções do cluster em clusters MSCS P2V protegidos com atualizações mais antigas, siga os passos de atualização mencionados na secção 12 das Notas de Lançamento do [Escuteiro de Recuperação](https://aka.ms/asr-scout-release-notes)do Local .
> * se no momento da re-protecção, o mesmo conjunto de discos estiver ativo em cada um dos nós de cluster como estavam quando inicialmente protegidos, então a re-protecção de um cluster de MSCS físico só pode reutilizar os discos-alvo existentes. Caso contrário, utilize os passos manuais na secção 12 das Notas de lançamento do [Escoteiro de Recuperação](https://aka.ms/asr-scout-release-notes)do Local , para mover os discos laterais-alvo para o caminho correto da loja de dados, para reutilização durante a re-protecção. Se reprotegir o cluster MSCS no modo P2V sem seguir os passos de atualização, cria um novo disco no servidor ESXi alvo. Terá de eliminar manualmente os discos antigos da datastore.
> * Quando um servidor SLES11 ou SLES11 (com qualquer pacote de serviço) for reiniciado graciosamente, marque manualmente os pares de replicação do disco **raiz** para re-sincronização. Não há notificação na interface CX. Se não marcar o disco raiz para a resincronização, poderá notar problemas de integridade dos dados.


### <a name="azure-site-recovery-scout-801-update-4"></a>Escuteiro de recuperação do local de Azure 8.0.1 Atualização 4
Scout Update 4 é uma atualização cumulativa. Inclui todas as correções da Atualização 1 para a Atualização 3 e as novas correções descritas abaixo.

#### <a name="new-platform-support"></a>Suporte de nova plataforma

* Foi adicionado suporte para vCenter/vSphere 6.0, 6.1 e 6.2
* Foi adicionado suporte a estes sistemas operativos Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 e 7.2
  * CentOS 7.0, 7.1 e 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 bit  **InMage_UA_8.0.0.0_RHEL7-64_GA_06Oct2016_release.tar.gz** é embalado com o pacote base Escoteiro GA **InMage_Scout_Standard_8.0.1 GA.zip**. Descarregue o pacote de GA scout do portal, conforme descrito na criação de um cofre.

#### <a name="bug-fixes-and-enhancements"></a>Correções e melhorias de bugs

* Melhor manuseamento de paragem para os seguintes sistemas operativos Linux e clones, para evitar problemas de ressincronização indesejados:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Para o Linux, todas as permissões de acesso a pastas no diretório de instalação do agente unificado estão agora restritas apenas ao utilizador local.
* No Windows, uma correção para um problema de tempo fora que ocorreu ao emitir marcadores comuns de consistência distribuída, em aplicações distribuídas fortemente carregadas, tais como sql Server e share point clusters.
* Uma correção relacionada com o registo no instalador da base do servidor de configuração.
* Um link de descarregamento para VMware vCLI 6.0 foi adicionado ao instalador principal do Windows.
* Foram adicionados controlos e registos adicionais para alterações na configuração da rede durante os exercícios de failover e recuperação de desastres.
* Uma correção para um problema que fez com que as informações de retenção não fossem reportadas ao servidor de configuração.  
* Para os aglomerados físicos, uma correção para um problema que fez com que o volume redimensionado falhasse no assistente vContinuum, ao reduzir o volume de origem.
* Uma correção para um problema de proteção do cluster que falhou com erro: "Falhou em encontrar a assinatura do disco", quando o disco de cluster é um disco PRDM.
* Uma correção para uma falha no servidor de transporte de CXPS, causada por uma exceção fora do alcance.
* O nome do servidor e as colunas de endereço IP são agora resizáveis na página de **instalação push** do assistente vContinuum.
* Melhorias da API RX:
  * Os cinco últimos pontos de consistência comum disponíveis já disponíveis (apenas etiquetas garantidas).
  * A capacidade e os detalhes do espaço gratuito são apresentados para todos os dispositivos protegidos.
  * O estado do condutor do scout no servidor de origem está disponível.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** pacote base tem:
>     * Um instalador de base de servidor de configuração atualizado **(InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Um instalador de base-alvo do Windows **(InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe).**
>     * Para todas as novas instalações, utilize o novo servidor de configuração e os bits GA alvo do Windows.
> * A atualização 4 pode ser aplicada diretamente em 8.0.1 GA.
> * O servidor de configuração e as atualizações RX não podem ser reviradas depois de terem sido aplicadas.


### <a name="azure-site-recovery-scout-801-update-3"></a>Escuteiro de recuperação do local de Azure 8.0.1 Atualização 3

Todas as atualizações de Recuperação do Site são cumulativas. A atualização 3 contém todas as correções da Atualização 1 e da Atualização 2. A atualização 3 pode ser aplicada diretamente em 8.0.1 GA. O servidor de configuração e as atualizações RX não podem ser reviradas depois de terem sido aplicadas.

#### <a name="bug-fixes-and-enhancements"></a>Correções e melhorias de bugs
A atualização 3 corrige as seguintes questões:

* O servidor de configuração e o RX não estão registados no cofre quando estão por detrás do representante.
* O número de horas em que o objetivo do ponto de recuperação (RPO) não foi atingido não é atualizado no relatório de saúde.
* O servidor de configuração não está a sincronizar com o RX quando os detalhes do hardware ESX, ou detalhes da rede, contêm quaisquer caracteres UTF-8.
* Os controladores de domínio R2008 do Windows Server 2008 não arrancam após a recuperação.
* A sincronização offline não está a funcionar como esperado.
* Após o failover VM, a eliminação do par de replicação não progride na consola do servidor de configuração durante muito tempo. Os utilizadores não podem completar as operações de failback ou retomar.
* As operações instantâneas globais pelo trabalho de consistência foram otimizadas, para ajudar a reduzir as desconexões de aplicações, tais como clientes do SQL Server.
* O desempenho da ferramenta de consistência (VACP.exe) foi melhorado. O uso de memória necessário para a criação de instantâneos no Windows foi reduzido.
* O serviço de instalação push falha quando a palavra-passe é maior do que 16 caracteres.
* vContinuum não verifica e solicita novas credenciais vCenter, quando as credenciais são modificadas.
* No Linux, o gestor de cache de alvo principal (cachemgr) não está a descarregar ficheiros do servidor de processo. Isto resulta em estrangulamento de pares de replicação.
* Quando a ordem do disco de failover físico (MSCS) não é a mesma em todos os nós, a replicação não está definida para alguns dos volumes de cluster. O cluster deve ser reprotegido para tirar partido desta correção.  
* A funcionalidade SMTP não está a funcionar como esperado, depois de o RX ser atualizado de Scout 7.1 para Scout 8.0.1.
* Foram adicionadas mais estatísticas no registo para a operação de reversão, para acompanhar o tempo necessário para a completar.
* Foi adicionado suporte para sistemas operativos Linux no servidor de origem:
  * Red Hat Enterprise Linux (RHEL) 6 atualização 7
  * Atualização CentOS 6 7
* O servidor de configuração e as consolas RX mostram agora notificações para o par, que entra em modo bitmap.
* Foram adicionadas as seguintes correções de segurança no RX:
    * Bypass de autorização através de adulteração de parâmetros: Acesso restrito a utilizadores não aplicáveis.
    * Falsificação de pedidos de cross-site: O conceito de símbolo de página foi implementado, e gera aleatoriamente para cada página. Isto significa que há apenas um único sign-in por exemplo para o mesmo utilizador, e a atualização de página não funciona. Em vez disso, redireciona para o painel.
    * Upload de ficheiros maliciosos: Os ficheiros estão restritos a extensões específicas: z, aiff, asf, avi, bmp, csv, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar tgz, tif, tiff, txt, vsd, , xlsx, xml, e zip.
    * Scripts persistentes no site: Foram adicionadas validações de entrada.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Atualização 2 (Atualização 03Dec15)

As correções na Atualização 2 incluem:

* **Servidor de configuração**: Problemas que impediram que a função de medição gratuita de 31 dias funcionasse como esperado, quando o servidor de configuração foi registado no cofre de recuperação do local de Azure.
* **Agente unificado**: Correção para um problema na Atualização 1 que resultou na não instalação da atualização no servidor alvo principal, durante a atualização da versão 8.0 para 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Escuteiro de recuperação do local de Azure 8.0.1 Atualização 1
A atualização 1 inclui as seguintes correções de bugs e novas funcionalidades:

* 31 dias de proteção gratuita por instância do servidor. Isto permite-lhe testar funcionalidades ou configurar uma prova de conceito.
* Todas as operações no servidor, incluindo failover e failback, são gratuitas durante os primeiros 31 dias. A hora começa quando um servidor é protegido pela primeira vez com o Scout de Recuperação do Site. A partir do 32º dia, cada servidor protegido é cobrado à taxa padrão de instância para a proteção de recuperação do site para um site propriedade do cliente.
* A qualquer momento, o número de servidores protegidos atualmente a ser carregados está disponível no **Dashboard** no cofre.
* Foi adicionado suporte para vSphere Command-Line Interface (vCLI) 5.5 Update 2.
* Foi adicionado suporte para estes sistemas operativos Linux no servidor de origem:
    * Atualização RHEL 6 6
    * Atualização RHEL 5 11
    * CentOS 6 Atualização 6
    * CentOS 5 Atualização 11
* Correções de bugs para resolver os seguintes problemas:
  * O registo do cofre falha para o servidor de configuração ou servidor RX.
  * Os volumes de cluster não aparecem como esperado quando os VM agrupados são reprotegidos à medida que retomam.
  * O failback falha quando o servidor principal do alvo é hospedado num servidor ESXi diferente dos VM de produção no local.
  * As permissões de ficheiros de configuração são alteradas quando faz o upgrade para 8.0.1. Esta alteração afeta a proteção e as operações.
  * O limiar de ressincronização não é aplicado como esperado, causando um comportamento de replicação inconsistente.
  * As definições de RPO não aparecem corretamente na consola do servidor de configuração. O valor dos dados não recomprimidos mostra incorretamente o valor comprimido.
  * A operação Remover não apaga como esperado no assistente vContinuum, e a replicação não é eliminada da consola do servidor de configuração.
  * No assistente vContinuum, o disco é automaticamente não selecionado quando clica em **Detalhes** na vista do disco, durante a proteção dos VMs MSCS.
  * No cenário físico-virtual (P2V), os serviços HP necessários (como CIMnotify e CqMgHost) não são movidos para manual na recuperação de VM. Este problema resulta em tempo adicional de arranque.
  * A proteção Linux VM falha quando existem mais de 26 discos no servidor alvo principal.

