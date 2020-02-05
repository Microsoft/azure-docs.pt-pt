---
title: incluir ficheiro
description: incluir ficheiro
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 02/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922e8a71a22db975685cb82bbd51a125c619ccf2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989502"
---
### <a name="regions"></a>Quais regiões estão disponíveis?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Preciso de um IP público em minha máquina virtual?

Quando se liga a um VM utilizando o Azure Bastion, NÃO precisa de um IP público na Máquina Virtual Azure a que está a ligar. O serviço de bastiões abrirá a sessão/conexão RDP/SSH para sua máquina virtual por meio do IP privado de sua máquina virtual, dentro de sua rede virtual.

### <a name="is-ipv6-supported"></a>Há suporte para IPv6?

No momento, não há suporte para IPv6. A bastiões do Azure dá suporte apenas a IPv4.

### <a name="rdpssh"></a>Preciso de um cliente RDP ou SSH?

Não precisa de um cliente RDP ou SSH para permitir o acesso RDP/SSH à sua máquina virtual do Azure no portal do Azure. Use o [portal do Azure](https://portal.azure.com) para permitir que você obtenha acesso RDP/SSH à sua máquina virtual diretamente no navegador.

### <a name="agent"></a>Preciso de um agente em execução na máquina virtual do Azure?

Não precisa de instalar um agente ou qualquer software no seu navegador ou na sua máquina virtual Azure. O serviço Bastion não utiliza agentes nem requer software adicional para RDP/SSH.

### <a name="browsers"></a>Quais navegadores têm suporte?

Use o navegador Microsoft Edge ou o Google Chrome no Windows. Para Apple Mac, utilize o browser Google Chrome. O Microsoft Edge Chromium também é suportado no Windows e Mac, respetivamente.

### <a name="roles"></a>As funções são necessárias para acessar uma máquina virtual?

Para estabelecer uma conexão, as seguintes funções são necessárias:

* Função de leitor na máquina virtual
* Função de leitor na NIC com IP privado da máquina virtual
* Função de leitor no recurso de bastiões do Azure

### <a name="pricingpage"></a>Qual é o preço?

Para obter mais informações, veja a [página de preços](https://aka.ms/BastionHostPricing).

### <a name="session"></a>Por que obtenho a mensagem de erro "sua sessão expirou" antes do início da sessão de bastiões?

Uma sessão deve ser iniciada somente do portal do Azure. Entre no portal do Azure e inicie a sessão novamente. Se você for para a URL diretamente de outra sessão ou guia do navegador, esse erro será esperado. Ele ajuda a garantir que sua sessão seja mais segura e que a sessão possa ser acessada somente por meio do portal do Azure.

### <a name="keyboard"></a>Quais layouts de teclado têm suporte durante a sessão remota de bastiões?

Atualmente, a bastiões do Azure dá suporte ao layout de teclado en-US-QWERTY dentro da VM.  O suporte para outras localidades de layout de teclado é o trabalho em andamento.

### <a name="udr"></a>O UDR (roteamento definido pelo usuário) tem suporte em uma sub-rede de bastiões do Azure?

Não. Não há suporte para UDR em uma sub-rede de bastiões do Azure.
Para cenários que incluem o Azure bastião e o Firewall do Azure/NVA (dispositivo virtual de rede) na mesma rede virtual, você não precisa forçar o tráfego de uma sub-rede de bastiões do Azure para o Firewall do Azure, pois a comunicação entre a bastiões do Azure e suas VMs é privada. Para mais informações, consulte [Aceder a VMs atrás do Azure Firewall com a Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="filetransfer"></a>Há suporte para a transferência de arquivos com a sessão RDP de bastiões do Azure?

Estamos trabalhando duro para adicionar novos recursos. A partir de agora, a transferência de arquivos não é suportada, mas faz parte de nosso roteiro. Fique à vontade para compartilhar seus comentários sobre os novos recursos na [página de comentários de bastiões do Azure](https://feedback.azure.com/forums/217313-networking?category_id=367303).
