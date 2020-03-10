---
title: Atualizações e atualizações de componentes na Recuperação do Site Azure
description: Fornece uma visão geral das atualizações do serviço de recuperação do site Azure e atualizações de componentes.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363037"
---
# <a name="service-updates-in-site-recovery"></a>Atualizações de serviço na Recuperação do Site

Este artigo fornece uma visão geral das atualizações de Recuperação do [Site Azure,](site-recovery-overview.md) e descreve como atualizar os componentes de Recuperação do Site.

O Site Recovery publica atualizações de serviço regularmente. As atualizações incluem novas funcionalidades, melhorias de suporte, atualizações de componentes e correções de bugs. Para aproveitar as funcionalidades e correções mais recentes, recomendamos executar as versões mais recentes dos componentes de Recuperação do Site. 
 
 
## <a name="updates-support"></a>Suporte de atualizações

### <a name="support-statement-for-azure-site-recovery"></a>Declaração de apoio à recuperação do site azure

Recomendamos sempre a atualização para as versões mais recentes dos componentes:

**Com cada nova versão 'N' de um componente azure de recuperação de sites que é lançado, todas as versões abaixo de 'N-4' são consideradas fora de suporte**. 

> [!IMPORTANT]
> O suporte oficial é para upgrade da versão > N-4 para n. Por exemplo, se estiver a correr está no N-6, precisa primeiro de fazer upgrade para N-4 e, em seguida, fazer upgrade para N.


### <a name="links-to-currently-supported-update-rollups"></a>Links para rollups de atualização atualmente suportados

 Reveja a última atualização (versão N) [neste artigo](site-recovery-whats-new.md). Lembre-se que a Recuperação do Site fornece suporte para versões N-4.



## <a name="component-expiry"></a>Expiração do componente

A Recuperação do Site notifica-o de componentes expirados (ou perto de expirar) por e-mail (se tiver subscrito notificações de e-mail), ou no painel de instrumentos do cofre no portal.

- Além disso, quando as atualizações estão disponíveis, na vista da infraestrutura para o seu cenário no portal, aparece um botão **Update disponível** junto ao componente. Este botão redireciona-o para um link para descarregar a versão mais recente do componente.
-  As notificações do painel de abóbadas não estão disponíveis se estiver a replicar VMs Hiper-V. 

As notificações de e-mail são enviadas da seguinte forma.

**Tempo** | **Frequência**
--- | ---
60 dias antes do termo do componente | Uma vez bi-semanalmente
Próximos 53 dias | Uma vez por semana
Últimos 7 dias | Uma vez por dia
Após o termo | Uma vez bi-semanalmente


### <a name="upgrading-outside-official-support"></a>Modernização do apoio oficial externo

Se a diferença entre a sua versão componente e a versão de lançamento mais recente for superior a quatro, esta é considerada fora de suporte. Neste caso, atualize da seguinte forma: 

1. Atualize o componente atualmente instalado para a sua versão atual mais quatro. Por exemplo, se a sua versão se 9.16, em seguida, atualizar para 9.20.
2. Em seguida, atualize para a próxima versão compatível. Assim, no nosso exemplo, depois de atualizar 9.16 para 9.20, upgrade para 9.24. 

Siga o mesmo processo para todos os componentes relevantes.

### <a name="support-for-latest-operating-systemskernels"></a>Suporte para os mais recentes sistemas operativos/núcleos

> [!NOTE]
> Se tiver uma janela de manutenção programada, e estiver incluído um reboot, recomendamos que atualize primeiro os componentes de Recuperação do Site e, em seguida, proceda com o resto das atividades programadas na janela de manutenção.

