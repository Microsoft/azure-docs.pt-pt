---
title: incluir ficheiro
description: incluir ficheiro
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4f49220da5d996615c9f8ef7cad2b6c6793866b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466267"
---
### <a name="regions"></a>Quais regiões estão disponíveis?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Preciso de um IP público em minha máquina virtual?

Você não precisa de um IP público na máquina virtual do Azure ao qual está se conectando com o serviço de bastiões do Azure. O serviço de bastiões abrirá a sessão/conexão RDP/SSH para sua máquina virtual por meio do IP privado de sua máquina virtual, dentro de sua rede virtual.

### <a name="rdpssh"></a>Preciso de um cliente RDP ou SSH?

Não precisa de um cliente RDP ou SSH para permitir o acesso RDP/SSH à sua máquina virtual do Azure no portal do Azure. Use o [portal do Azure](https://portal.azure.com) para permitir que você obtenha acesso RDP/SSH à sua máquina virtual diretamente no navegador.

### <a name="agent"></a>Preciso de um agente em execução na máquina virtual do Azure?

Não tem de instalar um agente ou qualquer software no browser ou na sua máquina virtual do Azure. O serviço Bastion não utiliza agentes nem requer software adicional para RDP/SSH.

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
