---
title: Migrar o certificado de TDE - Instância Gerida da Base de Dados SQL do Azure | Microsoft Docs
description: Migrar o certificado de proteger a chave de encriptação de base de dados de uma base de dados com encriptação de dados transparente para a instância gerida da base de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: f27a5b0deb0dd446d4f05b0a6d6e96d67d24d9e9
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316001"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Migrar o certificado de base de dados do TDE protegido para a instância gerida da base de dados SQL do Azure

Ao migrar uma base de dados protegido pelo [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) para o Azure SQL Database Managed Instance usando a opção Restaurar nativo, o certificado correspondente do IaaS SQL Server ou no local tem de ser migrados antes do restauro de base de dados. Este artigo orienta-o pelo processo de migração manual do certificado para a Instância Gerida da Base de Dados SQL do Azure:

> [!div class="checklist"]
> * Exportar o certificado para um ficheiro Personal Information Exchange (.pfx)
> * Extrair o certificado do ficheiro para uma cadeia de base 64
> * Carregar o certificado com o cmdlet do PowerShell

Para uma opção alternativa através do serviço completamente gerido para uma migração totalmente integrada da base de dados protegida de TDE e do certificado correspondente, veja [Como migrar a base de dados no local para a Instância Gerida com o Azure Database Migration Service](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Encriptação de dados transparente para a instância gerida da base de dados SQL do Azure funciona em modo de serviço gerido. O certificado migrado é utilizado apenas no restauro da base de dados protegida de TDE. Logo após a conclusão do restauro, o certificado migrado é substituído por um certificado gerido pelo sistema diferente.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para executar os passos descritos neste artigo, os seguintes requisitos são necessários:

- Ferramenta de linha de comandos [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) instalada no servidor no local ou noutro computador com acesso ao certificado exportado como um ficheiro. A ferramenta Pvk2Pfx faz parte do [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), um ambiente de linha de comandos autónomo.
- Versão 5.0 ou posterior do [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell) instalada.
- Módulo Azure PowerShell [instalado e atualizado](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [Módulo de Az.Sql](https://www.powershellgallery.com/packages/Az.Sql).
  Execute os seguintes comandos no PowerShell para instalar/atualizar o módulo do PowerShell:

   ```powershell
   Install-Module -Name Az.Sql
   Update-Module -Name Az.Sql
   ```

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Exportar o certificado de TDE para um ficheiro Personal Information Exchange (.pfx)

O certificado pode ser exportado diretamente do SQL Server de origem ou do arquivo de certificados, se estiver nessa localização.

### <a name="export-certificate-from-the-source-sql-server"></a>Exportar o certificado a partir do SQL Server de origem

Utilize os seguintes passos para exportar o certificado com o SQL Server Management Studio e convertê-lo no formato .pfx. Os nomes genéricos *TDE_Cert* e *full_path* estão a ser utilizados nos passos como nomes de certificado e ficheiro, bem como caminhos. Devem ser substituídos pelos nomes reais.

1. No SSMS, abra uma nova janela de consulta e ligue ao SQL Server de origem.
2. Utilize o script seguinte para listar as bases de dados protegidas de TDE e obter o nome da encriptação que está a proteger o certificado da base de dados a migrar:

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

3. Execute o script seguinte para exportar o certificado para um par de ficheiros (.cer e .pvk) ao manter as informações da chave pública e privada:

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

4. Utilize a consola do PowerShell para copiar as informações do certificado de um par de ficheiros recentemente criados para um ficheiro Personal Information Exchange (.pfx) com a ferramenta Pvk2Pfx:

   ```powershell
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Exportar o certificado do arquivo de certificados

Se o certificado estiver no arquivo de certificados do computador local do SQL Server, pode ser exportado através dos seguintes passos:

1. Abra a consola do PowerShell e execute o comando seguinte para abrir o snap-in Certificados da Consola de Gestão da Microsoft:

   ```powershell
   certlm
   ```

2. No snap-in Certificados da MMC, expanda o caminho Pessoal -> Certificados para ver a lista de certificados

3. Clique com o botão direito do rato no certificado e clique em Exportar...

4. Siga o assistente para exportar o certificado e a chave privada para o formato Personal Information Exchange

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Carregar o certificado para o Azure SQL Database Managed Instance utilizando o cmdlet do PowerShell do Azure

1. Comece pelos passos de preparação no PowerShell:

   ```powershell
   # Import the module into the PowerShell session
   Import-Module Az
   # Connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # List subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # Set subscription for the session (replace Guid_Subscription_Id with actual subscription id)
   Select-AzSubscription Guid_Subscription_Id
   ```

2. Depois de concluir todos os passos de preparação, execute os comandos seguintes para carregar o certificado codificado de base 64 para a Instância Gerida de destino:

   ```powershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "SomeStrongPassword"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<ResourceGroupName>" -ManagedInstanceName "<ManagedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

O certificado está agora disponível para a Instância Gerida especificada e a cópia de segurança da base de dados protegida de TDE correspondente pode ser restaurada com êxito.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a migrar o certificado de proteger a chave de encriptação da base de dados com encriptação de dados transparente, desde o IaaS SQL Server para instância gerida da base de dados SQL do Azure ou no local.

Veja [Restaurar uma cópia de segurança da base de dados para uma Instância Gerida da Base de Dados SQL do Azure](sql-database-managed-instance-get-started-restore.md) para saber como restaurar uma cópia de segurança de base de dados para uma Instância Gerida da Base de Dados SQL do Azure.
