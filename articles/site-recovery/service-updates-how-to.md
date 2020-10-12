---
title: Atualizações e atualizações de componentes na Recuperação do Site Azure
description: Fornece uma visão geral das atualizações do serviço de recuperação do site Azure e atualizações de componentes.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: ramamill
ms.openlocfilehash: a1ea8b6fb9800d796670161288be0d86ce6ffc42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89424944"
---
# <a name="service-updates-in-site-recovery"></a>Atualizações de serviço na Recuperação do Site

Este artigo fornece uma visão geral das atualizações de [Recuperação do Site Azure](site-recovery-overview.md) e descreve como atualizar os componentes de Recuperação do Site.

A Recuperação do Site publica atualizações de serviço regularmente. As atualizações incluem novas funcionalidades, melhorias de suporte, atualizações de componentes e correções de bugs. Para tirar partido das funcionalidades e correções mais recentes, recomendamos a execução das versões mais recentes dos componentes de Recuperação do Site. 
 
 
## <a name="updates-support"></a>Suporte de atualizações

### <a name="support-statement-for-azure-site-recovery"></a>Declaração de apoio para recuperação do local de Azure

Recomendamos sempre o upgrade para as versões mais recentes dos componentes:

**Com cada nova versão 'N' de um componente de Recuperação do Site Azure que é lançado, todas as versões abaixo de 'N-4' são consideradas fora de suporte**. 

> [!IMPORTANT]
> O suporte oficial é para a atualização de > versão N-4 para a versão N. Por exemplo, se estiveres a correr estás na N-6, precisas primeiro de atualizar para N-4 e depois fazer upgrade para N.


### <a name="links-to-currently-supported-update-rollups"></a>Links para rollups de atualização suportados atualmente

 Reveja a última atualização (versão N) [neste artigo](site-recovery-whats-new.md). Lembre-se que a Recuperação do Site fornece suporte para versões N-4.



## <a name="component-expiry"></a>Expiração do componente

A Recuperação do Site notifica-o de componentes expirados (ou que se aproximam) por e-mail (se subscrever as notificações por e-mail), ou no painel de abóbadas do portal.

- Além disso, quando estão disponíveis atualizações, na vista de infraestrutura para o seu cenário no portal, aparece um botão **disponíveis atualização** ao lado do componente. Este botão redireciona-o para um link para descarregar a versão mais recente do componente.
-  As notificações do painel de abóbadas não estão disponíveis se estiveres a replicar VMs Hiper-V. 

As notificações de e-mail são enviadas da seguinte forma.

**Time** | **Frequência**
--- | ---
60 dias antes da expiração do componente | Uma vez bi-semanal
Próximos 53 dias | Uma vez por semana
Últimos 7 dias | Uma vez por dia
Após a expiração | Uma vez bi-semanal


### <a name="upgrading-outside-official-support"></a>Modernização do apoio oficial externo

Se a diferença entre a versão do componente e a versão mais recente de lançamento for superior a quatro, esta é considerada fora do suporte. Neste caso, atualização da seguinte forma: 

1. Atualize o componente atualmente instalado para a sua versão atual mais quatro. Por exemplo, se a sua versão se 9.16, então atualize para 9.20.
2. Em seguida, atualize para a próxima versão compatível. Assim, no nosso exemplo, depois de atualizar 9.16 para 9.20, atualização para 9.24. 

Siga o mesmo processo para todos os componentes relevantes.

### <a name="support-for-latest-operating-systemskernels"></a>Suporte para sistemas operativos/núcleos operativos mais recentes

> [!NOTE]
> Se tiver uma janela de manutenção programada e um reboot incluído nele, recomendamos que atualize primeiro os componentes da Recuperação do Local e, em seguida, proceda com o resto das atividades programadas na janela de manutenção.

