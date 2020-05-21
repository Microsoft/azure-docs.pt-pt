---
title: Mecanismos de autenticação com a declaração COPY
description: Descreve os mecanismos de autenticação para os dados de carga a granel
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 7f2d77f3b174d8a00df9f7a93b6fef80b9cd29e8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647612"
---
# <a name="securely-load-data-using-synapse-sql"></a>Carregue de forma segura os dados usando synapse SQL

Este artigo realça e fornece exemplos sobre os mecanismos de autenticação segura para a [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). A declaração COPY é a forma mais flexível e segura de carregar a granel dados em Synapse SQL.
## <a name="supported-authentication-mechanisms"></a>Mecanismos de autenticação suportados

A seguinte matriz descreve os métodos de autenticação suportados para cada tipo de ficheiro e conta de armazenamento. Isto aplica-se ao local de armazenamento de origem e à localização do ficheiro de erro.

|                      |                CSV                |              Parquet              |                ORC                |
| :------------------: | :-------------------------------: | :-------------------------------: | :-------------------------------: |
|  Armazenamento de blobs do Azure  | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |              SAS/KEY              |              SAS/KEY              |
| Lago de Dados Azure Gen2 | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>R. Chave da conta de armazenamento com LF como o exterminador de linha (nova linha estilo Unix)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - Utilize o valor hexadecimal (0x0A) para especificar o caracteres Line Feed/Newline. Note que a declaração copy interpretará a corda '\n' como '\r\n' (novalinha de retorno do transporte).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Assinaturas de Acesso Partilhado (SAS) com CRLF como o exterminador de linha (nova linha estilo Windows)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - Não especifique o ROWTERMINATOR como '\r\n' que será interpretado como '\r\r\n' e pode resultar em questões de análise

## <a name="c-managed-identity"></a>C. Identidade Gerida

A autenticação de identidade gerida é necessária quando a sua conta de armazenamento está anexada a um VNet. 

### <a name="prerequisites"></a>Pré-requisitos

1. Instale o Azure PowerShell utilizando este [guia](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Se tiver uma conta de armazenamento v1 ou blob de uso geral, tem primeiro de atualizar para v2 de uso geral utilizando este [guia](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
3. Deve permitir **que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento** ligada no menu de definições de **firewalls e configurações** de redes virtuais da conta Azure. Consulte este [guia](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions) para mais informações.
#### <a name="steps"></a>Passos

1. No PowerShell, **registe o seu servidor SQL** com o Azure Ative Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```

2. Crie uma **Conta de Armazenamento v2** de uso geral utilizando este [guia](../../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

   > [!NOTE]
   > Se tiver uma conta de armazenamento v1 ou blob de uso geral, tem primeiro de **atualizar para v2** utilizando este [guia](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

3. Na sua conta de armazenamento, navegue para o Controlo de **Acesso (IAM)** e selecione Adicionar a atribuição de **funções**. Atribuir a função **de Proprietário, Colaborador ou** RBAC do Storage Blob ao seu servidor SQL.

   > [!NOTE]
   > Só os membros com privilégio proprietário podem executar este passo. Para várias funções incorporadas para os recursos Azure, consulte este [guia.](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
   
4. Pode agora executar a declaração copy especificando "Identidade Gerida":

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

> [!IMPORTANT]
>
> - Especifique a função De proprietário de **dados** de **Blob de armazenamento,** colaborador ou rBAC do leitor. Estes papéis são diferentes dos papéis integrados do Azure de Proprietário, Colaborador e Leitor. 

## <a name="d-azure-active-directory-authentication-aad"></a>D. Autenticação de Diretório Ativo Azure (AAD)
#### <a name="steps"></a>Passos

1. Na sua conta de armazenamento, navegue para o Controlo de **Acesso (IAM)** e selecione Adicionar a atribuição de **funções**. Atribuir o papel de **Proprietário, Colaborador ou** RBAC do Storage Blob ao seu utilizador AAD. 

2. Configure a autenticação ad ad azure através da seguinte [documentação](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#create-an-azure-ad-administrator-for-azure-sql-server). 

3. Ligue-se ao seu pool SQL utilizando o Ative Directory onde pode agora executar a declaração COPY sem especificar quaisquer credenciais:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```

> [!IMPORTANT]
>
> - Especifique a função De proprietário de **dados** de **Blob de armazenamento,** colaborador ou rBAC do leitor. Estes papéis são diferentes dos papéis integrados do Azure de Proprietário, Colaborador e Leitor. 

## <a name="e-service-principal-authentication"></a>E. Autenticação principal do serviço
#### <a name="steps"></a>Passos

1. [Criar uma aplicação azure Ative Directory (AAD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)
2. [Obter ID de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)
3. [Obtenha a chave de autenticação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret)
4. [Obtenha o V1 OAuth 2.0 ponto final de token](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Atribuir permissões de leitura, escrita e execução para o seu pedido de AAD](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) na sua conta de armazenamento
6. Agora pode executar a declaração copy:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - Utilize a versão **V1** do ponto final de 2.0 token OAuth

## <a name="next-steps"></a>Próximos passos

- Consulte o artigo de declaração da [CÓPIA](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) para a sintaxe detalhada
- Consulte o artigo sobre [visão geral](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt) de dados para carregar as melhores práticas
