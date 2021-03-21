---
title: Criptografe discos de SISTEMA utilizando chaves geridas pelo cliente em Azure DevTest Labs
description: Saiba como encriptar os discos do sistema operativo (OS) utilizando chaves geridas pelo cliente em Azure DevTest Labs.
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 26ef4ff1529483da9956c6dcc43807af0ffd6463
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341215"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Criptografe discos do sistema operativo (OS) utilizando chaves geridas pelo cliente em Azure DevTest Labs
A encriptação do lado do servidor (SSE) protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade. A SSE encripta automaticamente os seus dados armazenados em discos geridos em Azure (OS e discos de dados) em repouso por padrão quando os persiste na nuvem. Saiba mais sobre [encriptação de disco](../virtual-machines/disk-encryption.md) no Azure. 

Dentro da DevTest Labs, todos os discos de SO e discos de dados criados como parte de um laboratório são encriptados usando chaves geridas pela plataforma. No entanto, como dono de laboratório, pode optar por encriptar os discos de SISTEMA de máquinas virtuais de laboratório usando as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, pode especificar uma **chave gerida pelo cliente** para encriptar dados em discos de sistema OPERATIVO. Para saber mais sobre encriptação do lado do Servidor (SSE) com teclas geridas pelo cliente e outros tipos de encriptação de discos geridos, consulte [as teclas geridas pelo Cliente](../virtual-machines/disk-encryption.md#customer-managed-keys). Além disso, consulte [as restrições com a utilização de chaves geridas pelo cliente](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions).

> [!NOTE]
> - Atualmente, a encriptação do disco com uma chave gerida pelo cliente é suportada apenas para discos OS em DevTest Labs. 
> 
> - A definição aplica-se aos discos de SO recém-criados no laboratório. Se optar por alterar o conjunto de encriptação do disco em algum momento, os discos mais antigos do laboratório continuarão a ser encriptados utilizando o conjunto de encriptação do disco anterior. 

A seguinte secção mostra como um proprietário de laboratório pode configurar encriptação usando uma chave gerida pelo cliente.

## <a name="pre-requisites"></a>Pré-requisitos

1. Se não tiver um conjunto de encriptação de disco, siga este artigo para [configurar um Cofre de Chaves e um Conjunto de Encriptação de Disco](../virtual-machines/disks-enable-customer-managed-keys-portal.md). Note os seguintes requisitos para o conjunto de encriptação do disco: 

    - O conjunto de encriptação de disco tem de estar **na mesma região e subscrição que o seu laboratório.** 
    - Certifique-se de que você (proprietário do laboratório) tem pelo menos um acesso ao **nível** do leitor ao conjunto de encriptação do disco que será usado para encriptar discos de sistema OPERATIVO. 
1. Para os laboratórios criados antes de 8/1/2020, o dono do laboratório terá de garantir que a identidade atribuída ao sistema de laboratório está ativada. Para tal, o proprietário do laboratório pode ir ao seu laboratório, clicar em **Configuração e políticas,** clicar na lâmina **identidade (preview),** alterar o **Estado** de identidade atribuída do sistema para **on** e clicar em **Guardar**. Para novos laboratórios criados após o sistema de laboratório 8/1/2020, a identidade atribuída será ativada por padrão. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="Chaves geridas":::
1. Para que o laboratório manuseie a encriptação de todos os discos de sistema, o proprietário do laboratório precisa de conceder explicitamente o papel de leitor **de identidade atribuído** ao sistema do laboratório no conjunto de encriptação do disco, bem como o papel de contribuinte de máquina virtual na subscrição do Azure subjacente. O proprietário do laboratório pode fazê-lo completando os seguintes passos:

   
    1. Certifique-se de que é membro da [função de Administrador](../role-based-access-control/built-in-roles.md#user-access-administrator) de Acesso ao Utilizador ao nível da subscrição do Azure para que possa gerir o acesso do utilizador aos recursos do Azure. 
    1. Na página Conjunto de Encriptação do **Disco,** selecione **Controlo de Acesso (IAM)** no menu esquerdo. 
    1. **Selecione + Adicione** na barra de ferramentas e selecione Adicione uma atribuição de **função**.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="Adicionar gestão de função - menu":::
    1. Na página **de atribuição de funções Adicionar,** selecione a função **Reader** ou uma função que permita mais acesso. 
    1. Digite o nome do laboratório para o qual o conjunto de encriptação do disco será usado e selecione o nome do laboratório (identidade atribuída pelo sistema para o laboratório) da lista de retiradas. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="Selecione a identidade gerida pelo sistema do laboratório":::        
    1. Selecione **Guardar** na barra de ferramentas. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="Guardar atribuição de funções":::
3. Adicione a **identidade atribuída** ao sistema do laboratório à função de Contribuinte de Máquina **Virtual** utilizando a página de controlo de acesso a   ->  **subscrição (IAM).** Os degraus são semelhantes aos dos degraus anteriores. 

    
    1. Navegue para a página **de Subscrição** no portal Azure. 
    1. Selecione **Controlo de acesso (IAM)** . 
    1. **Selecione +Adicione** na barra de ferramentas e selecione **Adicione uma atribuição de função**. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="Página de controlo de acesso > subscrição (IAM)":::
    1. Na página **de atribuição de funções Adicionar,** selecione **Virtual Machine Contributor** para o papel.
    1. Digite o nome do laboratório e selecione o nome do **laboratório** (identidade atribuída pelo sistema para o laboratório) da lista de suspensos. 
    1. Selecione **Guardar** na barra de ferramentas. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>Criptografe os discos OS do laboratório com uma chave gerida pelo cliente 

1. Na página inicial do seu laboratório no portal Azure, selecione **Configuração e políticas** no menu esquerdo. 
1. Na página **de Configuração e políticas,** selecione **Discos (Pré-visualização)** na secção **de Encriptação.** Por **predefinição, o tipo de encriptação** é definido para **encriptação em repouso com uma chave gerida pela plataforma**.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="Separador de discos da página de Configuração e políticas":::
1. Para **o tipo de encriptação**, selecione **Encriptação em repouso com uma chave gerida pelo cliente** a partir da lista de drop-down. 
1. Para **o conjunto de encriptação do disco,** selecione o conjunto de encriptação do disco que criou anteriormente. É o mesmo conjunto de encriptação de disco que a identidade atribuída ao sistema do laboratório pode aceder.
1. Selecione **Guardar** na barra de ferramentas. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="Ativar a encriptação com a chave gerida pelo cliente":::
1. Na caixa de mensagens com o seguinte texto: *Esta definição aplicar-se-á a máquinas recém-criadas em laboratório. O disco antigo do SO permanecerá encriptado com o conjunto de encriptação do disco antigo*, selecione **OK**. 

    Uma vez configurados, os discos de sistema OS serão encriptados com a chave gerida pelo cliente, fornecida utilizando o conjunto de encriptação do disco. 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>Como validar se os discos estão a ser encriptados

1. Vá a uma máquina virtual de laboratório criada depois de permitir a encriptação do disco com uma chave gerida pelo cliente no laboratório.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="VM com encriptação de disco ativada":::
1. Clique no grupo de recursos do VM e clique no disco oss.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="Grupo de recursos VM":::
1. Vá à Encriptação e valide se a encriptação estiver definida para a chave gerida pelo cliente com o Conjunto de Encriptação de Disco selecionado.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="Validar encriptação":::
  
## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos: 

- [Encriptação do disco Azure](../virtual-machines/disk-encryption.md). 
- [Chaves geridas pelo cliente](../virtual-machines/disk-encryption.md#customer-managed-keys)