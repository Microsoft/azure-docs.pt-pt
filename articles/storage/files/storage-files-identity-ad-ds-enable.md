---
title: Ativar a autenticação de DS AD para ações de ficheiros Azure
description: Saiba como ativar a autenticação dos Serviços de Domínio do Diretório Ativo sobre as ações de ficheiros Azure. As suas máquinas virtuais do Windows, unidas a domínios, podem então aceder às partilhas de ficheiros Azure utilizando credenciais de DS AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: rogarana
ms.openlocfilehash: 759b80ff3cf20bee1dd909cba59e67f5d36023b2
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660789"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Parte um: ativar a autenticação AD DS para as suas ações de ficheiroS Azure 

Antes de ativar a autenticação dos Serviços de Domínio do Diretório Ativo (DS AD), certifique-se de que leu o [artigo de visão geral](storage-files-identity-auth-active-directory-enable.md) para compreender os cenários e requisitos suportados.

Este artigo descreve o processo necessário para permitir a autenticação dos Serviços de Domínio do Diretório Ativo (DS AD) na sua conta de armazenamento. Depois de ativar a funcionalidade, tem de configurar a sua conta de armazenamento e o seu DS AD, de forma a utilizar credenciais AD DS para autenticar a sua partilha de ficheiros Azure. Para ativar a autenticação AD DS sobre as ações de ficheiroS Azure, é necessário registar a sua conta de armazenamento com DS AD e, em seguida, definir as propriedades de domínio necessárias na conta de armazenamento. Quando a funcionalidade está ativada na conta de armazenamento, aplica-se a todas as ações de ficheiros novas e existentes na conta.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Opção um (recomendado): Utilizar o módulo AzFilesHybrid PowerShell

Os cmdlets do módulo PowerShell AzFilesHybrid estão a fazer as modificações necessárias e permitem a funcionalidade para si. Uma vez que algumas partes dos cmdlets vão interagir com o seu DS AD no local, explicamos o que o cmdlet faz, para que possa determinar se as alterações se alinham com as suas políticas de conformidade e segurança, e garantir que tem as permissões adequadas para executar os cmdlets. Embora recomendemos a utilização do módulo AzFilesHybrid, se não conseguir fazê-lo, fornecemos os passos para que possa executá-los manualmente.

### <a name="download-azfileshybrid-module"></a>Baixar módulo AzFilesHybrid

- [Descarregue e desaperte o módulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases) (módulo GA: v0.2.0+)
- Instale e execute o módulo num dispositivo que seja de domínio associado a AD DS no local com credenciais AD DS que tenham permissões para criar uma conta de início de serviço ou uma conta de computador no AD alvo.
-  Execute o script utilizando uma credencial AD DS no local que está sincronizada com o seu AD Azure. A credencial AD DS no local deve ter permissões de papel do proprietário da conta de armazenamento ou do contribuinte RBAC.

### <a name="run-join-azstorageaccountforauth"></a>Executar Join-AzStorageAccountForAuth

