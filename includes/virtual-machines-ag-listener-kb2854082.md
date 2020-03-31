---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184207"
---
Em seguida, se algum servidor no cluster estiver a executar o Windows Server 2008 R2 ou o Windows Server 2012, deve verificar se o [hotfix KB2854082](https://support.microsoft.com/kb/2854082) está instalado em cada um dos servidores no local ou VMs Azure que fazem parte do cluster. Qualquer servidor ou VM que esteja no cluster, mas não no grupo de disponibilidade, também deve ter este hotfix instalado.

Na sessão remota de desktop para cada um dos nós do cluster, baixe [KB2854082](https://support.microsoft.com/kb/2854082) para um diretório local. Em seguida, instale o hotfix em cada nó de cluster sequencialmente. Se o serviço de cluster estiver atualmente em funcionamento no nó do cluster, o servidor é reiniciado no final da instalação hotfix.

> [!WARNING]
> Parar o serviço de cluster ou reiniciar o servidor afeta a saúde quórum do seu cluster e o grupo de disponibilidade, e pode fazer com que o seu cluster fique offline. Para manter a elevada disponibilidade do seu cluster durante a instalação, certifique-se de que:
> 
> * O cluster está em ótima saúde quórum. 
> * Antes de instalar o hotfix em qualquer nó, todos os nós do cluster estão on-line.
> * Antes de instalar o hotfix em qualquer outro nó do cluster, deixe a instalação de fixação de hotfix ser executada até à conclusão num nó, incluindo reiniciar totalmente o servidor.
> 
> 

