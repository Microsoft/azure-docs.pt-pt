---
title: Configure a versão mínima TLS - instância gerida
description: Saiba como configurar a versão mínima TLS para instância gerida
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 17d430946f3cba1aa4680d1eaf8979fa4338bc22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788405"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Configurar uma versão mínima do TLS no Azure SQL Managed Instance
A definição de versão minimal [transport layer security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) permite que os clientes controlem a versão de TLS utilizada pela sua Instância Gerida Azure SQL.

Atualmente, suportamos o TLS 1.0, 1.1 e 1.2. A definição de uma Versão Mínima do TLS garante que as versões do TLS subsequentes mais recentes são suportadas. Por exemplo, por exemplo, escolher uma versão TLS superior a 1.1. significa que apenas as ligações com o TLS 1.1 e 1.2 são aceites e o TLS 1.0 é rejeitado. Após testar para confirmar que as aplicações são suportadas, é recomendado definir a versão mínima do TLS como 1.2, já que esta inclui correções para vulnerabilidades encontradas em versões anteriores e é a versão mais recente do TLS suportada no Azure SQL Managed Instance.

Para clientes com aplicações que dependem de versões mais antigas do TLS, recomendamos definir a Versão Minimal TLS de acordo com os requisitos das suas aplicações. Para os clientes que dependem de aplicações para se conectarem utilizando uma ligação não encriptada, recomendamos não definir qualquer versão Minimal TLS. 

Para obter mais informações, consulte [considerações de TLS para a conectividade sql Database](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Depois de definir a versão Minimal TLS, as tentativas de login de clientes que estão a utilizar uma versão TLS inferior à versão Minimal TLS do servidor falharão com o seguinte erro:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Definir a versão mínima TLS via PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O seguinte script PowerShell mostra como `Get` e a propriedade de versão Minimal `Set` **TLS** ao nível de instância:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Definir versão mínima de TLS via Azure CLI

> [!IMPORTANT]
> Todos os scripts desta secção requerem [Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha de bash

O seguinte script CLI mostra como alterar a definição **de versão Minimal TLS** numa casca de bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```