---
title: Configurar o ambiente-alvo para servidores físicos na Recuperação do Site Azure
description: Este artigo descreve como configurar o ambiente Azure alvo para a recuperação de desastres de servidores físicos usando a Recuperação do Site Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73953891"
---
# <a name="prepare-target-vmware-to-azure"></a>Preparar alvo (VMware para Azure)

Este artigo descreve como preparar o seu ambiente Azure para começar a replicar servidores físicos (x64) executando Windows ou Linux em Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo assume:
- Criou um Cofre de Serviços de Recuperação para proteger os seus servidores físicos. Pode criar um Cofre de Serviços de Recuperação a partir do [portal Azure.](https://portal.azure.com "Portal do Azure")
- Configuraste [o ambiente no local](physical-azure-disaster-recovery.md) para replicar servidores físicos para o Azure.

## <a name="prepare-target"></a>Preparar alvo

Depois de completar o **passo 1:Selecionar a meta de proteção** e **o passo 2:Preparar a fonte,** é levado para o **passo 3: Alvo**

![Preparar alvo](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Assinatura:** A partir do menu suspenso, selecione a Subscrição para a que pretende replicar os seus servidores físicos.
2. **Modelo de implantação:** Selecione o modelo de implementação (Classic ou Resource Manager)

Com base no modelo de implementação escolhido, é executada uma validação para garantir que tem pelo menos uma conta de armazenamento compatível e rede virtual na subscrição-alvo para replicar e falhar os seus servidores físicos.

Assim que as validações estiverem concluídas com sucesso, clique em OK para ir ao passo seguinte.

Se não tiver uma conta de armazenamento compatível do Gestor de Recursos ou rede virtual, pode criar uma clicando nos botões **+ Conta de Armazenamento** ou + **Rede** no topo da página.

## <a name="next-steps"></a>Passos seguintes
[Configurar as definições de replicação](vmware-azure-set-up-replication.md).
