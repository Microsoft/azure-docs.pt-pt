---
title: A atualizar o fornecedor de recursos do SQL do Azure Stack | Documentos da Microsoft
description: Saiba como pode atualizar o fornecedor de recursos do SQL do Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: c4a00d62cc9ab049ed3c647e9d1e2ed1e5a28329
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760010"
---
# <a name="update-the-sql-resource-provider"></a>Atualizar o fornecedor de recursos do SQL

*Aplica-se a: Sistemas integrados do Azure Stack.*

Um novo fornecedor de recursos do SQL pode ser liberado quando o Azure Stack é atualizado para uma nova compilação. Embora o fornecedor de recursos existente continua a funcionar, recomendamos que Atualize para a compilação mais recente, logo que possível. 

A partir da versão de versão 1.1.33.0 de fornecedor de recursos do SQL, as atualizações são cumulativas e não precisam ser instalados na ordem em que foram lançadas; enquanto está a partir da versão 1.1.24.0 ou posterior. Por exemplo, se estiver a executar a versão 1.1.24.0 do fornecedor de recursos do SQL, em seguida, pode atualizar para a versão 1.1.33.0 ou mais tarde sem a necessidade de instalar primeiro a versão 1.1.30.0. Para rever as versões do fornecedor de recursos disponíveis e a versão do Azure Stack, só são suportados em, consulte a lista de versões no [implementar os pré-requisitos do fornecedor de recursos](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Para atualizar o fornecedor de recursos, utilize o *UpdateSQLProvider.ps1* script. Este script está incluído no download do novo fornecedor de recursos do SQL. O processo de atualização é semelhante para o processo usado para [implementar o fornecedor de recursos](./azure-stack-sql-resource-provider-deploy.md). O script de atualização utiliza os mesmos argumentos que o script de DeploySqlProvider.ps1 e terá de fornecer informações de certificado.

 > [!IMPORTANT]
 > Antes de atualizar o fornecedor de recursos, reveja as notas de versão para saber mais sobre novas funcionalidades, correções e os problemas conhecidos que podem afetar a sua implementação.

## <a name="update-script-processes"></a>Processos de script de atualização

O *UpdateSQLProvider.ps1* script cria uma nova máquina virtual (VM) com o código do Provedor de recursos mais recentes.

> [!NOTE]
> Recomendamos que baixe a imagem do Windows Server 2016 Core mais recente da gestão do Marketplace. Se precisar de instalar uma atualização, pode colocar um **único** pacote MSU no caminho do local de dependência. O script irá falhar se houver mais de um ficheiro MSU nesta localização.

Depois do *UpdateSQLProvider.ps1* script cria uma nova VM, o script migra as seguintes definições do fornecedor de antigo VM:

* informações da base de dados
* informações do servidor de alojamento
* registo DNS necessário

## <a name="update-script-parameters"></a>Atualizar parâmetros do script

Pode especificar os seguintes parâmetros da linha de comando ao executar o **UpdateSQLProvider.ps1** script do PowerShell. Se não o fizer, ou se nenhuma validação de parâmetro falhar, lhe for pedido para fornecer os parâmetros necessários.

| Nome do parâmetro | Descrição | Comentário ou o valor predefinido |
| --- | --- | --- |
| **CloudAdminCredential** | A credencial para o administrador da nuvem, necessário para acessar o ponto final com privilégios. | _Necessário_ |
| **AzCredential** | As credenciais para a conta de administrador de serviço do Azure Stack. Utilize as mesmas credenciais que utilizou para a implementação do Azure Stack. | _Necessário_ |
| **VMLocalCredential** | As credenciais para a conta de administrador local do fornecedor de recursos SQL VM. | _Necessário_ |
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto final com privilégios. |  _Necessário_ |
| **AzureEnvironment** | O ambiente do Azure da conta de administrador de serviço que utilizou para a implementação do Azure Stack. Apenas necessário para implementações do Azure AD. Os nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se utilizar uma Azure AD, da China **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Também tem de colocar o ficheiro. pfx de certificado neste diretório. | _Opcional para o nó único, mas obrigatório para vários nós_ |
| **DefaultSSLCertificatePassword** | A palavra-passe para o certificado. pfx. | _Necessário_ |
| **MaxRetryCount** | O número de vezes que pretende repetir a cada operação, se ocorrer uma falha.| 2 |
| **RetryDuration** |O intervalo de tempo limite entre repetições, em segundos. | 120 |
| **Desinstalar** | Remove o fornecedor de recursos e todos os recursos associados. | Não |
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não |

## <a name="update-script-powershell-example"></a>Atualizar o exemplo de script do PowerShell
Segue-se um exemplo de como utilizar o *UpdateSQLProvider.ps1* script que pode executar a partir de uma consola elevada do PowerShell. Certifique-se de que alterar as informações de variável e as palavras-passe, conforme necessário:  

> [!NOTE]
> Este processo de atualização só se aplica aos sistemas integrados do Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert 

 ```

## <a name="next-steps"></a>Passos Seguintes

[Manter o fornecedor de recursos do SQL](azure-stack-sql-resource-provider-maintain.md)
