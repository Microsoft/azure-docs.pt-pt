---
title: Utilize os Serviços de Domínio Azure AD para autorizar o acesso a dados de ficheiros sobre sMB
description: Saiba como ativar a autenticação baseada na identidade através do Bloco de Mensagens do Servidor (SMB) para Ficheiros Azure através dos Serviços de Domínio de Diretório Ativo Azure. As máquinas virtuais do Windows (VMs) podem aceder às partilhas de ficheiros Azure utilizando credenciais Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 06ff14b23057755a643e5a57fbaf711798cca00e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210487"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Ativar a autenticação dos Serviços de Domínio de Diretório Ativo azure em ficheiros Azure

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para uma visão geral da autenticação baseada na identidade suportada nos Ficheiros Azure, consulte a autenticação de [Diretório Ativo Azure sobre SMB para Ficheiros Azure](storage-files-active-directory-overview.md). Este artigo está focado em como permitir a autenticação com o Azure Ative Directory Domain Services (Azure AD DS) em Ficheiros Azure. 
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Visão geral do fluxo de trabalho

Antes de ativar a autenticação Azure AD DS sobre SMB para Ficheiros Azure, verifique se os seus ambientes de Armazenamento Azure AD e Azure estão devidamente configurados. Recomendamos que caminhe pelos [pré-requisitos](#prerequisites) para se certificar de que completou todos os passos necessários.

Em seguida, conceder acesso aos recursos do Azure Files com credenciais da AD Azure seguindo estas etapas:

1. Ative a autenticação Azure AD DS sobre SMB para que a sua conta de armazenamento registe a conta de armazenamento com a implementação de DS Azure associado.
2. Atribuir permissões de acesso para uma parte de uma identidade Azure AD (utilizador, grupo ou diretor de serviço).
3. Configure as permissões NTFS sobre sMB para diretórios e ficheiros.
4. Monte uma partilha de ficheiros Azure de um VM filiado em domínio.

O diagrama seguinte ilustra o fluxo de trabalho de ponta a ponta para permitir a autenticação Azure AD DS sobre SMB para Ficheiros Azure.

![Diagrama mostrando Azure AD sobre SMB para fluxo de trabalho de Ficheiros Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de ativar o Azure AD sobre o SMB para Ficheiros Azure, certifique-se de que preencheu os seguintes pré-requisitos:

1.  **Selecione ou crie um inquilino Azure AD.**

    Você pode usar um inquilino novo ou existente para autenticação Azure AD em sMB. O inquilino e a parte do ficheiro a que pretende aceder devem estar associados à mesma subscrição.

    Para criar um novo inquilino Da Azure, pode [adicionar um inquilino Azure AD e uma subscrição Azure AD.](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription) Se você tem um inquilino Azure AD existente mas quer criar um novo inquilino para uso com Arquivos Azure, consulte [Criar um inquilino Azure Ative Diretório](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Ative os Serviços de Domínio Azure AD no inquilino da AD Azure.**

    Para apoiar a autenticação com credenciais AD Azure, deve ativar os Serviços de Domínio Azure AD para o seu inquilino Azure AD. Se não for o administrador do inquilino da AD Azure, contacte o administrador e siga a orientação passo a passo para [enable Azure Ative Directory Domain Services utilizando o portal Azure](../../active-directory-domain-services/tutorial-create-instance.md).

    Normalmente demora cerca de 15 minutos para que uma implantação azure AD DS seja concluída. Verifique se o estado de saúde do Azure AD DS mostra **Running,** com sincronização de hash de palavra-passe ativada, antes de passar para o próximo passo.

3.  **Domain-join a Azure VM com Azure AD DS.**

    Para aceder a uma partilha de ficheiros utilizando credenciais Azure AD de um VM, o seu VM deve ser associado ao Azure ADDS. Para obter mais informações sobre como se juntar a um VM, consulte [Juntar uma máquina virtual do Windows Server para um domínio gerido](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > A autenticação Azure AD DS sobre SMB com Ficheiros Azure é suportada apenas em VMs Azure que executam em versões OS acima do Windows 7 ou Windows Server 2008 R2.

4.  **Selecione ou crie uma partilha de ficheiros Azure.**

    Selecione uma nova ou existente partilha de ficheiros que esteja associada à mesma subscrição que o seu inquilino Azure AD. Para obter informações sobre a criação de uma nova partilha de ficheiros, consulte [Criar uma participação de ficheiro em Ficheiros Azure](storage-how-to-create-file-share.md).
    Para um desempenho ótimo, recomendamos que a sua parte de ficheiro seja na mesma região que o VM a partir do qual planeia aceder à partilha.

5.  **Verifique a conectividade dos Ficheiros Azure através da montagem de partilhas de ficheiros Azure utilizando a chave da sua conta de armazenamento.**

    Para verificar se o seu VM e a sua parte de ficheiro estão corretamente configurados, tente montar a parte do ficheiro utilizando a chave da sua conta de armazenamento. Para mais informações, consulte o [Monte uma partilha de ficheiros Azure e aceda à partilha no Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Ativar a autenticação AD DS Azure para a sua conta

Para permitir a autenticação Azure AD DS sobre SMB para Ficheiros Azure, pode definir uma propriedade em contas de armazenamento utilizando o portal Azure, Azure PowerShell ou Azure CLI. Definir esta propriedade implicitamente "domínio junta" a conta de armazenamento com a implantação de AD DS Azure associado. A autenticação Azure AD DS sobre SMB está então ativada para todas as novas e existentes ações de ficheiros na conta de armazenamento.

Tenha em mente que só pode ativar a autenticação Azure AD DS sobre sMB depois de ter implantado com sucesso o Azure AD DS para o seu inquilino Azure AD. Para mais informações, consulte os [pré-requisitos](#prerequisites).

### <a name="azure-portal"></a>Portal do Azure

Para permitir a autenticação Azure AD DS sobre SMB com o [portal Azure,](https://portal.azure.com)siga estes passos:

1. No portal Azure, vá à sua conta de armazenamento existente, ou [crie uma conta](../common/storage-account-create.md)de armazenamento.
2. Na secção **Definições,** **selecione Configuração**.
3. Selecione **Azure Ative Directory Domain Services (Azure AD DS)** do Serviço de Diretório baseado na identidade para a lista de abandono de autenticação de **ficheiros Azure.**

A imagem que se segue mostra como ativar a autenticação Azure AD DS sobre SMB para a sua conta de armazenamento.

![Ativar a autenticação adato azure sobre SMB no portal Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Para ativar a autenticação Azure AD DS sobre sMB com a Azure PowerShell, instale o mais recente módulo Az (2.4 ou mais recente) ou o módulo Az.Storage (1.5 ou mais recente). Para obter mais informações sobre a instalação do PowerShell, consulte [Instalar o PowerShell Azure no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Para criar uma nova conta de armazenamento, ligue para [new-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0), e, em seguida, coloque o parâmetro **EnableAzureActiveDirectoryDomainServicesForFile** **verdadeiro**. No exemplo seguinte, lembre-se de substituir os valores do espaço reservado por valores próprios. (Se estiver a utilizar o módulo de pré-visualização anterior, o parâmetro para a ativação da funcionalidade é **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Para ativar esta funcionalidade nas contas de armazenamento existentes, utilize o seguinte comando:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>CLI do Azure

Para ativar a autenticação AD Azure sobre sMB com o Azure CLI, instale a versão MAIS recente do CLI (versão 2.0.70 ou mais recente). Para obter mais informações sobre a instalação do Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Para criar uma nova conta de armazenamento, ligue para a conta de[armazenamento az criar,](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)e definir a propriedade `--enable-files-aadds` **verdadeiramente.** No exemplo seguinte, lembre-se de substituir os valores do espaço reservado por valores próprios. (Se estiver a utilizar o módulo de pré-visualização anterior, o parâmetro para a ativação da funcionalidade é **aad de ficheiro**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Para ativar esta funcionalidade nas contas de armazenamento existentes, utilize o seguinte comando:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```


## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade

Para aceder aos recursos do Azure Files com credenciais AD Azure, uma identidade (utilizador, grupo ou diretor de serviço) deve ter as permissões necessárias ao nível das ações. Este processo é semelhante ao de especificar as permissões de partilha do Windows, onde especifica o tipo de acesso que um determinado utilizador tem para uma partilha de ficheiros. A orientação nesta secção demonstra como atribuir a leitura, escrita ou exclusão de permissões para uma partilha de ficheiros a uma identidade.

Introduzimos duas funções azure incorporadas para a concessão de permissões de nível de partilha aos utilizadores:

- **Arquivo de armazenamento Data SMB Share Reader** permite ler acesso em ações de ficheiros Azure Storage sobre SMB.
- **Arquivo de armazenamento Dados SMB Share Contributor** permite ler, escrever e excluir acesso em ações de ficheiros de armazenamento Azure sobre SMB.
- **Arquivo de armazenamento Dados SMB Share Colaborador Elevado** permite ler, escrever, excluir e modificar permissões NTFS em ações de ficheiros de armazenamento Azure sobre SMB.

> [!IMPORTANT]
> O controlo administrativo total de uma parte de ficheiro, incluindo a capacidade de atribuir uma função a uma identidade, requer a utilização da chave da conta de armazenamento. O controlo administrativo não é suportado com credenciais da AD Azure.

Pode utilizar o portal Azure, PowerShell ou Azure CLI para atribuir as funções incorporadas à identidade Azure AD de um utilizador para a concessão de permissões de nível de partilha.

#### <a name="azure-portal"></a>Portal do Azure
Para atribuir uma função RBAC a uma identidade Azure AD, utilizando o [portal Azure,](https://portal.azure.com)siga estes passos:

1. No portal Azure, vá à sua partilha de ficheiros ou [crie uma participação de ficheiro em Ficheiros Azure](storage-how-to-create-file-share.md).
2. Selecione **Controlo de Acesso (IAM)** .
3. Selecione **Adicionar uma atribuição de funções**
4. Na lâmina de atribuição de **funções Add,** selecione a função incorporada apropriada (Armazenamento File Data SMB Share Reader, Storage File Data SMB Share Contributor) da lista **Role.** Mantenha o **acesso atribuído à** opção na definição predefinida: Utilizador, grupo ou diretor de serviço **azure AD**. Selecione a identidade ad-alvo Azure por nome ou endereço de e-mail.
5. Selecione **Guardar** para completar a operação de atribuição de funções.

#### <a name="powershell"></a>PowerShell

O seguinte comando PowerShell mostra como atribuir uma função RBAC a uma identidade Azure AD, com base no nome de entrada. Para obter mais informações sobre a atribuição de funções RBAC com powerShell, consulte [Gerir o acesso utilizando rBAC e Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

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

O comando CLI 2.0 seguinte mostra como atribuir uma função RBAC a uma identidade Azure AD, com base no nome de entrada. Para obter mais informações sobre a atribuição de funções RBAC com o Azure CLI, consulte [Gerir o acesso utilizando o RBAC e o Azure CLI](../../role-based-access-control/role-assignments-cli.md).

Antes de executar o seguinte script de amostra, lembre-se de substituir os valores do espaço reservado, incluindo os parênteses, com os seus próprios valores.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Configure permissões NTFS sobre SMB
Depois de atribuir permissões de nível de partilha com RBAC, deve atribuir permissões NTFS adequadas no nível raiz, diretório ou ficheiro. Pense em permissões de nível de partilha como o gatekeeper de alto nível que determina se um utilizador pode aceder à partilha. Enquanto as permissões ntfs atuam a um nível mais granular para determinar que operações o utilizador pode fazer ao nível do diretório ou do ficheiro.

O Azure Files suporta todo o conjunto de permissões básicas e avançadas da NTFS. Pode visualizar e configurar permissões NTFS em diretórios e ficheiros numa partilha de ficheiros Azure, montando a parte e, em seguida, utilizando o Windows File Explorer ou executando os [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) do Windows ou [comando Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl)

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

Utilize o comando de **utilização da rede** Windows para montar a partilha de ficheiros Azure. Lembre-se de substituir os valores do espaço reservado no seguinte exemplo por valores próprios. Para obter mais informações sobre a montagem de partilhas de ficheiros, consulte a [partilha de ficheiros Do Monte Um Azure e aceda à participação no Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configure permissões NTFS com Windows File Explorer
Utilize o Windows File Explorer para conceder permissão completa a todos os diretórios e ficheiros sob a parte do ficheiro, incluindo o diretório raiz.

1. Abra o Windows File Explorer e clique à direita no ficheiro/diretório e selecione **Propriedades**
2. Clique no separador **Segurança**
3. Clique em **Editar..** botão para alterar permissões
4. Pode alterar a permissão dos utilizadores existentes, ou clicar em **Adicionar...** para conceder permissões a novos utilizadores
5. Na janela de solicitação para adicionar novos utilizadores, introduza o nome de utilizador-alvo que pretende conceder na **inmissão** dos nomes do objeto para selecionar a caixa e clique em **Ver Nomes** para encontrar o nome UPN completo do utilizador alvo.
7.  Clique em **OK**
8.  No separador Segurança, selecione todas as permissões que pretende conceder ao recém-adicionado utilizador
9.  Clique em **Aplicar**

### <a name="configure-ntfs-permissions-with-icacls"></a>Configure permissões NTFS com icacls
Utilize o seguinte comando do Windows para conceder permissões completas a todos os diretórios e ficheiros ao abrigo da partilha de ficheiros, incluindo o diretório raiz. Lembre-se de substituir os valores do espaço reservado no exemplo por valores próprios.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para obter mais informações sobre como utilizar os icacls para definir permissões NTFS e sobre os diferentes tipos de permissões suportadas, consulte [a referência da linha de comando para os icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Monte uma partilha de ficheiros de um VM filiado em domínio

O processo seguinte verifica se as suas credenciais De AD Azure foram configuradas corretamente e que pode aceder a uma parte do Ficheiro Azure a partir de um VM filiado em domínio:

Inscreva-se no VM utilizando a identidade Azure AD à qual concedeu permissões, como mostra a seguinte imagem.

![Screenshot mostrando ecrã de entrada de AD Azure para autenticação do utilizador](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Utilize o seguinte comando para montar a partilha de ficheiros Azure. Lembre-se de substituir os valores do espaço reservado por valores próprios. Como já foi autenticado, não precisa de fornecer a chave da conta de armazenamento ou o nome de utilizador e senha de utilizador da AD Azure. A Azure AD sobre sMB suporta uma única experiência de inscrição com credenciais Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Permitiu agora a autenticação Azure AD através de SMB e atribuiu uma função personalizada que dá acesso a uma partilha de ficheiros Azure com uma identidade Azure AD. Para conceder aos utilizadores adicionais acesso à sua parte de ficheiro, siga as instruções nas [permissões](#assign-access-permissions-to-an-identity) de acesso da Atribuição a uma identidade e [configure permissões NTFS através](#configure-ntfs-permissions-over-smb) das secções SMB.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure Files e como utilizar o Azure AD sobre a SMB, consulte estes recursos:

- [Introdução aos ficheiros Azure](storage-files-introduction.md)
- [Descrição geral da autenticação do Azure Active Directory através de SMB para Ficheiros do Azure](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
