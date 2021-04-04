---
title: Falha na resolução de problemas na recuperação de desastres em VMware VM com recuperação do local de Azure
description: Este artigo descreve formas de resolver problemas de falha e problemas de reprotecção durante a recuperação de desastres VMware VM para Azure com Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: ed4e52470264441a99c5ccf0a736bb00233510c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87423121"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Resolver problemas de reativação pós-falha no local a partir do Azure

Este artigo descreve como resolver problemas que pode encontrar quando falha os VMs do Azure para a sua infraestrutura VMware no local, depois de falhar para a Azure utilizando [a Recuperação do Local de Azure](site-recovery-overview.md).

O failback envolve essencialmente dois passos principais. Para o primeiro passo, após o failover, é necessário reprotectar os VMs do Azure para o local, para que comecem a replicar-se. O segundo passo é executar uma falha de Azure para falhar de volta ao seu local.

## <a name="common-issues"></a>Problemas comuns

- Se executar uma descoberta vCenter apenas de leitura e proteger máquinas virtuais, a proteção tem sucesso e funciona. Durante a reproteção, o failover falha porque as datastores não podem ser descobertas. Um sintoma é que as lojas de dados não estão listadas durante a reproteção. Para resolver este problema, pode atualizar as credenciais do vCenter com uma conta apropriada que tenha permissões e, em seguida, voltar a tentar o trabalho.
- Quando falhas numa máquina virtual Linux e a colocas no local, podes ver que o pacote do Network Manager foi desinstalado da máquina. Esta desinstalação ocorre porque o pacote Network Manager é removido quando a máquina virtual é recuperada em Azure.
- Quando uma máquina virtual Linux é configurada com um endereço IP estático e é falhada em Azure, o endereço IP é adquirido a DHCP. Quando falha no local, a máquina virtual continua a utilizar o DHCP para adquirir o endereço IP. Inscreva-se manualmente na máquina e, em seguida, volte a definir o endereço IP para um endereço estático, se necessário. Uma máquina virtual Windows pode voltar a adquirir o seu endereço IP estático.
- Se utilizar a edição gratuita ESXi 5.5 ou a edição gratuita vSphere 6 Hypervisor, o failover tem sucesso, mas o failback não consegue. Para ativar o failback, atualize a licença de avaliação de qualquer um dos programas.
- Se não conseguir alcançar o servidor de configuração a partir do servidor de processo, utilize a Telnet para verificar a conectividade do servidor de configuração na porta 443. Também pode tentar extrair o servidor de configuração do servidor de processo. Um servidor de processo também deve ter um batimento cardíaco quando está ligado ao servidor de configuração.
- Um servidor R2 SP1 do Windows Server 2008 que esteja protegido como um servidor físico no local não pode ser falhado de volta do Azure para um site no local.
- Não pode falhar nas seguintes circunstâncias:
    - Emigrou as máquinas para Azure. 
    - Mudaste um VM para outro grupo de recursos.
    - Apagou o Azure VM.
    - Desativaste a proteção do VM.
    - Criou o VM manualmente em Azure. A máquina deveria ter sido inicialmente protegida no local e ter falhado em Azure antes da reproteção.
    - Só pode falhar com um anfitrião ESXi. Não é possível falhar VMware VMs ou servidores físicos para anfitriões Hiper-V, máquinas físicas ou estações de trabalho VMware.


## <a name="troubleshoot-reprotection-errors"></a>Erros de reprotecção de resolução de problemas

Esta secção detalha erros comuns de reproteção e como corrigi-los.

### <a name="error-code-95226"></a>Código de erro 95226

**A Reprotect falhou uma vez que a máquina virtual Azure não foi capaz de chegar ao servidor de configuração no local.**

Este erro ocorre quando:

* O Azure VM não consegue chegar ao servidor de configuração no local. O VM não pode ser descoberto e registado no servidor de configuração.
* O serviço de aplicação inMage Scout não está a funcionar no Azure VM após o fracasso. O serviço é necessário para comunicações com o servidor de configuração no local.

Para resolver este problema:

* Verifique se a rede Azure VM permite que o VM Azure comunique com o servidor de configuração no local. Pode configurar uma VPN site-to-site para o seu datacenter no local ou configurar uma ligação Azure ExpressRoute com olhando para a rede virtual do Azure VM.
* Se o VM conseguir comunicar com o servidor de configuração no local, inscreva-se no VM. Em seguida, verifique o serviço de aplicação inMage Scout. Se vir que não está a funcionar, inicie o serviço manualmente. Verifique se o tipo de arranque de serviço está definido para **Automático**.

### <a name="error-code-78052"></a>Código de erro 78052

**A proteção não podia ser completada para a máquina virtual.**

Este problema pode acontecer se já houver um VM com o mesmo nome no servidor principal alvo ao qual está a falhar.

Para resolver este problema:

* Selecione um servidor-alvo diferente num anfitrião diferente para que a reprotecção crie a máquina num anfitrião diferente, onde os nomes não colidem.
* Também pode usar o VMotion para mover o alvo principal para um hospedeiro diferente, onde a colisão do nome não acontecerá. Se o VM existente for uma máquina desgarrada, rebatize-a para que o novo VM possa ser criado no mesmo anfitrião ESXi.


### <a name="error-code-78093"></a>Código de erro 78093

**O VM não está a funcionar, não responde, nem está acessível.**

Para resolver este problema:

Para reprotegir um VM falhado, o VM Azure tem de estar a funcionar de modo a que o Serviço de Mobilidade se registe com o servidor de configuração no local e possa começar a replicar-se comunicando com o servidor de processo. Se a máquina estiver numa rede incorreta ou não estiver a funcionar (não respondendo ou desligando), o servidor de configuração não pode chegar ao Serviço de Mobilidade no VM para começar a reproteção.

* Reinicie o VM para que possa voltar a comunicar no local.
* Reinicie o trabalho reprotegido depois de iniciar a máquina virtual Azure.

### <a name="error-code-8061"></a>Código de erro 8061

**A loja de dados não está acessível a partir do anfitrião ESXi.**

Verifique os [pré-requisitos principais do alvo e as lojas de dados suportadas](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) para obter falhas.


## <a name="troubleshoot-failback-errors"></a>Erros de falha de resolução de problemas

Esta secção descreve erros comuns que poderá encontrar durante o failback.

### <a name="error-code-8038"></a>Código de erro 8038

**Não conseguiu trazer a máquina virtual no local devido ao erro.**

Esta questão acontece quando o VM no local é criado num hospedeiro que não tem memória suficiente. 

Para resolver este problema:

* Fornecendo mais memória no anfitrião ESXi.
* Além disso, pode utilizar o VMotion para mover o VM para outro anfitrião ESXi que tenha memória suficiente para iniciar o VM.
