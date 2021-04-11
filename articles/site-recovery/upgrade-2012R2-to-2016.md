---
title: Atualizar servidor do Windows/System Center VMM 2012 R2 para recuperação do site do Windows Server 2016-Azure
description: Saiba como atualizar os anfitriões do Windows Server 2012 R2 & SCVMM 2012 R2 que estão configurados com Azure Site Recovery, para Windows Server 2016 & SCVMM 2016.
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: sharrai
ms.openlocfilehash: b3df487d690befadd249142c449163c2393f6df6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640327"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Upgrade Servidor do Servidor do Windows/Centro do Sistema 2012 R2 VMM para Windows Server/VMM 2016 

Este artigo mostra como atualizar os anfitriões do Windows Server 2012 R2 & SCVMM 2012 R2 que estão configurados com Azure Site Recovery, para Windows Server 2016 & SCVMM 2016

A Recuperação do Site contribui para a estratégia de continuidade do seu negócio e recuperação de desastres (BCDR). O serviço garante que as suas cargas de trabalho em VM permanecem disponíveis quando ocorrem interrupções esperadas e inesperadas.

> [!IMPORTANT]
> Quando atualizar os anfitriões R2 do Windows Server 2012 que já estão configurados para replicação com a Recuperação do Site Azure, deve seguir os passos mencionados neste documento. Qualquer caminho alternativo escolhido para atualização pode resultar em estados não apoiados e pode resultar numa rutura na replicação ou capacidade de executar falhas.


Neste artigo, aprende a atualizar as seguintes configurações no seu ambiente:

> [!div class="checklist"]
> * **Anfitriões R2 do Windows Server 2012 que não são geridos pela SCVMM** 
> * **Servidores Windows Server 2012 R2 que são geridos por um servidor SCVMM 2012 R2 autónomo** 
> * **Servidores R2 do Windows Server 2012 que são geridos por servidor SCVMM 2012 R2 altamente disponível**


## <a name="prerequisites--factors-to-consider"></a>Pré-requisitos & fatores a ter em conta

Antes de atualizar, tome nota do seguinte: -

- Se tiver anfitriões R2 do Windows Server 2012 que não são geridos pela SCVMM, e é uma configuração de ambiente autónomo, haverá uma rutura na replicação se tentar realizar a atualização.
- Se tivesse selecionado "*não armazenar as minhas Chaves em Ative Directory em Gestão de Chave Distribuída*" enquanto instalava o SCVMM 2012 R2 em primeiro lugar, as atualizações não serão concluídas com sucesso.

- Se estiver a utilizar o System Center 2012 R2 VMM, 

    - Consulte as informações da base de dados em VMM: Definições de **consola VMM**  ->    ->    ->  **Ligação Geral base de dados**
    - Verifique as contas de serviço que estão a ser utilizadas para o serviço de agente de gestor de máquinas virtuais do System Center
    - Certifique-se de que tem uma cópia de segurança da Base de Dados VMM.
    - Note o nome da base de dados dos servidores SCVMM envolvidos. Isto pode ser feito navegando para a **consola VMM**  ->  **Definições**  ->    ->  **Ligação Geral de Base de Dados**
    - Note para baixo o ID VMM dos servidores VMM primário e de recuperação de 2012R2. O ID VMM pode ser encontrado a partir do registo "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Certifique-se de que os novos SCVMMs que adiciona ao cluster têm os mesmos nomes que antes. 

- Se estiver a replicar entre dois dos seus sites geridos por SCVMMs de ambos os lados, certifique-se de que atualiza primeiro o seu lado de recuperação antes de atualizar o lado principal.
  > [!WARNING]
  > Ao atualizar o SCVMM 2012 R2, em Gestão de Chaves Distribuídas, selecione para **armazenar chaves de encriptação no Ative Directory**. Escolha cuidadosamente as definições para a conta de serviço e distribua cuidadosamente a gestão da chave. Com base na sua seleção, dados encriptados como palavras-passe em modelos podem não estar disponíveis após a atualização, e podem potencialmente afetar a replicação com a Recuperação do Site Azure

