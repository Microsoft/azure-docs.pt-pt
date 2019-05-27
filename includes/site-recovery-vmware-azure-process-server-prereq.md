---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 96cba4e077be8b7658c270b09b177a845e16c8b0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66169993"
---
Este artigo assume que

1. Uma ligação **VPN de Site para Site** ou uma **Rota Expresso** entre a rede local e a Rede Virtual do Azure já foi estabelecida.
2. A conta de utilizador tem permissões para criar uma nova máquina virtual na Subscrição do Azure que as máquinas virtuais têm falhado.
3. A subscrição tem um mínimo de 4 Núcleos disponíveis para girar uma nova máquina virtual de Servidor de Processos.
4. Tem a **Frase de Acesso do Servidor de Configuração** disponível.

> [!TIP]
> Certifique-se de que pode estabelecer uma ligação da porta 443 do Servidor de Configuração (em execução no local) a partir da Rede Virtual do Azure que as máquinas virtuais têm falhado.
