---
title: Configurar o ambiente-alvo para servidores físicos na Recuperação do Site Azure
description: Este artigo descreve como configurar o ambiente-alvo Azure para a recuperação de desastres de servidores físicos usando a Recuperação do Site Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73953891"
---
# <a name="prepare-target-vmware-to-azure"></a>Preparar o alvo (VMware para O Azure)

Este artigo descreve como preparar o seu ambiente Azure para começar a replicar servidores físicos (x64) que executam o Windows ou o Linux no Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo assume:
- Criou um Cofre de Serviços de Recuperação para proteger os seus servidores físicos. Pode criar um Cofre de Serviços de Recuperação a partir do [portal Azure.](https://portal.azure.com "Portal do Azure")
- Instalou [o seu ambiente no local](physical-azure-disaster-recovery.md) para replicar servidores físicos para o Azure.

## <a name="prepare-target"></a>Preparar o alvo

Depois de completar o **passo 1:Selecione o objetivo** de proteção e passo **2:Prepare**a fonte, é levado para o **passo 3: Target**

![Preparar o alvo](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Subscrição:** A partir do menu suspenso, selecione a Subscrição a que pretende replicar os seus servidores físicos.
2. **Modelo de implantação:** Selecione o modelo de implementação (Classic ou Resource Manager)

Com base no modelo de implementação escolhido, é executada uma validação para garantir que tem pelo menos uma conta de armazenamento compatível e rede virtual na subscrição do alvo para replicar e falhar sobre os seus servidores físicos.

Assim que as validações estiverem concluídas com sucesso, clique em OK para passar ao passo seguinte.

Se não tiver uma conta de armazenamento compatível do Gestor de Recursos ou rede virtual, pode criar uma clicando na **Conta + Armazenamento** ou + botões de **rede** na parte superior da página.

## <a name="next-steps"></a>Passos seguintes
[Configurar as definições](vmware-azure-set-up-replication.md)de replicação .
