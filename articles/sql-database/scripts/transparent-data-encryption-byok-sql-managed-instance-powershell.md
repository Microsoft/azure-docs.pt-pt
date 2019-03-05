---
title: 'PowerShell: Ativar a BYOK TDE - instância gerida de base de dados SQL do Azure | Documentos da Microsoft'
description: Saiba como configurar uma instância de gerida do SQL do Azure para começar a utilizar o BYOK encriptação de dados transparente (TDE) para encriptação em repouso com o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: 71137717a08888eaf092301287e6b117c7ce8253
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312142"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Gerir a encriptação de dados transparente numa instância gerida com a sua própria chave de Cofre de chaves do Azure

Este exemplo de script do PowerShell configura a encriptação de dados transparente (TDE) no cenário traga a sua própria chave para SQL instância gerida do Azure, utilizar uma chave do Cofre de chaves do Azure. Para saber mais sobre o TDE com suporte de Bring Your Own Key (BYOK), veja [TDE traga a sua própria chave para o Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o módulo do Azure PowerShell versão 5.7.0 ou posterior. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

Se optar por instalar e utilizar o PowerShell localmente, este tutorial também requer a versão de pré-visualização do azurerm. SQL PowerShell pacote *4.11.6-preview*. Execute o comando seguinte para instalá-lo: `Install-Module -Name AzureRM.Sql -RequiredVersion 4.11.6-preview -AllowPrerelease`

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
