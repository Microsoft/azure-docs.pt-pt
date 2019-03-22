---
title: Configurar o ambiente de destino para recuperação após desastre de servidores físicos no local para o Azure | Documentos da Microsoft
description: Este artigo descreve como configurar o destino de ambiente do Azure para recuperação após desastre de servidores físicos com o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 41220ccdca945610d7d8ca87af0857114e2cef85
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57866297"
---
# <a name="prepare-target-vmware-to-azure"></a>Preparar o destino (VMware para o Azure)

Este artigo descreve como preparar seu ambiente do Azure para começar a replicar servidores físicos (x64) com o Windows ou Linux no Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo supõe:
- Criou um cofre dos serviços de recuperação para proteger os seus servidores físicos. Pode criar um cofre dos serviços de recuperação do [portal do Azure](https://portal.azure.com "portal do Azure").
- Tiver [configure o ambiente no local](physical-azure-disaster-recovery.md) para replicar servidores físicos para o Azure.

## <a name="prepare-target"></a>Preparar o destino

Depois de concluir o **objetivo de proteção do passo 1:Select** e **passo 2: preparar origem**, é direcionado para **passo 3: Destino**

![Preparar o destino](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Subscrição:** No menu pendente, selecione a subscrição que pretende replicar os seus servidores físicos para.
2. **Modelo de implementação:** Selecione o modelo de implementação (clássico ou do Resource Manager)

Com base no modelo de implementação escolhida, é executada uma validação para garantir que tenha pelo menos uma conta de armazenamento compatíveis e a rede virtual na subscrição de destino para replicar e ativação pós-falha seus servidores físicos para.

Assim que as validações concluir com êxito, clique em OK para ir para o passo seguinte.

Se não tiver uma conta de armazenamento do Resource Manager compatível ou a rede virtual, pode criar uma ao clicar o **+ conta de armazenamento** ou **+ rede** botões na parte superior da página.

## <a name="next-steps"></a>Passos Seguintes
[Configurar definições de replicação](vmware-azure-set-up-replication.md).
