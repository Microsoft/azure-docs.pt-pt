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
ms.openlocfilehash: 2c7d312910c6d38c54b291da34bfb827246c7dad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79504393"
---
## <a name="prepare-for-akv-integration"></a>Prepare-se para a integração do AKV
Para utilizar a Integração do Cofre de Chaves Azure para configurar o seu VM do Servidor SQL, existem vários pré-requisitos: 

1. [Instalar a Powershell Azure](#install)
2. [Criar um Diretório Ativo Azure](#register)
3. [Criar um cofre chave](#createkeyvault)

As seguintes secções descrevem estes pré-requisitos e as informações que precisa de recolher para posteriormente executar os cmdlets PowerShell.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a>Instalar a PowerShell Azure
Certifique-se de que instalou o mais recente módulo Azure PowerShell. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a>Registe uma candidatura no seu Diretório Ativo Azure

Em primeiro lugar, precisa de ter um [Diretório Ativo Azure](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) na sua subscrição. Entre muitos benefícios, isto permite-lhe conceder permissão ao seu cofre chave para determinados utilizadores e aplicações.

Em seguida, registe uma candidatura com AAD. Isto lhe dará uma conta principal de serviço que tenha acesso ao seu cofre chave, que o seu VM precisará. No artigo do Cofre chave Azure, pode encontrar estes passos no Registo de uma aplicação com secção [de Diretório Ativo Azure,](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) ou pode ver os passos com imagens no Get uma identidade para a secção de **aplicação** [deste post de blog](https://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Antes de completar estes passos, precisa de recolher as seguintes informações durante este registo que são necessárias mais tarde quando ativa a Integração do Cofre de Chaves Azure no seu VM SQL.

* Depois de adicionada a aplicação, encontre o ID da **aplicação** (também conhecido como AAD ClientID ou AppID) na lâmina da **aplicação Registada.**
    O ID da aplicação é atribuído posteriormente ao parâmetro **$spName** (nome principal de serviço) no script PowerShell para permitir a Integração do Cofre de Chaves Azure.

   ![ID da aplicação](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Durante estes passos quando criar a sua chave, copie o segredo para a sua chave, como é mostrado na seguinte imagem. Este segredo chave é atribuído mais tarde ao parâmetro **$spSecret** (Service Principal secret) no script PowerShell.

   ![Segredo da AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* O ID da aplicação e o segredo também serão usados para criar uma credencial no Servidor SQL.

* Deve autorizar este novo ID de aplicação (ou ID do cliente) a ter as seguintes permissões de acesso: **obter,** **wrapKey**, **unwrapKey**. Isto é feito com o [cmdlet Set-AzKeyVaultAccessPolicy.](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Para mais informações, consulte a [visão geral do Cofre de Chaves azure](../articles/key-vault/key-vault-overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a>Criar um cofre chave
Para utilizar o Azure Key Vault para armazenar as chaves que utilizará para encriptação no seu VM, precisa de acesso a um cofre chave. Se ainda não montou o seu cofre chave, crie um seguindo os passos do artigo ["Getting Started with Azure Key Vault".](../articles/key-vault/key-vault-overview.md) Antes de completar estes passos, existem algumas informações que precisa de recolher durante esta configuração que são necessárias mais tarde quando ativar a Integração do Cofre de Chaves Azure no seu VM SQL.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Quando chegar ao "Criar um passo de cofre chave", note a propriedade de **abóbada devolvidaUri,** que é a URL do cofre chave. No exemplo indicado nesse passo, mostrado abaixo, o nome chave do cofre é ContosoKeyVault, portanto o URL do cofre chave seria https://contosokeyvault.vault.azure.net/.

O URL do cofre chave é atribuído mais tarde ao parâmetro **$akvURL** no script PowerShell para permitir a Integração do Cofre de Chave Azure.

Depois de criado o cofre chave, precisamos adicionar uma chave ao cofre chave, esta chave será referida quando criarmos uma chave assimétrica criada no SQL Server mais tarde.
