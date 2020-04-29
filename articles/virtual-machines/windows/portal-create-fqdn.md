---
title: Crie fQDN para um VM Windows no portal Azure
description: Aprenda a criar um Nome de Domínio Totalmente Qualificado, ou FQDN, para uma máquina virtual baseada em Recursos no portal Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ece0877c97d3c2cda30aab1b3f7fe56f20080996
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101659"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Crie um nome de domínio totalmente qualificado no portal Azure para um Windows VM

Quando se cria uma máquina virtual (VM) no [portal Azure,](https://portal.azure.com)é automaticamente criado um recurso IP público para a máquina virtual. Utiliza este endereço IP para aceder remotamente ao VM. Embora o portal não crie um nome de [domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou FQDN, pode criar um assim que o VM for criado. Este artigo demonstra os passos para criar um nome DNS ou FQDN.

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo assume que já criou um VM. Se necessário, pode [criar um VM no portal](quick-create-portal.md) ou com o [Azure PowerShell](quick-create-powershell.md). Siga estes passos assim que o vM estiver em funcionamento:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Pode agora ligar-se remotamente ao VM utilizando este nome DNS, como para o Protocolo de Ambiente de Trabalho Remoto (RDP).

## <a name="next-steps"></a>Passos seguintes
Agora que o seu VM tem um nome público de IP e DNS, pode implementar quadros ou serviços comuns de aplicação, tais como IIS, SQL ou SharePoint.

Também pode ler mais sobre [a utilização do Gestor de Recursos](../../azure-resource-manager/management/overview.md) para obter dicas sobre a construção das suas implementações Azure.

