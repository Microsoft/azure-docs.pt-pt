---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184209"
---
## <a name="next-steps"></a>Passos seguintes

Depois de ativar a integração do cofre de chaves Azure, pode ativar a encriptação do SQL Server no seu VM SQL. Primeiro, terá de criar uma chave assimétrica dentro do seu cofre chave e uma chave simétrica dentro do Servidor SQL no seu VM. Em seguida, poderá executar declarações T-SQL para permitir a encriptação das suas bases de dados e backups.

Existem várias formas de encriptação que pode tirar partido:

* [Encriptação de Dados Transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Backups encriptados](https://msdn.microsoft.com/library/dn449489.aspx)
* [Encriptação do nível da coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Os seguintes scripts Transact-SQL fornecem exemplos para cada uma destas áreas.

### <a name="prerequisites-for-examples"></a>Pré-requisitos para exemplos

Cada exemplo baseia-se nos dois pré-requisitos: uma chave assimétrica do seu cofre-chave chamada **CONTOSO_KEY** e uma credencial criada pela funcionalidade de integração AKV chamada **Azure_EKM_TDE_cred**. Os seguintes comandos Transact-SQL configuram estes pré-requisitos para executar os exemplos.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Encriptação de Dados Transparente (TDE)

1. Crie um login do Servidor SQL para ser utilizado pelo Motor de Base de Dados para TDE e, em seguida, adicione-lhe a credencial.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Crie a chave de encriptação da base de dados que será usada para o TDE.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Backups encriptados

1. Crie um login do SQL Server para ser utilizado pelo Motor de Base de Dados para encriptar cópias de segurança e adicione-lhe a credencial.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Faça backup da base de dados especificando encriptação com a chave assimétrica armazenada no cofre da chave.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Encriptação do nível da coluna (CLE)

Este script cria uma chave simétrica protegida pela chave assimétrica no cofre da chave e, em seguida, utiliza a chave simétrica para encriptar dados na base de dados.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre como utilizar estas funcionalidades de encriptação, consulte [a Utilização do EKM com as funcionalidades de encriptação do Servidor SQL](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Note que os passos neste artigo assumem que já tem o SQL Server a funcionar numa máquina virtual Azure. Caso contrário, consulte [a Provision a SQL Server virtual machine in Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Para outras orientações sobre a execução do Servidor SQL em VMs Azure, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