1. Antes de atualizar as versões do sistema operativo/kernel, verifique se a versão-alvo é suportada pela Recuperação do Site. 

    - [Suporte Azure VM.](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)
    - [Suporte vMware/servidor físico](vmware-physical-azure-support-matrix.md#replicated-machines)
    - [Suporte hiper-V.](hyper-v-azure-support-matrix.md#replicated-vms)
2. Reveja [as atualizações disponíveis](site-recovery-whats-new.md) para saber o que pretende fazer upgrade.
3. Atualize para a versão mais recente da Recuperação do Site.
4. Atualize o sistema operativo/núcleo para as versões necessárias.
5. Reiniciar.


Este processo garante que o sistema operativo/núcleo da máquina é atualizado para a versão mais recente, e que as mais recentes alterações de Recuperação do Site necessárias para suportar a nova versão são carregadas na máquina.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Recuperação após desastre da VM para o Azure

Neste cenário, recomendamos vivamente que [ative atualizações automáticas](azure-to-azure-autoupdate.md). Pode permitir que a Recuperação do Site gere as atualizações da seguinte forma:

- Durante o processo de replicação ativa.
- Ao definir as definições de atualização de extensão dentro do cofre.

Se pretender gerir manualmente as atualizações, faça o seguinte:

1. No cofre > **Itens Replicados,** clique nesta notificação na parte superior do ecrã: 
    
    **A atualização do agente de replicação de nova recuperação do site está disponível. Clique para instalar ->**

4. Selecione os VMs para os quais pretende aplicar a atualização e, em seguida, clique em **OK**.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware VM/recuperação de desastre de servidor físico para Azure

1. Com base na versão atual e na declaração de [suporte,](#support-statement-for-azure-site-recovery)instale a atualização primeiro no servidor de configuração no local, utilizando [estas instruções](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). 
2. Se tiver servidores de processo sem escala, atualize-os em seguida, utilizando [estas instruções](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Para atualizar o agente de Mobilidade em cada máquina protegida, consulte [este](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) artigo.

### <a name="reboot-after-mobility-service-upgrade"></a>Reiniciar após atualização do serviço de mobilidade

Recomenda-se o reinício após cada atualização do serviço mobility, para garantir que todas as alterações mais recentes são carregadas na máquina de origem.

Um reboot não é obrigatório, a menos que a diferença entre a versão do agente durante o último reboot, e a versão atual, seja maior do que quatro.

O exemplo na tabela mostra como isto funciona.

|**Versão do agente (última reinicialização)** | **Upgrade para** | **Reinicialização obrigatória?**|
|---------|---------|---------|
|9.16 |  9.18 | Não obrigatório|
|9.16 | 9.19 | Não obrigatório|
| 9.16 | 9.20 | Não obrigatório
 | 9.16 | 9.21 | Obrigatório.<br/><br/> Atualize para 9.20 e, em seguida, reinicie antes de atualizar para 9.21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Recuperação após desastre da VM do Hyper-V para o Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Entre um site Hyper-V e Azure

1. Faça o download da atualização para o Fornecedor de Recuperação do Site Microsoft Azure.
2. Instale o Fornecedor em cada servidor Hyper-V registado na Recuperação do Site. Se estiver a executar um cluster, atualize todos os nós do cluster.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Entre um site vMM no local e Azure
1. Faça o download da atualização para o Fornecedor de Recuperação do Site Microsoft Azure.
2. Instale o Fornecedor no servidor VMM. Se o VMM for implantado num cluster, instale o Fornecedor em todos os nós do cluster.
3. Instale o mais recente agente dos Serviços de Recuperação do Microsoft Azure em todos os anfitriões hiper-V ou nós de cluster.


## <a name="between-two-on-premises-vmm-sites"></a>Entre dois locais de VMM no local
1. Faça o download da mais recente atualização para o Fornecedor de Recuperação de Sites do Microsoft Azure.
2. Instale o mais recente Fornecedor no servidor VMM que gere o site de recuperação secundária. Se o VMM for implantado num cluster, instale o Fornecedor em todos os nós do cluster.
3. Depois de atualizado o site de recuperação, instale o Fornecedor no servidor VMM que gere o site principal.

## <a name="next-steps"></a>Passos Seguintes

Acompanhe a nossa página [de Atualizações Do Azure](https://azure.microsoft.com/updates/?product=site-recovery) para acompanhar novas atualizações e lançamentos.
