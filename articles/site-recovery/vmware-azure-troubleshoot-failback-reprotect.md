---
title: Falha na recuperação de desastres vMware VM com recuperação do site Azure
description: Este artigo descreve formas de resolução de problemas de falhas e problemas de reproteção durante a recuperação de desastres VMware VM para Azure com a Recuperação do Site Azure.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b577b82585ffad0547818b4f19554a2f39cb830c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498098"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Resolver problemas de reativação pós-falha no local a partir do Azure

Este artigo descreve como resolver problemas que poderá encontrar quando reinseria os VMs Azure para a sua infraestrutura vMware no local, depois de ter falhado com o Azure utilizando a Recuperação do [Site Azure.](site-recovery-overview.md)

O failback envolve essencialmente dois passos principais. Para o primeiro passo, depois do fracasso, é necessário reproteger os VMs Azure para as instalações para que comecem a replicar-se. O segundo passo é executar uma falha do Azure para falhar no seu local no local.

## <a name="common-issues"></a>Problemas comuns

- Se realizar uma descoberta vCenter de utilizador apenas de leitura e proteger máquinas virtuais, a proteção tem sucesso e falha os trabalhos. Durante a reproteção, falha porque as lojas de dados não podem ser descobertas. Um sintoma é que as lojas de dados não estão listadas durante a reproteção. Para resolver este problema, pode atualizar as credenciais vCenter com uma conta apropriada que tem permissões e, em seguida, voltar a tentar o trabalho.
- Quando falha uma máquina virtual Linux e a executa no local, pode ver que o pacote Do Gestor de Rede foi desinstalado da máquina. Esta desinstalação ocorre porque o pacote Do Gestor de Rede é removido quando a máquina virtual é recuperada em Azure.
- Quando uma máquina virtual Linux é configurada com um endereço IP estático e é falhada no Azure, o endereço IP é adquirido a DHCP. Quando falha no local, a máquina virtual continua a utilizar o DHCP para adquirir o endereço IP. Inscreva-se manualmente na máquina e, em seguida, volte a colocar o endereço IP num endereço estático, se necessário. Uma máquina virtual do Windows pode voltar a adquirir o seu endereço IP estático.
- Se utilizar a edição gratuita ESXi 5.5 ou a edição gratuita do Hypervisor vSphere 6, o failover tem sucesso, mas o failback não tem sucesso. Para permitir o reversão, atualize para a licença de avaliação de qualquer um dos programas.
- Se não conseguir contactar o servidor de configuração a partir do servidor de processos, utilize o Telnet para verificar a conectividade com o servidor de configuração na porta 443. Também pode tentar obter o servidor de configuração a partir do servidor de processos. Um servidor de processo também deve ter um batimento cardíaco quando está ligado ao servidor de configuração.
- Um servidor R2 SP1 do Windows Server 2008 que está protegido como um servidor físico no local não pode ser falhado de volta do Azure para um site no local.
- Não pode falhar nas seguintes circunstâncias:
    - Emigrou máquinas para Azure. [Saiba mais](migrate-overview.md#what-do-we-mean-by-migration).
    - Mudaste um VM para outro grupo de recursos.
    - Apagou o VM Azure.
    - Desativou a proteção do VM.
    - Criaste o VM manualmente em Azure. A máquina deveria ter sido inicialmente protegida no local e não ter conseguido a Segurança Azul antes da reprotecção.
    - Só pode falhar com um hospedeiro esxi. Não é possível falhar vMware VMs ou servidores físicos para anfitriões hiper-V, máquinas físicas ou estações de trabalho VMware.


## <a name="troubleshoot-reprotection-errors"></a>Erros de reproteção de resolução de problemas

Esta secção detalha erros comuns de reproteção e como corrigi-los.

### <a name="error-code-95226"></a>Código de erro 95226

**A reproteção falhou, uma vez que a máquina virtual Azure não foi capaz de chegar ao servidor de configuração no local.**

Este erro ocorre quando:

* O Azure VM não consegue chegar ao servidor de configuração no local. O VM não pode ser descoberto e registado no servidor de configuração.
* O serviço de aplicações InMage Scout não está a funcionar no Azure VM depois de ter falhado. O serviço é necessário para comunicações com o servidor de configuração no local.

Para resolver este problema:

* Verifique se a rede Azure VM permite ao Azure VM comunicar com o servidor de configuração no local. Pode configurar uma VPN site-to-site para o seu datacenter no local ou configurar uma ligação Azure ExpressRoute com o peering privado na rede virtual do VM Azure.
* Se o VM conseguir comunicar com o servidor de configuração no local, inscreva-se no VM. Em seguida, verifique o serviço de aplicação InMage Scout. Se vir que não está a funcionar, inicie o serviço manualmente. Verifique se o tipo de início de serviço está definido para **Automático**.

### <a name="error-code-78052"></a>Código de erro 78052

**A proteção não podia ser completada para a máquina virtual.**

Este problema pode acontecer se já houver um VM com o mesmo nome no servidor de alvo principal ao qual está a falhar.

Para resolver este problema:

* Selecione um servidor de alvo principal diferente num hospedeiro diferente para que a reproteção crie a máquina num hospedeiro diferente, onde os nomes não colidem.
* Também pode usar a vMotion para mover o alvo principal para outro hospedeiro onde a colisão do nome não acontecerá. Se o VM existente for uma máquina de saque, mude o nome para que o novo VM possa ser criado no mesmo hospedeiro ESXi.


### <a name="error-code-78093"></a>Código de erro 78093

**O VM não está a funcionar, num estado suspenso, ou não está acessível.**

Para resolver este problema:

Para reproteger um VM falhado, o VM Azure deve estar em execução para que o Serviço de Mobilidade se registe com o servidor de configuração no local e possa começar a replicar comunicando com o servidor de processo. Se a máquina estiver numa rede incorreta ou não estiver a funcionar (não respondendo ou desligada), o servidor de configuração não pode contactar o Serviço de Mobilidade no VM para iniciar a reproteção.

* Reinicie o VM para que possa voltar a comunicar no local.
* Reinicie o trabalho de reproteção depois de iniciar a máquina virtual Azure.

### <a name="error-code-8061"></a>Código de erro 8061

**A loja de dados não é acessível a partir do hospedeiro ESXi.**

Verifique os [pré-requisitos](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) do alvo principal e as lojas de dados suportadas para obter o seu recuo.


## <a name="troubleshoot-failback-errors"></a>Erros de reprovação de resolução de problemas

Esta secção descreve erros comuns que pode encontrar durante o failback.

### <a name="error-code-8038"></a>Código de erro 8038

**Não conseguiu trazer a máquina virtual no local devido ao erro.**

Esta questão acontece quando o VM no local é criado num hospedeiro que não tem memória suficiente. 

Para resolver este problema:

* Fornecer mais memória no hospedeiro ESXi.
* Além disso, pode usar o vMotion para mover o VM para outro hospedeiro ESXi que tenha memória suficiente para iniciar o VM.
