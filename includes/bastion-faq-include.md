---
title: incluir ficheiro
description: incluir ficheiro
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e29a9265e010c3f442b742faf62b16dae02739fa
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191183"
---
### <a name="preview"></a>Como participar na pré-visualização pública

Tem de carregar para participar na pré-visualização pública. Utilize os passos em [este artigo](../articles/bastion/bastion-create-host-portal.md) para criar um novo recurso de Bastion do Azure. Atualmente, quando aceder e utilizar este serviço, tem de utilizar o [portal do Azure - pré-visualização](https://aka.ms/BastionHost) em vez do portal do Azure normal.

### <a name="regions"></a>Que regiões estão disponíveis durante a pré-visualização?

Pode implementar e utilizar o Bastion regiões através de pré-visualização de recursos em qualquer uma da [portal do Azure - ligação de pré-visualização](https://aka.ms/BastionHost).

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="portal"></a>Não consigo encontrar o recurso de Bastion no portal do Azure. O que devo fazer?

Certifique-se de que está a utilizar o [portal do Azure - ligação de pré-visualização](https://aka.ms/BastionHost), não o portal do Azure normal.

### <a name="publicip"></a>É necessário um IP público na minha máquina virtual?

Não é necessário um IP público na máquina Virtual do Azure que estiver a estabelecer ligação com o serviço de Bastion do Azure. O serviço de Bastion abrirá o RDP/SSH sessão/ligação à máquina virtual através de IP privado da sua máquina virtual, na sua rede virtual.

### <a name="rdpssh"></a>É necessário um cliente RDP ou SSH?

Não precisa de um cliente RDP ou SSH para permitir o acesso RDP/SSH à sua máquina virtual do Azure no portal do Azure. Utilize o [portal do Azure - ligação de pré-visualização](https://aka.ms/BastionHost) para acessar o vôo de pré-visualização do portal. Isto irá permitir-lhe obter acesso RDP/SSH à sua máquina virtual diretamente no browser.

### <a name="agent"></a>É necessário um agente em execução na máquina virtual do Azure?

Não tem de instalar um agente ou qualquer software no browser ou na sua máquina virtual do Azure. O serviço Bastion não utiliza agentes nem requer software adicional para RDP/SSH.

### <a name="browsers"></a>Que browsers são suportados?

Durante a pré-visualização pública, utilize o browser Microsoft Edge ou o Google Chrome no Windows. Para Apple Mac, utilize o browser Google Chrome. O Microsoft Edge Chromium também é suportado no Windows e Mac, respetivamente.

### <a name="roles"></a>São quaisquer funções necessárias para aceder a uma máquina virtual?

Para estabelecer uma ligação, as seguintes funções são necessárias:

* Função de leitor na máquina virtual
* Função de leitor na NIC com o IP privado da máquina virtual
* Função de leitor do recurso de Bastion do Azure

### <a name="previewbill"></a>Preços - me será faturado por participar na pré-visualização?

Só serão cobradas parcialmente durante a pré-visualização pública. No entanto, não existe nenhum SLA anexado à sua implementação. Para obter mais informações, veja a [página de preços](https://aka.ms/BastionHostPricing).