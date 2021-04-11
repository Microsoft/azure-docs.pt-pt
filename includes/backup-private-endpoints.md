---
author: v-amallick
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: v-amallick
ms.openlocfilehash: d20ed4d39921f8000f77f947c4372bd8b10ca642
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294120"
---
Agora pode utilizar [o Private Endpoints](../articles/private-link/private-endpoint-overview.md) para fazer o back up de segurança dos seus dados de servidores dentro de uma rede virtual até ao cofre dos Serviços de Recuperação. O ponto final privado utiliza um IP a partir do espaço de endereço VNET para o seu cofre. O tráfego de rede entre os seus recursos dentro da rede virtual e o cofre viaja pela sua rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft. Isto elimina a exposição da internet pública. Os pontos finais privados podem ser utilizados para fazer o backup e restaurar as bases de dados SQL e SAP HANA que funcionam dentro dos seus VMs Azure. Também pode ser usado para os seus servidores no local usando o agente MARS.

A cópia de segurança Azure VM não requer conectividade à Internet e por isso não requer pontos de apoio privados para permitir o isolamento da rede.

Leia mais sobre os pontos finais privados para o Azure Backup [aqui](../articles/backup/private-endpoints.md).