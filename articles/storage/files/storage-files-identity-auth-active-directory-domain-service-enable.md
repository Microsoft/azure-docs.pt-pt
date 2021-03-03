---
title: Utilize os Serviços de Domínio Azure AD para autorizar o acesso aos dados dos ficheiros sobre o SMB
description: Saiba como ativar a autenticação baseada na identidade sobre o Bloco de Mensagens do Servidor (SMB) para Ficheiros Azure através dos Serviços de Domínio do Diretório Ativo Azure. As suas máquinas virtuais (VMs) associadas a domínios podem então aceder às partilhas de ficheiros Azure utilizando credenciais AD do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: d75b60c715a758684e6f3a4b331c0b12d17eaf21
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705114"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Ativar a autenticação dos Serviços de Domínio do Diretório Ativo do Azure em Ficheiros Azure

Ficheiros Azure [](storage-files-introduction.md)   suporta a autenticação baseada na identidade sobre o Bloco de Mensagens do Servidor (SMB) através de dois tipos de Serviços de Domínio: no local Serviços de Domínio de Diretório Ativo (AD DS) e Azure Ative Directory Domain Services (Azure AD DS). Recomendamos vivamente que reveja a [secção Como funciona](./storage-files-active-directory-overview.md#how-it-works) para selecionar o serviço de domínio certo para autenticação. A configuração é diferente dependendo do serviço de domínio que escolher. Este artigo centra-se em ativar e configurar Azure AD DS para autenticação com ações de ficheiros Azure.

Se é novo nas ações de ficheiros Azure, recomendamos que leia o nosso [guia de planeamento](storage-files-planning.md) antes de ler a seguinte série de artigos.

> [!NOTE]
> A Azure Files suporta a autenticação Kerberos com Azure AD DS apenas com RC4-HMAC. A encriptação AES Kerberos ainda não está suportada.
> A Azure Files suporta a autenticação para Azure AD DS com sincronização completa com Azure AD. Se tiver ativado a sincronização de âmbito em Azure AD DS que apenas sincroniza um conjunto limitado de identidades do Azure AD, a autenticação e a autorização não são suportadas.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ativar a Azure AD sobre AMB para ações de ficheiros Azure, certifique-se de que completou os seguintes pré-requisitos:

1.  **Selecione ou crie um inquilino AZure AD.**

    Você pode usar um inquilino novo ou existente para a autenticação Azure AD em SMB. O inquilino e a partilha de ficheiros a que pretende aceder devem estar associados à mesma subscrição.

    Para criar um novo inquilino AD Azure, você pode [adicionar um inquilino AD Azure e uma assinatura AD AZure](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se tem um inquilino AZure AD existente mas quer criar um novo inquilino para uso com ações de arquivo Azure, consulte [Create a Azure Ative Directory in tenanty](/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Ativar os Serviços de Domínio Azure AD no inquilino AZure AD.**

    Para apoiar a autenticação com credenciais AZure AD, você deve ativar os Serviços de Domínio AZure AD para o seu inquilino AZure AD. Se não for o administrador do inquilino Azure AD, contacte o administrador e siga a orientação passo a passo para Ativar os [Serviços de Domínio do Diretório Ativo Azure utilizando o portal Azure](../../active-directory-domain-services/tutorial-create-instance.md).

    Normalmente leva cerca de 15 minutos para uma implementação AD DS AZure para ser concluída. Verifique se o estado de saúde do Azure AD DS mostra **running**, com sincronização de hash de palavra-passe ativada, antes de avançar para o passo seguinte.

1.  **Junte-se a um Azure VM com Azure AD DS.**

    Para aceder a uma partilha de ficheiros utilizando credenciais AD AZure a partir de um VM, o seu VM deve ser associado ao Azure AD DS. Para obter mais informações sobre como aderir a um VM, consulte [juntar uma máquina virtual do Windows Server a um domínio gerido](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > A autenticação AZure AD DS sobre as ações de ficheiroS Azure é suportada apenas em VMs Azure em execução em versões DE acima do Windows 7 ou Windows Server 2008 R2.

1.  **Selecione ou crie uma partilha de ficheiros Azure.**

    Selecione uma parte de ficheiro nova ou existente que esteja associada à mesma subscrição que o seu inquilino AZure AD. Para obter informações sobre a criação de uma nova partilha de ficheiros, consulte [Criar uma partilha de ficheiros nos Ficheiros Azure](storage-how-to-create-file-share.md).
    Para um melhor desempenho, recomendamos que a sua partilha de ficheiros seja na mesma região que o VM a partir do qual planeia aceder à partilha.

1.  **Verifique a conectividade dos Ficheiros Azure através da montagem de ações de ficheiros Azure utilizando a chave da sua conta de armazenamento.**

    Para verificar se o seu VM e a partilha de ficheiros estão devidamente configurados, tente montar a parte do ficheiro utilizando a chave da sua conta de armazenamento. Para obter mais informações, consulte [a partilha de ficheiros Azure e aceda à partilha no Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Disponibilidade regional

A autenticação Azure Files com Azure AD DS está disponível em [todas as regiões Azure Public, Gov e China.](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="overview-of-the-workflow"></a>Visão geral do fluxo de trabalho

Antes de ativar a autenticação Azure AD DS sobre a SMB para ações de ficheiros Azure, verifique se os ambientes AD e Azure Storage estão devidamente configurados. Recomendamos que caminhe pelos [pré-requisitos](#prerequisites) para se certificar de que completou todas as etapas necessárias.

Em seguida, faça as seguintes coisas para conceder acesso aos recursos da Azure Files com credenciais AZure AD:

1. Ativar a autenticação Azure AD DS em SMB para que a sua conta de armazenamento registe a conta de armazenamento com a implantação de Azure AD DS associada.
2. Atribua permissões de acesso para uma partilha a uma identidade AZure AD (um utilizador, grupo ou principal de serviço).
3. Configure permissões NTFS sobre SMB para diretórios e ficheiros.
4. Monte uma partilha de ficheiros Azure a partir de um VM de domínio.

O diagrama seguinte ilustra o fluxo de trabalho de ponta a ponta para permitir a autenticação Azure AD DS sobre SMB para Ficheiros Azure.

![Diagrama mostrando Azure AD sobre SMB para fluxo de trabalho de ficheiros Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Ativar a autenticação Azure AD DS para a sua conta

Para ativar a autenticação Azure AD DS sobre SMB para Ficheiros Azure, pode definir uma propriedade em contas de armazenamento utilizando o portal Azure PowerShell ou Azure CLI. A definição implícita desta propriedade "domínio une" a conta de armazenamento com a implantação de DS AD AD Ad associado. A autenticação Azure AD DS sobre SMB é então ativada para todas as ações de ficheiros novas e existentes na conta de armazenamento.

Tenha em mente que só pode ativar a autenticação Azure AD DS em SMB depois de ter implementado com sucesso a Azure AD DS para o seu inquilino AZure AD. Para mais informações, consulte os [pré-requisitos.](#prerequisites)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para ativar a autenticação Azure AD DS sobre sMB com o [portal Azure,](https://portal.azure.com)siga estes passos:

1. No portal Azure, aceda à sua conta de armazenamento existente ou [crie uma conta de armazenamento.](../common/storage-account-create.md)
1. Na secção **Definições,** selecione **Configuração**.
1. No **acesso à identidade para ações de ficheiros,** o toggle for **Azure Ative Directory Domain Service (AAD DS)** para **Enabled**.
1. Selecione **Guardar**.

A imagem a seguir mostra como ativar a autenticação Azure AD DS em SMB para a sua conta de armazenamento.

![Ativar a autenticação do Azure AD DS sobre o SMB no portal Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para ativar a autenticação Azure AD DS sobre o SMB com a Azure PowerShell, instale o módulo Az mais recente (2.4 ou mais recente) ou o módulo Az.Storage (1.5 ou mais recente). Para obter mais informações sobre a instalação do PowerShell, consulte [instalar o Azure PowerShell no Windows com o PowerShellGet](/powershell/azure/install-Az-ps).

Para criar uma nova conta de armazenamento, ligue para [o New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)e, em seguida, desacema o parâmetro **EnableAzureActiveDirectoryDomainServicesForFile** para **ser verdadeiro**. No exemplo seguinte, lembre-se de substituir os valores do espaço reservado pelos seus próprios valores. (Se estava a utilizar o módulo de pré-visualização anterior, o parâmetro para ativar a funcionalidade é **EnableAzureFilesAadIntegrationForSMB**.)

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


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para ativar a autenticação Azure AD sobre O SMB com O Azure CLI, instale a versão CLI mais recente (versão 2.0.70 ou mais recente). Para obter mais informações sobre a instalação do Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Para criar uma nova conta de armazenamento, ligue para [a conta de armazenamento AZ criar,](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)e definir a propriedade como `--enable-files-aadds` **verdadeira**. No exemplo seguinte, lembre-se de substituir os valores do espaço reservado pelos seus próprios valores. (Se estava a utilizar o módulo de pré-visualização anterior, o parâmetro para ativação de funcionalidades é **ficheiro-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Para ativar esta funcionalidade nas contas de armazenamento existentes, utilize o seguinte comando:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Você agora ativou com sucesso a autenticação Azure AD DS sobre SMB e atribuiu uma função personalizada que fornece acesso a uma partilha de ficheiros Azure com uma identidade AD Azure. Para conceder aos utilizadores acesso adicionais à sua partilha de ficheiros, siga as instruções nas [permissões de acesso atribuir](#assign-access-permissions-to-an-identity) para utilizar uma identidade e [configurar permissões NTFS através das secções SMB](#configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os Ficheiros Azure e como utilizar o Azure AD em SMB, consulte estes recursos:

- [Descrição geral do suporte de autenticação baseado em identidades dos Ficheiros do Azure para acesso SMB](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
