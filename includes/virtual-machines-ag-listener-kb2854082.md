---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184207"
---
Em seguida, se algum servidor do cluster estiver a executar o Windows Server 2008 R2 ou Windows Server 2012, deve verificar se o hotfix [KB2854082](https://support.microsoft.com/kb/2854082) está instalado em cada um dos servidores no local ou VMs Azure que fazem parte do cluster. Qualquer servidor ou VM que esteja no cluster, mas não no grupo de disponibilidade, também deve ter este hotfix instalado.

Na sessão de desktop remota para cada um dos nós do cluster, baixe [o KB2854082](https://support.microsoft.com/kb/2854082) para um diretório local. Em seguida, instale o hotfix em cada nó de cluster sequencialmente. Se o serviço de cluster estiver atualmente a funcionar no nó de cluster, o servidor é reiniciado no final da instalação do hotfix.

> [!WARNING]
> Parar o serviço de cluster ou reiniciar o servidor afeta a saúde do quórum do seu cluster e do grupo de disponibilidade, e pode fazer com que o seu cluster fique offline. Para manter a elevada disponibilidade do seu cluster durante a instalação, certifique-se de que:
> 
> * O aglomerado está na saúde ideal do quórum. 
> * Antes de instalar o hotfix em qualquer nó, todos os nós de cluster estão online.
> * Antes de instalar o hotfix em qualquer outro nó no cluster, deixe que a instalação de hotfix seja concluída até ao final de um nó, incluindo reiniciar completamente o servidor.
> 
> 

