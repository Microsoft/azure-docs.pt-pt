---
title: Ativar a autenticação de Diretório Ativo sobre SMB para Ficheiros Azure
description: Saiba como permitir a autenticação baseada na identidade sobre sMB para ações de ficheiros Azure através do Ative Directory. As máquinas virtuais do Windows (VMs) podem aceder às partilhas de ficheiros Azure utilizando credenciais AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 4a80565df10b470fe6d3fe590578681f85a3bdd9
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566286"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Ativar a autenticação de Diretório Ativo sobre SMB para ações de ficheiros Azure

[O Azure Files](storage-files-introduction.md) suporta a autenticação baseada na identidade através do Bloco de Mensagens do Servidor (SMB) através de dois tipos de Serviços de Domínio: Serviços de Domínio de Diretório Ativo Azure (Azure AD DS) (GA) e Diretório Ativo (AD) (pré-visualização). Este artigo centra-se no suporte recém-introduzido (pré-visualização) de alavancagem do Ative Directory Domain Service para autenticação em ações de ficheiros Azure. Se estiver interessado em permitir a autenticação azure AD DS (GA) para ações de ficheiro sinuosamente consulte [o nosso artigo sobre o assunto](storage-files-active-directory-enable.md). 

> [!NOTE]
> As partilhas de ficheiros Azure apenas suportam a autenticação contra um serviço de domínio, quer o Azure Ative Directory Domain Service (Azure AD DS) quer o Ative Directory (AD). 
>
> As identidades ad utilizadas para a autenticação de partilha de ficheiros Azure devem ser sincronizadas com a Azure AD. A sincronização de hash de senha é opcional. 
> 
> A autenticação ad não suporta a autenticação contra contas de Computador criadas em DS AD Azure. 
> 
> A autenticação ad só pode ser suportada contra uma floresta ad onde a conta de armazenamento está registada. Só pode aceder a partilhas de ficheiros Azure com as credenciais ad a partir de uma única floresta ad por defeito. Se precisar de aceder à sua partilha de ficheiros Azure a partir de uma floresta diferente, certifique-se de que tem a confiança florestal adequada configurada.  
> 
> A autenticação ad para acesso SMB e persistência ACL é suportada para ações de ficheiro SMD geridas pelo Azure File Sync.

Quando ativa as ações de ficheiro smb do Azure, o seu domínio AD pode montar partilhas de ficheiros Azure utilizando as credenciais adexistentes. Esta capacidade pode ser ativada com um ambiente ad alojado em máquinas on-prem ou hospedados em Azure.

As identidades ad utilizadas para aceder às ações de ficheiros Azure devem ser sincronizadas com a Azure AD para impor permissões de ficheiros de nível de partilha através do modelo padrão de controlo de [acesso baseado em funções (RBAC).](../../role-based-access-control/overview.md) [Os DACLs ao estilo do Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) em ficheiros/diretórios transportados a partir de servidores de ficheiros existentes serão preservados e aplicados. Isto oferece uma integração perfeita com a sua empresa ad ad infraestrutura de domínio. À medida que substitui os servidores de ficheiros on-prem por partilhas de ficheiros Azure, os utilizadores existentes podem aceder às partilhas de ficheiros Azure dos seus clientes atuais com uma única experiência de início de sessão, sem qualquer alteração às credenciais em uso.  
 
## <a name="prerequisites"></a>Pré-requisitos 

Antes de ativar a autenticação de AD para as ações de ficheiros Azure, certifique-se de que preencheu os seguintes pré-requisitos: 

