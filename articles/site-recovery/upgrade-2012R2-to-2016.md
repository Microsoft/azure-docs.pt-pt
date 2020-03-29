---
title: Atualizar O Servidor/Sistema Do Windows VMM 2012 R2 para a Recuperação do Site Windows Server 2016-Azure
description: Saiba como configurar a recuperação de desastres para Azure para VMs Azure Stack com o serviço de recuperação de sites Azure.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 1d94935db542a0e64754ab8769996fe906f88b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954410"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Atualize o Servidor/Centro de Sistemas Do Servidor do Windows 2012 R2 VMM para o Windows Server/VMM 2016 

Este artigo mostra como atualizar o Windows Server 2012 R2 acolhe & SCVMM 2012 R2 que estão configurados com a Recuperação do Site Azure, para o Windows Server 2016 & SCVMM 2016

A Recuperação do Site contribui para a continuidade do seu negócio e estratégia de recuperação de desastres (BCDR). O serviço garante que as suas cargas de trabalho VM permanecem disponíveis quando ocorrem interrupções inesperadas.

> [!IMPORTANT]
> Quando atualizar os anfitriões R2 do Windows Server 2012 que já estão configurados para replicação com a Recuperação do Site Azure, deve seguir os passos mencionados neste documento. Qualquer caminho alternativo escolhido para atualização pode resultar em estados não apoiados e pode resultar numa rutura na replicação ou capacidade de executar failover.


Neste artigo, aprende a atualizar as seguintes configurações no seu ambiente:

> [!div class="checklist"]
> * **Anfitriões R2 do Windows Server 2012 que não são geridos pelo SCVMM** 
> * **Anfitriões R2 do Windows Server 2012 que são geridos por um servidor R2 SCVMM 2012 autónomo** 
> * **Anfitriões R2 do Windows Server 2012 que são geridos por servidor R2 SCVMM 2012 altamente disponível**


## <a name="prerequisites--factors-to-consider"></a>Pré-requisitos & fatores a considerar

Antes de fazer o upgrade, note o seguinte: -

- Se tiver anfitriões R2 do Windows Server 2012 que não são geridos pelo SCVMM, e se for uma configuração ambiente autónoma, haverá uma quebra na replicação se tentar realizar a atualização.
- Se tiver selecionado "*não armazenar as minhas Chaves no Diretório Ativo sob gestão de chaves distribuídas*" enquanto instalava o SCVMM 2012 R2 em primeiro lugar, as atualizações não serão concluídas com sucesso.

- Se estiver a utilizar o System Center 2012 R2 VMM, 

    - Verifique as informações da base de dados no VMM: -> **Definições** -> de **consola VMM****Ligação Geral** -> **base de dados**
    - Verifique as contas de serviço que estão a ser utilizadas para o serviço de agente de gestor de máquinas virtuais do System Center
    - Certifique-se de que tem uma cópia de segurança da Base de Dados VMM.
    - Note o nome da base de dados dos servidores SCVMM envolvidos. Isto pode ser feito navegando para a**Settings** -> **General** -> **ligação** geral de base de dados da **consola** -> VMM
    - Note o ID VMM dos servidores VMM primários e de recuperação de 2012R2. O ID VMM pode ser encontrado a partir do registo "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Certifique-se de que os novos SCVMMs que adiciona ao cluster tem os mesmos nomes que antes. 

- Se estiver a replicar-se entre dois dos seus sites geridos por SCVMMs de ambos os lados, certifique-se de que atualiza o seu lado de recuperação primeiro antes de atualizar o lado primário.
  > [!WARNING]
  > Ao atualizar o SCVMM 2012 R2, no âmbito da Distributed Key Management, selecione para armazenar chaves de **encriptação no Diretório Ativo**. Escolha cuidadosamente as definições para a conta de serviço e distribua a gestão da chave. Com base na sua seleção, dados encriptados, como palavras-passe em modelos, podem não estar disponíveis após a atualização, e podem potencialmente afetar a replicação com a Recuperação do Site Azure

