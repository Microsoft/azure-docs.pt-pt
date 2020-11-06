---
title: incluir ficheiro
description: incluir ficheiro
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a38c4bb18e16a814b626a6941d626b66311cd8ea
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329579"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Que regiões estão disponíveis?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>Preciso de um IP público na minha máquina virtual?

Quando se conecta a um VM utilizando O Bastião Azure, NÃO necessita de um IP público na Máquina Virtual Azure a que está a ligar. O serviço Bastion abrirá a sessão/ligação RDP/SSH à sua máquina virtual durante o IP privado da sua máquina virtual, dentro da sua rede virtual.

### <a name="is-ipv6-supported"></a>O IPv6 está apoiado?

Neste momento, o IPv6 não é apoiado. Azure Bastion suporta apenas o IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Preciso de um cliente RDP ou SSH?

Não precisa de um cliente RDP ou SSH para aceder ao RDP/SSH à sua máquina virtual Azure no seu portal Azure. Utilize o [portal Azure](https://portal.azure.com) para lhe permitir ter acesso RDP/SSH à sua máquina virtual diretamente no navegador.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Preciso de um agente a correr na máquina virtual do Azure?

Não precisa de instalar um agente ou qualquer software no seu navegador ou na sua máquina virtual Azure. O serviço Bastion é sem agente e não requer qualquer software adicional para RDP/SSH.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Quantas sessões simultâneas de RDP e SSH suportam cada Azure Bastion?

Tanto o RDP como o SSH são um protocolo baseado no uso. O elevado uso das sessões fará com que o anfitrião do bastião suporte um menor número total de sessões. Os números abaixo assumem fluxos de trabalho normais no dia-a-dia.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Quais as funcionalidades suportadas numa sessão de PDR?

Neste momento, apenas está disponível a operação de copiar/colar texto. As funcionalidades como a cópia de ficheiros não são suportadas. Pode partilhar os seus comentários sobre as novas funcionalidades na [página Comentários sobre o Azure Bastion](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>O endurecimento do Bastion funciona com VMs de extensão AADJ?

Esta funcionalidade não funciona com máquinas de extensão AADJ VM que utilizem utilizadores Azure AD. Para mais informações, consulte [o Windows Azure VMs e a AD AZure.](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements)

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Quais os navegadores suportados?

Utilize o navegador Microsoft Edge ou o Google Chrome no Windows. Para o Apple Mac, utilize o navegador Google Chrome. O Microsoft Edge Chromium também é suportado tanto no Windows como no Mac, respectivamente.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Onde é que a Azure Bastion armazena os dados dos clientes?

AZure Bastion não move nem armazena os dados dos clientes para fora da região em que está implantado.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>São necessárias funções para aceder a uma máquina virtual?

Para estabelecer uma ligação, são necessárias as seguintes funções:

* Papel do leitor na máquina virtual
* Função do leitor no NIC com IP privado da máquina virtual
* Papel do leitor no recurso Azure Bastion

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Qual é o preço?

Para obter mais informações, veja a [página de preços](https://aka.ms/BastionHostPricing).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>O Azure Bastion requer um RDS CAL para fins administrativos em VMs hospedados em Azure?
Não, o acesso aos VMs do Windows Server por Azure Bastion não requer um [RDS CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) quando utilizado exclusivamente para fins administrativos.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Quais os layouts de teclado suportados durante a sessão remota de Bastion?

AZure Bastion suporta atualmente o layout do teclado en-us-qwerty dentro do VM.  O suporte para outros locais para o layout do teclado está em andamento.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>O encaminhamento definido pelo utilizador (UDR) é suportado numa sub-rede Azure Bastion?

Não. A UDR não é suportada numa sub-rede Azure Bastion.
Para cenários que incluam tanto o Azure Bastion como o Azure Firewall/Network Virtual Appliance (NVA) na mesma rede virtual, não é necessário forçar o tráfego de uma sub-rede de Bastião Azure para Azure Firewall porque a comunicação entre o Azure Bastion e os seus VMs é privada. Para obter mais informações, consulte [Accessing VMs por trás da Azure Firewall com Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Porque é que recebo uma mensagem de erro "A tua sessão expirou" antes da sessão de Bastião começar?

Uma sessão só deve ser iniciada a partir do portal Azure. Inscreva-se no portal Azure e comece a sua sessão novamente. Se for diretamente para o URL a partir de outra sessão ou separador do navegador, espera-se este erro. Ajuda a garantir que a sua sessão é mais segura e que a sessão só pode ser acedida através do portal Azure.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Como lido com falhas de implantação?

Reveja quaisquer mensagens de erro e [levante um pedido de apoio no portal Azure,](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) se necessário. Falhas de implantação podem resultar de [limites de subscrição, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)de subscrição da Azure . Especificamente, os clientes podem encontrar um limite no número de endereços IP públicos permitidos por subscrição que faz com que a implantação do Bastião Azure falhe.

### <a name="how-do-i-incorporate-azure-bastion-in-my-dr-plan"></a><a name="dr"></a>Como incorporar Azure Bastion no meu plano DR?

Como o Azure Bastion está associado a uma Região Azure e é implantado dentro de VNets ou VNets espreitados, você é responsável por implantar Azure Bastion para um VNet de recuperação de desastres (DR). Em caso de falha na região de Azure, executa uma operação de failover para máquinas virtuais, em seguida, use o hospedeiro Azure Bastion que está implantado na região DR para ligar aos VMs implantados na região DR.
