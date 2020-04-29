---
title: Como conceder permissões à identidade gerida no espaço de trabalho azure synapse
description: Um artigo que explica como configurar permissões para identidade gerida no espaço de trabalho Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428020"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Conceder permissões à identidade gerida pelo espaço de trabalho (pré-visualização)

Este artigo ensina-o a conceder permissões à identidade gerida no espaço de trabalho sinapse Azure. Permissões, por sua vez, permitem o acesso a piscinas SQL no espaço de trabalho e na conta de armazenamento gen2 da ADLS através do portal Azure.

>[!NOTE]
>Este espaço de trabalho gerido identidade será referido como identidade gerida através do resto deste documento.

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>Conceda as permissões de identidade geridas para a piscina SQL

A identidade gerida concede permissões às piscinas SQL no espaço de trabalho. Com permissões concedidas, pode orquestrar oleodutos que realizem atividades relacionadas com piscinaS SQL. Ao criar um espaço de trabalho Azure Synapse utilizando o portal Azure, pode conceder as permissões de CONTROLO de identidade geridas em piscinas SQL.

Selecione **Security + networking** quando estiver a criar o seu espaço de trabalho Azure Synapse. Em seguida, selecione Grant CONTROL para a identidade gerida do espaço de **trabalho em piscinas SQL**.

![Permissão CONTROL em piscinas SQL](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Conceda as permissões de identidade geridas à conta de armazenamento gen2 da ADLS

É necessária uma conta de armazenamento de gen2 ADLS para criar um espaço de trabalho Azure Synapse. Para lançar com sucesso piscinas Spark no espaço de trabalho Azure Synapse, a identidade gerida pela Azure Synapse necessita do papel de Colaborador de Dados da *Blob* de Armazenamento nesta conta de armazenamento. A orquestração de gasodutos em Azure Synapse também beneficia deste papel.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Conceder permissões à identidade gerida durante a criação do espaço de trabalho

A Azure Synapse tentará atribuir o papel de Colaborador de Dados blob de armazenamento à identidade gerida depois de criar o espaço de trabalho Azure Synapse utilizando o portal Azure. Fornece os dados da conta de armazenamento gen2 da ADLS no separador **Basics.**

![Separador básico no fluxo de criação do espaço de trabalho](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Escolha a conta de armazenamento aDLS gen2 e o sistema de ficheiros no **nome da conta** e no nome do sistema de **ficheiros**.

![Fornecendo detalhes da conta de armazenamento gen2 da ADLS](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Se o criador do espaço de trabalho também é **proprietário** da conta de armazenamento gen2 da ADLS, então o Azure Synapse atribuirá o papel de Contribuinte de Dados de *Armazenamento Blob* à identidade gerida. Verá a seguinte mensagem abaixo dos detalhes da conta de armazenamento que inseriu.

![Atribuição de colaboradores de dados de armazenamento bem-sucedido](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Se o criador do espaço de trabalho não é o proprietário da conta de armazenamento gen2 da ADLS, então o Azure Synapse não atribui o papel de Colaborador de *Dados blob* de armazenamento à identidade gerida. A mensagem que aparece abaixo da conta de armazenamento indica ao criador do espaço de trabalho que não tem permissões suficientes para conceder o papel de Contribuinte de *Dados blob* de armazenamento à identidade gerida.

![Atribuição fracassada de colaboradorde dados blob de armazenamento](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Como a mensagem indica, não pode criar piscinas spark a menos que o Colaborador de *Dados blob* de armazenamento seja atribuído à identidade gerida.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Conceder permissões à identidade gerida após criação do espaço de trabalho

Durante a criação do espaço de trabalho, se não atribuir o colaborador do *Storage Blob Data* à identidade gerida, então o **Proprietário** da conta de armazenamento gen2 adlS atribui manualmente essa função à identidade. Os seguintes passos irão ajudá-lo a realizar a atribuição manual.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Passo 1: Navegar para a conta de armazenamento gen2 da ADLS no portal Azure

No portal Azure, abra a conta de armazenamento adLS gen2 e selecione **Visão Geral** da navegação esquerda. Só terá de atribuir a função de Colaborador de *Dados blob* de armazenamento ao nível do contentor ou do sistema de ficheiros. Selecione **Recipientes**.  
![Visão geral da conta de armazenamento gen2 da ADLS](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Passo 2: Selecione o recipiente

A identidade gerida deve ter acesso aos dados ao contentor (sistema de ficheiros) que foi fornecido quando o espaço de trabalho foi criado. Pode encontrar este contentor ou sistema de ficheiros no portal Azure. Abra o espaço de trabalho Azure Synapse no portal Azure e selecione o separador **Overview** da navegação esquerda.
![Recipiente de conta de armazenamento gen2 da ADLS](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Selecione esse mesmo recipiente ou sistema de ficheiros para atribuir a função de Colaborador de *Dados blob* de armazenamento à identidade gerida.
![Seleção de contentores de conta de armazenamento gen2 da ADLS](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Passo 3: Navegar para controlo de acesso

Selecione **Controlo de Acesso (IAM)**.

![Controlo de acessos (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Passo 4: Adicionar uma nova atribuição de funções

Selecione **+ Adicionar**.

![Adicionar nova atribuição de papéis](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>Passo 5: Selecione a função RBAC

Selecione a função de Colaborador de **Dados blob de armazenamento.**

![Selecione a função RBAC](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Passo 6: Selecione o diretor de segurança da AD Azure

Selecione **utilizador, grupo ou diretor** de serviço azure a partir do acesso **designador para** descer.

![Selecione diretor de segurança AAD](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Passo 7: Procurar a identidade gerida

O nome da identidade gerida é também o nome do espaço de trabalho. Procure a sua identidade gerida introduzindo o seu nome de espaço de trabalho Azure Synapse no **Select**. Devia ver a identidade gerida listada.

![Encontre a identidade gerida](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Passo 8: Selecione a identidade gerida

Selecione a identidade gerida para os **membros Selecionados**. Selecione **Guardar** para adicionar a atribuição de funções.

![Selecione a identidade gerida](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Passo 9: Verificar se a função de Colaborador de Dados blob de armazenamento é atribuída à identidade gerida

Selecione Controlo de **Acesso (IAM)** e, em seguida, selecione atribuições de **funções**.

![Verificar a atribuição de funções](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Deve ver a sua identidade gerida listada na secção De contribuinte de dados da **Blob** de Armazenamento com a função de Colaborador de *Dados blob* de armazenamento atribuída à si. 
![Seleção de contentores de conta de armazenamento gen2 da ADLS](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a identidade gerida pelo Workspace](./synapse-workspace-managed-identity.md)
