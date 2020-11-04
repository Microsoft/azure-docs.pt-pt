---
title: Criar FQDN para um VM no portal Azure
description: Saiba como criar um Nome de Domínio Totalmente Qualificado (FQDN) para uma máquina virtual no portal Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351913"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Crie um nome de domínio totalmente qualificado no portal Azure para um Linux VM

Quando cria uma máquina virtual (VM) no [portal Azure,](https://portal.azure.com)é automaticamente criado um recurso IP público para a máquina virtual. Utilize este endereço IP para aceder remotamente ao VM. Embora o portal não crie um [nome de domínio totalmente qualificado,](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)ou FQDN, pode adicionar um assim que o VM for criado. Este artigo demonstra os passos para criar um nome DNS ou FQDN. 

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo assume que já criou um VM. Se necessário, pode criar um [Linux](./linux/quick-create-portal.md) ou [Windows](./windows/quick-create-portal.md) VM no portal. Siga estes passos assim que o seu VM estiver a funcionar:


1. Selecione o seu VM no portal. Sob **o nome DNS** , selecione **Configure**.
2. Introduza o nome DNS e, em seguida, **selecione Guardar** no topo da página.
3. Para voltar à lâmina de visão geral VM, feche a lâmina **de configuração** selecionando o **X** no canto superior direito. 
4. Verifique se o *nome DNS* está agora corretamente indicado.
   



## <a name="next-steps"></a>Próximos passos
Agora que o seu VM tem um nome público de IP e DNS, pode implementar quadros ou serviços comuns de aplicação como nginx, MongoDB e Docker.

Também pode ler mais sobre [a utilização do Gestor de Recursos](../azure-resource-manager/management/overview.md) para obter dicas sobre a construção das suas implementações Azure.

