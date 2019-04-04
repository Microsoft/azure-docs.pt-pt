---
title: A atualizar o fornecedor de recursos do MySQL do Azure Stack | Documentos da Microsoft
description: Saiba como pode atualizar o fornecedor de recursos do MySQL do Azure Stack.
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
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 976c05449704b0ecbc48ee5bd4799f300fcac0aa
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651690"
---
# <a name="update-the-mysql-resource-provider"></a>Atualizar o fornecedor de recursos do MySQL 

*Aplica-se a: Sistemas integrados do Azure Stack.*

Um novo MySQL adaptador do fornecedor de recursos pode ser liberado quando cria o Azure Stack são atualizadas. Enquanto o adaptador existente continua a trabalhar, recomendamos que Atualize para a compilação mais recente, logo que possível. 

A partir da versão de versão 1.1.33.0 de fornecedor de recursos do MySQL, as atualizações são cumulativas e não precisam ser instalados na ordem em que foram lançadas; enquanto está a partir da versão 1.1.24.0 ou posterior. Por exemplo, se estiver a executar a versão 1.1.24.0 do fornecedor de recursos do MySQL, em seguida, pode atualizar para a versão 1.1.33.0 ou mais tarde sem a necessidade de instalar primeiro a versão 1.1.30.0. Para rever as versões do fornecedor de recursos disponíveis e a versão do Azure Stack, só são suportados em, consulte a lista de versões no [implementar os pré-requisitos do fornecedor de recursos](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Para atualizar do fornecedor de recursos que utiliza a **UpdateMySQLProvider.ps1** script. O processo é semelhante ao processo utilizado para instalar um fornecedor de recursos, conforme descrito na implementar a secção de fornecedor de recursos deste artigo. O script está incluído no download do fornecedor de recursos. 

 > [!IMPORTANT]
 > Antes de atualizar o fornecedor de recursos, reveja as notas de versão para saber mais sobre novas funcionalidades, correções e os problemas conhecidos que podem afetar a sua implementação.

## <a name="update-script-processes"></a>Processos de script de atualização

O **UpdateMySQLProvider.ps1** script cria uma nova VM com o código do Provedor de recursos mais recentes e migra as definições da VM antiga para a nova VM. As definições que migram incluem a base de dados e informações do servidor de hospedagem e registe o DNS necessário. 

>[!NOTE]
>Recomendamos que baixe a imagem do Windows Server 2016 Core mais recente da gestão do Marketplace. Se precisar de instalar uma atualização, pode colocar um **único** pacote MSU no caminho do local de dependência. O script irá falhar se houver mais de um ficheiro MSU nesta localização.

O script requer a utilização dos mesmos argumentos que são descritos para o script de DeployMySqlProvider.ps1. Forneça o certificado aqui também.  


## <a name="update-script-parameters"></a>Atualizar parâmetros do script 
Pode especificar os seguintes parâmetros da linha de comando ao executar o **UpdateMySQLProvider.ps1** script do PowerShell. Se não o fizer, ou se nenhuma validação de parâmetro falhar, lhe for pedido para fornecer os parâmetros necessários. 

| Nome do Parâmetro | Descrição | Comentário ou o valor predefinido | 
| --- | --- | --- | 
| **CloudAdminCredential** | A credencial para o administrador da nuvem, necessário para acessar o ponto final com privilégios. | _Necessário_ | 
| **AzCredential** | As credenciais para a conta de administrador de serviço do Azure Stack. Utilize as mesmas credenciais que tenha utilizado para implementar o Azure Stack. | _Necessário_ | 
| **VMLocalCredential** |As credenciais para a conta de administrador local do fornecedor de recursos SQL VM. | _Necessário_ | 
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto final com privilégios. |  _Necessário_ | 
| **AzureEnvironment** | O ambiente do Azure da conta de administrador de serviço que utilizou para a implementação do Azure Stack. Apenas necessário para implementações do Azure AD. Os nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se utilizar uma Azure AD, da China **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | O ficheiro. pfx de certificado deve ser colocado neste diretório também. | _Opcional_ (_obrigatório_ para vários nós) | 
| **DefaultSSLCertificatePassword** | A palavra-passe para o certificado. pfx. | _Necessário_ | 
| **MaxRetryCount** | O número de vezes que pretende repetir a cada operação, se ocorrer uma falha.| 2 | 
| **RetryDuration** | O intervalo de tempo limite entre repetições, em segundos. | 120 | 
| **Desinstalar** | Remova o fornecedor de recursos e todos os recursos associados (veja as seguintes notas). | Não | 
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não | 
| **AcceptLicense** | Ignora a linha de comandos para aceitar a licença GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Exemplo de script de atualização
Segue-se um exemplo de como utilizar o *UpdateMySQLProvider.ps1* script que pode executar a partir de uma consola elevada do PowerShell. Certifique-se de que alterar as informações de variável e as palavras-passe, conforme necessário:  

> [!NOTE] 
> O processo de atualização aplica-se apenas a sistemas integrados. 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
```  

## <a name="next-steps"></a>Passos Seguintes
[Manter o fornecedor de recursos MySQL](azure-stack-mysql-resource-provider-maintain.md)
