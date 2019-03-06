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
ms.openlocfilehash: 4ff99cab168abeb0bbeeb8b29de0ea0285c255b0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57457829"
---
## <a name="prepare-for-akv-integration"></a>Preparar a integração AKV
Para utilizar a integração do Azure Key Vault para configurar a sua VM do SQL Server, existem vários pré-requisitos: 

1. [Instalar o Azure Powershell](#install)
2. [Criar um Azure Active Directory](#register)
3. [Criar um cofre de chaves](#createkeyvault)

As secções seguintes descrevem estes pré-requisitos e as informações que necessárias para recolher mais tarde, executar os cmdlets do PowerShell.

### <a id="install"></a> Instalar o Azure PowerShell
Certifique-se de que instalou o Azure PowerShell SDK mais recente. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azureps-cmdlets-docs).

### <a id="register"></a> Registar uma aplicação no Azure Active Directory

Em primeiro lugar, tem de ter uma [do Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) na sua subscrição. Entre os muitos benefícios, isto permite-lhe conceder permissão ao seu Cofre de chaves para determinados usuários e aplicativos.

Em seguida, registe uma aplicação com o AAD. Isso lhe fornecerá uma conta de Principal de serviço que tenha acesso ao seu Cofre de chaves, que terá da VM. No artigo do Azure Key Vault, pode encontrar estes passos no [registar uma aplicação com o Azure Active Directory](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) secção, ou pode ver os passos com capturas de ecrã a **obter uma identidade para a secção de aplicação**  dos [nesta mensagem de blogue](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Antes de concluir estes passos, terá de recolher as informações seguintes durante esse Registro que é necessária mais tarde, ao ativar a integração de Cofre de chaves do Azure na sua VM de SQL.

* Depois do aplicativo é adicionado, localize a **ID da aplicação** sobre o **aplicação registada** painel.
    O ID da aplicação é atribuído mais tarde para o **$spName** parâmetro (nome Principal de serviço) no script do PowerShell para ativar a integração do Azure Key Vault.

   ![ID da aplicação](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Durante estes passos ao criar a chave, copie o segredo para a sua chave conforme é mostrado na captura de ecrã seguinte. Este segredo da chave é atribuído mais tarde para o **$spSecret** parâmetro (segredo do Principal de serviço) no script do PowerShell.

   ![Segredo do AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* O ID da aplicação e o segredo também vão ser utilizados para criar uma credencial no SQL Server.

* Tem de autorizar este novo ID de cliente para ter as seguintes permissões de acesso: **Obtenha**, **wrapKey**, **unwrapKey**. Isso é feito com o [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet. Para obter mais informações, consulte [descrição geral do Azure Key Vault](../articles/key-vault/key-vault-overview.md).

### <a id="createkeyvault"></a> Criar um cofre de chaves
Para utilizar o Azure Key Vault para armazenar as chaves que irá utilizar para a encriptação na sua VM, precisa de acesso para um cofre de chaves. Se já não tiver configurado o seu Cofre de chaves, criar uma ao seguir os passos a [introdução ao Azure Key Vault](../articles/key-vault/key-vault-overview.md) artigo. Antes de concluir estes passos, há algumas informações necessárias para recolher durante este conjunto de cópia de segurança que são precisos mais tarde, ao ativar a integração de Cofre de chaves do Azure na sua VM de SQL.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Quando estiver o criar um passo de Cofre de chaves, tenha em atenção o retornado **vaultUri** propriedade, que é o URL do Cofre de chaves. No exemplo fornecido esse passo, conforme mostrado abaixo, o nome do Cofre de chaves é ContosoKeyVault, portanto o Cofre de chaves URL seria https://contosokeyvault.vault.azure.net/.

O URL do Cofre de chaves é atribuído mais tarde para o **$akvURL** parâmetro no script do PowerShell para ativar a integração do Azure Key Vault.

Depois do Cofre de chaves é criado, precisamos adicionar uma chave para o Cofre de chaves, esta chave será referida quando criamos uma chave assimétrica criar no SQL Server mais tarde.
