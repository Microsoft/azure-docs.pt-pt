---
title: Atualizações e atualizações de componentes no Azure Site Recovery
description: Fornece uma visão geral de Azure Site Recovery atualizações de serviço e atualizações de componentes.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 305ff610c0f8962f2b50789d8af0a5f6e2fbf046
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717363"
---
# <a name="service-updates-in-site-recovery"></a>Atualizações de serviço no Site Recovery

Este artigo fornece uma visão geral de [Azure site Recovery](site-recovery-overview.md) atualizações e descreve como atualizar site Recovery componentes.

Site Recovery publica atualizações de serviço regularmente. As atualizações incluem novos recursos, melhorias de suporte, atualizações de componentes e correções de bugs. Para aproveitar os recursos e as correções mais recentes, recomendamos a execução das versões mais recentes dos componentes do Site Recovery. 
 
 
## <a name="updates-support"></a>Suporte a atualizações

### <a name="support-statement-for-azure-site-recovery"></a>Instrução de suporte para Azure Site Recovery

Recomendamos sempre atualizar para as versões de componente mais recentes:

**Com cada nova versão ' n' de um componente Azure site Recovery que é lançada, todas as versões abaixo de ' n-4 ' são consideradas sem suporte**. 

> [!IMPORTANT]
> O suporte oficial é para a atualização da versão > N-4 para a versão N. Por exemplo, se você estiver executando você estiver em N-6, precisará primeiro atualizar para N-4 e, em seguida, atualizar para N.


### <a name="links-to-currently-supported-update-rollups"></a>Links para pacotes cumulativos de atualizações com suporte no momento

 Examine o pacote cumulativo de atualizações mais recente (versão N) neste [artigo](site-recovery-whats-new.md). Lembre-se de que Site Recovery fornece suporte para versões N-4.



## <a name="component-expiry"></a>Expiração do componente

Site Recovery notifica sobre os componentes expirados (ou próximos à expiração) por email (se você assinou notificações por email) ou no painel do cofre no Portal.

- Além disso, quando as atualizações estão disponíveis, no modo de exibição de infraestrutura para seu cenário no portal, um botão **Atualizar disponível** é exibido ao lado do componente. Esse botão redireciona você para um link para baixar a versão mais recente do componente.
-  As notificações do painel de cofres não estarão disponíveis se você estiver replicando VMs do Hyper-V. 

As notificações de emails são enviadas da seguinte maneira.

**Momento** | **Frequência**
--- | ---
60 dias antes da expiração do componente | Uma vez por semana
Próximos 53 dias | Uma vez por semana
Últimos 7 dias | Uma vez por dia
Após a expiração | Uma vez por semana


### <a name="upgrading-outside-official-support"></a>Atualizando fora do suporte oficial

Se a diferença entre a versão do componente e a versão mais recente do lançamento for maior que quatro, isso será considerado sem suporte. Nesse caso, atualize da seguinte maneira: 

1. Atualize o componente atualmente instalado para a versão atual mais quatro. Por exemplo, se sua versão se 9,16, atualize para 9,20.
2. Em seguida, atualize para a próxima versão compatível. Então, em nosso exemplo, depois de atualizar 9,16 para 9,20, atualize para 9,24. 

Siga o mesmo processo para todos os componentes relevantes.

### <a name="support-for-latest-operating-systemskernels"></a>Suporte para os sistemas operacionais/kernels mais recentes

> [!NOTE]
> Se você tiver uma janela de manutenção agendada e uma reinicialização estiver incluída nela, recomendamos que primeiro atualize Site Recovery componentes e continue com o restante das atividades agendadas na janela de manutenção.

