---
title: Reativação pós-falha para o local de resolução de problemas durante a recuperação após desastre de VM de VMware para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve as opções para solucionar problemas de reativação pós-falha e a nova proteção durante a recuperação após desastre de VM de VMware para o Azure com o Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 20cb7a446befb1d31f0e069d91d0230fc4a2a901
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565604"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Resolver problemas de reativação pós-falha para o local do Azure

Este artigo descreve como resolver problemas que poderão surgir quando realizar a ativação VMs atrás do Azure à sua infraestrutura de VMware no local, após a ativação pós-falha para o Azure, utilizando [do Azure Site Recovery](site-recovery-overview.md).

Reativação pós-falha essencialmente envolve dois passos principais. Para o primeiro passo, após a ativação pós-falha, terá de voltar a proteger as VMs do Azure para o local para que comecem a ser replicadas. O segundo passo é executar uma ativação pós-falha do Azure para reativação pós-falha para o seu site no local.

## <a name="troubleshoot-reprotection-errors"></a>Resolver erros de nova proteção

Esta secção fornece detalhes sobre os erros de nova proteção comuns e como corrigi-las.

### <a name="error-code-95226"></a>Código de erro 95226

**Voltar a proteger falhou uma vez que a máquina virtual do Azure não conseguiu aceder ao servidor de configuração no local.**

Este erro ocorre quando:

* A VM do Azure não é possível contactar o servidor de configuração no local. A VM não pode ser detetada e registrada no servidor de configuração.
* O serviço InMage Scout application não está em execução na VM do Azure após a ativação pós-falha. O serviço é necessário para comunicações com o servidor de configuração no local.

Para resolver este problema:

* Verifique que a rede de VM do Azure permite que a VM do Azure comunicar com o servidor de configuração no local. Pode configurar uma VPN de site a site para o seu datacenter no local ou configurar uma ligação do ExpressRoute do Azure com o peering privado na rede virtual da VM do Azure.
* Se a VM pode comunicar com o servidor de configuração no local, inicie sessão na VM. Em seguida, verifique o serviço InMage Scout application. Se vir que ele não está em execução, inicie o serviço manualmente. Verificação de que tipo de iniciar o serviço está definida como **automática**.

### <a name="error-code-78052"></a>Código de erro 78052

**Não foi possível concluir a proteção da máquina virtual.**

Este problema pode acontecer se já existe uma VM com o mesmo nome no servidor de destino mestre para o qual está a reativar novamente.

Para resolver este problema:

* Selecione um servidor de destino mestre diferente num host diferente para que a nova proteção cria a máquina num anfitrião diferente, onde os nomes não entrar em conflito.
* Também pode utilizar vMotion para mover o destino principal para outro anfitrião onde a colisão de nomes não acontecerá. Se a VM existente for uma máquina stray, renomeá-lo para que a nova VM pode ser criada no mesmo anfitrião ESXi.


### <a name="error-code-78093"></a>Código de erro 78093

**A VM não está em execução, em estado suspenso, ou não está acessível.**

Para resolver este problema:

Para voltar a proteger uma VM com ativação pós-falha, a VM do Azure tem de executar para que os registros do serviço de mobilidade com o configuration server no local e pode iniciar a replicação através da comunicação com o servidor de processos. Se a máquina estiver numa rede incorreta ou não está em execução (não responder ou desligado), o servidor de configuração não é possível contactar o serviço de mobilidade na VM para começar a nova proteção.

* Reinicie a VM para que possa começar a se comunicar ao site no local.
* Reinicie a tarefa de reproteção depois de iniciar a máquina virtual do Azure.

### <a name="error-code-8061"></a>Código de erro 8061

**O arquivo de dados não está acessível a partir do anfitrião ESXi.**

Verifique os [dominar os pré-requisitos de destino e arquivos de dados suportados](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) para reativação pós-falha.


## <a name="troubleshoot-failback-errors"></a>Resolver erros de reativação pós-falha

Esta secção descreve os erros comuns que poderá encontrar durante a reativação pós-falha.

### <a name="error-code-8038"></a>Código de erro 8038

**Falha ao colocar a máquina de virtual no local devido ao erro.**

Este problema ocorre quando a VM no local é colocada num anfitrião que não tem memória suficiente aprovisionada. 

Para resolver este problema:

* Aprovisione mais memória no anfitrião ESXi.
* Além disso, pode utilizar o vMotion para mover a VM para outro anfitrião ESXi que tenha memória suficiente para efetuar o arranque da VM.
