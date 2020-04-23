---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/11/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 47bd550bbd8d75a06d38babe88b5a95f3790af50
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106559"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. Atribuir permissões de acesso a uma identidade

Para aceder aos recursos do Azure Files com autenticação baseada na identidade, uma identidade (utilizador, grupo ou diretor de serviço) deve ter as permissões necessárias ao nível das ações. Este processo é semelhante ao de especificar as permissões de partilha do Windows, onde especifica o tipo de acesso que um determinado utilizador tem para uma partilha de ficheiros. A orientação nesta secção demonstra como atribuir a leitura, escrita ou exclusão de permissões para uma partilha de ficheiros a uma identidade. 

Introduzimos três funções azure incorporadas para a concessão de permissões de nível de partilha aos utilizadores:

- **Arquivo de armazenamento Data SMB Share Reader** permite ler acesso em ações de ficheiros Azure Storage sobre SMB.
- **Arquivo de armazenamento Dados SMB Share Contributor** permite ler, escrever e excluir acesso em ações de ficheiros de armazenamento Azure sobre SMB.
- **Arquivo de armazenamento Dados SMB Share Colaborador Elevado** permite ler, escrever, excluir e modificar permissões NTFS em ações de ficheiros de armazenamento Azure sobre SMB.

> [!IMPORTANT]
> O controlo administrativo total de uma parte de ficheiro, incluindo a capacidade de apropriação de um ficheiro, requer a utilização da chave da conta de armazenamento. O controlo administrativo não é suportado com credenciais da AD Azure.

Pode utilizar o portal Azure, PowerShell ou Azure CLI para atribuir as funções incorporadas à identidade Azure AD de um utilizador para a concessão de permissões de nível de partilha.

> [!NOTE]
> Lembre-se de [sincronizar as suas credenciais AD DS com a AD Azure](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) se pretender utilizar o seu DS AD no local para autenticação. A sincronização de hash de ad ds a Azure AD é opcional. A permissão de nível de partilha será concedida à identidade Azure AD que está sincronizada a partir do seu DS AD no local.

A recomendação geral é usar a permissão de nível de partilha para uma gestão de acesso de alto nível a um grupo ad que represente um grupo de utilizadores e identidades, em seguida, alavancar as permissões NTFS para o controlo de acesso granular no nível de diretório/arquivo. 

