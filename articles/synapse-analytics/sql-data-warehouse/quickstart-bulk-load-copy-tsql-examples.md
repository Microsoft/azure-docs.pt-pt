---
title: Mecanismos de autenticação com a declaração COPY
description: Delineia os mecanismos de autenticação para os dados de carga em massa
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 05dd1f1d429b59c4d621b63c6b78a1fc00e8d4dd
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528468"
---
# <a name="securely-load-data-using-synapse-sql"></a>Carregar de forma segura dados usando O SQL de Sinapse

Este artigo destaca e dá exemplos sobre os mecanismos de autenticação seguro para a [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). A declaração COPY é a forma mais flexível e segura de carregar dados em massa no Synapse SQL.
## <a name="supported-authentication-mechanisms"></a>Mecanismos de autenticação apoiados

A matriz a seguir descreve os métodos de autenticação suportados para cada tipo de ficheiro e conta de armazenamento. Isto aplica-se ao local de armazenamento de fontes e à localização do ficheiro de erro.

|                          |                CSV                |              Parquet              |                ORC                |
| :----------------------: | :-------------------------------: | :-------------------------------: | :-------------------------------: |
|  **Armazenamento de bolhas Azure**  | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |              SAS/CHAVE              |              SAS/CHAVE              |
| **Azure Data Lake Gen2** | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. Chave de conta de armazenamento com LF como exterminador de linha (nova linha estilo Unix)


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
> - Utilize o valor hexadecimal (0x0A) para especificar o caractere 'Feed/Newline' da Linha. Note que a declaração COPY interpretará a cadeia '\n' como '\r\n' (newline de retorno de transporte).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Assinaturas de acesso compartilhadas (SAS) com CRLF como exterminador de linha (nova linha estilo Windows)
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
> - Não especifique o ROWTERMINATOR como '\r\n' que será interpretado como '\r\r\n' e pode resultar em problemas de análise

## <a name="c-managed-identity"></a>C. Identidade Gerida

A autenticação de identidade gerida é necessária quando a sua conta de armazenamento está anexada a um VNet. 

### <a name="prerequisites"></a>Pré-requisitos

1. Siga este [guia](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para instalar o Azure PowerShell.
2. Se tiver uma conta de armazenamento de blobs ou fins gerais v1, terá primeiro de atualizar para a conta fins gerais v2. Para tal, siga este [guia](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
3. Deve ter **permitido que serviços fidedignos da Microsoft acedam a esta conta de armazenamento** ligados no menu de firewalls da conta de armazenamento Azure e redes **virtuais.** Para obter mais informações, veja este [guia](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions).
#### <a name="steps"></a>Passos

1. No PowerShell, **registe o seu servidor SQL** com o Azure Ative Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```

2. Crie uma **conta de armazenamento v2 para fins gerais** utilizando este [guia.](../../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

   > [!NOTE]
   > Se tiver uma conta de armazenamento v1 ou blob para fins gerais, tem primeiro de **atualizar para v2** utilizando este [guia](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

3. Na sua conta de armazenamento, navegue para **Access Control (IAM)** e selecione **Adicionar a atribuição de funções**. Atribua o papel **de Proprietário de Dados Blob de Armazenamento, Colaborador ou Leitor** Azure ao seu servidor SQL.

   > [!NOTE]
   > Só os membros com privilégio proprietário podem realizar este passo. Para várias funções incorporadas do Azure, consulte este [guia.](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
   
    > [!IMPORTANT]
    > Especifique a função De Proprietário de **Dados blob** **de armazenamento,** colaborador ou leitor Azure. Estes papéis são diferentes dos papéis incorporados do Azure de Proprietário, Colaborador e Leitor. 

    ![Concessão de permissão do RBAC para carregar](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. Pode agora executar a declaração COPY especificando "Identidade Gerida":

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

## <a name="d-azure-active-directory-authentication-aad"></a>D. Autenticação do Diretório Ativo Azure (AAD)
#### <a name="steps"></a>Passos

1. Na sua conta de armazenamento, navegue para **Access Control (IAM)** e selecione **Adicionar a atribuição de funções**. Atribua o papel **de Proprietário, Colaborador ou Azure do Leitor** de Armazenamento ao utilizador AAD. 

    > [!IMPORTANT]
    > Especifique a função De Proprietário de **Dados blob** **de armazenamento,** colaborador ou leitor Azure. Estes papéis são diferentes dos papéis incorporados do Azure de Proprietário, Colaborador e Leitor.

    ![Concessão de permissão do RBAC para carregar](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

2. Configure a autenticação AD através da [seguinte documentação](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#create-an-azure-ad-administrator-for-azure-sql-server). 

3. Conecte-se à sua piscina SQL utilizando o Ative Directory, onde agora pode executar a declaração COPY sem especificar quaisquer credenciais:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```


## <a name="e-service-principal-authentication"></a>E. Autenticação do Principal de Serviço
#### <a name="steps"></a>Passos

1. [Criar uma aplicação Azure Ative Directory (AAD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)
2. [Obtenha iD de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)
3. [Obtenha a chave de autenticação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret)
4. [Obtenha o V1 OAuth 2.0 token ponto final](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Atribua permissões de leitura, escrita e execução à sua aplicação AAD](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) na sua conta de armazenamento
6. Pode agora executar a declaração COPY:

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
> - Utilize a versão **V1** do ponto final simbólico OAuth 2.0

## <a name="next-steps"></a>Passos seguintes

- Consulte o artigo [do artigo de declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) para obter a sintaxe detalhada
- Consulte o artigo [geral de carregamento de dados](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt) para as melhores práticas de carregamento
