---
title: Criar políticas de replicação para a recuperação de desastres da VMware com a Recuperação do Site Azure Microsoft Docs
description: Descreve como configurar as definições de replicação para a recuperação de desastres vMware para Azure com a Recuperação do Site Azure.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257124"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>Configure e gerencie as políticas de replicação para a recuperação de desastres vMware

Este artigo descreve como configurar uma política de replicação quando se replica MMs VMware para Azure, utilizando a Recuperação do [Site Azure](site-recovery-overview.md).

## <a name="create-a-policy"></a>Criar uma política

1. Selecione **Gerir** > a infraestrutura de recuperação do**local.**
2. Em **Para VMware e máquinas físicas,** selecione políticas de **replicação.**
3. Clique na **política de replicação +,** e especifique o nome da política.
4. No **limiar RPO**, especifique o limite RPO. Os alertas são gerados quando a replicação contínua excede este limite.
5. Na **retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. As máquinas protegidas podem ser recuperadas para qualquer ponto numa janela de retenção. É suportada até 24 horas de retenção para máquinas replicadas para o armazenamento premium. Até 72 horas é suportada para armazenamento padrão.
6. Na frequência instantânea consistente com **aplicações,** escolha entre a queda da frequência (em horas) pontos de recuperação que contenham instantâneos consistentes com aplicações. Se desejar desligar a geração de pontos de consistência da aplicação, escolha o valor "Off" no dropdown.
7. Clique em **OK**. A política deve ser criada em 30 segundos a 60 segundos.

Quando se cria uma política de replicação, é criada automaticamente uma política de replicação de failback correspondente, com o sufixo "failback". Depois de criar a apólice, pode editá-la selecionando-a > Definições de **Edição**.

## <a name="associate-a-configuration-server"></a>Associar um servidor de configuração

Associe a política de replicação ao seu servidor de configuração no local.

1. Clique em **Associado**, e selecione o servidor de configuração.

    ![Associar servidor de configuração](./media/vmware-azure-set-up-replication/associate1.png)
2. Clique em **OK**. O servidor de configuração deve ser associado em um ou dois minutos.

    ![Associação do servidor de configuração](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Editar uma política

Pode modificar uma política de replicação depois de a criar.

- As alterações na política são aplicadas a todas as máquinas que utilizam a política.
- Se pretender associar máquinas replicadas a uma política de replicação diferente, tem de desativar e reativar a proteção das máquinas relevantes.

Editar uma política da seguinte forma:
1. Selecione **Gerir** > as políticas de**replicação**da infraestrutura > de recuperação do**local.**
2. Selecione a política de replicação que pretende modificar.
3. Clique em **definições de Edição**e atualize o limiar/áreas de retenção de pontos de recuperação de RPO/app-consistent para instantâneos, conforme necessário.
4. Se desejar desligar a geração de pontos de consistência da aplicação, escolha o valor "Off" na queda da frequência instantânea consistente com **aplicações**de campo .
5. Clique em **Guardar**. A política deve ser atualizada em 30 a 60 segundos.



## <a name="disassociate-or-delete-a-replication-policy"></a>Dissociar ou excluir uma política de replicação

1. Escolha a política de replicação.
    a. Para dissociar a política do servidor de configuração, certifique-se de que nenhuma máquina replicada está a utilizar a política. Em seguida, clique **em Dissociar.**
    b. Para eliminar a apólice, certifique-se de que não está associada a um servidor de configuração. Em seguida, clique em **Apagar**. Deve levar 30 a 60 segundos para apagar.
2. Clique em **OK**.
