---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 3509185baa3a9d7be90c1fa4bd8000da4a8a6fe5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95553372"
---
## <a name="prepare-for-akv-integration"></a>Preparar para integração AKV
Para utilizar a integração do cofre de chave Azure para configurar o seu SQL Server VM, existem vários pré-requisitos: 

1. [Instalar Azure Powershell](#install)
2. [Criar um Diretório Ativo Azure](#register)
3. [Criar um cofre de chaves](#createkeyvault)

As secções seguintes descrevem estes pré-requisitos e as informações que precisa de recolher para posterior execução dos cmdlets PowerShell.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a> Instalar Azure PowerShell
Certifique-se de que instalou o mais recente módulo Azure PowerShell. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a> Registe uma candidatura no seu Diretório Ativo Azure

Em primeiro lugar, precisa de ter um [Diretório Ativo Azure](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) na sua subscrição. Entre muitos benefícios, isto permite-lhe conceder permissão para o seu cofre chave para determinados utilizadores e aplicações.

Em seguida, registe uma candidatura com a AAD. Isto lhe dará uma conta principal de serviço que tem acesso ao seu cofre chave, que o seu VM vai precisar. No artigo do Azure Key Vault, pode encontrar estes passos no Registo de uma aplicação com a secção [Azure Ative Directory,](../articles/key-vault/general/manage-with-cli2.md#registering-an-application-with-azure-active-directory) ou pode ver os passos com imagens no Get uma identidade para a secção de **aplicação** desta publicação de [blog.](/archive/blogs/kv/azure-key-vault-step-by-step) Antes de completar estes passos, precisa de recolher as seguintes informações durante este registo que são necessárias mais tarde quando ativar a Integração do Cofre de Chave Azure no seu SQL VM.

* Após a adição da aplicação, encontre o **ID** da aplicação (também conhecido como AAD ClientID ou AppID) na lâmina da **aplicação Registada.**
    O ID da aplicação é atribuído posteriormente ao parâmetro **$spName** (nome principal de serviço) no script PowerShell para permitir a integração do cofre de chave Azure.

   ![ID da Aplicação](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Durante estes passos, quando criar a sua chave, copie o segredo para a sua chave, como é mostrado na imagem seguinte. Este segredo chave é atribuído mais tarde ao parâmetro **$spSecret** (Segredo principal de serviço) no script PowerShell.

   ![Segredo da AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* O ID da aplicação e o segredo também serão usados para criar uma credencial no SQL Server.

* Tem de autorizar este novo ID (ou ID do cliente) a ter as seguintes permissões de acesso: **obter,** **wrapKey,** **desembrulhar aKey**. Isto é feito com o [cmdlet Set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Para mais informações, consulte [a visão geral do Cofre da Chave Azure](../articles/key-vault/general/overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a> Criar um cofre chave
Para utilizar o Cofre da Chave Azure para armazenar as chaves que utilizará para encriptação no seu VM, precisa de ter acesso a um cofre de chaves. Se ainda não montou o cofre da chave, crie um seguindo os passos do artigo "Começar com cofre [de chaves Azure".](../articles/key-vault/general/overview.md) Antes de completar estes passos, existe algumas informações que precisa de recolher durante esta configuração que é necessária mais tarde quando ativar a integração do cofre de chave Azure no seu SQL VM.

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'
```

Quando chegar ao Create a key vault step, note a propriedade **de VaultUri** devolvida, que é a URL do cofre chave. No exemplo fornecido nesse passo, mostrado abaixo, o nome do cofre chave é ContosoKeyVault, portanto o URL do cofre-chave seria https://contosokeyvault.vault.azure.net/ .

O URL do cofre-chave é atribuído mais tarde ao parâmetro **$akvURL** no script PowerShell para permitir a integração do cofre da chave Azure.

Após a criação do cofre da chave, precisamos de adicionar uma chave ao cofre da chave, esta chave será referida quando criarmos uma chave assimétrica criada no SQL Server mais tarde.