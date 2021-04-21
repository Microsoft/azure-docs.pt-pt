---
title: Quickstart - Criar serviços de análise Azure utilizando serviços de análise powerShell Azure | Microsoft Docs
description: Este quickstart descreve como criar um servidor Azure Analysis Services usando PowerShell
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom: devx-track-azurepowershell - references_regions - devx-track-azurepowershell - mode-api
ms.openlocfilehash: f1902a7192fc2153725e2b1d1e76f23cbd1426f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769369"
---
# <a name="quickstart-create-a-server---powershell"></a>Início Rápido: Criar um servidor - PowerShell

Este início rápido descreve a utilização do PowerShell a partir da linha de comandos para criar um servidor do Azure Analysis Services na sua subscrição do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura Azure**: Visite [o Azure Free Trial](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
- **Azure Active Directory**: a sua subscrição tem de estar associada a um inquilino do Azure Active Directory e tem de ter uma conta nesse diretório. Para saber mais, veja [Authentication and user permissions](analysis-services-manage-users.md) (Permissões de autenticação e utilizador).
- **Azure PowerShell**. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Para instalar ou atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="import-azanalysisservices-module"></a>Módulo Import Az.AnalysisServices

Para criar um servidor na sua subscrição, utilize o módulo [Az.AnalysisServices.](/powershell/module/az.analysisservices) Carregue o módulo Az.AnalysisServices na sessão PowerShell.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se na sua subscrição Azure utilizando o comando [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Siga as indicações no ecrã.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. Quando criar o servidor, tem de especificar um grupo de recursos na sua subscrição. Se ainda não tiver um grupo de recursos, pode criar um novo utilizando o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na região E.U.A. Oeste.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Criar um servidor

Crie um novo servidor utilizando o comando [New-AzAnalysisServicesServer.](/powershell/module/az.analysisservices/new-azanalysisservicesserver) O exemplo seguinte cria um servidor com o nome myServer em myResourceGroup, na região E.U.A. Oeste, no escalão D1 (gratuito), e especifica philipc@adventureworks.com como administrador do servidor.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Limpar os recursos

Pode remover o servidor da sua subscrição utilizando o comando [Remove-AzAnalysisServicesServer.](/powershell/module/az.analysisservices/new-azanalysisservicesserver) Se continuar com outros guias de início rápido e tutoriais nesta coleção, não remova o servidor. O exemplo seguinte remove o servidor que criou no passo anterior.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a criar um servidor na sua subscrição do Azure com o PowerShell. Agora que tem o servidor, pode ajudar a protegê-lo ao configurar uma firewall do servidor (opcional). Também pode adicionar um modelo de dados de exemplo básico ao seu servidor diretamente a partir do portal. Ter um modelo de exemplo é útil para saber como configurar as funções de base de dados do modelo e testar as ligações de cliente. Para obter mais informações, avance para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Quickstart: Configurar a firewall do servidor – Portal](analysis-services-qs-firewall.md)      