- Selecione ou crie o seu ambiente aD e sincronize-o com o Azure AD. 

    Pode ativar a funcionalidade num ambiente ad novo ou existente. As identidades utilizadas para o acesso devem ser sincronizadas com a AD Azure. O inquilino da AD Azure e a partilha de ficheiros a que acede devem estar associados à mesma subscrição. 

    Para configurar um ambiente de domínio aD, consulte a visão geral dos serviços de domínio do [diretório ativo](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Se não sincronizou o seu AD com o seu Anúncio Azure, siga a orientação em [Que é a identidade híbrida com o Diretório Ativo Azure,](../../active-directory/hybrid/whatis-hybrid-identity.md) de forma a determinar o seu método de autenticação preferido e a opção de configuração Azure AD Connect. 

- Junte-se a uma máquina no local ou a um VM Azure utilizando DS ou AD. 

    Para aceder a uma partilha de ficheiros utilizando credenciais AD de uma máquina ou VM, o seu dispositivo deve ser associado ao AD DS. Para obter informações sobre como aderir ao AD, consulte a AD, consulte [a AD](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)para um domínio . 

- Selecione ou crie uma conta de armazenamento Azure [numa região apoiada](#regional-availability). 

    Certifique-se de que a conta de armazenamento que contém as suas ações de ficheiro sem configuração para autenticação Azure AD DS. Se a autenticação Azure Files Azure AD DS estiver ativada na conta de armazenamento, esta deve ser desativada antes de mudar para a utilização de AD. Isto implica que os ACLexistentes existentes configurados em ambiente Azure AD DS terão de ser reconfigurados para uma aplicação adequada da permissão.
    
    Para obter informações sobre a criação de uma nova partilha de ficheiros, consulte [Criar uma participação de ficheiro em Ficheiros Azure](storage-how-to-create-file-share.md).
    
    Para um desempenho ótimo, recomendamos que a sua conta de armazenamento esteja na mesma região que o VM a partir do qual planeia aceder à partilha. 

- Verifique a conectividade montando as partilhas de ficheiros Azure utilizando a chave da sua conta de armazenamento. 

    Para verificar se o seu dispositivo e a partilha de ficheiros estão corretamente configurados, tente montar a parte do ficheiro utilizando a chave da sua conta de armazenamento. Para mais informações, consulte Utilize uma partilha de [ficheiros Azure com o Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Disponibilidade regional

A autenticação ad-ad da Azure Files está disponível na maioria das [regiões públicas,](https://azure.microsoft.com/global-infrastructure/regions/)o subconjunto de regiões em que ainda não está disponível é:

- E.U.A. Oeste
- E.U.A.Oeste 2
- E.U.A. Leste
- E.U.A. Leste 2
- Europa Ocidental
- Europa do Norte

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

Antes de ativar a Autenticação AD sobre SMB para partilhas de ficheiros Azure, recomendamos que passe pelos [pré-requisitos](#prerequisites) e certifique-se de que completou todos os passos. Os pré-requisitos validam que os seus ambientes de AD, Azure AD e Azure Storage estão devidamente configurados. 

Em seguida, conceder acesso aos recursos do Azure Files com credenciais AD: 

- Ativar a autenticação ad ida e volta da Azure Na sua conta de armazenamento.  

- Atribuir permissões de acesso para uma parte da identidade Azure AD (utilizador, grupo ou diretor de serviço) que esteja em sintonia com a identidade ad.a do target. 

- Configure OS ACLs em SMB para diretórios e ficheiros. 

- Monte uma partilha de ficheiros Azure de um domínio AD juntou-se à VM. 

O diagrama que se segue ilustra o fluxo de trabalho de ponta a ponta para permitir a autenticação da AD Azure sobre a SMB para partilhas de ficheiros Azure. 

![Diagrama de fluxo de trabalho de Ficheiros AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> A autenticação ad sobre SMB para partilhas de ficheiros Azure só é suportada em máquinas ou VMs em versões OS mais recentes do que o Windows 7 ou Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Ativar a autenticação de AD para a sua conta 

Para permitir a autenticação de AD sobre SMB para ações de ficheiroS Azure, tem de registar primeiro a sua conta de armazenamento com AD e, em seguida, definir as propriedades de domínio exigidas na conta de armazenamento. Quando a funcionalidade está ativada na conta de armazenamento, aplica-se a todas as novas e existentes ações de ficheiros na conta. Utilize `join-AzStorageAccountForAuth` para ativar a funcionalidade. Pode encontrar a descrição detalhada do fluxo de trabalho de ponta a ponta na secção abaixo. 

> [!IMPORTANT]
> O `join-AzStorageAccountForAuth` cmdlet fará modificações no seu ambiente ad. Leia a seguinte explicação para entender melhor o que está a fazer para garantir que tem as permissões adequadas para executar o comando e que as alterações aplicadas estão alinhadas com as políticas de conformidade e segurança. 

O `join-AzStorageAccountForAuth` cmdlet executará o equivalente a um domínio offline juntar-se-á em nome da conta de armazenamento indicada. Criará uma conta no seu domínio AD, seja uma [conta de computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) ou uma conta de [logon](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)de serviço . A conta AD criada representa a conta de armazenamento no domínio AD. Se a conta AD for criada ao abrigo de uma Unidade Organizacional AD (OU) que aplique a expiração da palavra-passe, deve atualizar a palavra-passe antes da idade máxima da senha. A não atualização da palavra-passe da conta AD resultará em falhas de autenticação ao aceder a ações de ficheiros do Azure. Para saber como atualizar a palavra-passe, consulte a [palavra-passe](#update-ad-account-password)da conta AD atualizada .

Pode utilizar o seguinte script para efetuar o registo e ativar a funcionalidade ou, em alternativa, pode executar manualmente as operações que o script faria. Estas operações são descritas na secção que segue o guião. Não precisas de fazer as duas coisas.

### <a name="script-prerequisites"></a>Pré-requisitos do script

- [Descarregue e desaperte o módulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Instale e execute o módulo num dispositivo que seja de domínio ligado à AD com credenciais AD que tenham permissões para criar uma conta de logon de serviço ou uma conta de computador no AD alvo.
-  Execute o script usando uma credencial de AD que esteja sincronizada com o seu Anúncio Azure. A credencial aD deve ter o proprietário da conta de armazenamento ou as permissões de funções rBAC contributivas.
- Certifique-se de que a sua conta de armazenamento se encontra numa [região apoiada.](#regional-availability)

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzureSubscription -SubscriptionId "<yourSubscriptionIdHere>"

#Register the target storage account with your active directory environment under the target OU
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationUnitName "<ou-name-here>"
```


Segue-se uma descrição das ações realizadas quando o comando `join-AzStorageAccountForAuth` é utilizado. Pode executar estes passos manualmente, se preferir não utilizar o comando:

### <a name="checking-environment"></a>Ambiente de verificação

Primeiro, verifica o seu ambiente. Especificamente, verifica se o [Ative Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) está instalado e se a concha está a ser executada com privilégios de administrador. Em seguida, verifica se o módulo de [pré-visualização Az.11.1](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) está instalado e instala-o se não for. Se esses cheques passarem, verificará o seu AD para ver se existe uma conta de [computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (predefinido) ou uma conta de [logon](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) de serviço que já foi criada com a SPN/UPN como "cifs/your-storage-name-here.file.core.windows.net" e criará uma se não existir.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Criar uma identidade que represente a conta de armazenamento no seu AD manualmente

Para criar esta conta manualmente, crie uma nova chave kerberos para a sua conta de armazenamento utilizando `New-AzStorageAccountKey -Keynam "yourKeyName"`. Em seguida, use a chave kerberos como senha para a sua conta. Esta chave só é utilizada durante a configuração e não pode ser utilizada para qualquer controlo ou operações de plano de dados contra a conta de armazenamento.

Assim que tiver essa chave, crie uma conta de serviço ou de computador sob a sua OU. Utilize a seguinte especificação: SPN: "cifs/your-storage-account-name-here.file.windows.net" Palavra-passe: Tecla Kerberos para a sua conta de armazenamento.

Se a sua OU impor a expiração da palavra-passe, deve atualizar a palavra-passe antes da idade máxima da senha para evitar falhas de autenticação ao aceder a ações de ficheiros do Azure. Consulte a [palavra-passe](#update-ad-account-password) da conta AD atualizada para obter detalhes.

Mantenha o SID da conta recém-criada, vai precisar dele para o próximo passo.

### <a name="enable-the-feature-on-your-storage-account"></a>Ativar a funcionalidade na sua conta de armazenamento

O script permitiria então a funcionalidade na sua conta de armazenamento. Para fazer isto manualmente, forneça alguns detalhes de configuração para as propriedades de domínio no seguinte comando e, em seguida, executá-lo. A conta de armazenamento SID necessária no seguinte comando é o SID da identidade que criou em AD.

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServiesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStirageSid "<your-storage-account-sid>"
```


### <a name="check-if-the-feature-is-enabled"></a>Verifique se a funcionalidade está ativada

Se pretender verificar se a funcionalidade está ativada na sua conta de armazenamento, pode utilizar o seguinte script:

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Agora, permitiu a funcionalidade com sucesso na sua conta de armazenamento. Mesmo que a funcionalidade esteja ativada, ainda precisa de completar ações adicionais para poder utilizar a funcionalidade corretamente.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Permitiu agora a autenticação Azure AD através de SMB e atribuiu uma função personalizada que dá acesso a uma partilha de ficheiros Azure com uma identidade AD. Para conceder aos utilizadores adicionais acesso à sua parte de ficheiro, siga as instruções nas [permissões](#assign-access-permissions-to-an-identity) de acesso da Atribuição para utilizar uma identidade e [configure permissões NTFS através](#configure-ntfs-permissions-over-smb) das secções SMB.

## <a name="update-ad-account-password"></a>Atualizar a palavra-passe da conta AD

Se registou a conta AD que representa a sua conta de armazenamento sob um OU que aplique o tempo de validade da palavra-passe, deve rodar a palavra-passe antes da idade máxima da senha. A não atualização da palavra-passe da conta AD resultará em falhas de autenticação no acesso a ações de ficheiros DoIe.  

Para desencadear a rotação da palavra-passe, pode executar o comando `Update-AzStorageAccountADObjectPassword` a partir do [módulo AzFilesHybrid](#script-prerequisites). O cmdlet executa ações semelhantes à rotação da chave da conta de armazenamento. Obtém a segunda chave Kerberos da conta de armazenamento e usa-a para atualizar a palavra-passe da conta registada em AD. Em seguida, regenera a chave kerberos alvo da conta de armazenamento e atualiza a palavra-passe da conta registada em AD. Você deve executar este cmdlet em um ambiente adesificado de domínio AD.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os Ficheiros Azure e como utilizar a AD sobre sMB, consulte estes recursos:

- [Visão geral do suporte de autenticação baseado na identidade do Azure Files para acesso a SMB](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)