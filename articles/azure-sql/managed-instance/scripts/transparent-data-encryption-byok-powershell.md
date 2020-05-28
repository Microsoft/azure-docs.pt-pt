---
title: 'PowerShell: Ativar o TDE bring-your-your-key (BYOK)'
titleSuffix: Azure SQL Managed Instance
description: Aprenda a configurar um Caso Gerido Azure SQL para começar a usar a encriptação transparente de dados (BYOK) bring-your-your-own-key (BYOK) para encriptação em repouso utilizando o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: cff98c0acfe06a9dbf9e3d7c7dae1b2411823d28
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051795"
---
# <a name="transparent-data-encryption-tde-in-a-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Encriptação transparente de dados (TDE) em uma Instância Gerida SQL usando a sua própria chave do Cofre chave Azure
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Este exemplo de script PowerShell configura encriptação de dados transparentes (TDE) com chave gerida pelo cliente para a Instância Gerida Azure SQL, utilizando uma chave do Cofre de Chaves Azure. Isto é frequentemente referido como um cenário de trazer a sua própria chave para tDE. Para saber mais sobre o TDE com a chave gerida pelo cliente, consulte [TDE Bring Your Own Key to Azure SQL](../../database/transparent-data-encryption-byok-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância gerida pela SQL existente. Consulte [a Use PowerShell para criar uma instância gerida azure SQL](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

A utilização da PowerShell localmente ou a utilização da Azure Cloud Shell requer a AZ PowerShell 2.3.2 ou uma versão posterior. Se precisar de fazer o upgrade, consulte instalar o [módulo PowerShell Azure,](/powershell/azure/install-az-ps)ou executar o script de amostra abaixo para instalar o módulo para o utilizador atual:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras adicionais de script powerShell de instância gerida sql podem ser encontradas nos [scripts PowerShell geridos pelo Azure SQL](../../database/powershell-script-content-guide.md).
