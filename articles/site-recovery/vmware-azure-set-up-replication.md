---
title: Estabeleça políticas de replicação para recuperação de desastres VMware com recuperação do site Azure| Microsoft Docs
description: Descreve como configurar as definições de replicação para a recuperação de desastres VMware para Azure com Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84699605"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>Configurar e gerir políticas de replicação para a recuperação após desastre do VMware

Este artigo descreve como configurar uma política de replicação quando se replica VMware VMs para Azure, utilizando [a recuperação do site Azure](site-recovery-overview.md).

## <a name="create-a-policy"></a>Criar uma política

1. Selecione **Gerir**  >  **a Infraestrutura de Recuperação do Local**.
2. In **For VMware and Physical machines**, selecione **as políticas de replicação**.
3. Clique **na política de +replicação** e especifique o nome da política.
4. No **limiar RPO**, especifique o limite RPO. Os alertas são gerados quando a replicação contínua excede este limite.
5. Na **retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. As máquinas protegidas podem ser recuperadas para qualquer ponto numa janela de retenção. É suportada até 24 horas de retenção para máquinas replicadas para o armazenamento premium. Até 72 horas é suportado para armazenamento padrão.
6. Na **frequência snapshot consistente da App,** escolha a partir do dropdown quantas vezes (em horas) pontos de recuperação que contenham instantâneos consistentes com aplicações devem ser criados. Se desejar desligar a geração de pontos de consistência da aplicação, escolha o valor "Off" no dropdown.
7. Clique em **OK**. A política deve ser criada em 30 segundos a 60 segundos.

Quando se cria uma política de replicação, é criada automaticamente uma política de replicação de falhas correspondente, com o sufixo "failback". Depois de criar a política, pode editá-la selecionando-a > **Configurações de Edição**.

## <a name="associate-a-configuration-server"></a>Associar um servidor de configuração

Associe a política de replicação ao servidor de configuração no local.

1. Clique **em 'Associate'** e selecione o servidor de configuração.

    ![Associar servidor de configuração](./media/vmware-azure-set-up-replication/associate1.png)
2. Clique em **OK**. O servidor de configuração deve ser associado em um ou dois minutos.

    ![Associação do servidor de configuração](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Editar uma política

Pode modificar uma política de replicação depois de a criar.

- As alterações na política são aplicadas a todas as máquinas que utilizam a política.
- Se pretender associar máquinas replicadas a uma política de replicação diferente, tem de desativar e reencandido proteção para as máquinas relevantes.

Editar uma política da seguinte forma:
1. **Selecione Políticas de**  >  replicação **da infraestrutura de recuperação de**  >  **locais** de gestão do local .
2. Selecione a política de replicação que pretende modificar.
3. Clique **em Editar as definições** e atualize as horas de retenção de pontos de rpo/ponto de recuperação/áreas de frequência instantânea consistentes com aplicações, conforme necessário.
4. Se desejar desligar a geração de pontos de consistência da aplicação, escolha o valor "Off" no dropdown do campo **Frequência instantânea consistente** com a app .
5. Clique em **Guardar**. A apólice deve ser atualizada dentro de 30 a 60 segundos.



## <a name="disassociate-or-delete-a-replication-policy"></a>Desassociar ou apagar uma política de replicação

1. Escolha a política de replicação.
    a. Para dissociar a política do servidor de configuração, certifique-se de que não há máquinas replicadas a utilizar a política. Em seguida, clique **em Dissociar**.
    b. Para eliminar a apólice, certifique-se de que não está associada a um servidor de configuração. Em seguida, clique em **Eliminar**. Deve levar 30-60 segundos para apagar.
2. Clique em **OK**.
