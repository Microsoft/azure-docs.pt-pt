---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 200bf290543747cf9abab6113b8013e2eec852a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513394"
---
## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade

Para aceder aos recursos do Azure Files com autenticação baseada na identidade, uma identidade (um utilizador, grupo ou principal de serviço) deve ter as permissões necessárias ao nível da partilha. Este processo é semelhante a especificar permissões de partilha do Windows, onde especifica o tipo de acesso que um determinado utilizador tem a uma partilha de ficheiros. A orientação nesta secção demonstra como atribuir a leitura, a escrita ou a eliminação de permissões para uma partilha de ficheiros a uma identidade. 

Introduzimos três funções integradas da Azure para a concessão de permissões de nível de partilha aos utilizadores:

- **O Storage File Data SMB Share Reader** permite o acesso de leitura em ações de ficheiros de armazenamento Azure sobre SMB.
- **O Storage File Data SMB Share Contributor** permite ler, escrever e eliminar o acesso em ações de ficheiros de armazenamento Azure em todo o SMB.
- **Dados de ficheiros de armazenamento SMB Share Contribuinte Elevado** permite ler, escrever, eliminar e modificar permissões NTFS em ações de ficheiros de armazenamento Azure em todo o SMB.

> [!IMPORTANT]
> O controlo administrativo total de uma parte de ficheiro, incluindo a capacidade de apropriar-se de um ficheiro, requer a utilização da chave da conta de armazenamento. O controlo administrativo não é suportado com credenciais Azure AD.

Pode utilizar o portal Azure, PowerShell ou Azure CLI para atribuir as funções incorporadas à identidade AD AZure de um utilizador para a concessão de permissões de nível de partilha. Esteja ciente de que a atribuição de papel azure de nível de ação pode levar algum tempo a estar em vigor. 

> [!NOTE]
> Lembre-se de [sincronizar as suas credenciais de DS AD AD para Azure AD](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) se planeia utilizar o seu DS AD no local para autenticação. A sincronização de hash de palavra-passe de AD DS a Azure AD é opcional. A permissão de nível de partilha será concedida à identidade Azure AD que está sincronizada a partir do seu DS AD no local.

