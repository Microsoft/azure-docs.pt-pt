---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008493"
---
Este artigo assume que

1. Uma ligação **VPN de Site para Site** ou uma **Rota Expresso** entre a rede local e a Rede Virtual do Azure já foi estabelecida.
2. A conta de utilizador tem permissões para criar uma nova máquina virtual na Subscrição do Azure que as máquinas virtuais têm falhado.
3. A sua subscrição tem um mínimo de 8 Núcleos disponíveis para girar uma nova máquina virtual process Server.
4. Tem a **Frase de Acesso do Servidor de Configuração** disponível.

> [!TIP]
> Certifique-se de que pode estabelecer uma ligação da porta 443 do Servidor de Configuração (em execução no local) a partir da Rede Virtual do Azure que as máquinas virtuais têm falhado.
