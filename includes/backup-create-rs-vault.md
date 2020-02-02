---
title: incluir ficheiro
description: incluir ficheiro
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 17ef8dd121c987c2771d274c8f0e6f9f199e386c
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938167"
---
## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre de Serviços de Recuperação é uma entidade que armazena backups e pontos de recuperação criados ao longo do tempo. O cofre dos serviços de recuperação também contém as políticas de backup associadas às máquinas virtuais protegidas.

Para criar um cofre dos Serviços de Recuperação, siga estes passos.

1. Entre em sua assinatura no [portal do Azure](https://portal.azure.com/).

1. No menu à esquerda, selecione **todos os serviços**.

    ![selecionar todos os serviços](./media/backup-create-rs-vault/click-all-services.png)

1. Na caixa de diálogo **todos os serviços** , insira *serviços de recuperação*. A lista de recursos filtra de acordo com sua entrada. Na lista de recursos, selecione **cofres dos serviços de recuperação**.

    ![Insira e escolha os cofres dos serviços de recuperação](./media/backup-create-rs-vault/all-services.png)

    A lista de cofres dos serviços de recuperação na assinatura é exibida.

1. No painel **cofres dos serviços de recuperação** , selecione **Adicionar**.

    ![Adicionar um cofre dos serviços de recuperação](./media/backup-create-rs-vault/add-button-create-vault.png)

    A caixa de diálogo **cofre dos serviços de recuperação** é aberta. Forneça valores para o **nome**, a **assinatura**, o **grupo de recursos**e o **local**.

    ![Configurar o cofre dos serviços de recuperação](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: Insira um nome amigável para identificar o cofre. O nome deve ser exclusivo para a assinatura do Azure. Especifique um nome que tenha pelo menos 2, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hifens.
   - **Assinatura**: escolha a assinatura a ser usada. Se você for membro de apenas uma assinatura, verá esse nome. Se você não tiver certeza de qual assinatura usar, use a assinatura padrão (sugerida). Há várias opções somente se sua conta corporativa ou de estudante estiver associada a mais de uma assinatura do Azure.
   - **Grupo de recursos**: Use um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **Use existente**, e, em seguida, selecione um recurso da lista de drop-down. Para criar um novo grupo de recursos, selecione **criar novo** e insira o nome. Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Local**: selecione a região geográfica para o cofre. Para criar um cofre para proteger as máquinas virtuais, o cofre *deve* estar na mesma região que as máquinas virtuais.

      > [!IMPORTANT]
      > Se você não tiver certeza do local da VM, feche a caixa de diálogo. Vá para a lista de máquinas virtuais no Portal. Se você tiver máquinas virtuais em várias regiões, crie um cofre de serviços de recuperação em cada região. Crie o cofre no primeiro local antes de criar o cofre para outro local. Não é necessário especificar contas de armazenamento para armazenar os dados de backup. O cofre dos Serviços de Recuperação e o cabo de backup Azure que automaticamente.
      >
      >

1. Quando estiver pronto para criar o cofre dos serviços de recuperação, selecione **criar**.

    ![Criar o cofre dos serviços de recuperação](./media/backup-create-rs-vault/click-create-button.png)

    Pode levar algum tempo para criar o cofre dos serviços de recuperação. Monitore as notificações de status na área de **notificações** no canto superior direito do Portal. Depois que o cofre for criado, ele ficará visível na lista de cofres dos serviços de recuperação. Se você não vir seu cofre, selecione **Atualizar**.

     ![Atualizar a lista de cofres de backup](./media/backup-create-rs-vault/refresh-button.png)
