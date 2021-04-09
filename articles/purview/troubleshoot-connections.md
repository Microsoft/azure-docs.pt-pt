---
title: Resolva as suas ligações em Azure Purview
description: Este artigo explica os passos para resolver os seus problemas no Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c176fcafe13749ba89c04b34854f036aa5aea516
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677643"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Resolva as suas ligações em Azure Purview

Este artigo descreve como resolver erros de conexão ao configurar verificações em fontes de dados em Azure Purview.

## <a name="permission-the-credential-on-the-data-source"></a>Permissão da credencial na fonte de dados

Se estiver a utilizar uma identidade ou um principal de serviço gerido como um método de autenticação para digitalizações, terá de permitir que estas identidades tenham acesso à sua fonte de dados.

Existem instruções específicas para cada tipo de fonte:

- [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [BD do Cosmos para o Azure](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Armazenamento do Azure Data Lake Ger1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan) (Armazenamento do Azure Data Lake Gen2)
- [Base de Dados SQL do Azure](register-scan-azure-sql-database.md)
- [Exemplo gerido pela base de dados Azure SQL](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-bucket-scan)
## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>Armazenar a sua credencial no cofre e usar o nome e versão secretos certos

Também deve armazenar a sua credencial na sua instância Azure Key Vault e utilizar o nome e versão secretos certos.

Verifique-o seguindo os passos abaixo:

1. Navegue para o seu Cofre de Chaves.
1. Selecione **Definições** > **Segredos**.
1. Selecione o segredo que está a usar para autenticar contra a sua fonte de dados para digitalizações.
1. Selecione a versão que pretende utilizar e verifique se a palavra-passe ou a chave da conta estão corretas clicando no **Show Secret Value**. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Verifique as permissões para a identidade gerida em Purview no seu Cofre de Chaves Azure

Verifique se as permissões corretas foram configuradas para que a identidade gerida pela Purview aceda ao seu Cofre de Chave Azure.

Para verificar isto, faça os seguintes passos:

1. Navegue para o seu cofre chave e para a secção **de políticas de acesso**

1. Verifique se a sua identidade gerida pela Purview aparece na secção *de políticas de acesso atuais* com, pelo menos, permissões de **Obter** e **Listar** em Segredos

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="Imagem mostrando seleção de dropdown de opções de permissão Get e List":::

Se não vir a sua identidade gerida por Purview listada, siga os passos na [Create e gere as credenciais para digitalizar](manage-credentials.md) para adicioná-la. 

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)
