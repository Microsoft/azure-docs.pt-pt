---
title: Criar FQDN para um VM no portal Azure
description: Saiba como criar um Nome de Domínio Totalmente Qualificado (FQDN) para uma máquina virtual no portal Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42c53e72de7f6b2943086544f607042957f4b849
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220084"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Crie um nome de domínio totalmente qualificado no portal Azure para um Linux VM

Quando cria uma máquina virtual (VM) no [portal Azure,](https://portal.azure.com)é automaticamente criado um recurso IP público para a máquina virtual. Utilize este endereço IP para aceder remotamente ao VM. Embora o portal não crie um [nome de domínio totalmente qualificado,](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)ou FQDN, pode adicionar um assim que o VM for criado. Este artigo demonstra os passos para criar um nome DNS ou FQDN. 

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo assume que já criou um VM. Se necessário, pode criar um [Linux](./linux/quick-create-portal.md) ou [Windows](./windows/quick-create-portal.md) VM no portal. Siga estes passos assim que o seu VM estiver a funcionar:


1. Selecione o seu VM no portal. 
1. No menu esquerdo, selecione **Propriedades**
1. No **endereço IP público\etiqueta do nome DNS,** selecione o seu endereço IP.
2. Na **etiqueta do nome do ADN,** introduza o prefixo que pretende utilizar.
3. **Selecione Guardar** no topo da página.
4. Selecione **visão geral** no menu esquerdo para voltar à lâmina geral VM.
5. Verifique se o **nome DNS** aparece corretamente. 

## <a name="next-steps"></a>Passos seguintes

Também pode gerir DNS utilizando [zonas Azure DNS](../dns/dns-getstarted-portal.md).

