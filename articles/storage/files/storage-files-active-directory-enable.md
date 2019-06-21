---
title: Ativar a autenticação do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização) - armazenamento do Azure
description: Saiba como ativar a autenticação baseada em identidade através de SMB (Server Message Block) (pré-visualização) para ficheiros do Azure através dos serviços de domínio do Azure Active Directory (Azure AD). Máquinas de virtuais de Windows (VMs) do seu associados a um domínio, em seguida, pode aceder a partilhas de ficheiros do Azure com credenciais do Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/19/2019
ms.author: rogarana
ms.openlocfilehash: 80d871bdc17c3f93e113b08201d6c53f29bfeff0
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295606"
---
# <a name="enable-azure-active-directory-domain-service-authentication-over-smb-for-azure-files-preview"></a>Ativar a autenticação de serviço de domínio do Active Directory do Azure através de SMB para ficheiros do Azure (pré-visualização)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para uma descrição geral da autenticação do Azure AD através de SMB para ficheiros do Azure, consulte [autenticação de descrição geral do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Descrição geral do fluxo de trabalho
Antes de ativar o Azure AD através de SMB para ficheiros do Azure, certifique-se de que o Azure AD e ambientes de armazenamento do Azure estão configurados corretamente. Recomenda-se que irá ajudá-lo a [pré-requisitos](#prerequisites) para se certificar de que o utilizador tive executado todos os passos necessários. 

Em seguida, conceda acesso aos recursos de ficheiros do Azure com credenciais do Azure AD através dos seguintes passos: 

1. Ative a autenticação do Azure AD através de SMB para a sua conta de armazenamento para registar a conta de armazenamento com a implementação de serviços de domínio do Azure AD associada.
2. Atribua permissões de acesso para uma partilha para uma identidade do Azure AD (um utilizador, grupo ou principal de serviço).
3. Configure permissões de NTFS através de SMB para ficheiros e diretórios.
4. Monte uma partilha de ficheiros do Azure a partir de uma VM associados a um domínio.

O diagrama abaixo ilustra o fluxo de trabalho ponto-a-ponto para ativar a autenticação do Azure AD através de SMB para ficheiros do Azure. 

![Diagrama que mostra os do Azure AD através de SMB para fluxo de trabalho de ficheiros do Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Pré-requisitos 

Antes de ativar o Azure AD através de SMB para ficheiros do Azure, certifique-se de que concluiu os seguintes pré-requisitos:

1.  **Selecione ou crie um inquilino do Azure AD.**

    Pode utilizar um inquilino novo ou existente para a autenticação do Azure AD através de SMB. O inquilino e a partilha de ficheiros que pretende aceder tem de ser associados à mesma subscrição.

    Para criar um novo inquilino do Azure AD, pode [adicionar um inquilino do Azure AD e uma subscrição do Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se tiver um inquilino do Azure AD existente, mas deseja criar um novo inquilino para utilização com ficheiros do Azure, veja [criar um inquilino do Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Ative os serviços de domínio do Azure AD no inquilino do Azure AD.**

    Para suportar a autenticação com credenciais do Azure AD, tem de ativar o Azure AD Domain Services para o seu inquilino do Azure AD. Se não o administrador de inquilino do Azure AD, contacte o administrador e siga as orientações passo a passo para [ativar o Azure Active Directory Domain Services no portal do Azure](../../active-directory-domain-services/create-instance.md).

    Normalmente, demora cerca de 15 minutos para uma implementação de serviços de domínio do Azure AD concluir. Certifique-se de que mostra o estado de funcionamento dos seus serviços de domínio do Azure AD **em execução**, com a sincronização de hash de palavra-passe ativada, antes de avançar para o passo seguinte.

3.  **Associação ao domínio dos serviços de uma VM do Azure com o domínio do Azure AD.**

    Para aceder a uma partilha de ficheiros com credenciais do Azure AD a partir de uma VM, a VM tem de ser associado ao Azure AD Domain Services. Para obter mais informações sobre como associação a domínios uma VM, consulte [associar uma máquina de virtual do Windows Server a um domínio gerido](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Autenticação do Azure AD através de SMB com ficheiros do Azure só é suportada em VMs do Azure em execução em versões de SO acima Windows 7 ou Windows Server 2008 R2.

4.  **Selecione ou crie uma partilha de ficheiros do Azure.**

    Selecione uma partilha de ficheiros de novo ou existente que está associada à mesma subscrição que o inquilino do Azure AD. Para obter informações sobre como criar uma nova partilha de ficheiros, consulte [criar uma partilha de ficheiros nos ficheiros do Azure](storage-how-to-create-file-share.md). 
    Para um desempenho ideal, a Microsoft recomenda que a partilha de ficheiros está na mesma região que a VM a partir do qual pretende aceder à partilha.

5.  **Verifique a conectividade de ficheiros do Azure ao montar partilhas de ficheiros do Azure com a sua chave de conta de armazenamento.**

    Para verificar que a partilha de ficheiros e de VM estão configurados corretamente, tente montar a partilha de ficheiros utilizando a sua chave de conta de armazenamento. Para obter mais informações, consulte [montar uma partilha de ficheiros do Azure e aceder à partilha no Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-authentication-for-your-account"></a>Ativar a autenticação do Azure AD para a sua conta

Para ativar a autenticação do Azure AD através de SMB para ficheiros do Azure, pode definir uma propriedade em contas de armazenamento criadas após 24 de Setembro de 2018, com o portal do Azure, o Azure PowerShell ou o CLI do Azure. Definir essa propriedade registra a conta de armazenamento com a implementação de serviços de domínio do Azure AD associada. Autenticação do Azure AD através de SMB, em seguida, está ativada para todas as partilhas de ficheiros de novas e existentes na conta de armazenamento. 

Tenha em atenção que pode ativar a autenticação do Azure AD através de SMB apenas depois de implementar com êxito do Azure AD Domain Services para o inquilino do Azure AD. Para obter mais informações, consulte a [pré-requisitos](#prerequisites).

### <a name="azure-portal"></a>Portal do Azure

Para ativar a autenticação do Azure AD com o uso de SMB os [portal do Azure](https://portal.azure.com), siga estes passos:

1. No portal do Azure, navegue até à sua conta de armazenamento existente, ou [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md).
2. Na **configurações** secção, selecione **configuração**.
3. Ativar **autenticação do Active Directory do Azure para ficheiros do Azure (pré-visualização)** .

A imagem seguinte mostra como ativar a autenticação do Azure AD através de SMB para a sua conta de armazenamento.

![Ativar autenticação do Azure AD através de SMB no portal do Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Para ativar a autenticação do Azure AD através de SMB do Azure PowerShell, primeiro de instalar uma compilação de pré-visualização do `Az.Storage` módulo com o suporte do Azure AD. Para obter mais informações sobre como instalar o PowerShell, consulte [instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Em seguida, crie um novo armazenamento de conta, em seguida, chamar [conjunto AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) e defina a **EnableAzureFilesAadIntegrationForSMB** parâmetro **verdadeiro**. No exemplo abaixo, não se esqueça de substituir os valores de marcador de posição pelos seus próprios valores.

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true
```

### <a name="azure-cli"></a>CLI do Azure

Para ativar a autenticação do Azure AD através de SMB da CLI 2.0 do Azure, instale primeiro o `storage-preview` extensão:

```cli-interactive
az extension add --name storage-preview
```
  
Em seguida, crie um novo armazenamento de conta, em seguida, chamar [atualização da conta de armazenamento az](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) e defina a `--file-aad` propriedade **verdadeiro**. No exemplo abaixo, não se esqueça de substituir os valores de marcador de posição pelos seus próprios valores.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Atribua permissões de acesso para uma identidade 

Para aceder a recursos de ficheiros do Azure com credenciais do Azure AD, uma identidade (um utilizador, grupo ou principal de serviço) tem de ter as permissões necessárias ao nível da partilha. As orientações nesta secção demonstra como atribuir ler, escrever ou eliminar as permissões para uma partilha de ficheiros para uma identidade.

> [!IMPORTANT]
> Controle administrativo completo de uma partilha de ficheiros, incluindo a capacidade de atribuir uma função para uma identidade, é necessário usar a chave de conta de armazenamento. Controlo administrativo não é suportado com credenciais do Azure AD. 

### <a name="define-a-custom-role"></a>Definir uma função personalizada

Para conceder permissões de nível de compartilhamento, definir uma função RBAC personalizada e atribua-a para uma identidade, controlo de âmbito para uma partilha de ficheiros específicos. Este processo é semelhante a especificar permissões de partilha do Windows, em que especifique o tipo de acesso que um determinado usuário tem uma partilha de ficheiros.  

Os modelos mostrados nas seções a seguir fornecem as permissões de leitura ou alterar para uma partilha de ficheiros. Para definir uma função personalizada, crie um ficheiro JSON e copie o modelo apropriado para esse ficheiro. Para obter mais informações sobre como definir funções RBAC personalizadas, consulte [funções personalizadas no Azure](../../role-based-access-control/custom-roles.md).

**Definição de função para permissões de alteração do nível da partilha**  
O modelo de função personalizada seguinte fornece permissões de alteração de nível de compartilhamento, conceder uma identidade de leitura, escrita e acesso de eliminação para a partilha.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share over SMB",
  "Actions": [
    "Microsoft.Storage/storageAccounts/fileServices/*"
  ],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/fileServices/*"
  ],
  "NotDataActions": [
    "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action",
    "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

**Definição de função para permissões de leitura de nível de compartilhamento**  
O modelo de função personalizada seguinte fornece permissões de leitura de nível de compartilhamento, conceder um acesso de leitura de identidade para a partilha.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure File Share over SMB",
  "Actions": [
    "Microsoft.Storage/storageAccounts/fileServices/*/read"
  ],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/fileServices/*/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

### <a name="create-the-custom-role"></a>Criar a função personalizada

Para criar a função personalizada, utilize o PowerShell ou CLI do Azure. 

#### <a name="powershell"></a>PowerShell

O seguinte comando do PowerShell cria uma função personalizada com base em um dos modelos de exemplo.

```powershell
#Create a custom role based on the sample template above
New-AzRoleDefinition -InputFile "<custom-role-def-json-path>"
```

#### <a name="cli"></a>CLI 

O seguinte comando da CLI do Azure cria uma função personalizada com base em um dos modelos de exemplo.

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
```

### <a name="assign-the-custom-role-to-the-target-identity"></a>Atribuir a função personalizada para a identidade de destino

Em seguida, utilize o PowerShell ou CLI do Azure para atribuir a função personalizada para uma identidade do Azure AD. 

#### <a name="powershell"></a>PowerShell

O comando do PowerShell seguinte mostra como listar as funções personalizadas disponíveis e, em seguida, atribuir uma função personalizada para uma identidade do Azure AD, com base no nome de início de sessão. Para obter mais informações sobre a atribuição de funções RBAC com o PowerShell, consulte [gerir o acesso com RBAC e o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Quando executar o seguinte script de exemplo, não se esqueça de substituir os valores de marcador de posição, incluindo os parênteses, com seus próprios valores.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
O seguinte comando da CLI 2.0 mostra como listar as funções personalizadas disponíveis e, em seguida, atribuir uma função personalizada para uma identidade do Azure AD, com base no nome de início de sessão. Para obter mais informações sobre a atribuição de funções RBAC com a CLI do Azure, consulte [gerir o acesso com RBAC e a CLI do Azure](../../role-based-access-control/role-assignments-cli.md). 

Quando executar o seguinte script de exemplo, não se esqueça de substituir os valores de marcador de posição, incluindo os parênteses, com seus próprios valores.

```azurecli-interactive
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custom-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Configurar permissões de NTFS através de SMB 
Depois de atribuir permissões ao nível da partilha com o RBAC, tem de atribuir permissões de NTFS adequadas no nível de arquivo, diretório ou raiz. Considere as permissões de nível de compartilhamento como o controlador de chamadas de alto nível que determina se um usuário pode acessar o compartilhamento, enquanto as permissões NTFS atuam num nível mais granular para determinar as operações que o utilizador pode realizar no nível de arquivo ou diretório. 

Os ficheiros do Azure suporta o conjunto completo de permissões de NTFS básicas e avançadas. Pode ver e configurar permissões NTFS em diretórios e ficheiros numa partilha de ficheiros do Azure montar a partilha e, em seguida, executando o Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) comando. 

> [!NOTE]
> A versão de pré-visualização suporta a visualização com o Explorador de ficheiros do Windows apenas permissões. Ainda, o permissões de edição não é suportada.

Para configurar permissões NTFS com privilégios de Superutilizador, tem de montar a partilha com a sua chave de conta de armazenamento da VM associados a um domínio. Siga as instruções na secção seguinte para montar uma partilha de ficheiros do Azure na linha de comandos e configurar permissões NTFS em conformidade.

Os seguintes conjuntos de permissões são suportados no diretório de raiz de uma partilha de ficheiros:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(Rx)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CRIADOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montar uma partilha de ficheiros a partir da linha de comandos

Utilizar o Windows **net use** comandos para montar a partilha de ficheiros do Azure. Lembre-se substituir os valores de marcador de posição no exemplo pelos seus próprios valores. Para obter mais informações sobre as partilhas de ficheiros de montagem, consulte [montar uma partilha de ficheiros do Azure e aceder à partilha no Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurar permissões NTFS com icacls
Utilize o seguinte comando do Windows para conceder permissões totais a todos os diretórios e arquivos no compartilhamento de arquivos, incluindo o diretório de raiz. Lembre-se substituir os valores de marcador de posição mostrados entre parênteses Retos, exemplo pelos seus próprios valores.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para obter mais informações sobre como usar icacls para definir permissões NTFS e do tipo diferente de permissões suportados, consulte [a referência da linha de comandos para a icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montar uma partilha de ficheiros a partir de uma VM associados a um domínio 

Agora, está pronto para verificar que concluiu os passos anteriores com êxito utilizando partilham as suas credenciais do Azure AD para acessar um arquivo do Azure a partir de uma VM associados a um domínio. Primeiro, inicie sessão para a VM com a identidade do Azure AD aos quais concedeu permissões, conforme mostrado na imagem seguinte.

![Captura de ecrã que mostra do Azure AD início de sessão no ecrã para a autenticação de utilizador](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Em seguida, utilize o comando seguinte para montar a partilha de ficheiros do Azure. Lembre-se substituir os valores de marcador de posição pelos seus próprios valores. Uma vez que já tiver sido autenticado, não é necessário fornecer a chave de conta de armazenamento ou o nome de utilizador do Azure AD e a palavra-passe. O Azure AD através de SMB suporta uma única experiência de logon usando credenciais do Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Agora com êxito ativou a autenticação do Azure AD através de SMB e atribuída uma função personalizada que fornece acesso a uma partilha de ficheiros para uma identidade do Azure AD. Para conceder acesso à partilha de ficheiros para os utilizadores adicionais, siga as instruções fornecidas no passo 2 e 3.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os ficheiros do Azure e utilizar o Azure AD através de SMB, consulte estes recursos:

- [Introdução aos ficheiros do Azure](storage-files-introduction.md)
- [Descrição geral da autenticação do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
