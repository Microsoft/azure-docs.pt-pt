---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 2509bdae01fc9de5511dcd67eb95bf0d13d5cf39
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391162"
---
## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre de Serviços de Recuperação é uma entidade de gestão que armazena pontos de recuperação criados ao longo do tempo e fornece uma interface para realizar operações relacionadas com backup. Estes incluem assumir backups a pedido, realizar restauros e criar políticas de backup.

Para criar um cofre dos Serviços de Recuperação, siga estes passos.

1. Inscreva-se na sua subscrição no [portal Azure.](https://portal.azure.com/)

1. No menu à esquerda, selecione **Todos os serviços**.

    ![Selecione Todos os serviços](./media/backup-create-rs-vault/click-all-services.png)

1. Na caixa de diálogo **de todos os serviços,** insira *os Serviços de Recuperação.* A lista de filtros de recursos de acordo com a sua entrada. Na lista de recursos, selecione **Cofres dos Serviços de Recuperação.**

    ![Insira e escolha cofres dos Serviços de Recuperação](./media/backup-create-rs-vault/all-services.png)

    A lista de cofres dos Serviços de Recuperação na subscrição aparece.

1. No painel de **cofres dos Serviços de Recuperação,** selecione **Add**.

    ![Adicione um cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/add-button-create-vault.png)

    A caixa de diálogo do **cofre dos Serviços de Recuperação** abre. Fornecer valores para o **Nome,** **Assinatura,** **Grupo de Recursos**e **Localização.**

    ![Configure o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: Introduza um nome amigável para identificar o cofre. O nome deve ser exclusivo da assinatura Azure. Especifique um nome que tenha pelo menos 2, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hífenes.
   - **Assinatura**: Escolha a subscrição para utilizar. Se for membro de apenas uma subscrição, verá esse nome. Se não tiver a certeza de qual subscrição utilizar, utilize a subscrição padrão (sugerida). Só existem múltiplas escolhas se o seu trabalho ou conta escolar estiver associado a mais de uma subscrição do Azure.
   - **Grupo de recursos**: Utilize um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **Utilizar os existentes**e, em seguida, selecione um recurso da lista de drop-down. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome. Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Localização**: Selecione a região geográfica para o cofre. Para criar um cofre para proteger qualquer fonte de dados, o cofre *deve* estar na mesma região que a fonte de dados.

      > [!IMPORTANT]
      > Se não tiver a certeza da localização da sua fonte de dados, feche a caixa de diálogo. Vá à lista dos seus recursos no portal. Se tiver fontes de dados em várias regiões, crie um cofre dos Serviços de Recuperação para cada região. Crie o cofre no primeiro local antes de criar o cofre para outro local. Não há necessidade de especificar contas de armazenamento para armazenar os dados de reserva. O cofre dos Serviços de Recuperação e o cabo de backup Azure que automaticamente.
      >
      >

1. Quando estiver pronto para criar o cofre dos Serviços de Recuperação, selecione **Criar**.

    ![Criar o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/click-create-button.png)

    Pode levar algum tempo a criar o cofre dos Serviços de Recuperação. Monitorize as notificações de estado na área **de Notificações** no canto superior direito do portal. Depois do seu cofre ser criado, está visível na lista de cofres dos Serviços de Recuperação. Se não vir o seu cofre, selecione **Refresh**.

     ![Refresque a lista de cofres de reserva](./media/backup-create-rs-vault/refresh-button.png)
