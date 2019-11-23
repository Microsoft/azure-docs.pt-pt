---
title: Migrate TDE certificate - managed instance
description: Migrate certificate protecting Database Encryption Key of a database with transparent Data Encryption to Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: 6ef8d49ba7c9ac2c3c60197c11b9bf5936171f9e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420739"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Migrate certificate of TDE protected database to Azure SQL Database Managed Instance

When migrating a database protected by [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) to Azure SQL Database Managed Instance using native restore option, the corresponding certificate from the on-premises or IaaS SQL Server needs to be migrated before database restore. Este artigo orienta-o pelo processo de migração manual do certificado para a Instância Gerida da Base de Dados SQL do Azure:

> [!div class="checklist"]
> * Exportar o certificado para um ficheiro Personal Information Exchange (.pfx)
> * Extrair o certificado do ficheiro para uma cadeia de base 64
> * Carregar o certificado com o cmdlet do PowerShell

Para uma opção alternativa através do serviço completamente gerido para uma migração totalmente integrada da base de dados protegida de TDE e do certificado correspondente, veja [Como migrar a base de dados no local para a Instância Gerida com o Azure Database Migration Service](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> O certificado migrado é utilizado apenas no restauro da base de dados protegida de TDE. Soon after restore is done, the migrated certificate gets replaced by a different protector, either service-managed certificate or asymmetric key from the key vault, depending on the type of the transparent data encryption you set on the instance.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo, os seguintes requisitos são necessários:

- Ferramenta de linha de comandos [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) instalada no servidor no local ou noutro computador com acesso ao certificado exportado como um ficheiro. A ferramenta Pvk2Pfx faz parte do [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), um ambiente de linha de comandos autónomo.
- Versão 5.0 ou posterior do [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell) instalada.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Certifique-se de que tem o seguinte:

- Azure PowerShell module [installed and updated](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [Az.Sql module](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

Run the following commands in PowerShell to install/update the module:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

* * *

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Exportar o certificado de TDE para um ficheiro Personal Information Exchange (.pfx)

O certificado pode ser exportado diretamente do SQL Server de origem ou do arquivo de certificados, se estiver nessa localização.

### <a name="export-certificate-from-the-source-sql-server"></a>Exportar o certificado a partir do SQL Server de origem

Utilize os seguintes passos para exportar o certificado com o SQL Server Management Studio e convertê-lo no formato .pfx. Os nomes genéricos *TDE_Cert* e *full_path* estão a ser utilizados nos passos como nomes de certificado e ficheiro, bem como caminhos. Devem ser substituídos pelos nomes reais.

1. No SSMS, abra uma nova janela de consulta e ligue ao SQL Server de origem.

1. Utilize o script seguinte para listar as bases de dados protegidas de TDE e obter o nome da encriptação que está a proteger o certificado da base de dados a migrar:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![Lista de certificados de TDE](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

1. Execute o script seguinte para exportar o certificado para um par de ficheiros (.cer e .pvk) ao manter as informações da chave pública e privada:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![Cópia de segurança do certificado de TDE](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

1. Utilize a consola do PowerShell para copiar as informações do certificado de um par de ficheiros recentemente criados para um ficheiro Personal Information Exchange (.pfx) com a ferramenta Pvk2Pfx:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Exportar o certificado do arquivo de certificados

Se o certificado estiver no arquivo de certificados do computador local do SQL Server, pode ser exportado através dos seguintes passos:

1. Abra a consola do PowerShell e execute o comando seguinte para abrir o snap-in Certificados da Consola de Gestão da Microsoft:

   ```cmd
   certlm
   ```

2. No snap-in Certificados da MMC, expanda o caminho Pessoal -> Certificados para ver a lista de certificados

3. Clique com o botão direito do rato no certificado e clique em Exportar...

4. Siga o assistente para exportar o certificado e a chave privada para o formato Personal Information Exchange

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Upload certificate to Azure SQL Database Managed Instance using Azure PowerShell cmdlet

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Comece pelos passos de preparação no PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Depois de concluir todos os passos de preparação, execute os comandos seguintes para carregar o certificado codificado de base 64 para a Instância Gerida de destino:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

You need to first [setup an Azure Key Vault](/azure/key-vault/key-vault-manage-with-cli2) with your *.pfx* file.

1. Comece pelos passos de preparação no PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Depois de concluir todos os passos de preparação, execute os comandos seguintes para carregar o certificado codificado de base 64 para a Instância Gerida de destino:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

O certificado está agora disponível para a Instância Gerida especificada e a cópia de segurança da base de dados protegida de TDE correspondente pode ser restaurada com êxito.

## <a name="next-steps"></a>Passos seguintes

In this article, you learned how to migrate certificate protecting encryption key of database with Transparent Data Encryption, from the on-premises or IaaS SQL Server to Azure SQL Database Managed Instance.

Veja [Restaurar uma cópia de segurança da base de dados para uma Instância Gerida da Base de Dados SQL do Azure](sql-database-managed-instance-get-started-restore.md) para saber como restaurar uma cópia de segurança de base de dados para uma Instância Gerida da Base de Dados SQL do Azure.
