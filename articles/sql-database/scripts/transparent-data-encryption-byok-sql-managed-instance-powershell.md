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
ms.date: 04/19/2019
ms.openlocfilehash: 8eb924b3dc6ff912db402596c763dd69b85147a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390720"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Gerir a encriptação de dados transparente numa instância gerida com a sua própria chave de Cofre de chaves do Azure (pré-visualização)

Este exemplo de script do PowerShell configura a encriptação de dados transparente (TDE) no cenário traga a sua própria chave (pré-visualização) para SQL instância gerida do Azure, utilizar uma chave do Cofre de chaves do Azure. Para saber mais sobre o TDE com suporte de Bring Your Own Key (BYOK), veja [TDE traga a sua própria chave para o Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância gerida existente. Ver [utilize o PowerShell para criar uma base de dados do SQL do Azure de instância gerida](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Utilizar ambas as PowerShell localmente ou utilizar o Azure Cloud Shell requer 1.1.1-preview AZ PowerShell ou uma versão de pré-visualização posterior. Se precisar de atualizar, veja [módulo de instalar o Azure PowerShell](/powershell/azure/install-az-ps), ou executar o script de exemplo para instalar o módulo abaixo.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
