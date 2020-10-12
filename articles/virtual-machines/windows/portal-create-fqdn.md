---
title: Criar FQDN para um Windows VM no portal Azure
description: Saiba como criar um Nome de Domínio Totalmente Qualificado, ou FQDN, para uma máquina virtual baseada em Gestor de Recursos no portal Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7708e2ad7e84c4449d425e8bd6eb7d4d1f6a27eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288242"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Crie um nome de domínio totalmente qualificado no portal Azure para um Windows VM

Quando cria uma máquina virtual (VM) no [portal Azure,](https://portal.azure.com)é automaticamente criado um recurso IP público para a máquina virtual. Utilize este endereço IP para aceder remotamente ao VM. Embora o portal não crie um [nome de domínio totalmente qualificado,](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)ou FQDN, pode criar um assim que o VM for criado. Este artigo demonstra os passos para criar um nome DNS ou FQDN.

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo assume que já criou um VM. Se necessário, pode [criar um VM no portal](quick-create-portal.md) ou com a [Azure PowerShell](quick-create-powershell.md). Siga estes passos assim que o seu VM estiver a funcionar:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Pode agora ligar-se remotamente ao VM utilizando este nome DNS, como para o Protocolo de Ambiente de Trabalho Remoto (RDP).

## <a name="next-steps"></a>Passos seguintes
Agora que o seu VM tem um nome público de IP e DNS, pode implementar quadros ou serviços comuns de aplicação como IIS, SQL ou SharePoint.

Também pode ler mais sobre [a utilização do Gestor de Recursos](../../azure-resource-manager/management/overview.md) para obter dicas sobre a construção das suas implementações Azure.

