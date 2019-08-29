---
title: Criar FQDN para uma VM do Windows no portal do Azure | Microsoft Docs
description: Saiba como criar um nome de domínio totalmente qualificado, ou FQDN, para uma máquina virtual baseada no Resource Manager no portal do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c86e2739efd951ad9274f2fa0829b9fd7e13e3e5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102603"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Criar um nome de domínio totalmente qualificado no portal do Azure para uma VM do Windows

Quando você cria uma VM (máquina virtual) no [portal do Azure](https://portal.azure.com), um recurso de IP público para a máquina virtual é criado automaticamente. Use esse endereço IP para acessar remotamente a VM. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou FQDN, você pode criar um depois que a VM é criada. Este artigo demonstra as etapas para criar um nome DNS ou FQDN.

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo pressupõe que você já criou uma VM. Se necessário, você pode [criar uma VM no portal](quick-create-portal.md) ou [com Azure PowerShell](quick-create-powershell.md). Siga estas etapas quando sua VM estiver em execução:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Agora você pode se conectar remotamente à VM usando esse nome DNS, como para o protocolo RDP (RDP).

## <a name="next-steps"></a>Passos seguintes
Agora que sua VM tem um IP público e um nome DNS, você pode implantar estruturas de aplicativo ou serviços comuns, como IIS, SQL ou SharePoint.

Você também pode ler mais sobre como [usar o Gerenciador de recursos](../../azure-resource-manager/resource-group-overview.md) para obter dicas sobre como criar suas implantações do Azure.

