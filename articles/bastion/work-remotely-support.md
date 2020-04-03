---
title: 'Trabalhar remotamente usando Bastião: Bastião Azure'
description: Esta página descreve como pode alavancar o Bastião Azure para permitir o trabalho remotamente devido à pandemia COVID-19.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 182195190fed70b46185f98f595de6b6c32bbffe
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619387"
---
# <a name="working-remotely-using-azure-bastion"></a>Trabalhar remotamente usando o Bastião Azure

A Azure Bastion desempenha um papel fundamental no apoio a cenários de trabalho remoto, permitindo aos utilizadores com conectividade de internet acederem a máquinas virtuais Azure. Em particular, permite que os administradores de TI gerem as suas aplicações em Funcionamento no Azure a qualquer momento e de qualquer parte do mundo.

>[!NOTE]
>Este artigo descreve como pode alavancar o Azure Bastion, o Azure, a rede Microsoft e o ecossistema parceiro Azure para trabalhar remotamente e mitigar problemas de rede que está a enfrentar devido à crise covid-19.
>

## <a name="securely-access-virtual-machines"></a>Acesso seguro a máquinas virtuais

Especificamente, o Azure Bastion fornece conectividade RDP/SSH segura e sem emenda a máquinas virtuais dentro da rede virtual Azure, diretamente no portal Azure, sem a utilização de um endereço IP público. Para mais informações sobre a arquitetura do Bastião Azure e as principais características, confira [o What is Azure Bastion.](bastion-overview.md)

O Azure Bastion é implantado por rede virtual, o que significa que as empresas podem configurar e gerir um Bastião Azure para suportar rapidamente o acesso remoto dos utilizadores a máquinas virtuais dentro de uma rede virtual Azure. Para obter orientações sobre como criar e gerir o Bastião Azure, consulte a [Create a Bastion host](bastion-create-host-portal.md).

## <a name="next-steps"></a>Passos seguintes

* Configure O Bastião Azure utilizando o [portal Azure,](bastion-create-host-portal.md) [PowerShell](bastion-create-host-powershell.md)ou Azure CLI.

* Leia o [Bastião FAQ](bastion-faq.md) para obter informações adicionais.
