---
title: 'PowerShell: Habilitar BYOK TDE-Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs'
description: Saiba como configurar um Instância Gerenciada SQL do Azure para começar a usar o BYOK Transparent Data Encryption (TDE) para criptografia em repouso usando o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 04/19/2019
ms.openlocfilehash: d8f0f4a8e603a9040d166b00682077cff23abd8d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569699"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Gerenciar Transparent Data Encryption em um Instância Gerenciada usando sua própria chave de Azure Key Vault (versão prévia)

Este exemplo de script do PowerShell configura Transparent Data Encryption (TDE) no cenário Bring Your Own Key (versão prévia) para Instância Gerenciada do SQL do Azure, usando uma chave de Azure Key Vault. Para saber mais sobre o suporte do TDE com Bring Your Own Key (BYOK), confira [TDE Bring your own Key ao Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um Instância Gerenciada existente. Consulte [usar o PowerShell para criar uma instância gerenciada do banco de dados SQL do Azure](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Usar o PowerShell localmente ou usando Azure Cloud Shell requer AZ PowerShell 1.1.1-Preview ou uma versão de visualização posterior. Se você precisar atualizar, consulte [instalar Azure PowerShell módulo](/powershell/azure/install-az-ps)ou execute o script de exemplo abaixo para instalar o módulo.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