A recomendação geral é usar a permissão de nível de partilha para uma gestão de acesso de alto nível a um grupo de AD que representa um grupo de utilizadores e identidades, em seguida, aproveitar as permissões NTFS para o controlo de acesso granular no diretório/nível de ficheiro. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Atribuir um papel de Azure a uma identidade de AD

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para atribuir um papel de Azure a uma identidade AD Azure, utilizando o [portal Azure,](https://portal.azure.com)siga estes passos:

1. No portal Azure, vá à sua partilha de ficheiros ou [crie uma partilha de ficheiros.](../articles/storage/files/storage-how-to-create-file-share.md)
2. Selecione **Controlo de Acesso (IAM)**.
3. **Selecione Adicionar uma atribuição de função**
4. Na lâmina de atribuição de **funções Add,** selecione a função incorporada adequada (Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor) da lista **Role.** Deixar **Atribuir acesso à** definição predefinida: **utilizador, grupo ou principal de serviço Azure**. Selecione a identidade AD Azure alvo pelo nome ou endereço de e-mail.
5. **Selecione Guardar** para completar a operação de atribuição de funções.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A seguinte amostra powerShell mostra como atribuir um papel Azure a uma identidade AD Azure, com base no nome de inscrição. Para obter mais informações sobre a atribuição de funções Azure com PowerShell, consulte [Gerir o acesso utilizando o RBAC e o Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Antes de executar o seguinte script de amostra, lembre-se de substituir os valores do espaço reservado, incluindo os suportes, pelos seus próprios valores.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
  
O seguinte comando CLI 2.0 mostra como atribuir um papel Azure a uma identidade AD AZure, com base no nome de inscrição. Para obter mais informações sobre a atribuição de funções Azure com Azure CLI, consulte [Gerir o acesso utilizando o RBAC e o Azure CLI](../articles/role-based-access-control/role-assignments-cli.md). 

Antes de executar o seguinte script de amostra, lembre-se de substituir os valores do espaço reservado, incluindo os suportes, pelos seus próprios valores.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>Configure permissões NTFS sobre SMB

Depois de atribuir permissões de nível de partilha com o RBAC, deve atribuir permissões NTFS adequadas no nível de raiz, diretório ou ficheiro. Pense nas permissões de nível de partilha como o gatekeeper de alto nível que determina se um utilizador pode aceder à partilha. Considerando que as permissões NTFS atuam a um nível mais granular para determinar que operações o utilizador pode fazer ao nível do diretório ou do ficheiro.

O Azure Files suporta o conjunto completo de permissões básicas e avançadas do NTFS. Pode visualizar e configurar permissões NTFS em diretórios e ficheiros numa partilha de ficheiros Azure, montando a partilha e, em seguida, utilizando o Windows File Explorer ou executando os [icacls](/windows-server/administration/windows-commands/icacls) windows ou o comando [Set-ACL.](/powershell/module/microsoft.powershell.security/set-acl) 

Para configurar o NTFS com permissões de super-er, tem de montar a parte utilizando a chave da sua conta de armazenamento a partir do seu VM ligado ao domínio. Siga as instruções na secção seguinte para montar uma partilha de ficheiroS Azure a partir da pronta de comando e para configurar as permissões NTFS em conformidade.

Os seguintes conjuntos de permissões são suportados no diretório de raiz de uma partilha de ficheiros:

- BUILTIN\Administradores:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Utilizadores:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Utilizadores autenticados:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- PROPRIETÁRIO DO CRIADOR:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Monte uma partilha de ficheiro a partir do pedido de comando

Utilize o comando **de utilização da rede** Windows para montar a partilha de ficheiros Azure. Lembre-se de substituir os valores do espaço reservado no exemplo seguinte pelos seus próprios valores. Para obter mais informações sobre a montagem de ações de ficheiros, consulte [utilizar uma partilha de ficheiros Azure com o Windows](../articles/storage/files/storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Se tiver problemas na ligação aos Ficheiros Azure, consulte [a ferramenta de resolução de problemas que publicámos para erros de montagem de Ficheiros Azure no Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Também fornecemos [orientações](../articles/storage/files/storage-files-faq.md#on-premises-access) para trabalhar em torno de cenários quando a porta 445 está bloqueada. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configure permissões NTFS com o Windows File Explorer

Utilize o Windows File Explorer para conceder a permissão total a todos os diretórios e ficheiros sob a partilha de ficheiros, incluindo o diretório de raiz.

1. Abra o Explorador de Ficheiros do Windows e clique no ficheiro/diretório e selecione **Propriedades**.
2. Selecione o separador **Segurança.**
3. **Selecione Edit..** para mudar permissões.
4. Pode alterar as permissões dos utilizadores existentes ou selecionar **Adicionar...** para conceder permissões a novos utilizadores.
5. Na janela de acesso para adicionar novos utilizadores, insira o nome de utilizador-alvo que pretende conceder permissão para introduzir **os nomes do objeto para selecionar** a caixa e selecione **Verificar Nomes** para encontrar o nome UPN completo do utilizador-alvo.
7.    Selecione **OK**.
8.    No separador **Segurança,** selecione todas as permissões que pretende conceder ao seu novo utilizador.
9.    Selecione **Aplicar**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Configure permissões NTFS com ICACLs

Utilize o seguinte comando do Windows para conceder permissões completas a todos os diretórios e ficheiros sob a partilha de ficheiros, incluindo o diretório de raiz. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para obter mais informações sobre como utilizar os ICACLs para definir permissões NTFS e sobre os diferentes tipos de permissões apoiadas, consulte [a referência da linha de comando para icacls](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Monte uma partilha de ficheiros a partir de um VM de domínio

O processo seguinte verifica que as permissões de partilha de ficheiros e acesso foram configuradas corretamente e que pode aceder a uma partilha de FicheiroS Azure a partir de um VM unido a domínio. Esteja ciente de que a atribuição de papel azure de nível de ação pode levar algum tempo a estar em vigor. 

Inscreva-se no VM utilizando a identidade Azure AD à qual concedeu permissões, como mostra a imagem seguinte. Se tiver ativado a autenticação AD DS no local para ficheiros Azure, utilize as suas credenciais de DS AD. Para autenticação Azure AD DS, inscreva-se com credenciais AZURE AD.

![Screenshot mostrando ecrã de inscrição AD AD para autenticação do utilizador](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Utilize o seguinte comando para montar a partilha de ficheiros Azure. Lembre-se de substituir os valores do espaço reservado pelos seus próprios valores. Como foi autenticado, não precisa de fornecer a chave da conta de armazenamento, as credenciais de DS AD no local ou as credenciais AD DS do Azure. Uma experiência única de inscrição é suportada para autenticação com DS AD ou Azure AD DS. Se encontrar problemas de montagem com credenciais de DS AD, consulte [os problemas de Troubleshoot Azure Files no Windows](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) para obter orientação.

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```