#### <a name="azure-portal"></a>Portal do Azure
Para atribuir uma função RBAC a uma identidade Azure AD, utilizando o [portal Azure,](https://portal.azure.com)siga estes passos:

1. No portal Azure, vá à sua partilha de ficheiros ou Crie uma partilha de [ficheiros.](../articles/storage/files/storage-how-to-create-file-share.md)
2. Selecione **Controlo de Acesso (IAM)**.
3. Selecione **Adicionar uma atribuição de funções**
4. Na lâmina de atribuição de **funções Add,** selecione a função incorporada apropriada (Armazenamento File Data SMB Share Reader, Storage File Data SMB Share Contributor) da lista **Role.** Deixe **o acesso atribuído** na definição predefinida: Utilizador, grupo ou diretor de serviço **Azure AD**. Selecione a identidade ad-alvo Azure por nome ou endereço de e-mail.
5. Selecione **Guardar** para completar a operação de atribuição de funções.

#### <a name="powershell"></a>PowerShell

A seguinte amostra powerShell mostra como atribuir uma função RBAC a uma identidade Azure AD, com base no nome de entrada. Para obter mais informações sobre a atribuição de funções RBAC com powerShell, consulte [Gerir o acesso utilizando rBAC e Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Antes de executar o seguinte script de amostra, lembre-se de substituir os valores do espaço reservado, incluindo os parênteses, com os seus próprios valores.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
O comando CLI 2.0 seguinte mostra como atribuir uma função RBAC a uma identidade Azure AD, com base no nome de entrada. Para obter mais informações sobre a atribuição de funções RBAC com o Azure CLI, consulte [Gerir o acesso utilizando o RBAC e o Azure CLI](../articles/role-based-access-control/role-assignments-cli.md). 

Antes de executar o seguinte script de amostra, lembre-se de substituir os valores do espaço reservado, incluindo os parênteses, com os seus próprios valores.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. Configure permissões NTFS sobre SMB 
Depois de atribuir permissões de nível de partilha com RBAC, deve atribuir permissões NTFS adequadas no nível raiz, diretório ou ficheiro. Pense em permissões de nível de partilha como o gatekeeper de alto nível que determina se um utilizador pode aceder à partilha. Enquanto as permissões ntfs atuam a um nível mais granular para determinar que operações o utilizador pode fazer ao nível do diretório ou do ficheiro.

O Azure Files suporta todo o conjunto de permissões básicas e avançadas da NTFS. Pode visualizar e configurar permissões NTFS em diretórios e ficheiros numa partilha de ficheiros Azure, montando a parte e, em seguida, utilizando o Windows File Explorer ou executando os [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) do Windows ou [comando Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl) 

Para configurar o NTFS com permissões de superutilizador, deve montar a parte utilizando a chave da sua conta de armazenamento a partir do VM unido ao domínio. Siga as instruções na secção seguinte para montar uma parte de ficheiro Azure a partir do pedido de comando e configurar as permissões ntfs em conformidade.

Os seguintes conjuntos de permissões são suportados no diretório raiz de uma parte de ficheiro:

- BUILTIN\Administradores:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(Oi)(CI)(IO)(GR,GE)
- NT AUTHORITY\Utilizadores autenticados:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- PROPRIETÁRIO DO CRIADOR:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Monte uma parte de arquivo do pedido de comando

Utilize o comando de **utilização da rede** Windows para montar a partilha de ficheiros Azure. Lembre-se de substituir os valores do espaço reservado no seguinte exemplo por valores próprios. Para obter mais informações sobre a montagem de partilhas de ficheiros, consulte Utilize uma partilha de [ficheiros Azure com o Windows](../articles/storage/files/storage-how-to-use-files-windows.md). 

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
```

Se tiver problemas na ligação ao Azure Files, consulte a ferramenta de resolução de [problemas que publicámos para erros](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)de montagem de Ficheiros Azure no Windows . Também fornecemos [orientações](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) para contornar cenários quando o porto 445 está bloqueado. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configure permissões NTFS com Windows File Explorer
Utilize o Windows File Explorer para conceder permissão completa a todos os diretórios e ficheiros sob a parte do ficheiro, incluindo o diretório raiz.

1. Abra o Windows File Explorer e clique à direita no ficheiro/diretório e selecione **Propriedades**.
2. Selecione o separador **Segurança.**
3. Selecione **Editar..** para mudar permissões.
4. Pode alterar as permissões dos utilizadores existentes ou selecionar **Adicionar...** para conceder permissões a novos utilizadores.
5. Na janela de solicitação para adicionar novos utilizadores, introduza o nome de utilizador-alvo que pretende conceder na inmissão dos **nomes** do objeto para selecionar a caixa e selecione **'Ver Nomes'** para encontrar o nome UPN completo do utilizador-alvo.
7.    Selecione **OK**.
8.    No separador **Segurança,** selecione todas as permissões que pretende conceder ao seu novo utilizador.
9.    Selecione **Aplicar**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Configure permissões NTFS com icacls
Utilize o seguinte comando do Windows para conceder permissões completas a todos os diretórios e ficheiros ao abrigo da partilha de ficheiros, incluindo o diretório raiz. Lembre-se de substituir os valores do espaço reservado no exemplo por valores próprios.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para obter mais informações sobre como utilizar os icacls para definir permissões NTFS e sobre os diferentes tipos de permissões suportadas, consulte [a referência da linha de comando para os icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. Monte uma parte de ficheiro de um VM filiado em domínio

O processo seguinte verifica que as suas permissões de partilha de ficheiros e de acesso foram configuradas corretamente e que pode aceder a uma parte do Ficheiro Azure a partir de um VM filiado em domínio. Esteja ciente de que a atribuição de funções rBAC de nível de partilha pode levar algum tempo a estar em vigor. 

Inscreva-se no VM utilizando a identidade Azure AD à qual concedeu permissões, como mostra a seguinte imagem. Se tiver ativado a autenticação AD DS no local para Ficheiros Azure, utilize as suas credenciais AD DS. Para autenticação Azure AD DS, inscreva-se com credenciais Azure AD.

![Screenshot mostrando ecrã de entrada de AD Azure para autenticação do utilizador](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Utilize o seguinte comando para montar a partilha de ficheiros Azure. Lembre-se de substituir os valores do espaço reservado por valores próprios. Como foi autenticado, não precisa de fornecer a chave da conta de armazenamento, as credenciais AD DS no local ou as credenciais Azure AD DS. A experiência de inscrição única é suportada para autenticação com AD DS ou Azure AD DS. Se tiver problemas de montagem com credenciais AD DS, consulte [os problemas de Troubleshoot Azure Files no Windows](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) para obter orientação.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
