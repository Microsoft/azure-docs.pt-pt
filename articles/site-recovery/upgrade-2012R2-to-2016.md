---
title: Atualizar o Windows Server/System Center VMM 2012 R2 para o Windows Server 2016-Azure Site Recovery
description: Saiba como configurar a recuperação de desastres para o Azure para VMs Azure Stack com o serviço Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 1d94935db542a0e64754ab8769996fe906f88b46
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954410"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Atualizar o Windows Server Server/System Center 2012 R2 VMM para o Windows Server/VMM 2016 

Este artigo mostra como atualizar hosts do Windows Server 2012 R2 & SCVMM 2012 R2 que são configurados com Azure Site Recovery, para o Windows Server 2016 & SCVMM 2016

Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre). O serviço garante que suas cargas de trabalho de VM permaneçam disponíveis quando esperadas e interrupções inesperadas ocorrerem.

> [!IMPORTANT]
> Ao atualizar os hosts do Windows Server 2012 R2 que já estão configurados para replicação com o Azure Site Recovery, você deve seguir as etapas mencionadas neste documento. Qualquer caminho alternativo escolhido para atualização pode resultar em Estados sem suporte e pode resultar em uma interrupção na replicação ou na capacidade de executar o failover.


Neste artigo, você aprenderá a atualizar as seguintes configurações em seu ambiente:

> [!div class="checklist"]
> * **Hosts do Windows Server 2012 R2 que não são gerenciados pelo SCVMM** 
> * **Hosts do Windows Server 2012 R2 que são gerenciados por um servidor SCVMM 2012 R2 autônomo** 
> * **Hosts do Windows Server 2012 R2 que são gerenciados pelo servidor SCVMM 2012 R2 altamente disponível**


## <a name="prerequisites--factors-to-consider"></a>Pré-requisitos & Fatores a serem considerados

Antes de atualizar o, observe o seguinte:-

- Se você tiver hosts do Windows Server 2012 R2 que não são gerenciados pelo SCVMM e for uma configuração de ambiente autônomo, haverá uma interrupção na replicação se você tentar executar a atualização.
- Se você selecionou "*não armazenar minhas chaves em Active Directory sob o gerenciamento de chaves distribuídas*" ao instalar o SCVMM 2012 R2 em primeiro lugar, as atualizações não serão concluídas com êxito.

- Se você estiver usando o System Center 2012 R2 VMM, 

    - Verifique as informações do banco de dados no VMM: **configurações** de -> do **console do VMM** -> conexão **geral** -> **banco de dados**
    - Verificar as contas de serviço que estão sendo usadas para System Center Virtual Machine Manager serviço do Agent
    - Verifique se você tem um backup do banco de dados do VMM.
    - Anote o nome do banco de dados dos servidores SCVMM envolvidos. Isso pode ser feito navegando até **o console do VMM** -> **configurações** -> **geral** -> conexão de **banco de dados**
    - Anote a ID do VMM dos servidores do VMM primário e de recuperação do 2012R2. A ID do VMM pode ser encontrada no registro "HKLM: \ SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Certifique-se de que os novos SCVMM que você adicionar ao cluster tenham os mesmos nomes que antes. 

- Se você estiver replicando entre dois de seus sites gerenciados por SCVMMs em ambos os lados, certifique-se de atualizar seu lado de recuperação primeiro antes de atualizar o lado primário.
  > [!WARNING]
  > Ao atualizar o SCVMM 2012 R2, em gerenciamento de chaves distribuídas, selecione para **armazenar as chaves de criptografia em Active Directory**. Escolha as configurações para a conta de serviço e gerenciamento de chaves distribuídas com cuidado. Com base na sua seleção, dados criptografados, como senhas em modelos, podem não estar disponíveis após a atualização e potencialmente podem afetar a replicação com Azure Site Recovery

