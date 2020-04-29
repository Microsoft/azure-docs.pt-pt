---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81539367"
---
Agora pode utilizar [pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para fazer o backback dos seus dados de forma segura a partir de servidores dentro de uma rede virtual para o seu cofre de Serviços de Recuperação. O ponto final privado usa um IP do espaço de endereço VNET para o seu cofre. O tráfego de rede entre os seus recursos dentro da rede virtual e o cofre viaja sobre a sua rede virtual e um link privado na rede de espinha dorsal da Microsoft. Isto elimina a exposição da internet pública. Os Pontos Finais Privados podem ser utilizados para fazer backup e restaurar as bases de dados SQL e SAP HANA que funcionam dentro dos seus VMs Azure. Também pode ser usado para os seus servidores no local utilizando o agente MARS.

O backup Azure VM não requer conectividade de internet e, por isso, não requer pontos finais privados para permitir o isolamento da rede.

>[!NOTE]
> Esta funcionalidade encontra-se atualmente em disponibilidade limitada, estando disponível nos EUA, Centro-Sul dos EUA, Oeste dos EUA 2 e Leste dos EUA (disponibilidade em outras regiões do Azure a seguir). Preencha [este inquérito](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e azbackupnetsec@microsoft.com envie-nos um e-mail se estiver interessado em utilizar pontos finais privados para backup azure. A capacidade de utilizar esta funcionalidade está sujeita à aprovação do serviço de backup Azure.