1. Antes de atualizar as versões do sistema operacional/kernel, verifique se a versão de destino tem suporte Site Recovery. 

    - Suporte de [VM do Azure](/azure-to-azure-support-matrix.md#replicated-machine-operating-systems) .
    - Suporte ao [VMware/servidor físico](vmware-physical-azure-support-matrix.md#replicated-machines)
    - Suporte do [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) .
2. Examine [as atualizações disponíveis](site-recovery-whats-new.md) para descobrir o que você deseja atualizar.
3. Atualize para a versão mais recente do Site Recovery.
4. Atualize o sistema operacional/kernel para as versões necessárias.
5. Inicialize.


Esse processo garante que o kernel/sistema operacional do computador seja atualizado para a versão mais recente e que as últimas alterações de Site Recovery necessárias para dar suporte à nova versão sejam carregadas no computador.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Recuperação após desastre da VM para o Azure

Nesse cenário, é altamente recomendável que você [habilite as atualizações automáticas](azure-to-azure-autoupdate.md). Você pode permitir que Site Recovery gerencie atualizações da seguinte maneira:

- Durante o processo de habilitação de replicação.
- Definindo as configurações de atualização de extensão no cofre.

Se você quiser gerenciar manualmente as atualizações, faça o seguinte:

1. No cofre > **itens replicados**, clique nessa notificação na parte superior da tela: 
    
    **O novo Site Recovery atualização do agente de replicação está disponível. Clique para instalar->**

4. Selecione as VMs para as quais você deseja aplicar a atualização e clique em **OK**.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Recuperação de desastre do servidor físico/VM do VMware no Azure

1. Com base na versão atual e na [instrução de suporte](#support-statement-for-azure-site-recovery), instale a atualização primeiro no servidor de configuração local, usando [estas instruções](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). 
2. Se você tiver servidores de processo de expansão, atualize-os em seguida usando [estas instruções](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Para atualizar o agente de mobilidade em cada computador protegido, abra itens **protegidos** > itens**replicados**.
4. Selecione a VM e selecione o botão **Atualizar agente** que aparece na parte inferior da página para cada VM. Isso atualiza o agente do serviço de mobilidade em todas as VMs protegidas.

### <a name="reboot-after-mobility-service-upgrade"></a>Reinicializar após a atualização do serviço de mobilidade

Uma reinicialização é recomendada após cada atualização do serviço de mobilidade, para garantir que todas as alterações mais recentes sejam carregadas no computador de origem.

Uma reinicialização não é obrigatória, a menos que a diferença entre a versão do agente durante a última reinicialização e a versão atual seja maior que quatro.

O exemplo na tabela mostra como isso funciona.

|**Versão do agente (última reinicialização)** | **Atualizar para o** | **Reinicialização obrigatória?**|
|---------|---------|---------|
|9,16 |  9,18 | Não obrigatório|
|9,16 | 9,19 | Não obrigatório|
| 9,16 | 9,20 | Não obrigatório
 | 9,16 | 9,21 | Obrigatório.<br/><br/> Atualize para 9,20 e reinicialize antes de atualizar para o 9,21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Recuperação após desastre da VM do Hyper-V para o Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Entre um site do Hyper-V e o Azure

1. Baixe a atualização para o provedor de Site Recovery de Microsoft Azure.
2. Instale o provedor em cada servidor Hyper-V registrado em Site Recovery. Se você estiver executando um cluster, atualize em todos os nós de cluster.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Entre um site do VMM local e o Azure
1. Baixe a atualização para o provedor de Site Recovery de Microsoft Azure.
2. Instale o provedor no servidor do VMM. Se o VMM for implantado em um cluster, instale o provedor em todos os nós de cluster.
3. Instale o agente de Serviços de Recuperação do Microsoft Azure mais recente em todos os hosts ou nós de cluster do Hyper-V.


## <a name="between-two-on-premises-vmm-sites"></a>Entre dois sites do VMM local
1. Baixe a atualização mais recente para o provedor de Site Recovery de Microsoft Azure.
2. Instale o provedor mais recente no servidor do VMM que gerencia o site de recuperação secundário. Se o VMM for implantado em um cluster, instale o provedor em todos os nós de cluster.
3. Depois que o site de recuperação for atualizado, instale o provedor no servidor do VMM que está gerenciando o site primário.

## <a name="next-steps"></a>Passos Seguintes

Siga nossa página de [atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery) para acompanhar novas atualizações e versões.
