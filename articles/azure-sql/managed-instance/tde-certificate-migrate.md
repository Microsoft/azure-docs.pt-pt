---
title: Certificado de TDE migrado - instância gerida
description: Migrar um certificado que proteja a chave de encriptação da base de dados de uma base de dados com encriptação de dados transparente para Azure SQL Gestd Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: 80ff16156348db9c3a209757b48b7d54615d9104
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790700"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>Migrar um certificado de uma base de dados protegida pelo TDE para a Azure SQL Gerenciada Instância
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Quando está a migrar uma base de dados protegida pela [Encriptação de Dados Transparente (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) para Azure SQL Gestdited Instance utilizando a opção de restauro nativo, o certificado correspondente da instância SQL Server precisa de ser migrado antes da restauração da base de dados. Este artigo acompanha-o através do processo de migração manual do certificado para Azure SQL Managed Instance:

> [!div class="checklist"]
>
> * Exportar o certificado para um ficheiro de Troca de Informações Pessoais (.pfx)
> * Extrair o certificado de um ficheiro para uma cadeia base-64
> * Faça o upload usando um cmdlet PowerShell

Para uma opção alternativa utilizando um serviço totalmente gerido para migração sem emenda de uma base de dados protegida pelo TDE e um certificado correspondente, consulte [como migrar a sua base de dados no local para Azure SQL Managed Instance usando o Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Um certificado migrado é utilizado apenas para restaurar a base de dados protegida pelo TDE. Logo após a restauração, o certificado migrado é substituído por um protetor diferente, seja um certificado gerido pelo serviço ou uma chave assimétrica do cofre da chave, dependendo do tipo de TDE que definiu no caso.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo, os seguintes requisitos são necessários:

* Ferramenta de linha de comandos [Pvk2Pfx](/windows-hardware/drivers/devtest/pvk2pfx) instalada no servidor no local ou noutro computador com acesso ao certificado exportado como um ficheiro. A ferramenta Pvk2Pfx faz parte do Kit de [Controlador do Windows Enterprise,](/windows-hardware/drivers/download-the-wdk)um ambiente de linha de comando autossuficiente.
* Versão 5.0 ou posterior do [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) instalada.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Certifique-se de que tem o seguinte:

* Módulo Azure PowerShell [instalado e atualizado](/powershell/azure/install-az-ps).
* [Módulo Az.Sql.](https://www.powershellgallery.com/packages/Az.Sql)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Azure SQL Managed Instance, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRM são substancialmente idênticos.

Executar os seguintes comandos no PowerShell para instalar/atualizar o módulo:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>Exportar o certificado TDE para um ficheiro .pfx

O certificado pode ser exportado diretamente a partir da origem SQL Server, ou da loja de certificados se estiver a ser mantido lá.

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>Exportar o certificado a partir da origem SQL Server instância

Utilize os seguintes passos para exportar o certificado com o SQL Server Management Studio e convertê-lo em formato .pfx. Os nomes genéricos *TDE_Cert* e *full_path* estão a ser usados para certificados e nomes de ficheiros e caminhos através dos degraus. Devem ser substituídos pelos nomes reais.

1. No SSMS, abra uma nova janela de consulta e ligue-se à instância do Servidor SQL de origem.

1. Utilize o seguinte script para listar bases de dados protegidas pelo TDE e obter o nome do certificado que protege a encriptação da base de dados a migrar:

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

   ![Lista de certificados TDE](./media/tde-certificate-migrate/onprem-certificate-list.png)

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

   ![Certificado de Reserva TDE](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. Utilize a consola PowerShell para copiar informações de certificados de um par de ficheiros recém-criados para um ficheiro .pfx, utilizando a ferramenta Pvk2Pfx:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>Exportar o certificado a partir de uma loja de certificados

Se o certificado for guardado na loja de certificados locais sql Server, pode ser exportado utilizando os seguintes passos:

1. Abra a consola PowerShell e execute o seguinte comando para abrir o snap-in certificados da Consola de Gestão da Microsoft:

   ```cmd
   certlm
   ```

2. No snap-in MMC dos Certificados, expanda o caminho Personal > Certificates para ver a lista de certificados.

3. Clique com o botão direito no certificado e clique em **Exportação.**

4. Siga o assistente para exportar o certificado e a chave privada para um formato .pfx.

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>Faça o upload do certificado para Azure SQL Managed Instance usando um cmdlet Azure PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Comece pelos passos de preparação no PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Uma vez que todos os passos de preparação estejam concluídos, executar os seguintes comandos para carregar o certificado codificado base-64 para a instância gerida do alvo:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, tens de [configurar um cofre de chaves Azure](../../key-vault/general/manage-with-cli2.md) com o teu ficheiro *.pfx.*

1. Comece pelos passos de preparação no PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Uma vez que todos os passos de preparação estejam concluídos, executar os seguintes comandos para carregar o certificado codificado base-64 para a instância gerida do alvo:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

O certificado está agora disponível para a instância gerida especificada, e a cópia de segurança da base de dados protegida pelo TDE correspondente pode ser restaurada com sucesso.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a migrar um certificado protegendo a chave de encriptação de uma base de dados com Encriptação de Dados Transparente, desde o local ou a instância do Servidor IAAS SQL até ao Azure SQL Managed Instance.

Consulte [Restaurar uma cópia de segurança da base de dados para uma Instância Gerida Azure SQL](restore-sample-database-quickstart.md) para aprender a restaurar uma cópia de segurança da base de dados para Azure SQL Managed Instance.