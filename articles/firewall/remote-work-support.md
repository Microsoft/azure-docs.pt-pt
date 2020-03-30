---
title: Suporte de trabalho remoto Azure Firewall
description: Este artigo mostra como o Azure Firewall pode suportar os seus requisitos de força de trabalho remota.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289644"
---
# <a name="azure-firewall-remote-work-support"></a>Suporte de trabalho remoto Azure Firewall

O Azure Firewall é um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos de rede virtual Azure. É uma firewall como um serviço com monitorização de estado com alta disponibilidade integrada e escalabilidade da cloud irrestrita. 

## <a name="firewall-rules"></a>Regras da firewall

Pode utilizar o Azure Firewall para garantir o acesso rdP de entrada da sua rede virtual Azure utilizando as regras de [DNAT](rule-processing.md)do Azure Firewall . O Windows Virtual Desktop (WVD) não requer que abra qualquer acesso de entrada à sua rede virtual. No entanto, deve permitir um conjunto de ligações de rede de saída para as máquinas virtuais WVD que funcionam na sua rede virtual. Para mais informações, consulte [o que é o Windows Virtual Desktop?](../virtual-desktop/overview.md#requirements)

Pode configurar este acesso de saída utilizando as regras de aplicação da Firewall Azure. Para mais informações, consulte [Tutorial: Desloque e configure a Firewall Azure utilizando o portal Azure](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/).