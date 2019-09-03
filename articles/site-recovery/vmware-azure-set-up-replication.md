---
title: Configurar e gerenciar políticas de replicação para a recuperação de desastres do VMware no Azure com o Azure Site Recovery | Microsoft Docs
description: Descreve como definir as configurações de replicação para a recuperação de desastres do VMware no Azure com o Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 019f9f2019619053f87a7923d656513a419d4675
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231440"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery-to-azure"></a>Configurar e gerenciar políticas de replicação para a recuperação de desastres do VMware no Azure
Este artigo descreve como configurar uma política de replicação quando você Replica VMs VMware para o Azure, usando [Azure site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Criar uma política

1. Selecione **Gerir** > **Infraestrutura do Site Recovery**.
2. Em **para VMware e máquinas físicas**, selecione **políticas de replicação**.
3. Clique em **+ política de replicação**e especifique o nome da política.
4. No **limiar RPO**, especifique o limite RPO. Os alertas são gerados quando a replicação contínua excede esse limite.
5. Na **retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. As máquinas protegidas podem ser recuperadas para qualquer ponto numa janela de retenção. É suportada até 24 horas de retenção para máquinas replicadas para o armazenamento premium. Há suporte para até 72 horas para o armazenamento Standard.
6. Em **frequência de instantâneo consistente com o aplicativo**, escolha na lista suspensa com que frequência (em horas) os pontos de recuperação que contêm instantâneos consistentes com o aplicativo devem ser criados. Se você quiser desativar a geração de pontos de consistência do aplicativo, escolha o valor "off" na lista suspensa.
7. Clique em **OK**. A política deve ser criada em 30 segundos a 60 segundos.

Quando você cria uma política de replicação, uma política de replicação de failback correspondente é criada automaticamente, com o sufixo "failback". Depois de criar a política, você pode editá-la selecionando-a > **Editar configurações**.

## <a name="associate-a-configuration-server"></a>Associar um servidor de configuração

Associe a política de replicação ao servidor de configuração local.

1. Clique em **associar**e selecione o servidor de configuração.

    ![Associar servidor de configuração](./media/vmware-azure-set-up-replication/associate1.png)
2. Clique em **OK**. O servidor de configuração deve ser associado em um ou dois minutos.

    ![Associação do servidor de configuração](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Editar uma política

Você pode modificar uma política de replicação depois de criá-la.

- As alterações na política são aplicadas a todos os computadores usando a política.
- Se você quiser associar computadores replicados a uma política de replicação diferente, será necessário desabilitar e reabilitar a proteção para os computadores relevantes.

Edite uma política da seguinte maneira:
1. Selecione **gerenciar** > **site Recovery** > **políticas de replicação**de infraestrutura.
2. Selecione a política de replicação que você deseja modificar.
3. Clique em **Editar configurações**e atualize os campos tempo de retenção do ponto de recuperação/limite de RPO/frequência de instantâneos consistentes com o aplicativo, conforme necessário.
4. Se você quiser desativar a geração de pontos de consistência do aplicativo, escolha o valor "desativado" na lista suspensa do campo **frequência do instantâneo consistente com o aplicativo**.
5. Clique em **Guardar**. A política deve ser atualizada em 30 a 60 segundos.



## <a name="disassociate-or-delete-a-replication-policy"></a>Desassociar ou excluir uma política de replicação

1. Escolha a política de replicação.
    a. Para dissociar a política do servidor de configuração, verifique se não há máquinas replicadas usando a política. Em seguida,clique em dissociar.
    b. Para excluir a política, verifique se ela não está associada a um servidor de configuração. Em seguida, clique em **excluir**. Deve levar de 30-60 a minutos para excluir.
2. Clique em **OK**.
