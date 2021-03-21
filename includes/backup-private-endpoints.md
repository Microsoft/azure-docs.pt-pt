---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: fd7124ad83a446d7dde39a836c337a97b12a0a0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95555576"
---
Agora pode utilizar [o Private Endpoints](../articles/private-link/private-endpoint-overview.md) para fazer o back up de segurança dos seus dados de servidores dentro de uma rede virtual até ao cofre dos Serviços de Recuperação. O ponto final privado utiliza um IP a partir do espaço de endereço VNET para o seu cofre. O tráfego de rede entre os seus recursos dentro da rede virtual e o cofre viaja pela sua rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft. Isto elimina a exposição da internet pública. Os pontos finais privados podem ser utilizados para fazer o backup e restaurar as bases de dados SQL e SAP HANA que funcionam dentro dos seus VMs Azure. Também pode ser usado para os seus servidores no local usando o agente MARS.

A cópia de segurança Azure VM não requer conectividade à Internet e por isso não requer pontos de apoio privados para permitir o isolamento da rede.

Leia mais sobre os pontos finais privados para o Azure Backup [aqui](../articles/backup/private-endpoints.md).