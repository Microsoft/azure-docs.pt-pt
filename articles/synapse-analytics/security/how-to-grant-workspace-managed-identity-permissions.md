---
title: Conceder permissões para gerir a identidade no espaço de trabalho da Sinapse
description: Um artigo que explica como configurar permissões para identidade gerida no espaço de trabalho Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 46fe27205a2c30d6cb2319bf620c6fe1ee34c31e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459006"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>Conceder permissões à identidade gerida da área de trabalho

Este artigo ensina-lhe como conceder permissões à identidade gerida no espaço de trabalho da Sinapse Azure. As permissões, por sua vez, permitem o acesso a piscinas SQL dedicadas no espaço de trabalho e à conta de armazenamento da ADLS Gen2 através do portal Azure.

>[!NOTE]
>Esta identidade gerida pelo espaço de trabalho será referida como identidade gerida através do resto deste documento.

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>Grant comunciou permissões de identidade para a piscina dedicada SQL

A identidade gerida concede permissões às piscinas SQL dedicadas no espaço de trabalho. Com permissões concedidas, você pode orquestrar oleodutos que realizam atividades dedicadas relacionadas com piscina SQL. Quando criar um espaço de trabalho Azure Synapse utilizando o portal Azure, pode conceder permissões de controlo de identidade geridas em piscinas SQL dedicadas.

Selecione **Segurança** quando estiver a criar o seu espaço de trabalho Azure Synapse. Em seguida, **selecione Permitir que os gasodutos (funcionando como identidade atribuída pelo sistema do espaço de trabalho) acedam às piscinas SQL.**

![Permissão de CONTROLO em piscinas SQL dedicadas](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Conceder as permissões de identidade geridas à conta de armazenamento da ADLS Gen2

Uma conta de armazenamento ADLS Gen2 é necessária para criar um espaço de trabalho Azure Synapse. Para lançar com sucesso piscinas Spark no espaço de trabalho Azure Synapse, a identidade gerida Azure Synapse necessita do papel *de Colaborador de Dados blob de armazenamento* nesta conta de armazenamento. A orquestração de gasodutos em Azure Synapse também beneficia deste papel.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Conceder permissões para gerir a identidade durante a criação do espaço de trabalho

A Azure Synapse tentará conceder o papel de Colaborador de Dados de Armazenamento blob à identidade gerida depois de criar o espaço de trabalho Azure Synapse utilizando o portal Azure. Você fornece os detalhes da conta de armazenamento ADLS Gen2 no **separador Básicos.**

![Separador básico no fluxo de criação de espaço de trabalho](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Escolha a conta de armazenamento ESLS Gen2 e o sistema de ficheiros em **nome de conta** e nome do sistema de **ficheiros.**

![Fornecendo detalhes da conta de armazenamento ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Se o criador do espaço de trabalho for também **proprietário** da conta de armazenamento ADLS Gen2, então a Azure Synapse atribuirá o papel *de Colaborador de Dados blob de armazenamento* à identidade gerida. Verá a seguinte mensagem abaixo dos detalhes da conta de armazenamento que inseriu.

![Atribuição de contribuinte de dados blob de armazenamento bem-sucedido](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Se o criador do espaço de trabalho não é o proprietário da conta de armazenamento ADLS Gen2, então a Azure Synapse não atribui o papel *de Colaborador de Dados blob de armazenamento* à identidade gerida. A mensagem que aparece abaixo dos detalhes da conta de armazenamento notifica o criador do espaço de trabalho de que não têm permissões suficientes para conceder o papel *de Colaborador de Dados blob de armazenamento* à identidade gerida.

![Atribuição de contribuinte de dados blob de armazenamento mal sucedido](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Como diz a mensagem, não é possível criar piscinas Spark a menos que o *Colaborador de Dados blob de armazenamento* seja atribuído à identidade gerida.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Conceder permissões para gerir identidade após criação de espaço de trabalho

Durante a criação do espaço de trabalho, se não atribuir o *colaborador de Dados de Armazenamento blob* à identidade gerida, então o **Proprietário** da conta de armazenamento ADLS Gen2 atribui manualmente essa função à identidade. Os seguintes passos irão ajudá-lo a realizar a atribuição manual.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Passo 1: Navegue para a conta de armazenamento da ADLS Gen2 no portal Azure

No portal Azure, abra a conta de armazenamento ADLS Gen2 e selecione **a visão geral** da navegação à esquerda. Só terá de atribuir a função *de Contribuinte de Dados de Armazenamento Blob* ao nível do contentor ou do sistema de ficheiros. Selecione **Recipientes**.  
![Visão geral da conta de armazenamento da ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Passo 2: Selecione o recipiente

A identidade gerida deve ter acesso de dados ao contentor (sistema de ficheiros) que foi fornecido quando o espaço de trabalho foi criado. Pode encontrar este recipiente ou sistema de ficheiros no portal Azure. Abra o espaço de trabalho Azure Synapse no portal Azure e selecione o **separador Visão Geral** a partir da navegação à esquerda.
![Recipiente de conta de armazenamento ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Selecione o mesmo sistema de ficheiros ou contentores para conceder a função *de Contribuinte de Dados de Armazenamento blob* para a identidade gerida.
![Screenshot que mostra o sistema de contentores ou ficheiros que deve selecionar.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Passo 3: Navegar para o controlo de acesso

Selecione **Controlo de Acesso (IAM)**.

![Controlo de acesso (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Passo 4: Adicionar uma nova atribuição de funções

Selecione **+ Adicionar**.

![Adicionar nova atribuição de funções](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>Passo 5: Selecione o papel de Azure

Selecione a **função de contribuinte de dados de armazenamento** blob.

![Selecione o papel Azure](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Passo 6: Selecione o diretor de segurança Azure AD

Selecione **O utilizador, grupo ou principal** de serviço da Atribuição de acesso **a** drop down.

![Selecione o principal de segurança da AAD](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Passo 7: Procurar a identidade gerida

O nome da identidade gerida é também o nome do espaço de trabalho. Procure a sua identidade gerida inserindo-lhe o nome do espaço de trabalho Azure Synapse em **Select**. Devia ver a identidade gerida listada.

![Encontre a identidade gerida](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Passo 8: Selecione a identidade gerida

Selecione a identidade gerida para os **membros Selecionados**. **Selecione Guardar** para adicionar a atribuição de funções.

![Selecione a identidade gerida](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Passo 9: Verificar se a função de contribuinte de dados de armazenamento blob é atribuída à identidade gerida

Selecione **Access Control (IAM)** e, em seguida, selecione **atribuições de funções**.

![Verificar atribuição de funções](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Deverá ver a sua identidade gerida listada na secção **Storage Blob Data Contributor** com a função de Contribuinte de *Dados blob de armazenamento* que lhe foi atribuída. 
![Seleção de contentores de conta de armazenamento ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a identidade gerida do Workspace](./synapse-workspace-managed-identity.md)
