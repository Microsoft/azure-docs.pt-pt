---
title: Ativar a autenticação de Diretório Ativo sobre SMB para Ficheiros Azure
description: Saiba como permitir a autenticação baseada na identidade sobre sMB para ações de ficheiros Azure através do Ative Directory. As máquinas virtuais do Windows (VMs) podem aceder às partilhas de ficheiros Azure utilizando credenciais AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: rogarana
ms.openlocfilehash: febb796a47b9f5e78906d513c115b62b35c7c7d5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196508"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Ativar no local a autenticação de Serviços de Domínio ativo de diretório ativo sobre SMB para ações de ficheiros Azure

[Ficheiros](storage-files-introduction.md)   Azure suporta a autenticação baseada na identidade sobre o Bloco de Mensagens de Servidor (SMB) através de dois tipos de Serviços de Domínio: Serviços de Domínio ativo azure (Azure AD DS) e no local Ative Directory Domain Services (AD DS) (pré-visualização). Este artigo centra-se no suporte recém-introduzido (pré-visualização) de alavancagem do Ative Directory Domain Service para autenticação em ações de ficheiros Azure. Se estiver interessado em permitir a autenticação azure AD DS (GA) para ações de ficheiros Azure, consulte [o nosso artigo sobre o assunto](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> As ações de ficheiros Azure apenas suportam a autenticação contra um serviço de domínio, quer o Azure Ative Directory Domain Service (Azure AD DS) quer os Serviços de Domínio ativo do Diretório Ativo (AD DS). 
>
> As identidades AD DS utilizadas para a autenticação de partilha de ficheiros Azure devem ser sincronizadas com a Azure AD. A sincronização de hash de senha é opcional. 
> 
> No local, a autenticação AD DS não suporta a autenticação contra contas de Computador criadas em DS AD. 
> 
> No local, a autenticação AD DS só pode ser suportada contra uma floresta ad onde a conta de armazenamento está registada. Só pode aceder às partilhas de ficheiros Azure com as credenciais AD DS de uma única floresta por defeito. Se precisar de aceder à sua partilha de ficheiros Azure a partir de uma floresta diferente, certifique-se de que tem a confiança florestal adequada configurada, consulte [as FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) para obter mais detalhes.  
> 
> A autenticação AD DS para acesso SMB e persistência ACL é suportada para ações de ficheiroS Azure geridas pelo Azure File Sync.
>
> O Azure Files suporta a autenticação kerberos com encriptação RC4-HMAC. A encriptação da AES Kerberos ainda não é suportada.

Quando ativa as ações de ficheiroS AD DS para o Ficheiro Azure sobre a SMB, as suas máquinas associadas a AD DS podem montar partilhas de ficheiros Azure utilizando as credenciais ad existentes. Esta capacidade pode ser ativada com um ambiente AD DS hospedado em máquinas on-prem ou hospedados em Azure.

As identidades utilizadas para aceder às ações de ficheiros Azure devem ser sincronizadas com a Azure AD para impor permissões de ficheiros de nível de partilha através do modelo de controlo de [acesso baseado em funções (RBAC).](../../role-based-access-control/overview.md) [Os DACLs ao estilo do Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) em ficheiros/diretórios transportados a partir de servidores de ficheiros existentes serão preservados e aplicados. Esta funcionalidade oferece uma integração perfeita com o ambiente AD DS da sua empresa. À medida que substitui os servidores de ficheiros on-prem por partilhas de ficheiros Azure, os utilizadores existentes podem aceder às partilhas de ficheiros Azure dos seus clientes atuais com uma única experiência de início de sessão, sem qualquer alteração às credenciais em uso.  

> [!NOTE]
> Para ajudá-lo a configurar a autenticação da AD Azure Files para alguns casos de uso comum, publicamos [dois vídeos](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) com orientação passo a passo:
> - Substituição de servidores de ficheiros no local por Ficheiros Azure (incluindo configuração em link privado para ficheiros e autenticação AD)
> - Utilização de Ficheiros Azure como recipiente de perfil para o Windows Virtual Desktop (incluindo configuração na autenticação AD e configuração FsLogix)

## <a name="prerequisites"></a>Pré-requisitos 

Antes de ativar a autenticação AD DS para as ações de ficheiros Azure, certifique-se de que preencheu os seguintes pré-requisitos: 

- Selecione ou crie o seu ambiente AD DS e [sincronize-o com a AD Azure](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    Pode ativar a funcionalidade num ambiente AD DS novo ou existente no local. As identidades utilizadas para o acesso devem ser sincronizadas com a AD Azure. O inquilino da AD Azure e a partilha de ficheiros a que acede devem estar associados à mesma subscrição. 

    Para configurar um ambiente de domínio aD, consulte a visão geral dos serviços de domínio do [diretório ativo](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Se não tiver sincronizado o seu AD com o seu AD Azure, siga as orientações no [Azure AD Connect e no Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-install-roadmap.md) para configurar e configurar o Azure AD Connect. 

- Junte-se a uma máquina no local ou a um VM Azure para o DS AD no local. 

    Para aceder a uma partilha de ficheiros utilizando credenciais AD de uma máquina ou VM, o seu dispositivo deve ser associado ao AD DS. Para obter informações sobre como aderir ao domínio, consulte [a Adesão](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)a um Computador a um Domínio . 

- Selecione ou crie uma conta de armazenamento Azure.  Para um desempenho ótimo, recomendamos que implemente a conta de armazenamento na mesma região que a VM a partir da qual planeia aceder à partilha.

    Certifique-se de que a conta de armazenamento que contém as suas ações de ficheiro sem configuração para autenticação Azure AD DS. Se a autenticação Azure Files Azure AD DS estiver ativada na conta de armazenamento, esta deve ser desativada antes de mudar para utilização no local AD DS. Isto implica que os ACLexistentes existentes configurados em ambiente Azure AD DS terão de ser reconfigurados para uma aplicação adequada da permissão.
    
    Para obter informações sobre a criação de uma nova partilha de ficheiros, consulte [Criar uma participação de ficheiro em Ficheiros Azure](storage-how-to-create-file-share.md).

- Verifique a conectividade montando as partilhas de ficheiros Azure utilizando a chave da sua conta de armazenamento. 

    Para verificar se o seu dispositivo e a partilha de ficheiros estão corretamente configurados, tente [montar a parte do ficheiro](storage-how-to-use-files-windows.md) utilizando a chave da sua conta de armazenamento. Se tiver problemas na ligação ao Azure Files, consulte a ferramenta de resolução de [problemas que publicámos para erros](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)de montagem de Ficheiros Azure no Windows . Também fornecemos [orientações](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) para contornar cenários quando o porto 445 está bloqueado. 

## <a name="regional-availability"></a>Disponibilidade regional

A autenticação dos Ficheiros Azure com DS AD (pré-visualização) está disponível em [todas as regiões públicas e regiões de Azure Gov.](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="overview"></a>Descrição geral

Se planeia ativar quaisquer configurações de rede na sua partilha de ficheiros, recomendamos que avalie a consideração de [networking](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) e complete a configuração relacionada primeiro antes de permitir a autenticação AD DS.

Ativar a autenticação AD DS para as suas ações de ficheiro Saque Teais permite autenticar as suas ações de ficheiro Azure com as suas credenciais AD DS on-prem. Além disso, permite-lhe gerir melhor as suas permissões para permitir o controlo de acesso granular. Fazê-lo requer identidades sincronizadas de AD DS on-prem para Azure AD com ligação aD. Controla o acesso ao nível de partilha com identidades sincronizadas com a AD Azure enquanto gere o acesso ao nível de ficheiro/partilha com credenciais ADDS on-prem.

Em seguida, siga os passos abaixo para configurar ficheiros Azure para autenticação DS AD: 

1. [Ativar a autenticação AD DS de Ficheiros Azure na sua conta de armazenamento](#1-enable-ad-ds-authentication-for-your-account)

1. [Atribuir permissões de acesso para uma parte da identidade Azure AD (utilizador, grupo ou diretor de serviço) que esteja em sintonia com a identidade ad-alvo](#2-assign-access-permissions-to-an-identity)

1. [Configure ACLs sobre SMB para diretórios e ficheiros](#3-configure-ntfs-permissions-over-smb)
 
1. [Monte uma partilha de ficheiros Azure para um VM juntou-se ao seu DS AD](#4-mount-a-file-share-from-a-domain-joined-vm)

1. [Atualize a palavra-passe da sua identidade de conta de armazenamento em DS AD](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

O diagrama que se segue ilustra o fluxo de trabalho de ponta a ponta para permitir a autenticação da AD Azure sobre a SMB para partilhas de ficheiros Azure. 

![Diagrama de fluxo de trabalho de Ficheiros AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> A autenticação AD DS sobre SMB para ações de ficheiros Azure só é suportada em máquinas ou VMs em versões OS mais recentes do que o Windows 7 ou Windows Server 2008 R2. 

## <a name="1-enable-ad-ds-authentication-for-your-account"></a>1 Ativar a autenticação AD DS para a sua conta 

Para permitir a autenticação aD DS sobre sMB para ações de ficheiroS Azure, tem de registar primeiro a sua conta de armazenamento com DS AD e, em seguida, definir as propriedades de domínio exigidas na conta de armazenamento. Quando a funcionalidade está ativada na conta de armazenamento, aplica-se a todas as novas e existentes ações de ficheiros na conta. Descarregue o módulo AzFilesHybrid Powershell e utilize `join-AzStorageAccountForAuth` para ativar a funcionalidade. Pode encontrar a descrição detalhada do fluxo de trabalho de ponta a ponta no script dentro desta secção. 

> [!IMPORTANT]
> O `Join-AzStorageAccountForAuth` cmdlet irá fazer modificações no seu ambiente aD. Leia a seguinte explicação para entender melhor o que está a fazer para garantir que tem as permissões adequadas para executar o comando e que as alterações aplicadas estão alinhadas com as políticas de conformidade e segurança. 

O `Join-AzStorageAccountForAuth` cmdlet realizará o equivalente a um domínio offline em nome da conta de armazenamento indicada. O script utiliza o cmdlet para criar uma conta no seu domínio AD, seja uma [conta de computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (predefinido) ou uma conta de [logon](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)de serviço . Se optar por fazer isto manualmente, deverá selecionar a conta mais adequada para o seu ambiente.

A conta AD DS criada pelo cmdlet representa a conta de armazenamento no domínio AD. Se a conta AD DS for criada sob uma unidade organizacional (OU) que aplique a expiração da palavra-passe, deve atualizar a palavra-passe antes da idade máxima da senha. A não atualização da palavra-passe da conta resultará em falhas de autenticação ao aceder a ações de ficheiros do Azure. Para saber como atualizar a palavra-passe, consulte a [palavra-passe da conta AD DS .](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

Pode utilizar o seguinte script para efetuar o registo e ativar a funcionalidade ou, em alternativa, pode executar manualmente as operações que o script faria. Estas operações são descritas na secção que segue o guião. Não precisas de fazer as duas coisas.

### <a name="11-script-prerequisites"></a>1.1 Requisitos do script
- [Descarregue e desaperte o módulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Instale e execute o módulo num dispositivo que seja de domínio ligado ao AD DS no local com credenciais AD DS que tenham permissões para criar uma conta de logon de serviço ou uma conta de computador na AD alvo.
-  Execute o script usando uma credencial AD DS no local que esteja sincronizada com o seu AD Azure. A credencial AD DS no local deve ter o proprietário da conta de armazenamento ou as permissões de funções rBAC contributivas.
- Certifique-se de que a sua conta de armazenamento se encontra numa [região apoiada.](#regional-availability)

### <a name="12-domain-join-your-storage-account"></a>1.2 Domínio junte-se à sua conta de armazenamento
Lembre-se de substituir os valores do espaço reservado por si próprio nos parâmetros abaixo antes de executá-lo no PowerShell.
> [!IMPORTANT]
> O cmdlet de adesão ao domínio criará uma conta AD para representar a conta de armazenamento (parte de ficheiro) em AD. Pode optar por se registar como conta de computador ou conta de login de serviço, consulte [as FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) para obter mais detalhes. Para as contas de computador, existe uma idade de validade da palavra-passe padrão definida em AD aos 30 dias. Da mesma forma, a conta de logon de serviço pode ter uma idade de validade da palavra-passe predefinida definida no domínio ad ou Unidade Organizacional (OU).
> Para ambos os tipos de conta, recomendamos vivamente que verifique qual é a idade de validade da palavra-passe configurada no seu ambiente ad e planeie [atualizar a palavra-passe da sua identidade de conta](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) de armazenamento em AD da conta AD abaixo antes da idade máxima da senha. Pode considerar [criar uma nova Unidade Organizacional ad (OU) em AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) e desativar a política de expiração de palavras-passe em contas de [computador](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) ou contas de logon de serviço em conformidade. 

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
# You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" `
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

A seguinte descrição resume todas as ações realizadas quando o `Join-AzStorageAccountForAuth` cmdlet é executado. Pode executar estes passos manualmente, se preferir não utilizar o comando:

> [!NOTE]
> Se já executou o `Join-AzStorageAccountForAuth` script acima com sucesso, vá para a secção seguinte "1.3 Confirme que a funcionalidade está ativada". Não precisa de realizar as operações abaixo novamente.

#### <a name="a-checking-environment"></a>a. Ambiente de verificação

Primeiro, o guião verifica o seu ambiente. Especificamente, verifica se o [Ative Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) está instalado e se a concha está a ser executada com privilégios de administrador. Em seguida, verifica se o módulo de [pré-visualização Az.11.1](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) está instalado e instala-o se não for. Se esses cheques passarem, verificará o seu AD DS para ver se existe uma conta de [computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (predefinido) ou uma conta de [logon](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) de serviço que já foi criada com a SPN/UPN como "cifs/your-storage-name-here.file.core.windows.net". Se a conta não existir, criará uma como descrito na secção B abaixo.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Criar uma identidade que represente a conta de armazenamento no seu AD manualmente

Para criar esta conta manualmente, crie uma nova chave Kerberos para a sua conta de armazenamento utilizando `New-AzStorageAccountKey -KeyName kerb1` . Em seguida, use a tecla Kerberos como senha para a sua conta. Esta chave só é utilizada durante a configuração e não pode ser utilizada para qualquer controlo ou operações de plano de dados contra a conta de armazenamento.

Assim que tiver essa chave, crie uma conta de serviço ou de computador sob a sua OU. Utilize a seguinte especificação: SPN: "cifs/your-storage-account-name-here.file.windows.net" Palavra-passe: Tecla Kerberos para a sua conta de armazenamento.

Se a sua OU impor a expiração da palavra-passe, deve atualizar a palavra-passe antes da idade máxima da senha para evitar falhas de autenticação ao aceder a ações de ficheiros do Azure. Consulte a [palavra-passe atualizada da sua identidade de conta de armazenamento em DS AD](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) para obter mais detalhes.

Mantenha o SID da conta recém-criada, vai precisar dele para o próximo passo. A identidade que criou que representam a conta de armazenamento não precisa de ser sincronizada com a Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Ativar a funcionalidade na sua conta de armazenamento

O script permitiria então a funcionalidade na sua conta de armazenamento. Para efetuar esta configuração manualmente, forneça alguns detalhes de configuração para as propriedades de domínio no comando seguinte e, em seguida, execute-a. A conta de armazenamento SID necessária no seguinte comando é o SID da identidade que criou no seu DS AD na [secção anterior](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually).

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 Confirme que a funcionalidade está ativada

Pode verificar se a funcionalidade está ativada na sua conta de armazenamento, pode utilizar o seguinte script:

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

Agora, permitiu a funcionalidade com sucesso na sua conta de armazenamento. Agora que a funcionalidade está ativada, há mais passos que deve executar para utilizar a funcionalidade.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Agora permitiu a autenticação AD DS através de SMB e atribuiu uma função personalizada que fornece acesso a uma partilha de ficheiros Azure com uma identidade AD DS. Para conceder aos utilizadores adicionais acesso à sua parte de ficheiro, siga as instruções nas [permissões](#2-assign-access-permissions-to-an-identity) de acesso da Atribuição para utilizar uma identidade e [configure permissões NTFS através](#3-configure-ntfs-permissions-over-smb) das secções SMB.

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5 Atualize a palavra-passe da sua identidade de conta de armazenamento em DS AD

Se registou a identidade/conta AD DS que representa a sua conta de armazenamento num OU que aplique o tempo de validade da palavra-passe, deve rodar a palavra-passe antes da idade máxima da senha. A não atualização da palavra-passe da conta AD DS resultará em falhas de autenticação no acesso a ações de ficheiros do Azure.  

Para desencadear a rotação da palavra-passe, pode executar o `Update-AzStorageAccountADObjectPassword` comando a partir do módulo AzFilesHybrid. O cmdlet executa ações semelhantes à rotação da chave da conta de armazenamento. Obtém a segunda chave Kerberos da conta de armazenamento e usa-a para atualizar a palavra-passe da conta registada em DS AD. Em seguida, regenera a chave kerberos alvo da conta de armazenamento e atualiza a palavra-passe da conta registada em DS AD. Você deve executar este cmdlet em um ambiente adenádino no local DS.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os Ficheiros Azure e como utilizar a AD sobre sMB, consulte estes recursos:

- [Visão geral do suporte de autenticação baseado na identidade do Azure Files para acesso a SMB](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
