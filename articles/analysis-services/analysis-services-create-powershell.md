---
title: Início Rápido - Criar um servidor do Azure Analysis Services com o PowerShell | Microsoft Docs
description: Saiba como criar um servidor do Azure Analysis Services com o PowerShell
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a5517d62e0fa4070411a087e241cad9d82f379b2
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72295088"
---
# <a name="quickstart-create-a-server---powershell"></a>Início Rápido: Criar um servidor - PowerShell

Este início rápido descreve a utilização do PowerShell a partir da linha de comandos para criar um servidor do Azure Analysis Services na sua subscrição do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subscrição do Azure**: aceda à [Avaliação Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
- **Azure Active Directory**: a sua subscrição tem de estar associada a um inquilino do Azure Active Directory e tem de ter uma conta nesse diretório. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador).
- **Azure PowerShell**. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Para instalar ou atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="import-azanalysisservices-module"></a>Importar módulo AZ. AnalysisServices

Para criar um servidor em sua assinatura, use o módulo [AZ. AnalysisServices](/powershell/module/az.analysisservices) . Carregue o módulo AZ. AnalysisServices em sua sessão do PowerShell.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Entre em sua assinatura do Azure usando o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Siga as indicações no ecrã.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. Quando criar o servidor, tem de especificar um grupo de recursos na sua subscrição. Se você ainda não tiver um grupo de recursos, poderá criar um novo usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na região E.U.A. Oeste.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Criar um servidor

Crie um novo servidor usando o comando [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) . O exemplo seguinte cria um servidor com o nome myServer em myResourceGroup, na região E.U.A. Oeste, no escalão D1 (gratuito), e especifica philipc@adventureworks.com como administrador do servidor.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Limpar recursos

Você pode remover o servidor da sua assinatura usando o comando [Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) . Se continuar com outros guias de início rápido e tutoriais nesta coleção, não remova o servidor. O exemplo seguinte remove o servidor que criou no passo anterior.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a criar um servidor na sua subscrição do Azure com o PowerShell. Agora que tem o servidor, pode ajudar a protegê-lo ao configurar uma firewall do servidor (opcional). Também pode adicionar um modelo de dados de exemplo básico ao seu servidor diretamente a partir do portal. Ter um modelo de exemplo é útil para saber como configurar as funções de base de dados do modelo e testar as ligações de cliente. Para obter mais informações, avance para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Início Rápido: Configurar a firewall do servidor - Portal](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Tutorial: Adicionar um modelo de exemplo ao servidor](analysis-services-create-sample-model.md)
