---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: b8941bb4fa77cb0008cb7271681e972bc21d6588
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659537"
---
Agora pode utilizar [pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para fazer o backback dos seus dados de forma segura a partir de servidores dentro de uma rede virtual para o seu cofre de Serviços de Recuperação. O ponto final privado usa um IP do espaço de endereço VNET para o seu cofre. O tráfego de rede entre os seus recursos dentro da rede virtual e o cofre viaja sobre a sua rede virtual e um link privado na rede de espinha dorsal da Microsoft. Isto elimina a exposição da internet pública. Os Pontos Finais Privados podem ser utilizados para fazer backup e restaurar as bases de dados SQL e SAP HANA que funcionam dentro dos seus VMs Azure. Também pode ser usado para os seus servidores no local utilizando o agente MARS.

O backup Azure VM não requer conectividade de internet e, por isso, não requer pontos finais privados para permitir o isolamento da rede.

Leia mais sobre pontos finais privados para Azure Backup [aqui](https://docs.microsoft.com/azure/backup/private-endpoints).
