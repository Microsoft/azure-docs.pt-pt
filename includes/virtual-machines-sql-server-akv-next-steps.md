---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165311"
---
## <a name="next-steps"></a>Passos Seguintes

Depois de ativar a integração do Azure Key Vault, pode ativar a encriptação do SQL Server na sua VM de SQL. Em primeiro lugar, terá de criar uma chave assimétrica dentro do seu Cofre de chaves e uma chave simétrica no SQL Server na sua VM. Em seguida, será capaz de executar instruções T-SQL para ativar a encriptação para as suas bases de dados e as cópias de segurança.

Existem várias formas de criptografia que pode tirar partido de:

* [Encriptação de Dados Transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Cópias de segurança encriptadas](https://msdn.microsoft.com/library/dn449489.aspx)
* [Encriptação de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Os scripts de Transact-SQL seguintes fornecem exemplos para cada uma dessas áreas.

### <a name="prerequisites-for-examples"></a>Pré-requisitos para obter exemplos

Cada exemplo baseia-se os dois pré-requisitos: chamado de uma chave assimétrica do seu Cofre de chaves **CONTOSO_KEY** e uma credencial criada pela funcionalidade de integração AKV chamado **Azure_EKM_TDE_cred**. Os seguintes comandos de Transact-SQL estes pré-requisitos para executar os exemplos de configuração.

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

1. Criar um início de sessão do SQL Server a ser utilizada pelo motor de base de dados para TDE, em seguida, adicionar a credencial a ele.

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

1. Crie a chave de encriptação de base de dados que será utilizada para TDE.

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

### <a name="encrypted-backups"></a>Cópias de segurança encriptadas

1. Criar um início de sessão do SQL Server para ser utilizada pelo motor da base de dados para encriptar as cópias de segurança e adicionar a credencial a ele.

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

1. Cópia de segurança a encriptação de especificação de base de dados com a chave assimétrica armazenada no Cofre de chaves.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Encriptação de nível de coluna (CLE)

Este script cria uma chave simétrica protegida pela chave assimétrica no Cofre de chaves e, em seguida, utiliza a chave simétrica para criptografar dados na base de dados.

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

Para obter mais informações sobre como utilizar estas funcionalidades de encriptação, consulte [usando EKM com recursos de criptografia do SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Tenha em atenção que os passos neste artigo pressupõem que já tem o SQL Server em execução numa máquina virtual do Azure. Caso contrário, veja [aprovisionar uma máquina virtual do SQL Server no Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Para outras diretrizes sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
