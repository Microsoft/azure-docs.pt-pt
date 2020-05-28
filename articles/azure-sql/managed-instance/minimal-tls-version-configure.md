---
title: Configurar versão TLS mínima - instância gerida
description: Saiba como configurar a versão TLS mínima para instância gerida
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: bf09994e8ebae09771ce110f9284aa3e99fc1816
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122480"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Configure versão TLS mínima em Instância Gerida Azure SQL
A definição de versão De forma ção de Segurança da Camada de Transporte Mínima [(TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) permite aos clientes controlar a versão do TLS utilizada pela sua Instância Gerida Azure SQL.

Atualmente apoiamos TLS 1.0, 1.1 e 1.2. A definição de uma versão TLS Mínima garante que as versões TLS subsequentes e mais recentes são suportadas. Por exemplo, por exemplo, escolher uma versão TLS superior a 1.1. significa que apenas são aceites ligações com TLS 1.1 e 1.2 e o TLS 1.0 é rejeitado. Após testes para confirmar que as suas aplicações suportam o mesmo, recomendamos a definição de versão TLS mínima para 1.2, uma vez que inclui correções para vulnerabilidades encontradas em versões anteriores e é a versão mais alta do TLS suportado em Instância Gerida Azure SQL.

Para clientes com aplicações que dependam de versões mais antigas do TLS, recomendamos a definição da versão TLS mínima de acordo com os requisitos das suas aplicações. Para clientes que dependem de aplicações para se conectarem usando uma ligação não encriptada, recomendamos que não estabeleça qualquer versão TLS mínima. 

Para mais informações, consulte [as considerações de TLS para](../database/connect-query-content-reference-guide.md#tls-considerations-for-sql-database-connectivity)a conectividade da Base de Dados SQL .

Depois de definir a versão TLS Mínima, as tentativas de login dos clientes que estão a utilizar uma versão TLS inferior à versão TLS Mínima do servidor falharão com o seguinte erro:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Definir versão TLS mínima via PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O seguinte script PowerShell mostra como `Get` e a propriedade versão `Set` **TLS Mínima** ao nível da instância:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Definir versão TLS mínima via Azure CLI

> [!IMPORTANT]
> Todos os scripts desta secção requerem [O ClI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha de festa

O seguinte script CLI mostra como alterar a definição de **versão TLS mínima** numa concha de bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```
