---
title: incluir ficheiro
description: incluir ficheiro
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b8d30e7fe3138a26d9b64ec35d18260933df7999
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780317"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Quais as regiões disponíveis?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>Preciso de um IP público na minha máquina virtual?

Quando se liga a um VM utilizando o Azure Bastion, NÃO precisa de um IP público na Máquina Virtual Azure a que está a ligar. O serviço Bastion abrirá a sessão/ligação RDP/SSH à sua máquina virtual sobre o IP privado da sua máquina virtual, dentro da sua rede virtual.

### <a name="is-ipv6-supported"></a>O IPv6 é suportado?

Neste momento, o IPv6 não é suportado. O Azure Bastion suporta apenas o IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Preciso de um cliente RDP ou SSH?

Não precisa de um cliente RDP ou SSH para aceder ao RDP/SSH à sua máquina virtual Azure no seu portal Azure. Utilize o [portal Azure](https://portal.azure.com) para permitir o acesso rdp/SSH à sua máquina virtual diretamente no navegador.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Preciso de um agente a trabalhar na máquina virtual Azure?

Não precisa de instalar um agente ou qualquer software no seu navegador ou na sua máquina virtual Azure. O serviço Bastion é sem agente e não requer qualquer software adicional para RDP/SSH.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Quantas sessões siplâneas de RDP e SSH suportam cada Bastião Azure?

Tanto o RDP como o SSH são um protocolo baseado no uso. O elevado uso das sessões fará com que o anfitrião do bastião apoie um menor número total de sessões. Os números abaixo assumem fluxos normais de trabalho do dia-a-dia.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Quais as funcionalidades suportadas numa sessão de RDP?

Neste momento, apenas é suportado o texto/pasta. As funcionalidades tais como a cópia do ficheiro não são suportadas. Por favor, sinta-se livre para partilhar o seu feedback sobre novas funcionalidades na página de Feedback do [Bastião Azure](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Que navegadores são suportados?

Utilize o navegador Microsoft Edge ou o Google Chrome no Windows. Para o Apple Mac, utilize o navegador Google Chrome. O Microsoft Edge Chromium também é suportado tanto no Windows como no Mac, respectivamente.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>São necessários papéis para aceder a uma máquina virtual?

Para efazer uma ligação, são necessárias as seguintes funções:

* Papel do leitor na máquina virtual
* Papel do leitor no NIC com IP privado da máquina virtual
* Papel do leitor no recurso Azure Bastion

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Qual é o preço?

Para obter mais informações, veja a [página de preços](https://aka.ms/BastionHostPricing).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>A Azure Bastion exige um RDS CAL para fins administrativos em VMs hospedados em Azure?
Não, o acesso aos VMs do Windows Server por Azure Bastion não requer um [RDS CAL](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) quando utilizado apenas para fins administrativos.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Que layouts de teclado são suportados durante a sessão remota de Bastion?

ATualmente, a Azure Bastion suporta o layout de teclado en-us-qwerty dentro do VM.  O apoio a outros locais para o layout do teclado está em andamento.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>O encaminhamento definido pelo utilizador (UDR) é suportado numa subnet do Bastião Azure?

Não. A UDR não é apoiada numa subnet a Ue Bastion.
Para cenários que incluam tanto o Azure Bastion como o Azure Firewall/Network Virtual Appliance (NVA) na mesma rede virtual, não é necessário forçar o tráfego de uma subnet Azure Bastion para a Firewall Azure porque a comunicação entre o Azure Bastion e os seus VMs é privada. Para mais informações, consulte [Aceder a VMs atrás do Azure Firewall com a Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Porque é que recebo uma mensagem de erro "A tua sessão expirou" antes da sessão do Bastion começar?

Uma sessão deve ser iniciada apenas a partir do portal Azure. Inicie sessão no portal Azure e comece a sua sessão novamente. Se for diretamente ao URL de outra sessão ou separador do navegador, espera-se este erro. Ajuda a garantir que a sua sessão é mais segura e que a sessão só pode ser acedida através do portal Azure.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Como lido com falhas de implantação?

Reveja quaisquer mensagens de erro e [levante um pedido de apoio no portal Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) conforme necessário. As falhas de implantação podem resultar de [limites de subscrição do Azure, quotas e restrições.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) Especificamente, os clientes podem encontrar um limite no número de endereços IP públicos permitidos por subscrição que faz com que a implantação do Bastião Azure falhe.
