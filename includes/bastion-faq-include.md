---
title: incluir ficheiro
description: incluir ficheiro
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 03/02/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 17d391a7e6b8ef0558fb73afe363cd96deb60a7d
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262231"
---
### <a name="regions"></a>Quais as regiões disponíveis?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Preciso de um IP público na minha máquina virtual?

Quando se liga a um VM utilizando o Azure Bastion, NÃO precisa de um IP público na Máquina Virtual Azure a que está a ligar. O serviço Bastion abrirá a sessão/ligação RDP/SSH à sua máquina virtual sobre o IP privado da sua máquina virtual, dentro da sua rede virtual.

### <a name="is-ipv6-supported"></a>O IPv6 é suportado?

Neste momento, o IPv6 não é suportado. O Azure Bastion suporta apenas o IPv4.

### <a name="rdpssh"></a>Preciso de um cliente RDP ou SSH?

Não precisa de um cliente RDP ou SSH para aceder ao RDP/SSH à sua máquina virtual Azure no seu portal Azure. Utilize o [portal Azure](https://portal.azure.com) para permitir o acesso rdp/SSH à sua máquina virtual diretamente no navegador.

### <a name="rdscal"></a>A Azure Bastion exige um RDS CAL para fins administrativos em VMs hospedados em Azure?
Não, o acesso aos VMs do Windows Server por Azure Bastion não requer um [RDS CAL](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) quando utilizado apenas para fins administrativos.

### <a name="agent"></a>Preciso de um agente a trabalhar na máquina virtual Azure?

Não precisa de instalar um agente ou qualquer software no seu navegador ou na sua máquina virtual Azure. O serviço Bastion é sem agente e não requer qualquer software adicional para RDP/SSH.

### <a name="browsers"></a>Que navegadores são suportados?

Utilize o navegador Microsoft Edge ou o Google Chrome no Windows. Para o Apple Mac, utilize o navegador Google Chrome. O Microsoft Edge Chromium também é suportado tanto no Windows como no Mac, respectivamente.

### <a name="roles"></a>São necessários papéis para aceder a uma máquina virtual?

Para efazer uma ligação, são necessárias as seguintes funções:

* Papel do leitor na máquina virtual
* Papel do leitor no NIC com IP privado da máquina virtual
* Papel do leitor no recurso Azure Bastion

### <a name="pricingpage"></a>Qual é o preço?

Para obter mais informações, veja a [página de preços](https://aka.ms/BastionHostPricing).

### <a name="session"></a>Porque é que recebo uma mensagem de erro "A tua sessão expirou" antes da sessão do Bastion começar?

Uma sessão deve ser iniciada apenas a partir do portal Azure. Inicie sessão no portal Azure e comece a sua sessão novamente. Se for diretamente ao URL de outra sessão ou separador do navegador, espera-se este erro. Ajuda a garantir que a sua sessão é mais segura e que a sessão só pode ser acedida através do portal Azure.

### <a name="keyboard"></a>Que layouts de teclado são suportados durante a sessão remota de Bastion?

ATualmente, a Azure Bastion suporta o layout de teclado en-us-qwerty dentro do VM.  O apoio a outros locais para o layout do teclado está em andamento.

### <a name="udr"></a>O encaminhamento definido pelo utilizador (UDR) é suportado numa subnet do Bastião Azure?

Não. A UDR não é apoiada numa subnet a Ue Bastion.
Para cenários que incluam tanto o Azure Bastion como o Azure Firewall/Network Virtual Appliance (NVA) na mesma rede virtual, não é necessário forçar o tráfego de uma subnet Azure Bastion para a Firewall Azure porque a comunicação entre o Azure Bastion e os seus VMs é privada. Para mais informações, consulte [Aceder a VMs atrás do Azure Firewall com a Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="filetransfer"></a>A transferência de ficheiros é suportada com a sessão do RdP do Bastião Azure?

Estamos a trabalhar arduamente para adicionar novas funcionalidades. A partir de agora, a transferência de ficheiros não é suportada, mas faz parte do nosso roteiro. Por favor, sinta-se livre para partilhar o seu feedback sobre novas funcionalidades na página de Feedback do [Bastião Azure](https://feedback.azure.com/forums/217313-networking?category_id=367303).
