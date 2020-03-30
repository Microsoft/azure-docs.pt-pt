---
title: 'PowerShell: Enable BYOK TDE - Azure SQL Database Managed Instance '
description: Aprenda a configurar um Caso Gerido Azure SQL para começar a usar encriptação transparente de dados (TDE) para encriptação em repouso usando powerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691402"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Gerencie a encriptação de dados transparente sintetizado numa instância gerida utilizando a sua própria chave do Cofre de Chaves Azure

Este exemplo de script PowerShell configura encriptação de dados transparentes (TDE) com chave gerida pelo cliente para a Instância Gerida Azure SQL, utilizando uma chave do Cofre de Chaves Azure. Isto é frequentemente referido como um cenário de trazer a sua própria chave para tDE. Para saber mais sobre o TDE com a chave gerida pelo cliente, consulte [TDE Bring Your Own Key to Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância gerida existente. Consulte a Utilização powerShell para criar uma instância gerida pela Base de [Dados Azure SQL](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A utilização da PowerShell localmente ou a utilização da Azure Cloud Shell requer a AZ PowerShell 2.3.2 ou uma versão posterior. Se precisar de fazer o upgrade, consulte instalar o [módulo PowerShell Azure,](/powershell/azure/install-az-ps)ou executar o script de amostra abaixo para instalar o módulo para o utilizador atual:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
