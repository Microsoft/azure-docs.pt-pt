---
title: Criar FQDN para um VM no portal Azure
description: Saiba como criar um Nome de Domínio Totalmente Qualificado, ou FQDN, para uma máquina virtual baseada em Gestor de Recursos no portal Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f51b9ecf8e300af6b1e3f11d8431de7a282ab342
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81759348"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Crie um nome de domínio totalmente qualificado no portal Azure para um Linux VM

Quando cria uma máquina virtual (VM) no [portal Azure,](https://portal.azure.com)é automaticamente criado um recurso IP público para a máquina virtual. Utilize este endereço IP para aceder remotamente ao VM. Embora o portal não crie um [nome de domínio totalmente qualificado,](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)ou FQDN, pode adicionar um assim que o VM for criado. Este artigo demonstra os passos para criar um nome DNS ou FQDN.

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo assume que já criou um VM. Se necessário, pode [criar um VM no portal](quick-create-portal.md) ou com o [Azure CLI](quick-create-cli.md). Siga estes passos assim que o seu VM estiver a funcionar:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Pode agora ligar-se remotamente ao VM utilizando este nome DNS, como `ssh azureuser@mydns.westus.cloudapp.azure.com` .

## <a name="next-steps"></a>Próximos passos
Agora que o seu VM tem um nome público de IP e DNS, pode implementar quadros ou serviços comuns de aplicação como nginx, MongoDB, Docker, etc.

Também pode ler mais sobre [a utilização do Gestor de Recursos](../../azure-resource-manager/management/overview.md) para obter dicas sobre a construção das suas implementações Azure.

