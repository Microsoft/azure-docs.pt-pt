---
title: Visão geral dos cofres de reserva
description: Uma visão geral dos cofres de reserva.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 773152212ed831c0083cfdf912f45ece578d079f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997260"
---
# <a name="backup-vaults-overview"></a>Visão geral dos cofres de reserva

Este artigo descreve as características de um cofre de reserva. Um cofre de backup é uma entidade de armazenamento em Azure que abriga dados de backup para certas cargas de trabalho mais recentes que a Azure Backup suporta. Pode utilizar cofres de backup para conter dados de backup para vários serviços Azure, tais como Azure Database para servidores PostgreSQL e cargas de trabalho mais recentes que o Azure Backup irá suportar. Os cofres de reserva facilitam a organização dos seus dados de backup, minimizando a sobrecarga de gestão. Os cofres de backup são baseados no modelo Azure Resource Manager da Azure, que fornece funcionalidades como:

- **Capacidades melhoradas para ajudar a proteger dados de backup**: Com cofres de backup, o Azure Backup fornece capacidades de segurança para proteger cópias de segurança na nuvem. As funcionalidades de segurança garantem que pode proteger as suas cópias de segurança e recuperar dados com segurança, mesmo que os servidores de produção e cópia de segurança estejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Controlo de Acesso Baseado em Função (RBAC)**: O RBAC fornece um controlo de gestão de acessos finos em Azure. [O Azure oferece vários papéis incorporados,](../role-based-access-control/built-in-roles.md)e o Azure Backup tem três [funções incorporadas para gerir pontos de recuperação.](backup-rbac-rs-vault.md) Os cofres de backup são compatíveis com o RBAC, que restringe o backup e restaura o acesso ao conjunto definido de funções do utilizador. [Saiba mais](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Definições de armazenamento no cofre de reserva

Um cofre de backup é uma entidade que armazena os backups e pontos de recuperação criados ao longo do tempo. O cofre de backup também contém as políticas de backup que estão associadas com as máquinas virtuais protegidas.

- O Azure Backup manuseia automaticamente o armazenamento para o cofre. Escolha a redundância de armazenamento que corresponda às necessidades do seu negócio ao criar o cofre de reserva.

- Para saber mais sobre a redundância de armazenamento, consulte estes artigos sobre [redundância geo](../storage/common/storage-redundancy.md#geo-redundant-storage) e [local.](../storage/common/storage-redundancy.md#locally-redundant-storage)

## <a name="encryption-settings-in-the-backup-vault"></a>Definições de encriptação no cofre de reserva

Esta secção discute as opções disponíveis para encriptar os dados de backup armazenados no cofre de reserva.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Encriptação de dados de backup usando chaves geridas pela plataforma

Por predefinição, todos os seus dados são encriptados utilizando chaves geridas pela plataforma. Não precisa de tomar nenhuma ação explícita do seu lado para ativar esta encriptação. Aplica-se a todas as cargas de trabalho que estão a ser apoiadas até ao cofre de reserva.

## <a name="create-a-backup-vault"></a>Criar um cofre de reserva

Um cofre de backup é uma entidade de gestão que armazena pontos de recuperação criados ao longo do tempo e fornece uma interface para realizar operações relacionadas com backup. Estas incluem criar cópias de segurança a pedido, realizar restauros e criar políticas de cópia de segurança.

Para criar um cofre de reserva, siga estes passos.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Criar cofre de reserva

1. Digite **cofres** de reserva na caixa de pesquisa.
1. Em **Serviços**, selecione **cofres de reserva**.
1. Na página **de cofres de reserva,** selecione **Adicionar**.
1. No **separador Basics**, nos **detalhes do Projeto,** certifique-se de que a subscrição correta é selecionada e, em seguida, escolha **Criar novo** grupo de recursos. Digite *myResourceGroup* para o nome.

  ![Criar novo grupo de recursos](./media/backup-vault-overview/new-resource-group.png)

1. De acordo com **os detalhes de Exemplo,** *escreva myVault* para o nome do **cofre de reserva** e escolha a sua região de eleição, neste *caso, Leste dos EUA* para a sua **Região.**
1. Agora escolha a sua **redundância de Armazenamento.** A redundância de armazenamento não pode ser alterada depois de proteger os itens para o cofre.
1. Recomendamos que, se estiver a utilizar o Azure como ponto final de armazenamento de backup primário, continue a utilizar a definição **geo-redundante** padrão.
1. Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure.
1. Saiba mais sobre [a redundância geo](../storage/common/storage-redundancy.md#geo-redundant-storage) e [local.](../storage/common/storage-redundancy.md#locally-redundant-storage)

  ![Escolha a redundância de armazenamento](./media/backup-vault-overview/storage-redundancy.png)

1. Selecione o botão 'Rever +' na parte inferior da página.

    ![Selecione Review + Criar](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Apagar um cofre de reserva

Esta secção descreve como apagar um cofre de reserva. Contém instruções para remover dependências e depois apagar um cofre.

### <a name="before-you-start"></a>Antes de começar

Não é possível apagar um cofre de reserva com qualquer uma das seguintes dependências:

- Não é possível eliminar um cofre que contenha fontes de dados protegidas (por exemplo, base de dados Azure para servidores PostgreSQL).
- Não é possível apagar um cofre que contenha dados de reserva.

Se tentar apagar o cofre sem remover as dependências, encontrará as seguintes mensagens de erro:

>Não é possível eliminar o cofre de reserva, uma vez que existem instâncias de backup existentes ou políticas de backup no cofre. Elimine todas as instâncias de backup e políticas de backup que estejam presentes no cofre e tente apagar o cofre.

### <a name="proper-way-to-delete-a-vault"></a>Maneira apropriada de apagar um cofre

>[!WARNING]
A seguinte operação é destrutiva e não pode ser desfeita. Todos os dados de backup e itens de backup associados ao servidor protegido serão permanentemente eliminados. Avance com cuidado.

Para eliminar corretamente um cofre, deve seguir os passos desta ordem:

- Tem de verificar se existem itens protegidos:
  - Vá a **Instâncias de Reserva** na barra de navegação esquerda. Todos os itens listados aqui devem ser apagados primeiro.

Depois de completar estes passos, pode continuar a apagar o cofre.

### <a name="delete-the-backup-vault"></a>Apague o cofre de reserva

Quando não houver mais itens no cofre, **selecione Delete** no painel de abóbada. Verá uma mensagem de confirmação perguntando se deseja apagar o cofre.

![Apagar cofre](./media/backup-vault-overview/delete-vault.png)

1. Selecione **Sim** para verificar se deseja apagar o cofre. O cofre está apagado. O portal regressa ao menu de serviço **Novo.**

## <a name="monitor-and-manage-the-backup-vault"></a>Monitorize e gere o cofre de reserva

Esta secção explica como usar o painel **de visão geral** do cofre de reserva para monitorizar e gerir os cofres de reserva. O painel geral contém dois azulejos: **Empregos** e **Instâncias**.

![Painel de visão geral](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Gerir instâncias de backup

No azulejo **Jobs,** você tem uma visão resumida de todos os backups e restaurar empregos relacionados no seu cofre de reserva. A seleção de qualquer um dos números deste azulejo permite-lhe visualizar mais informações sobre trabalhos para um determinado tipo de fonte de dados, tipo de operação e estado.

![Instâncias de backup](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Gerir Tarefas de cópia de segurança

No azulejo **de Instâncias de Reserva,** obtém-se uma visão resumida de todas as instâncias de backup no seu cofre de reserva. A seleção de qualquer um dos números deste azulejo permite-lhe visualizar mais informações sobre casos de backup para um determinado tipo de fonte de dados e estado de proteção.

![Trabalhos de reserva](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Passos seguintes

- [Configure backup em bases de dados Azure PostgreSQL](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