O `Join-AzStorageAccountForAuth` cmdlet executa o equivalente a um domínio offline, juntando-se em nome da conta de armazenamento especificada. O script utiliza o cmdlet para criar uma conta no seu domínio de AD, seja uma [conta de computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (padrão) ou uma conta de início de [serviço](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Se optar por executar o comando manualmente, deverá selecionar a conta mais adequada para o seu ambiente.

A conta DS AD criada pelo cmdlet representa a conta de armazenamento. Se a conta DS AD for criada sob uma unidade organizacional (OU) que aplica a expiração da palavra-passe, deve atualizar a palavra-passe antes da idade máxima da palavra-passe. Não atualizar a palavra-passe da conta antes desse portão resulta em falhas de autenticação ao aceder a ações de ficheiros Azure. Para saber como atualizar a palavra-passe, consulte a [palavra-passe da conta AD DS](storage-files-identity-ad-ds-update-password.md)de atualização .

Lembre-se de substituir os valores do espaço reservado pelos seus nos parâmetros abaixo antes de executá-lo em PowerShell.
> [!IMPORTANT]
> O domínio que une o cmdlet criará uma conta AD para representar a conta de armazenamento (partilha de ficheiros) em AD. Pode optar por se registar como uma conta de computador ou conta de início de sé, consulte [as FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) para obter mais detalhes. Para contas de computador, existe uma idade de validade de senha padrão definida em AD em 30 dias. Da mesma forma, a conta de início de saúde do serviço pode ter uma idade de validade de senha padrão definida no domínio AD ou Unidade Organizacional (OU).
> Para ambos os tipos de conta, recomendamos que verifique a idade de validade da palavra-passe configurada no seu ambiente de AD e planeie [atualizar a palavra-passe da identidade da sua conta](storage-files-identity-ad-ds-update-password.md) de armazenamento da conta AD antes da idade máxima da senha. Pode considerar [a criação de uma nova Unidade Organizacional AD (OU) em AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) e desativar a política de expiração de [senhas](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) em contas de computador ou contas de início de serviço em conformidade. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" `
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>Opção 2: Realizar manualmente as ações de ativação

Se já executou o `Join-AzStorageAccountForAuth` script acima com sucesso, vá ao [Confirme se a função está ativada.](#confirm-the-feature-is-enabled) Não é necessário executar os seguintes passos manuais.

### <a name="checking-environment"></a>Ambiente de verificação

Primeiro, tem de verificar o estado do seu ambiente. Especificamente, deve verificar se o [Ative Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) está instalado e se a concha está a ser executada com privilégios de administrador. Em seguida, verifique se o [módulo Az.Storage 2.0](https://www.powershellgallery.com/packages/Az.Storage/2.0.0) está instalado e instale-o se não estiver. Depois de completar essas verificações, verifique o seu DS AD para ver se existe uma [conta de computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (padrão) ou uma conta de início de [serviço](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) que já foi criada com a SPN/UPN como "cifs/your-your-storage-account-name-here.file.core.windows.net". Se a conta não existir, crie uma como descrito na secção seguinte.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Criar uma identidade que represente a conta de armazenamento na sua AD manualmente

Para criar esta conta manualmente, crie uma nova chave Kerberos para a sua conta de armazenamento. Em seguida, use a chave Kerberos como a palavra-passe para a sua conta com os cmdlets PowerShell abaixo. Esta chave é utilizada apenas durante a configuração e não pode ser utilizada para qualquer controlo ou operações de plano de dados contra a conta de armazenamento. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

Assim que tiver essa chave, crie um serviço ou uma conta de computador sob a sua U. Utilize a seguinte especificação (lembre-se de substituir o texto do exemplo pelo nome da sua conta de armazenamento):

SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Password: Chave Kerberos para a sua conta de armazenamento.

Se a sua UO aplicar a expiração da palavra-passe, tem de atualizar a palavra-passe antes da idade máxima da palavra-passe para evitar falhas de autenticação ao aceder às ações de ficheiros Azure. Consulte [a Atualização da palavra-passe da sua conta de armazenamento em AD](storage-files-identity-ad-ds-update-password.md) para obter mais detalhes.

Mantenha o SID da identidade recém-criada, vai precisar para o próximo passo. A identidade que criou que representa a conta de armazenamento não precisa de ser sincronizada com a Azure AD.

### <a name="enable-the-feature-on-your-storage-account"></a>Ativar a funcionalidade na sua conta de armazenamento

Agora pode ativar a funcionalidade na sua conta de armazenamento. Forneça alguns detalhes de configuração para as propriedades do domínio no comando seguinte e, em seguida, execute-o. A conta de armazenamento SID exigida no seguinte comando é o SID da identidade que criou no seu DS AD [na secção anterior](#creating-an-identity-representing-the-storage-account-in-your-ad-manually).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>Depurar

Pode executar o cmdlet Debug-AzStorageAccountAuth para realizar um conjunto de verificações básicas na sua configuração de AD com o registado no utilizador AD. Este cmdlet é suportado na versão v0.1.2 e superior do AzFilesHybrid. Para obter mais detalhes sobre as verificações efetuadas neste cmdlet, consulte [Não conseguir montar Ficheiros Azure com credenciais AD](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) no guia de resolução de problemas do Windows.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Confirme que a funcionalidade está ativada

Pode verificar se a funcionalidade está ativada na sua conta de armazenamento com o seguinte script:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

## <a name="next-steps"></a>Próximos passos

Por esta altura, conseguiu ativar a funcionalidade na sua conta de armazenamento. Para utilizar a funcionalidade, tem de configurar e fazer alterações. Continue para a próxima secção.

[Parte dois: atribuir permissões de nível de partilha a uma identidade](storage-files-identity-ad-ds-assign-permissions.md)
