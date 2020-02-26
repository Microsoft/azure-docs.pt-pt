---
title: Utilize os Serviços de Domínio Azure AD para autorizar o acesso a dados de ficheiros sobre sMB
description: Saiba como ativar a autenticação baseada na identidade através do Bloco de Mensagens do Servidor (SMB) para Ficheiros Azure através dos Serviços de Domínio de Diretório Ativo Azure. As máquinas virtuais do Windows (VMs) podem aceder às partilhas de ficheiros Azure utilizando credenciais Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e2e3c7763a13c8850554b079a426ed4172b74d28
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599278"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Ativar a autenticação dos Serviços de Domínio de Diretório Ativo azure em ficheiros Azure

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para uma visão geral da autenticação Azure AD sobre SMB para ações de ficheiros Azure, consulte a autenticação de [Diretório Ativo Azure sobre SMB para Ficheiros Azure](storage-files-active-directory-overview.md). Este artigo está focado em como permitir a autenticação com o Azure Ative Directory Domain Services (Azure AD DS) em Ficheiros Azure.

> [!NOTE]
> O Azure Files suporta a autenticação Kerberos com o Azure AD DS com encriptação RC4-HMAC. A encriptação da AES Kerberos ainda não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ativar o Azure AD sobre a SMB para as ações de ficheiros Azure, certifique-se de que preencheu os seguintes pré-requisitos:

1.  **Selecione ou crie um inquilino Azure AD.**

    Você pode usar um inquilino novo ou existente para autenticação Azure AD em sMB. O inquilino e a parte do ficheiro a que pretende aceder devem estar associados à mesma subscrição.

    Para criar um novo inquilino Da Azure, pode [adicionar um inquilino Azure AD e uma subscrição Azure AD.](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription) Se você tem um inquilino Azure AD existente mas quer criar um novo inquilino para uso com ações de arquivo Azure, consulte [Criar um inquilino azure Ative Diretório](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Ative os Serviços de Domínio Azure AD no inquilino da AD Azure.**

    Para apoiar a autenticação com credenciais AD Azure, deve ativar os Serviços de Domínio Azure AD para o seu inquilino Azure AD. Se não for o administrador do inquilino da AD Azure, contacte o administrador e siga a orientação passo a passo para [enable Azure Ative Directory Domain Services utilizando o portal Azure](../../active-directory-domain-services/tutorial-create-instance.md).

    Normalmente demora cerca de 15 minutos para que uma implantação azure AD DS seja concluída. Verifique se o estado de saúde do Azure AD DS mostra **Running,** com sincronização de hash de palavra-passe ativada, antes de passar para o próximo passo.

1.  **Domain-join a Azure VM com Azure AD DS.**

    Para aceder a uma partilha de ficheiros utilizando credenciais Azure AD de um VM, o seu VM deve ser associado ao Azure ADDS. Para obter mais informações sobre como se juntar a um VM, consulte [Juntar uma máquina virtual do Windows Server para um domínio gerido](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > A autenticação Azure AD DS sobre SMB com as partilhas de ficheiros Azure é suportada apenas em VMs Azure que executam em versões OS acima do Windows 7 ou Windows Server 2008 R2.

1.  **Selecione ou crie uma partilha de ficheiros Azure.**

    Selecione uma nova ou existente partilha de ficheiros que esteja associada à mesma subscrição que o seu inquilino Azure AD. Para obter informações sobre a criação de uma nova partilha de ficheiros, consulte [Criar uma participação de ficheiro em Ficheiros Azure](storage-how-to-create-file-share.md).
    Para um desempenho ótimo, recomendamos que a sua parte de ficheiro seja na mesma região que o VM a partir do qual planeia aceder à partilha.

1.  **Verifique a conectividade dos Ficheiros Azure através da montagem de partilhas de ficheiros Azure utilizando a chave da sua conta de armazenamento.**

    Para verificar se o seu VM e a sua parte de ficheiro estão corretamente configurados, tente montar a parte do ficheiro utilizando a chave da sua conta de armazenamento. Para mais informações, consulte o [Monte uma partilha de ficheiros Azure e aceda à partilha no Windows](storage-how-to-use-files-windows.md).

## <a name="overview-of-the-workflow"></a>Visão geral do fluxo de trabalho

Antes de ativar a autenticação Azure AD DS sobre SMB para partilhas de ficheiros Azure, verifique se os seus ambientes de Armazenamento Azure AD e Azure estão devidamente configurados. Recomendamos que caminhe pelos [pré-requisitos](#prerequisites) para se certificar de que completou todos os passos necessários.

Em seguida, faça as seguintes coisas para dar acesso aos recursos do Azure Files com credenciais da AD Azure:

- Ative a autenticação Azure AD DS sobre SMB para que a sua conta de armazenamento registe a conta de armazenamento com a implementação de DS Azure associado.
- Atribuir permissões de acesso para uma parte de uma identidade Azure AD (utilizador, grupo ou diretor de serviço).
- Configure as permissões NTFS sobre sMB para diretórios e ficheiros.
- Monte uma partilha de ficheiros Azure de um VM filiado em domínio.

O diagrama seguinte ilustra o fluxo de trabalho de ponta a ponta para permitir a autenticação Azure AD DS sobre SMB para Ficheiros Azure.

![Diagrama mostrando Azure AD sobre SMB para fluxo de trabalho de Ficheiros Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Ativar a autenticação AD DS Azure para a sua conta

Para permitir a autenticação Azure AD DS sobre SMB para Ficheiros Azure, pode definir uma propriedade em contas de armazenamento utilizando o portal Azure, Azure PowerShell ou Azure CLI. Definir esta propriedade implicitamente "domínio junta" a conta de armazenamento com a implantação de AD DS Azure associado. A autenticação Azure AD DS sobre SMB está então ativada para todas as novas e existentes ações de ficheiros na conta de armazenamento.

Tenha em mente que só pode ativar a autenticação Azure AD DS sobre sMB depois de ter implantado com sucesso o Azure AD DS para o seu inquilino Azure AD. Para mais informações, consulte os [pré-requisitos](#prerequisites).

### <a name="azure-portal"></a>Portal do Azure

Para permitir a autenticação Azure AD DS sobre SMB com o [portal Azure,](https://portal.azure.com)siga estes passos:

1. No portal Azure, vá à sua conta de armazenamento existente, ou [crie uma conta](../common/storage-account-create.md)de armazenamento.
1. Na secção **Definições,** **selecione Configuração**.
1. No **acesso baseado na identidade para ações de ficheiros,** alterna o toggle para o **Azure Ative Directory Domain Service (AAD DS)** para **Ativado**.
1. Selecione **Guardar**.

A imagem que se segue mostra como ativar a autenticação Azure AD DS sobre SMB para a sua conta de armazenamento.

![Ativar a autenticação Azure AD DS sobre SMB no portal Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

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

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Permitiu agora a autenticação Azure AD DS através de SMB e atribuiu uma função personalizada que dá acesso a uma partilha de ficheiros Azure com uma identidade Azure AD. Para conceder aos utilizadores adicionais acesso à sua parte de ficheiro, siga as instruções nas [permissões](#assign-access-permissions-to-an-identity) de acesso da Atribuição para utilizar uma identidade e [configure permissões NTFS através das secções SMB](#configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure Files e como utilizar o Azure AD sobre a SMB, consulte estes recursos:

- [Visão geral do suporte de autenticação baseado na identidade do Azure Files para acesso a SMB](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
