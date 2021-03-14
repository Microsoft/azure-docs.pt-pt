---
title: Criar e gerir credenciais para digitalizações
description: Conheça os passos para criar e gerir credenciais em Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 3802d25ebd8f21ab5b8991a66ceb6650f2f276a9
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461713"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Credenciais para autenticação de origem em Azure Purview

Este artigo descreve como pode criar credenciais em Azure Purview. Estas credenciais guardadas permitem-lhe reutilizar rapidamente e aplicar informações de autenticação guardada nas suas análises de fontes de dados.

## <a name="prerequisites"></a>Pré-requisitos

- Um cofre de chaves Azure. Para aprender a criar um, consulte [Quickstart: Crie um cofre-chave utilizando o portal Azure](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Introdução

Uma credencial é a informação de autenticação que o Azure Purview pode usar para autenticar nas suas fontes de dados registadas. Um objeto credencial pode ser criado para vários tipos de cenários de autenticação, como a Autenticação Básica que requer nome de utilizador/senha. Credenciais capturam informações específicas necessárias à autenticação, com base no tipo escolhido de método de autenticação. As credenciais utilizam os seus segredos Azure Key Vaults existentes para recuperar informações de autenticação sensível durante o processo de criação de credenciais.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Use a identidade gerida de Purview para configurar digitalizações

Se estiver a usar a identidade gerida pela Purview para configurar as sondagens, não terá de criar explicitamente uma credencial e ligar o cofre-chave ao Purview para os armazenar. Para obter instruções detalhadas sobre a adição da identidade gerida pela Alça deVisão para ter acesso a digitalizar as suas fontes de dados, consulte as secções de autenticação específicas da fonte de dados abaixo:

- [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Armazenamento do Azure Data Lake Ger1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan) (Armazenamento do Azure Data Lake Gen2)
- [Base de Dados SQL do Azure](register-scan-azure-sql-database.md)
- [Exemplo gerido pela base de dados Azure SQL](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Crie ligações Azure Key Vaults na sua conta Azure Purview

Antes de poder criar um Credencial, primeiro associe uma ou mais das suas instâncias existentes do Azure Key Vault com a sua conta Azure Purview.

1. A partir do [portal Azure,](https://portal.azure.com)selecione a sua conta Azure Purview e Open Azure Purview Studio. Navegue para o **Centro de Gestão** no Azure Purview Studio e, em seguida, navegue para **credenciais.**

2. Na página **Credenciais,** **selecione 'Gerir as ligações 'Gestão do Cofre de Chaves'.**

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Gerir ligações Azure Key Vault":::

3. Selecione **+ Novo** na página 'Gerir chaves' de ligações.

4. Forneça as informações necessárias e, em seguida, **selecione Criar**.

5. Confirme que o seu Key Vault foi associado com sucesso à sua conta Azure Purview, como mostrado neste exemplo:

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Veja as ligações do Cofre da Chave Azure para confirmar.":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Conceda ao Purview acesso de identidade gerido ao seu Cofre chave Azure

1. Navegue até ao cofre da chave Azure.

2. Selecione a página **'Políticas de Acesso'.**

3. Selecione **Adicionar Política de Acesso**.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Adicionar MSI de Purview à AKV":::

4. Nas **permissões Secrets** dropdown, selecione **permissões Get** and **List.**

5. Para **Select principal,** escolha a identidade gerida por Purview. Pode pesquisar o MSI do Purview utilizando o nome da instância de Purview **ou** o ID de aplicação de identidade gerido. Não suportamos atualmente identidades compostas (nome de identidade gerido + ID de aplicação).

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Adicionar política de acesso":::

6. Selecione **Adicionar**.

7. **Selecione Guardar** para guardar a política de Acesso.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Salvar a política de acesso":::

## <a name="create-a-new-credential"></a>Criar uma nova credencial

Estes tipos de credenciais são suportados em Purview:

- Autenticação básica: Adicione a **palavra-passe** como segredo no cofre de chaves.
- Diretor de serviço: Adiciona-se a **chave principal** do serviço como segredo no cofre das chaves.
- Autenticação SQL: Adicione a **palavra-passe** como segredo no cofre de chaves.
- Chave de conta: Adicione a chave da **conta** como um segredo no cofre de chaves.
- Role ARN: Para uma fonte de dados Damazon S3, adicione o seu **papel ARN** em AWS. 

Para obter mais informações, consulte [Adicionar um segredo ao Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) e criar um novo papel [AWS para a Purga.](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview)

Depois de guardar os seus segredos no cofre da chave:

1. Em Azure Purview, vá à página de Credenciais.

2. Crie a sua nova Credencial selecionando **+ Novo**.

3. Forneça as informações necessárias. Selecione o **método de autenticação** e uma **ligação key Vault** para selecionar um segredo.

4. Uma vez preenchidos todos os detalhes, **selecione Criar**.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nova credencial":::

5. Verifique se a sua nova credencial aparece na vista da lista e está pronta a ser utilizada.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Ver credencial":::

## <a name="manage-your-key-vault-connections"></a>Gerencie as suas ligações de cofre chave

1. Procurar/encontrar ligações key vault pelo nome

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Cofre de chaves de pesquisa":::

2. Eliminar uma ou mais ligações do Cofre de Chaves

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Apagar cofre de chaves":::

## <a name="manage-your-credentials"></a>Gerencie as suas credenciais

1. Procurar/encontrar Credenciais pelo nome.
  
2. Selecione e faça atualizações para uma Credencial existente.

3. Apague uma ou mais credenciais.

## <a name="next-steps"></a>Passos seguintes

[Criar um conjunto de regras de análise](create-a-scan-rule-set.md)
