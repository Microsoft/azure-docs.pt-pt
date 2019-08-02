---
title: Habilitar a autenticação de Azure Active Directory sobre o SMB para arquivos do Azure (versão prévia) – armazenamento do Azure
description: Saiba como habilitar a autenticação baseada em identidade sobre o protocolo SMB para arquivos do Azure por meio de Azure Active Directory Domain Services. Suas VMs (máquinas virtuais) do Windows ingressadas no domínio podem acessar compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: c0cfb8b7f0d6e3988ccdfa51cae2748b7008308d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699756"
---
# <a name="enable-azure-active-directory-domain-services-authentication-over-smb-for-azure-files-preview"></a>Habilitar a autenticação de Azure Active Directory Domain Services sobre o SMB para arquivos do Azure (versão prévia)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para obter uma visão geral da autenticação do Azure AD sobre SMB para arquivos do Azure, consulte [visão geral da autenticação de Azure Active Directory sobre o SMB para arquivos do Azure (versão prévia)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Visão geral do fluxo de trabalho
Antes de habilitar a autenticação do Azure AD DS sobre o SMB para arquivos do Azure, verifique se os ambientes do Azure AD e do armazenamento do Azure estão configurados corretamente. Recomendamos que você percorra os [pré-requisitos](#prerequisites) para ter certeza de que concluiu todas as etapas necessárias.

Em seguida, conceda acesso aos recursos de arquivos do Azure com as credenciais do Azure AD seguindo estas etapas: 

1. Habilite a autenticação de AD DS do Azure sobre SMB para sua conta de armazenamento para registrar a conta de armazenamento com a implantação de AD DS do Azure associada.
2. Atribuir permissões de acesso para um compartilhamento a uma identidade do Azure AD (um usuário, grupo ou entidade de serviço).
3. Configure permissões NTFS sobre SMB para diretórios e arquivos.
4. Monte um compartilhamento de arquivos do Azure de uma VM ingressada no domínio.

O diagrama a seguir ilustra o fluxo de trabalho de ponta a ponta para habilitar a autenticação de AD DS do Azure sobre SMB para arquivos do Azure. 

![Diagrama mostrando o Azure AD sobre SMB para o fluxo de trabalho de arquivos do Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de habilitar o Azure AD sobre SMB para arquivos do Azure, verifique se você concluiu os seguintes pré-requisitos:

1.  **Selecione ou crie um locatário do Azure AD.**

    Você pode usar um locatário novo ou existente para a autenticação do Azure AD em SMB. O locatário e o compartilhamento de arquivos que você deseja acessar devem estar associados à mesma assinatura.

    Para criar um novo locatário do Azure AD, você pode [Adicionar um locatário do Azure AD e uma assinatura do Azure ad](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se você tiver um locatário existente do Azure AD, mas quiser criar um novo locatário para uso com os arquivos do Azure, consulte [criar um locatário Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Habilite Azure AD Domain Services no locatário do Azure AD.**

    Para dar suporte à autenticação com as credenciais do Azure AD, você deve habilitar o Azure AD DS para seu locatário do Azure AD. Se você não for o administrador do locatário do Azure AD, entre em contato com o administrador e siga as orientações passo a passo para [habilitar o Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/create-instance.md).

    Normalmente, leva cerca de 15 minutos para que uma implantação de AD DS do Azure seja concluída. Verifique se o status de integridade do Azure AD DS mostra **em execução**, com a sincronização de hash de senha habilitada, antes de prosseguir para a próxima etapa.

3.  **Ingressar no domínio de uma VM do Azure com o Azure AD DS.**

    Para acessar um compartilhamento de arquivos usando as credenciais do Azure AD de uma VM, sua VM deve estar ingressada no domínio para o Azure AD DS. Para obter mais informações sobre como ingressar em um domínio de uma VM, consulte [unir uma máquina virtual do Windows Server a um domínio gerenciado](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > A autenticação do Azure AD DS sobre SMB com arquivos do Azure tem suporte apenas em VMs do Azure em execução nas versões do sistema operacional acima do Windows 7 ou do Windows Server 2008 R2.

4.  **Selecione ou crie um compartilhamento de arquivos do Azure.**

    Selecione um compartilhamento de arquivos novo ou existente que esteja associado à mesma assinatura que o seu locatário do Azure AD. Para obter informações sobre como criar um novo compartilhamento de arquivos, consulte [criar um compartilhamento de arquivos em arquivos do Azure](storage-how-to-create-file-share.md). 
    Para obter um desempenho ideal, recomendamos que o compartilhamento de arquivos esteja na mesma região da VM da qual você planeja acessar o compartilhamento.

5.  **Verifique a conectividade dos arquivos do Azure montando compartilhamentos de arquivos do Azure usando sua chave de conta de armazenamento.**

    Para verificar se a VM e o compartilhamento de arquivos estão configurados corretamente, tente montar o compartilhamento de arquivos usando sua chave de conta de armazenamento. Para obter mais informações, consulte [montar um compartilhamento de arquivos do Azure e acessar o compartilhamento no Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Habilitar a autenticação de AD DS do Azure para sua conta

Para habilitar a autenticação de AD DS do Azure sobre SMB para arquivos do Azure, você pode definir uma propriedade em contas de armazenamento criadas após 24 de setembro de 2018, usando o portal do Azure, Azure PowerShell ou CLI do Azure. Definir essa propriedade registra a conta de armazenamento com a implantação de AD DS do Azure associada. A autenticação do Azure AD DS sobre o SMB é então habilitada para todos os compartilhamentos de arquivos novos e existentes na conta de armazenamento.

Tenha em mente que você pode habilitar a autenticação AD DS do Azure somente por SMB depois de ter implantado com êxito o Azure AD DS em seu locatário do Azure AD. Para obter mais informações, consulte os [pré-requisitos](#prerequisites).

### <a name="azure-portal"></a>Portal do Azure

Para habilitar a autenticação de AD DS do Azure sobre SMB com o [portal do Azure](https://portal.azure.com), siga estas etapas:

1. Na portal do Azure, vá para sua conta de armazenamento existente ou [crie uma conta de armazenamento](../common/storage-quickstart-create-account.md).
2. Na seção **configurações** , selecione **configuração**.
3. Habilite **a autenticação de Azure Active Directory para arquivos do Azure (versão prévia)** .

A imagem a seguir mostra como habilitar a autenticação de AD DS do Azure sobre SMB para sua conta de armazenamento.

![Habilitar a autenticação do Azure AD sobre SMB no portal do Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Para habilitar a autenticação de AD DS do Azure sobre SMB com Azure PowerShell, instale o módulo AZ mais recente (2,4 ou mais recente) ou o módulo AZ. Storage (1,5 ou mais recente). Para obter mais informações sobre como instalar o PowerShell, consulte [instalar Azure PowerShell no Windows com PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Crie uma nova conta de armazenamento, chame [set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount)e, em seguida, defina o parâmetro **EnableAzureActiveDirectoryDomainServicesForFile** como **true**. No exemplo a seguir, lembre-se de substituir os valores de espaço reservado pelos seus próprios valores. (Se você estava usando o módulo de visualização anterior, o parâmetro para a habilitação de recursos é **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
Para habilitar esse recurso em contas de armazenamento existentes, use o seguinte comando:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>CLI do Azure

Para habilitar a autenticação do Azure ad via SMB do CLI do Azure 2,0, primeiro `storage-preview` instale a extensão:

```cli-interactive
az extension add --name storage-preview
```
  
Em seguida, crie uma nova conta de armazenamento, chame [AZ Storage Account Update](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update)e defina `--file-aad` a propriedade como **true**. No exemplo a seguir, lembre-se de substituir os valores de espaço reservado pelos seus próprios valores.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade

Para acessar os recursos de arquivos do Azure com as credenciais do Azure AD, uma identidade (um usuário, grupo ou entidade de serviço) deve ter as permissões necessárias no nível de compartilhamento. Esse processo é semelhante à especificação de permissões de compartilhamento do Windows, em que você especifica o tipo de acesso que um usuário específico tem a um compartilhamento de arquivos. As diretrizes nesta seção demonstram como atribuir permissões de leitura, gravação ou exclusão para um compartilhamento de arquivos a uma identidade.

Introduzimos duas funções internas do Azure para conceder permissões de nível de compartilhamento aos usuários:

- O **leitor de compartilhamento SMB de dados de arquivo de armazenamento** permite acesso de leitura em compartilhamentos de arquivos de armazenamento do Azure via SMB
- O **colaborador de compartilhamento SMB de dados de arquivo de armazenamento** permite acesso de leitura, gravação e exclusão em compartilhamentos de arquivos de armazenamento do Azure via SMB.

> [!IMPORTANT]
> O controle administrativo total de um compartilhamento de arquivos, incluindo a capacidade de atribuir uma função a uma identidade, requer o uso da chave da conta de armazenamento. Não há suporte para o controle administrativo com as credenciais do Azure AD.

Você pode usar o portal do Azure, o PowerShell ou o CLI do Azure para atribuir as funções internas à identidade do Azure AD de um usuário para conceder permissões de nível de compartilhamento.

#### <a name="azure-portal"></a>Portal do Azure
Para atribuir uma função de RBAC a uma identidade do Azure AD, usando o [portal do Azure](https://portal.azure.com), siga estas etapas:

1. Na portal do Azure, vá para o compartilhamento de arquivos ou [crie um compartilhamento de arquivos nos arquivos do Azure](storage-how-to-create-file-share.md).
2. Selecione **controlo de acesso (IAM)** .
3. Selecione **Adicionar uma atribuição de função**
4. Na folha **Adicionar atribuição de função** , selecione a função interna apropriada (leitor de compartilhamento SMB de dados de arquivo de armazenamento, colaborador de compartilhamento SMB de dados de arquivo de armazenamento) na lista de **funções** . Mantenha a opção **atribuir acesso a** na configuração padrão: **Usuário, grupo ou entidade de serviço do Azure ad**. Selecione a identidade do Azure AD de destino por nome ou endereço de email.
5. Selecione **salvar** para concluir a operação de atribuição de função.

#### <a name="powershell"></a>PowerShell

O comando do PowerShell a seguir mostra como atribuir uma função de RBAC a uma identidade do Azure AD, com base no nome de entrada. Para obter mais informações sobre como atribuir funções RBAC com o PowerShell, consulte [gerenciar o acesso usando RBAC e Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Antes de executar o script de exemplo a seguir, lembre-se de substituir os valores de espaço reservado, incluindo colchetes, pelos seus próprios valores.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
O comando da CLI 2,0 a seguir mostra como atribuir uma função de RBAC a uma identidade do Azure AD, com base no nome de entrada. Para obter mais informações sobre como atribuir funções RBAC com CLI do Azure, consulte [gerenciar o acesso usando RBAC e CLI do Azure](../../role-based-access-control/role-assignments-cli.md). 

Antes de executar o script de exemplo a seguir, lembre-se de substituir os valores de espaço reservado, incluindo colchetes, pelos seus próprios valores.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Configurar permissões NTFS sobre SMB 
Depois de atribuir permissões de nível de compartilhamento com o RBAC, você deve atribuir permissões de NTFS adequadas no nível de raiz, diretório ou arquivo. Considere as permissões de nível de compartilhamento como o gatekeeper de alto nível que determina se um usuário pode acessar o compartilhamento. Enquanto as permissões de NTFS atuam em um nível mais granular para determinar quais operações o usuário pode fazer no nível do diretório ou do arquivo.

Os arquivos do Azure dão suporte ao conjunto completo de permissões básicas e avançadas do NTFS. Você pode exibir e configurar as permissões NTFS em diretórios e arquivos em um compartilhamento de arquivos do Azure montando o compartilhamento e, em seguida, executando o comando [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) do Windows ou [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

> [!NOTE]
> A versão de visualização dá suporte à exibição de permissões somente com o explorador de arquivos do Windows. Ainda não há suporte para a edição de permissões.

Para configurar o NTFS com permissões de superusuário, você deve montar o compartilhamento usando sua chave de conta de armazenamento de sua VM ingressada no domínio. Siga as instruções na próxima seção para montar um compartilhamento de arquivos do Azure no prompt de comando e configurar as permissões NTFS adequadamente.

Os seguintes conjuntos de permissões têm suporte no diretório raiz de um compartilhamento de arquivos:

- BUILTIN\Administrators: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (e/s) (GR, GE)
- Usuários do NT AUTHORITY\Authenticated: (OI) (CI) (M)
- NT AUTHORITY\SYSTEM: (F)
- PROPRIETÁRIO CRIADOR: (OI) (CI) (E/S) (F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montar um compartilhamento de arquivos do prompt de comando

Use o comando **net use** do Windows para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores de espaço reservado no exemplo a seguir pelos seus próprios valores. Para obter mais informações sobre como montar compartilhamentos de arquivos, consulte [montar um compartilhamento de arquivos do Azure e acessar o compartilhamento no Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurar permissões NTFS com o icacls
Use o seguinte comando do Windows para conceder permissões totais a todos os diretórios e arquivos no compartilhamento de arquivos, incluindo o diretório raiz. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para obter mais informações sobre como usar o icacls para definir permissões NTFS e sobre os diferentes tipos de permissões com suporte, consulte [a referência de linha de comando para icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montar um compartilhamento de arquivos de uma VM ingressada no domínio

O processo a seguir verifica se suas credenciais do Azure AD foram configuradas corretamente e se você pode acessar um compartilhamento de arquivos do Azure de uma VM ingressada no domínio: 

Entre na VM usando a identidade do Azure AD à qual você concedeu permissões, conforme mostrado na imagem a seguir.

![Captura de tela mostrando telas de entrada do Azure AD para autenticação de usuário](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Use o comando a seguir para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores de espaço reservado pelos seus próprios valores. Como você já foi autenticado, não precisa fornecer a chave da conta de armazenamento ou o nome de usuário e a senha do Azure AD. O Azure AD sobre SMB dá suporte a uma experiência de logon único com as credenciais do Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Agora você habilitou com êxito a autenticação do Azure AD sobre o SMB e atribuiu uma função personalizada que fornece acesso a um compartilhamento de arquivos do Azure com uma identidade do Azure AD. Para conceder a usuários adicionais acesso ao compartilhamento de arquivos, siga as instruções nas seções [atribuir permissões de acesso a uma identidade](#assign-access-permissions-to-an-identity) e [configurar permissões NTFS em relação ao SMB](#configure-ntfs-permissions-over-smb) .

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os arquivos do Azure e como usar o Azure AD em SMB, consulte estes recursos:

- [Introdução aos arquivos do Azure](storage-files-introduction.md)
- [Descrição geral da autenticação do Azure Active Directory através de SMB para Ficheiros do Azure (pré-visualização)](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
