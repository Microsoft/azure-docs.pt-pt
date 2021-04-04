---
title: 'Trabalhar remotamente usando Bastion: Azure Bastion'
description: Esta página descreve como pode aproveitar a Bastião Azure para ativar o funcionamento remotamente devido à pandemia COVID-19.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 515a6f7175aee23c6f720258b7fdaefe76b3b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92077954"
---
# <a name="working-remotely-using-azure-bastion"></a>Trabalhando remotamente usando Azure Bastion

O Azure Bastion desempenha um papel fundamental no apoio a cenários de trabalho remoto, permitindo aos utilizadores com conectividade de internet aceder a máquinas virtuais Azure. Em particular, permite que os administradores de TI gerem as suas aplicações em execução no Azure a qualquer momento e de qualquer parte do mundo.

>[!NOTE]
>Este artigo descreve como pode aproveitar a Azure Bastion, Azure, a rede Microsoft e o ecossistema parceiro Azure para trabalhar remotamente e mitigar problemas de rede que está a enfrentar devido à crise covid-19.
>

## <a name="securely-access-virtual-machines"></a>Aceda de forma segura a máquinas virtuais

Especificamente, o Azure Bastion fornece conectividade RDP/SSH segura e sem costura a máquinas virtuais dentro da rede virtual Azure, diretamente no portal Azure, sem a utilização de um endereço IP público. Para mais informações sobre a arquitetura do Bastião Azure e as principais características, confira [o que é Azure Bastion.](bastion-overview.md)

O Azure Bastion é implantado por rede virtual, o que significa que as empresas podem configurar e gerir um Azure Bastion para suportar rapidamente o acesso remoto do utilizador a máquinas virtuais dentro de uma rede virtual Azure. Para obter orientações sobre como criar e gerir o Bastião Azure, consulte criar [um hospedeiro de bastião.](./tutorial-create-host-portal.md)

## <a name="next-steps"></a>Passos seguintes

* Configure Bastião Azure utilizando o [portal Azure](./tutorial-create-host-portal.md), [PowerShell](bastion-create-host-powershell.md)ou Azure CLI.

* Leia as [FAQ de Bastião](bastion-faq.md) para obter informações adicionais.