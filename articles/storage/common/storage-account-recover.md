---
title: Recuperar uma conta de armazenamento eliminada
titleSuffix: Azure Storage
description: Saiba como recuperar uma conta de armazenamento eliminada dentro do portal Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57cd3361d7888d9d7f747955257d96282274fd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357351"
---
# <a name="recover-a-deleted-storage-account"></a>Recuperar uma conta de armazenamento eliminada

Uma conta de armazenamento eliminada pode ser recuperada em alguns casos a partir do portal Azure. Para recuperar uma conta de armazenamento, as seguintes condições devem ser verdadeiras:

- A conta de armazenamento foi eliminada nos últimos 14 dias.
- A conta de armazenamento foi criada com o modelo de implementação do Gestor de Recursos Azure.
- Não foi criada uma nova conta de armazenamento com o mesmo nome desde que a conta original foi eliminada.

Antes de tentar recuperar uma conta de armazenamento eliminada, certifique-se de que o grupo de recursos para essa conta existe. Se o grupo de recursos foi eliminado, deve recriá-lo. Recuperar um grupo de recursos não é possível. Para obter mais informações sobre , consulte [Gerir grupos de recursos.](../../azure-resource-manager/management/manage-resource-groups-portal.md)

Se a conta de armazenamento eliminada usou chaves geridas pelo cliente com o Cofre da Chave Azure e o cofre da chave também foi apagado, então deve restaurar o cofre antes de restaurar a conta de armazenamento. Para obter mais informações, consulte [a visão geral da recuperação do Cofre da Chave Azure](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> A recuperação de uma conta de armazenamento eliminada não está garantida. A recuperação é uma tentativa de esforço. A Microsoft recomenda bloquear recursos para evitar a eliminação acidental de contas. Para obter mais informações sobre os bloqueios de recursos, consulte [os recursos de bloqueio para evitar alterações](../../azure-resource-manager/management/lock-resources.md).
>
> Outra das melhores práticas para evitar a eliminação acidental de contas é limitar o número de utilizadores que têm permissões para apagar uma conta através do controlo de acesso baseado em funções (Azure RBAC). Para mais informações, consulte [as melhores práticas para o Azure RBAC.](../../role-based-access-control/best-practices.md)

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Recuperar uma conta eliminada do portal Azure

Para recuperar uma conta de armazenamento eliminada de outra conta de armazenamento, siga estes passos:

1. Navegue na página geral para obter uma conta de armazenamento existente no portal Azure.
1. Na secção **Suporte + resolução de problemas,** selecione **Recuperar conta eliminada**.
1. A partir do dropdown, selecione a conta para recuperar, como mostra a imagem seguinte. Se a conta de armazenamento que pretende recuperar não estiver no dropdown, então não pode ser recuperada.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Screenshot mostrando como recuperar a conta de armazenamento no portal Azure":::

1. Selecione o botão **Recuperar** para restaurar a conta. O portal apresenta uma notificação de que a recuperação está em curso.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>Recuperar uma conta eliminada através de um bilhete de apoio

1. No portal Azure, navegue para **Ajudar + suporte.**
1. Selecione **Novo pedido de suporte**.
1. No separador **Básico,** no campo **tipo Emissão,** selecione **Técnico**.
1. No campo **Subscrição,** selecione a subscrição que continha a conta de armazenamento eliminada.
1. No campo **Serviço,** selecione **Gestão de Conta de Armazenamento.**
1. No campo **Recursos,** selecione qualquer recurso de conta de armazenamento. A conta de armazenamento eliminada não constará da lista.
1. Adicione um breve resumo da questão.
1. No campo do **tipo Problema,** selecione **Eliminação e Recuperação**.
1. No campo do **subtipo Problema,** selecione **Recuperar conta de armazenamento eliminada**. A imagem a seguir mostra um exemplo do separador **Basics** que está a ser preenchido:

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="Screenshot mostrando como recuperar uma conta de armazenamento através de bilhete de suporte - Separador básico":::

1. Em seguida, navegue no separador **Soluções** e selecione **recuperação de conta de armazenamento controlada pelo cliente,** como mostra a seguinte imagem:

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="Screenshot mostrando como recuperar uma conta de armazenamento através de bilhete de suporte - Separador soluções":::

1. A partir do dropdown, selecione a conta para recuperar, como mostra a imagem seguinte. Se a conta de armazenamento que pretende recuperar não estiver no dropdown, então não pode ser recuperada.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="Screenshot mostrando como recuperar uma conta de armazenamento através de bilhete de apoio":::

1. Selecione o botão **Recuperar** para restaurar a conta. O portal apresenta uma notificação de que a recuperação está em curso.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da conta de armazenamento](storage-account-overview.md)
- [Criar uma conta de armazenamento](storage-account-create.md)
- [Upgrade para uma conta de armazenamento v2 para fins gerais](storage-account-upgrade.md)
- [Mover uma conta de Armazenamento Azure para outra região](storage-account-move.md)