1. Antes de atualizar as versões do sistema operativo/kernel, verifique se a versão alvo é suportada recuperação do local. 

    - [Suporte Azure VM.](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)
    - [Suporte vMware/servidor físico](vmware-physical-azure-support-matrix.md#replicated-machines)
    - [Suporte hiper-V.](hyper-v-azure-support-matrix.md#replicated-vms)
2. [Reveja as atualizações disponíveis](site-recovery-whats-new.md) para saber o que pretende atualizar.
3. Atualize para a versão mais recente da Recuperação do Site.
4. Atualize o sistema operativo/núcleo para as versões necessárias.
5. Reiniciar.


Este processo garante que o sistema operativo/kernel da máquina é atualizado para a versão mais recente e que as mais recentes alterações de Recuperação do Site necessárias para suportar a nova versão são carregadas na máquina.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Recuperação após desastre da VM para o Azure

Neste cenário, recomendamos vivamente que [permita atualizações automáticas](azure-to-azure-autoupdate.md). Pode permitir que a Recuperação do Site gere as atualizações da seguinte forma:

- Durante o processo de replicação de ativação.
- Definindo as definições de atualização de extensão no interior do cofre.

Se pretender gerir manualmente as atualizações, poderá escolher uma das seguintes opções:

1. Quando uma nova atualização do agente está disponível, a Recuperação do Site fornece uma notificação no cofre para o topo da página. No cofre > **Itens Replicados,** clique nesta notificação na parte superior do ecrã: 
    
    **A nova atualização do agente de replicação da recuperação do site está disponível. Clique para instalar ->** <br/><br/>Selecione os VMs para os quais pretende aplicar a atualização e, em seguida, clique em **OK**.

2. Na página geral de recuperação de desastres VM, encontrará o campo 'Agente status' que dirá "Atualização Crítica" se o agente tiver de expirar. Clique nele e siga as instruções seguintes para atualizar manualmente a máquina virtual.

## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware VM/recuperação de desastres de servidor físico para Azure

1. Com base na versão atual e na [declaração de suporte,](#support-statement-for-azure-site-recovery)instale a atualização primeiro no servidor de configuração no local, utilizando [estas instruções](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). 
2. Se tiver servidores de processo de escala, atualize-os em seguida, utilizando [estas instruções](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Para atualizar o agente mobility em cada máquina protegida, consulte [este](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) artigo.

### <a name="reboot-after-mobility-service-upgrade"></a>Reiniciar após atualização do serviço de mobilidade

Recomenda-se um reboot após cada atualização do serviço mobility, para garantir que todas as alterações mais recentes sejam carregadas na máquina de origem.

Um reboot não é obrigatório, a menos que a diferença entre a versão do agente durante o último reboot, e a versão atual, seja superior a quatro.

O exemplo na tabela mostra como isto funciona.

|**Versão do agente (último reboot)** | **Atualizar para o** | **Reinicialização obrigatória?**|
|---------|---------|---------|
|9.16 |  9.18 | Não é obrigatório|
|9.16 | 9.19 | Não é obrigatório|
| 9.16 | 9.20 | Não é obrigatório
 | 9.16 | 9.21 | Obrigatório.<br/><br/> Atualize para 9.20 e, em seguida, reinicie antes de atualizar para 9.21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Recuperação após desastre da VM do Hyper-V para o Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Entre um site de Hiper-V e Azure

1. Descarregue a atualização para o Microsoft Azure Site Recovery Provider.
2. Instale o Fornecedor em cada servidor Hiper-V registado na Recuperação do Site. Se estiver a executar um cluster, atualize todos os nós de cluster.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Entre um local de VMM e Azure
1. Descarregue a atualização para o Microsoft Azure Site Recovery Provider.
2. Instale o Fornecedor no servidor VMM. Se o VMM for implantado num cluster, instale o Fornecedor em todos os nós do cluster.
3. Instale o mais recente agente dos Serviços de Recuperação do Microsoft Azure em todos os anfitriões Hiper-V ou nos nós de cluster.


## <a name="between-two-on-premises-vmm-sites"></a>Entre dois locais de VMM no local
1. Descarregue a mais recente atualização para o Microsoft Azure Site Recovery Provider.
2. Instale o mais recente Fornecedor no servidor VMM que gere o local de recuperação secundária. Se o VMM for implantado num cluster, instale o Fornecedor em todos os nós do cluster.
3. Após a atualização do site de recuperação, instale o Fornecedor no servidor VMM que está a gerir o site principal.

## <a name="next-steps"></a>Passos seguintes

Siga a nossa página [Azure Updates](https://azure.microsoft.com/updates/?product=site-recovery) para acompanhar novas atualizações e lançamentos.