> [!IMPORTANT]
> Consulte a documentação detalhada do SCVMM de [pré-requisitos](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Anfitriões R2 do Windows Server 2012 que não são geridos pela SCVMM 
A lista de etapas abaixo referidas aplica-se à configuração do utilizador de [anfitriões Hiper-V a Azure](./hyper-v-azure-architecture.md) executados seguindo este [tutorial](./hyper-v-prepare-on-premises-tutorial.md)

> [!WARNING]
> Como mencionado nos pré-requisitos, estes passos aplicam-se apenas a um cenário de ambiente agrupado, e não a uma configuração autónoma do anfitrião Hyper-V.

1. Siga os passos para realizar a atualização do [cluster rolante.](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) para executar o processo de atualização do cluster rolante.
2. Com cada novo anfitrião do Windows Server 2016 que é introduzido no cluster, remova a referência de um anfitrião R2 do Windows Server 2012 da Azure Site Recovery seguindo os passos mencionados [aqui]. Este deve ser o anfitrião que escolheu para drenar & despejar do aglomerado.
3. Uma vez executado o comando *Update-VMVersion* para todas as máquinas virtuais, as atualizações foram concluídas. 
4. Utilize os passos [aqui](./hyper-v-azure-tutorial.md#set-up-the-source-environment) mencionados para registar o novo anfitrião do Windows Server 2016 para a Recuperação do Site Azure. Por favor, note que o site Hyper-V já está ativo e só precisa de registar o novo hospedeiro no cluster. 
5. Vá ao portal Azure e verifique o estado de saúde replicado dentro dos Serviços de Recuperação

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Upgrade Windows Server 2012 R2 hosts geridos por servidor SCVMM 2012 R2 autónomo
Antes de atualizar os seus anfitriões Windows Sever 2012 R2, tem de atualizar o SCVMM 2012 R2 para SCVMM 2016. Siga os passos abaixo:-

**Upgrade autónomo SCVMM 2012 R2 para SCVMM 2016**

1.  Desinstale o fornecedor ASR navegando para Control Panel -> Programas e Funcionalidades -> ->Recuperação do Site microsoft Azure , e clique em Desinstalar
2. [Reter a base de dados SCVMM e atualizar o sistema operativo](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#back-up-and-upgrade-the-operating-system)
3. In **Adicionar programas de remoção**, selecione **VMM**  >  **Desinstalar**. b. Selecione **Remover Funcionalidades** e, em seguida, selecione **V MM management Server e VMM Console**. c. Nas **Opções de Base de Dados,** selecione **Retena a base de dados**. d. Reveja o resumo e clique em **Desinstalar**.

4. [Instalar VMM 2016](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#install-vmm-2016)
5. Lance o SCVMM e verifique o estado de verificação de cada anfitrião no **separador Tecidos.** Clique **em Refresh** para obter o estado mais recente. Devia ver o estatuto de "Precisa de atenção". 
17.    Instale o mais recente [Provedor de Recuperação do Site microsoft Azure](https://aka.ms/downloaddra) no SCVMM.
16.    Instale o mais recente [agente do Microsoft Azure Recovery Service (MARS)](https://aka.ms/latestmarsagent) em cada anfitrião do cluster. Refresque-se para garantir que o SCVMM seja capaz de consultar com sucesso os anfitriões.

**Upgrade Windows Server 2012 R2 anfitriões para Windows Server 2016**

1. Siga os passos [aqui](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) mencionados para executar o processo de upgrade do cluster rolante. 
2. Depois de adicionar o novo anfitrião ao cluster, refresque o anfitrião da consola SCVMM para instalar o Agente VMM neste anfitrião atualizado.
3. Execute *actualização-VMVersion* para atualizar as versões VM das máquinas Virtuais. 
4. Vá ao portal Azure e verifique o estado de saúde replicado das máquinas virtuais dentro do Cofre dos Serviços de Recuperação. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Os anfitriões R2 do Windows Server 2012 são geridos por servidor SCVMM 2012 R2 altamente disponível
Antes de atualizar os seus anfitriões Windows Sever 2012 R2, tem de atualizar o SCVMM 2012 R2 para SCVMM 2016. Os seguintes modos de atualização são suportados ao atualizar os servidores SCVMM 2012 R2 configurados com Azure Site Recovery - Modo misto sem servidores VMM adicionais & modo misto com servidores VMM adicionais.

**Upgrade SCVMM 2012 R2 para SCVMM 2016**

1.  Desinstale o fornecedor ASR navegando para Control Panel -> Programas e Funcionalidades -> ->Recuperação do Site microsoft Azure , e clique em Desinstalar
2. Siga os passos [aqui](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#upgrade-a-standalone-vmm-server) mencionados com base no modo de atualização que pretende executar.
3. Lance a consola SCVMM e verifique o estado de cada anfitrião no **separador Fabrics.** Clique **em Refresh** para obter o estado mais recente. Devia ver o estatuto de "Precisa de atenção".
4. Instale o mais recente [Provedor de Recuperação do Site microsoft Azure](https://aka.ms/downloaddra) no SCVMM.
5. Atualize o mais recente [agente do Microsoft Azure Recovery Service (MARS)](https://aka.ms/latestmarsagent) em cada anfitrião do cluster. Refresque-se para garantir que o SC VMM seja capaz de consultar com sucesso os anfitriões.


**Upgrade Windows Server 2012 R2 anfitriões para Windows Server 2016**

1. Siga os passos [aqui](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) mencionados para executar o processo de upgrade do cluster rolante.
2. Depois de adicionar o novo anfitrião ao cluster, refresque o anfitrião da consola SCVMM para instalar o Agente VMM neste anfitrião atualizado.
3. Execute *actualização-VMVersion* para atualizar as versões VM das máquinas Virtuais. 
4. Vá ao portal Azure e verifique o estado de saúde replicado das máquinas virtuais dentro do Cofre dos Serviços de Recuperação. 

## <a name="next-steps"></a>Passos seguintes
Uma vez realizada a atualização dos anfitriões, pode efetuar um [teste falhado](tutorial-dr-drill-azure.md) para testar a saúde da sua replicação e estado de recuperação de desastres.
