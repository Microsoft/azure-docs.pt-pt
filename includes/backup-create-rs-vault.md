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
ms.openlocfilehash: 6167774171affda7e5469d5852a79657a6da700d
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262516"
---
## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre de Serviços de Recuperação é uma entidade de armazenamento que armazena os pontos de recuperação criados ao longo do tempo. Contém também as políticas de backup que estão associadas a itens protegidos.

Para criar um cofre dos Serviços de Recuperação, siga estes passos.

1. Inscreva-se na sua subscrição no [portal Azure.](https://portal.azure.com/)

1. No menu esquerdo, selecione **Todos os serviços.**

    ![Selecione Todos os serviços](./media/backup-create-rs-vault/click-all-services.png)

1. Na caixa de diálogo **de todos os serviços,** insira Serviços de *Recuperação.* A lista de filtros de recursos de acordo com a sua entrada. Na lista de recursos, selecione cofres de Serviços de **Recuperação.**

    ![Insira e escolha cofres de Serviços de Recuperação](./media/backup-create-rs-vault/all-services.png)

    A lista de cofres dos Serviços de Recuperação na subscrição aparece.

1. No painel de **abóbadas** dos Serviços de Recuperação, selecione **Adicionar**.

    ![Adicione um cofre de Serviços de Recuperação](./media/backup-create-rs-vault/add-button-create-vault.png)

    A caixa de diálogo do **cofre dos Serviços de Recuperação** abre. Forneça valores para o **nome,** **subscrição,** **grupo de recursos**e **localização.**

    ![Configure o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: Introduza um nome amigável para identificar o cofre. O nome deve ser exclusivo da subscrição azure. Especifique um nome que tenha pelo menos 2, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hífenes.
   - **Subscrição**: Escolha a subscrição para utilizar. Se for membro de apenas uma subscrição, verá esse nome. Se não tiver a certeza de qual subscrição utilizar, utilize a subscrição predefinida (sugerida). Existem várias opções apenas se o seu trabalho ou conta escolar estiver associado a mais de uma subscrição Azure.
   - **Grupo de recursos**: Utilize um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **Use existente**, e, em seguida, selecione um recurso da lista de drop-down. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome. Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Localização**: Selecione a região geográfica para o cofre. Para criar um cofre para proteger máquinas virtuais, o cofre *deve* estar na mesma região que as máquinas virtuais.

      > [!IMPORTANT]
      > Se não tiver a certeza da localização do seu VM, feche a caixa de diálogo. Vá à lista de máquinas virtuais no portal. Se tiver máquinas virtuais em várias regiões, crie um cofre de Serviços de Recuperação em cada região. Crie o cofre no primeiro local, antes de criar o cofre para outro local. Não há necessidade de especificar contas de armazenamento para armazenar os dados de reserva. O cofre dos Serviços de Recuperação e o cabo de backup Azure que automaticamente.
      >
      >

1. Quando estiver pronto para criar o cofre dos Serviços de Recuperação, selecione **Criar**.

    ![Criar o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/click-create-button.png)

    Pode demorar um pouco para criar o cofre dos Serviços de Recuperação. Monitorize as notificações de estado na área de **Notificações** no canto superior direito do portal. Depois do seu cofre ser criado, é visível na lista de cofres dos Serviços de Recuperação. Se não vir o seu cofre, selecione **Refresh**.

     ![Atualizar a lista de cofres de reserva](./media/backup-create-rs-vault/refresh-button.png)
