---
title: Atualizar os anfitriões do Windows Server 2012 R2 e SCVMM configurado com o Azure Site Recovery para o Windows Server 2016
description: Saiba como configurar a recuperação após desastre para o Azure das VMs de pilha do Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: b67290f72f762331a6d699fb79aef0c0d7f9fb65
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57853278"
---
# <a name="upgrade-windows-server-2012-r2-hosts-scvmm-2012-r2-configured-with-azure-site-recovery-to-windows-server-2016--scvmm-2016"></a>Atualizar os anfitriões do Windows Server 2012 R2, o SCVMM 2012 R2, se for configurado com o Azure Site Recovery para o Windows Server 2016 & SCVMM de 2016

Este artigo mostra-lhe como atualizar os anfitriões do Windows Server 2012 R2 e o SCVMM 2012 R2 que estão configurados com o Azure Site Recovery, para o Windows Server 2016 & SCVMM de 2016

Site Recovery contribui para a sua estratégia de recuperação (BCDR) de desastre e continuidade empresariais. O serviço garante que as cargas de trabalho VM permaneçam disponíveis quando esperado e ocorrerem falhas inesperadas.

> [!IMPORTANT]
> Ao atualizar os anfitriões do Windows Server 2012 R2 que já estão configurados para a replicação com o Azure Site Recovery, tem de seguir os passos mencionados neste documento. Qualquer caminho alternativo escolhido para a atualização pode resultar em Estados não suportados e pode resultar num intervalo de replicação ou a capacidade de executar a ativação pós-falha.


Neste artigo, irá aprender a atualizar as seguintes configurações no seu ambiente:

> [!div class="checklist"]
> * **Anfitriões Windows Server 2012 R2, que não são gerenciados pelo SCVMM** 
> * **Anfitriões Windows Server 2012 R2, que são geridos pelo servidor autónomo SCVMM 2012 R2** 
> * **Anfitriões Windows Server 2012 R2, que são geridos pelo servidor de elevada disponibilidade SCVMM 2012 R2**


## <a name="prerequisites--factors-to-consider"></a>Pré-requisitos e fatores a considerar

Antes de atualizar, tenha em atenção o seguinte:-

- Se tiver anfitriões do Windows Server 2012 R2 que não são geridos pelo SCVMM e, é um programa de configuração do ambiente autônomo, haverá uma interrupção na replicação se tentar efetuar a atualização.
- Caso tenha selecionado "*não armazenar minhas chaves no Active Directory sob a gestão distribuída de chaves*" ao instalar o SCVMM 2012 R2 em primeiro lugar, as atualizações não serão concluída com êxito.

- Se estiver a utilizar o VMM do System Center 2012 R2, 

    - Verifique as informações de base de dados no VMM: **Consola do VMM** -> **definições** -> **geral** -> **ligação de base de dados**
    - Verificar as contas de serviço que está a ser utilizadas para o serviço de agente do System Center Virtual Machine Manager
    - Certifique-se de que tem uma cópia de segurança da base de dados do VMM.
    - Aponte o nome de base de dados dos servidores de SCVMM envolvidos. Isso pode ser feito ao navegar até **consola do VMM** -> **definições** -> **geral** -> **ligação de base de dados**
    - Tenha em atenção o ID de VMM do 2012R2 principal e servidores do VMM de recuperação. ID do VMM pode ser encontrado no Registro "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Certifique-se de que os SCVMMs novos que adicionar ao cluster tem os mesmos nomes como era antes. 

- Se estiver a replicar entre dois dos sites geridos pelo SCVMMs em ambos os lados, certifique-se de que atualiza seu lado da recuperação primeiro antes de atualizar o lado primário.
  > [!WARNING]
  > Ao atualizar o SCVMM 2012 R2, sob a gestão distribuída de chaves, optar por **armazenar chaves de encriptação no Active Directory**. Escolha cuidadosamente as definições para a conta de serviço e gestão distribuída de chaves. Com base na sua seleção, os dados encriptados, como palavras-passe em modelos não podem estar disponíveis após a atualização e pode potencialmente afetam a replicação com o Azure Site Recovery