> [!IMPORTANT]
> Consulte a documentação detalhada do SCVMM de [pré-requisitos](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Hosts do Windows Server 2012 R2 que não são gerenciados pelo SCVMM 
A lista de etapas mencionada abaixo se aplica à configuração do usuário de [hosts do Hyper-V para o Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) executado seguindo este [tutorial](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Conforme mencionado nos pré-requisitos, essas etapas se aplicam somente a um cenário de ambiente clusterizado e não em uma configuração de host Hyper-V autônoma.

1. Siga as etapas para executar a [atualização do cluster sem interrupção.](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) para executar o processo de atualização de cluster sem interrupção.
2. Com cada novo host do Windows Server 2016 introduzido no cluster, remova a referência de um host do Windows Server 2012 R2 de Azure Site Recovery seguindo as etapas mencionadas [aqui]. Esse deve ser o host que você escolheu para drenar & remover do cluster.
3. Depois que o comando *Update-VMVersion* tiver sido executado para todas as máquinas virtuais, as atualizações foram concluídas. 
4. Use as etapas mencionadas [aqui](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) para registrar o novo host do Windows Server 2016 em Azure site Recovery. Observe que o site do Hyper-V já está ativo e você só precisa registrar o novo host no cluster. 
5.  Vá para portal do Azure e verifique o status de integridade replicado nos serviços de recuperação

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Atualizar hosts do Windows Server 2012 R2 gerenciados pelo servidor do SCVMM 2012 R2 autônomo
Antes de atualizar seus hosts do Windows Server 2012 R2, você precisa atualizar o SCVMM 2012 R2 para o SCVMM 2016. Siga as etapas abaixo:-

**Atualizar o SCVMM 2012 R2 autônomo para o SCVMM 2016**

1.  Desinstalar o provedor de ASR navegando até painel de controle-> programas-> programas e recursos-> Microsoft Azure Site Recovery e clique em desinstalar
2. [Manter o banco de dados do SCVMM e atualizar o sistema operacional](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. Em **Adicionar ou remover programas**, selecione **VMM** > **desinstalar**. b. Selecione **remover recursos**e, em seguida, selecione**servidor de gerenciamento V mm e console do VMM**. c. Em **Opções de banco de dados**, selecione **reter banco de dados**. d. Examine o resumo e clique em **desinstalar**.

4. [Instalar o VMM 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Inicie o SCVMM e verifique o status de cada host na guia **malhas** . clique em **Atualizar** para obter o status mais recente. Você deve ver o status como "requer atenção". 
17. Instale o [provedor de Microsoft Azure site Recovery](https://aka.ms/downloaddra) mais recente no SCVMM.
16. Instale o [agente Mars (serviço de recuperação de Microsoft Azure](https://aka.ms/latestmarsagent) mais recente) em cada host do cluster. Atualize para garantir que o SCVMM seja capaz de consultar os hosts com êxito.

**Atualizar hosts do Windows Server 2012 R2 para o Windows Server 2016**

1. Siga as etapas mencionadas [aqui](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) para executar o processo de atualização de cluster sem interrupção. 
2. Depois de adicionar o novo host ao cluster, atualize o host do console do SCVMM para instalar o agente do VMM nesse host atualizado.
3. Execute *Update-VMVersion* para atualizar as versões de VM das máquinas virtuais. 
4.  Vá para portal do Azure e verifique o status de integridade replicado das máquinas virtuais dentro do cofre dos serviços de recuperação. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Atualizar os hosts do Windows Server 2012 R2 são gerenciados pelo servidor SCVMM 2012 R2 altamente disponível
Antes de atualizar seus hosts do Windows Server 2012 R2, você precisa atualizar o SCVMM 2012 R2 para o SCVMM 2016. Os seguintes modos de atualização têm suporte durante a atualização de servidores SCVMM 2012 R2 configurados com o modo Azure Site Recovery misto sem servidores VMM adicionais & modo misto com servidores VMM adicionais.

**Atualizar o SCVMM 2012 R2 para o SCVMM 2016**

1.  Desinstalar o provedor de ASR navegando até painel de controle-> programas-> programas e recursos-> Microsoft Azure Site Recovery e clique em desinstalar
2. Siga as etapas mencionadas [aqui](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) com base no modo de atualização que você deseja executar.
3. Inicie o console do SCVMM e verifique o status de cada host na guia **malhas** . clique em **Atualizar** para obter o status mais recente. Você deve ver o status como "requer atenção".
4. Instale o [provedor de Microsoft Azure site Recovery](https://aka.ms/downloaddra) mais recente no SCVMM.
5. Atualize o [agente Mars (serviço de recuperação de Microsoft Azure](https://aka.ms/latestmarsagent) mais recente) em cada host do cluster. Atualize para garantir que o SC VMM possa consultar os hosts com êxito.


**Atualizar hosts do Windows Server 2012 R2 para o Windows Server 2016**

1. Siga as etapas mencionadas [aqui](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) para executar o processo de atualização de cluster sem interrupção.
2. Depois de adicionar o novo host ao cluster, atualize o host do console do SCVMM para instalar o agente do VMM nesse host atualizado.
3. Execute *Update-VMVersion* para atualizar as versões de VM das máquinas virtuais. 
4.  Vá para portal do Azure e verifique o status de integridade replicado das máquinas virtuais dentro do cofre dos serviços de recuperação. 

## <a name="next-steps"></a>Passos seguintes
Depois que a atualização dos hosts for executada, você poderá executar um [failover de teste](tutorial-dr-drill-azure.md) para testar a integridade de seu status de recuperação de desastre e replicação.

