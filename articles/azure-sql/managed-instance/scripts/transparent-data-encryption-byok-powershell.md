---
title: 'PowerShell: Ative o TDE de trazer a sua própria chave (BYOK)'
titleSuffix: Azure SQL Managed Instance
description: Aprenda a configurar a Azure SQL Managed Instance para começar a usar a encriptação de dados transparente (BYOK) para encriptação em repouso utilizando o PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, sstein
ms.date: 08/25/2020
ms.openlocfilehash: 34a849fde315b45bdb1df577cf26c91f458abd72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323241"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Encriptação de dados transparente em SQL Caso gerido usando a sua própria chave a partir do Cofre de Chaves Azure

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Este exemplo de script PowerShell configura a Encriptação de Dados Transparente (TDE) com uma chave gerida pelo cliente para Azure SQL Managed Instance, utilizando uma chave do Cofre de Chaves Azure. Isto é frequentemente referido como um cenário de trazer a sua própria chave (BYOK) para tDE. Para saber mais, consulte [a Encriptação de Dados TransparenteS Azure SQL com a chave gerida pelo cliente.](../../database/transparent-data-encryption-byok-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

- Um caso gerido. Consulte [o Use PowerShell para criar um caso gerido](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

A utilização local do PowerShell ou a utilização da Azure Cloud Shell requer a azure PowerShell 2.3.2 ou uma versão posterior. Se precisar de atualizar, consulte instalar o [módulo Azure PowerShell,](/powershell/azure/install-az-ps)ou executar o script da amostra abaixo para instalar o módulo para o utilizador atual:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo 

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure PowerShell, consulte [a documentação da Azure PowerShell](/powershell/azure/).

As amostras adicionais de script powerShell para sql Managed Instance podem ser encontradas em [scripts PowerShell de instância gerida de Azure SQL](../../database/powershell-script-content-guide.md).