> [!IMPORTANT]
> Consulte a documentação do SCVMM detalhada de [pré-requisitos](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Anfitriões Windows Server 2012 R2, que não são gerenciados pelo SCVMM 
A lista de passos mencionados abaixo aplica-se para a configuração de utilizador a partir [anfitriões de Hyper-V para o Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) executado ao seguir este [tutorial](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Conforme mencionado nos pré-requisitos, estes passos aplicam-se apenas a um cenário de ambiente de cluster e não numa configuração de anfitrião de Hyper-V autónoma.

1. Siga os passos para efetuar o [cluster atualização sem interrupção.](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) para executar o processo de atualização sem interrupção do cluster.
2. Com todos os anfitriões Windows Server 2016 novo que foi introduzido no cluster, remova a referência de um anfitrião do Windows Server 2012 R2 a partir do Azure Site Recovery ao seguir os passos mencionados [aqui]. Deve ser o anfitrião que escolheu para drenar & expulsar do cluster.
3. Uma vez a *VMVersion atualização* comando foi executado para todas as máquinas virtuais, as atualizações foram concluídas. 
4. Utilize os passos mencionados [aqui](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) para registar o novo anfitrião Windows Server 2016 para o Azure Site Recovery. Tenha em atenção que o site Hyper-V já está ativo e só precisa de registar o novo anfitrião no cluster. 
5.  Aceda ao portal do Azure e verifique se o estado de funcionamento replicada dentro os serviços de recuperação

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Atualizar os anfitriões do Windows Server 2012 R2 geridos pelo servidor autónomo do SCVMM 2012 R2
Antes de atualizar os anfitriões do Windows Server 2012 R2, tem de atualizar o SCVMM 2012 R2 para o SCVMM de 2016. Siga os passos abaixo:-

**Atualizar autónomo SCVMM 2012 R2 para o SCVMM de 2016**

1.  Fornecedor de desinstalação ASR ao navegar para o painel de controlo -> programas -> programas e funcionalidades -> Microsoft Azure Site Recovery e clique em desinstalar
2. [Manter a base de dados do SCVMM e atualizar o sistema operativo](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. Na **Adicionar-remover programas**, selecione **VMM** > **desinstalação**. b. Selecione **remover funcionalidades**e, em seguida, selecione V**servidor de gestão de MM e consola do VMM**. c. Na **opções de base de dados**, selecione **reter base de dados**. d. Reveja o resumo e clique em **desinstalação**.

4. [Instalar o VMM 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Inicie o SCVMM e verificar o estado de cada anfitriões sob **recursos de infraestrutura** separador. Clique em **atualizar** para obter o estado mais recente. Deverá ver o estado como "Requer atenção". 
17. Instalar a versão mais recente [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra) do SCVMM.
16. Instalar a versão mais recente [agente do serviço de recuperação do Azure (MARS) da Microsoft](https://aka.ms/latestmarsagent) em cada anfitrião do cluster. Atualize para garantir o que SCVMM é capaz de consultar com êxito os anfitriões.

**Atualizar os anfitriões do Windows Server 2012 R2 para o Windows Server 2016**

1. Siga os passos mencionados [aqui](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) para executar o processo de atualização sem interrupção do cluster. 
2. Depois de adicionar o novo anfitrião ao cluster, atualize o anfitrião a partir da consola do SCVMM para instalar o agente do VMM neste anfitrião atualizado.
3. Execute *VMVersion atualização* para atualizar as versões VM das máquinas virtuais. 
4.  Aceda ao portal do Azure e verifique se o estado de funcionamento replicados das máquinas virtuais no interior do Cofre de serviços de recuperação. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Anfitriões do Windows Server 2012 R2 atualização são geridos pelo servidor de elevada disponibilidade SCVMM 2012 R2
Antes de atualizar os anfitriões do Windows Server 2012 R2, tem de atualizar o SCVMM 2012 R2 para o SCVMM de 2016. Os seguintes modos de atualização são suportados durante a atualização de servidores do SCVMM 2012 R2 configurados com o Azure Site Recovery - modo misto sem servidores adicionais do VMM e o modo misto com servidores adicionais do VMM.

**Atualizar o SCVMM 2012 R2 para o SCVMM de 2016**

1.  Fornecedor de desinstalação ASR ao navegar para o painel de controlo -> programas -> programas e funcionalidades -> Microsoft Azure Site Recovery e clique em desinstalar
2. Siga os passos mencionados [aqui](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) com base no modo de atualização que pretende executar.
3. Iniciar a consola do SCVMM e verificar o estado de cada anfitriões sob **recursos de infraestrutura** separador. Clique em **atualizar** para obter o estado mais recente. Deverá ver o estado como "Requer atenção".
4. Instalar a versão mais recente [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra) do SCVMM.
5. Atualizar a versão mais recente [agente do serviço de recuperação do Azure (MARS) da Microsoft](https://aka.ms/latestmarsagent) em cada anfitrião do cluster. Atualize para garantir o que sc VMM é capaz de consultar com êxito os anfitriões.


**Atualizar os anfitriões do Windows Server 2012 R2 para o Windows Server 2016**

1. Siga os passos mencionados [aqui](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) para executar o processo de atualização sem interrupção do cluster.
2. Depois de adicionar o novo anfitrião ao cluster, atualize o anfitrião a partir da consola do SCVMM para instalar o agente do VMM neste anfitrião atualizado.
3. Execute *VMVersion atualização* para atualizar as versões VM das máquinas virtuais. 
4.  Aceda ao portal do Azure e verifique se o estado de funcionamento replicados das máquinas virtuais no interior do Cofre de serviços de recuperação. 

## <a name="next-steps"></a>Passos Seguintes
Após a atualização dos anfitriões for executada, pode realizar uma [ativação pós-falha de teste](tutorial-dr-drill-azure.md) para testar o estado de funcionamento do seu estado de recuperação após desastre e de replicação.

