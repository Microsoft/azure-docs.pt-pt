---
title: Solucionar problemas de failback em recuperação de desastre de VM VMware com Azure Site Recovery
description: Este artigo descreve maneiras de solucionar problemas de failback e de nova proteção durante a recuperação de desastres de VM VMware no Azure com Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b597ecb67ab30c8617029fe741af1014444a9b70
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693145"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Solucionar problemas de failback para o local do Azure

Este artigo descreve como solucionar problemas que você pode encontrar ao realizar o failback de VMs do Azure para sua infraestrutura do VMware local, após o failover para o Azure usando o [Azure site Recovery](site-recovery-overview.md).

O failback envolve basicamente duas etapas principais. Para a primeira etapa, após o failover, você precisa proteger novamente as VMs do Azure para o local para que elas iniciem a replicação. A segunda etapa é executar um failover do Azure para fazer failback para o site local.

## <a name="troubleshoot-reprotection-errors"></a>Solucionar erros de nova proteção

Esta seção detalha os erros de nova proteção comuns e como corrigi-los.

### <a name="error-code-95226"></a>Código de erro 95226

**A nova proteção falhou porque a máquina virtual do Azure não pôde acessar o servidor de configuração local.**

Esse erro ocorre quando:

* A VM do Azure não pode acessar o servidor de configuração local. A VM não pode ser descoberta e registrada no servidor de configuração.
* O serviço de aplicativo InMage Scout não está em execução na VM do Azure após o failover. O serviço é necessário para comunicações com o servidor de configuração local.

Para resolver este problema:

* Verifique se a rede VM do Azure permite que a VM do Azure se comunique com o servidor de configuração local. Você pode configurar uma VPN site a site para seu datacenter local ou configurar uma conexão do Azure ExpressRoute com o emparelhamento privado na rede virtual da VM do Azure.
* Se a VM puder se comunicar com o servidor de configuração local, entre na VM. Em seguida, verifique o serviço de aplicativo InMage Scout. Se você perceber que ele não está em execução, inicie o serviço manualmente. Verifique se o tipo de inicialização do serviço está definido como **automático**.

### <a name="error-code-78052"></a>Código de erro 78052

**Não foi possível concluir a proteção para a máquina virtual.**

Esse problema pode ocorrer se já houver uma VM com o mesmo nome no servidor de destino mestre para o qual você está realizando o failback.

Para resolver este problema:

* Selecione um servidor de destino mestre diferente em um host diferente para que a nova proteção crie o computador em um host diferente, no qual os nomes não colidem.
* Você também pode usar o vMotion para mover o destino mestre para um host diferente em que a colisão de nome não acontecerá. Se a VM existente for um computador perdido, renomeie-a para que a nova VM possa ser criada no mesmo host ESXi.


### <a name="error-code-78093"></a>Código de erro 78093

**A VM não está em execução, está em um estado suspenso ou não está acessível.**

Para resolver este problema:

Para proteger novamente uma VM com failover, a VM do Azure deve estar em execução para que o serviço de mobilidade seja registrado no servidor de configuração local e possa iniciar a replicação comunicando-se com o servidor de processo. Se o computador estiver em uma rede incorreta ou não estiver em execução (não respondendo ou desligando), o servidor de configuração não poderá acessar o serviço de mobilidade na VM para iniciar a nova proteção.

* Reinicie a VM para que ela possa começar a se comunicar de volta no local.
* Reinicie o trabalho de nova proteção depois de iniciar a máquina virtual do Azure.

### <a name="error-code-8061"></a>Código de erro 8061

**O repositório de armazenamento não está acessível do host ESXi.**

Verifique os [pré-requisitos de destino mestre e os armazenamentos de dados com suporte](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) para failback.


## <a name="troubleshoot-failback-errors"></a>Solucionar problemas de erros de failback

Esta seção descreve os erros comuns que podem ser encontrados durante o failback.

### <a name="error-code-8038"></a>Código de erro 8038

**Falha ao abrir a máquina virtual local devido ao erro.**

Esse problema ocorre quando a VM local é colocada em um host que não tem memória suficiente. 

Para resolver este problema:

* Provisione mais memória no host ESXi.
* Além disso, você pode usar o vMotion para mover a VM para outro host ESXi com memória suficiente para inicializar a VM.