> [!IMPORTANT]
> Consulte a documentação detalhada do SCVMM dos [pré-requisitos](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Anfitriões R2 do Windows Server 2012 que não são geridos pelo SCVMM 
A lista de passos mencionados abaixo aplica-se à configuração do utilizador dos [anfitriões Hyper-V para O Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) executado seguem este [tutorial](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Como mencionado nos pré-requisitos, estes passos aplicam-se apenas a um cenário de ambiente agrupado, e não numa configuração autónoma do hospedeiro Hyper-V.

1. Siga os passos para realizar a atualização do [cluster rolante.](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) para executar o processo de atualização do cluster rolante.
2. Com cada novo anfitrião do Windows Server 2016 que é introduzido no cluster, remova a referência de um anfitrião R2 do Windows Server 2012 da Azure Site Recovery seguindo os passos mencionados [aqui]. Este deve ser o hospedeiro que escolheu para drenar & despejar do aglomerado.
3. Uma vez executado o comando *Update-VMVersion* para todas as máquinas virtuais, as atualizações foram concluídas. 
4. Utilize os passos [aqui](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) mencionados para registar o novo anfitrião do Windows Server 2016 para a Recuperação do Site Azure. Por favor, note que o site Hyper-V já está ativo e só precisa registar o novo anfitrião no cluster. 
5.  Vá ao portal Azure e verifique o estado de saúde replicado dentro dos Serviços de Recuperação

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Upgrade Windows Server 2012 R2 anfitriões geridos por servidor SCVMM 2012 R2 autónomo
Antes de atualizar os anfitriões Do Windows Sever 2012 R2, precisa de atualizar o SCVMM 2012 R2 para SCVMM 2016. Siga os passos abaixo:-

**Upgrade SCVMM 2012 R2 para SCVMM 2016**

1.  Desinstale o fornecedor ASR navegando para programas de controlo -> Programas - > programas e funcionalidades - >Microsoft Azure Recovery , e clique em Desinstalar
2. [Reter a base de dados SCVMM e atualizar o sistema operativo](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. Em **Adicionar programas de remoção,** selecione **VMM** > **Desinstalar**. b. Selecione **Remover funcionalidades**e, em seguida, selecione V**MM management Server e VMM Console**. c. Nas **Opções de Base de Dados,** selecione **Reter base de dados**. d. Reveja o resumo e clique em **Desinstalar**.

4. [Instalar VMM 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Lance o SCVMM e verifique o estado de cada anfitrião sob o separador **Fabrics.** Clique em **Refresh** para obter o estado mais recente. Deviaver o estado como "Precisa de atenção". 
17. Instale o mais recente Fornecedor de [Recuperação de Sites do Microsoft Azure](https://aka.ms/downloaddra) no SCVMM.
16. Instale o mais recente agente do [Microsoft Azure Recovery Service (MARS)](https://aka.ms/latestmarsagent) em cada anfitrião do cluster. Atualização para garantir que o SCVMM é capaz de consultar com sucesso os anfitriões.

**Upgrade Windows Server 2012 R2 anfitriões para Windows Server 2016**

1. Siga os passos [aqui](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) mencionados para executar o processo de atualização do cluster rolante. 
2. Depois de adicionar o novo anfitrião ao cluster, refresque o anfitrião da consola SCVMM para instalar o Agente VMM neste hospedeiro atualizado.
3. Execute *a Versão VMVersion* atualizada para atualizar as versões VM das máquinas Virtuais. 
4.  Vá ao portal Azure e verifique o estado de saúde replicado das máquinas virtuais dentro do Cofre de Serviços de Recuperação. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Upgrade Windows Server 2012 Os anfitriões R2 são geridos por um servidor R2 Altamente disponível
Antes de atualizar os anfitriões Do Windows Sever 2012 R2, precisa de atualizar o SCVMM 2012 R2 para SCVMM 2016. Os seguintes modos de atualização são suportados ao atualizar os servidores R2 SCVMM 2012 configurados com a Recuperação do Site Azure - modo misto sem servidores VMM adicionais & modo Misto com servidores VMM adicionais.

**Upgrade SCVMM 2012 R2 para SCVMM 2016**

1.  Desinstale o fornecedor ASR navegando para programas de controlo -> Programas - > programas e funcionalidades - >Microsoft Azure Recovery , e clique em Desinstalar
2. Siga os passos [aqui](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) mencionados com base no modo de atualização que pretende executar.
3. Lance a consola SCVMM e verifique o estado de cada anfitrião sob o separador **Fabrics.** Clique em **Refresh** para obter o estado mais recente. Deviaver o estado como "Precisa de atenção".
4. Instale o mais recente Fornecedor de [Recuperação de Sites do Microsoft Azure](https://aka.ms/downloaddra) no SCVMM.
5. Atualize o mais recente agente do [Microsoft Azure Recovery Service (MARS)](https://aka.ms/latestmarsagent) em cada anfitrião do cluster. Atualização para garantir que o SC VMM é capaz de consultar com sucesso os anfitriões.


**Upgrade Windows Server 2012 R2 anfitriões para Windows Server 2016**

1. Siga os passos [aqui](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) mencionados para executar o processo de atualização do cluster rolante.
2. Depois de adicionar o novo anfitrião ao cluster, refresque o anfitrião da consola SCVMM para instalar o Agente VMM neste hospedeiro atualizado.
3. Execute *a Versão VMVersion* atualizada para atualizar as versões VM das máquinas Virtuais. 
4.  Vá ao portal Azure e verifique o estado de saúde replicado das máquinas virtuais dentro do Cofre de Serviços de Recuperação. 

## <a name="next-steps"></a>Passos seguintes
Uma vez realizada a atualização dos anfitriões, pode efetuar uma falha no [teste](tutorial-dr-drill-azure.md) para testar a saúde da sua replicação e estado de recuperação de desastres.

