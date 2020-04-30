---
title: Quickstart - Criar um recurso de servidor de Serviços de Análise Azure utilizando o modelo do Gestor de Recursos Azure
description: Quickstart mostrando como um recurso de servidor de Serviços de Análise Azure usando um modelo de Gestor de Recursos Azure.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: analysis-services
ms.topic: quickstart
ms.date: 04/14/2020
ms.custom: subject-armqs
ms.openlocfilehash: d292500c5e26d3c07ff2402964166b3928cc7e44
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81384258"
---
# <a name="quickstart-create-a-server---azure-resource-manager-template"></a>Quickstart: Criar um servidor - Modelo de Gestor de Recursos Azure

Este quickstart descreve como criar um recurso de servidor de Serviços de Análise na sua subscrição Azure utilizando um modelo de Gestor de Recursos.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: aceda à [Avaliação Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
* **Azure Active Directory**: a sua subscrição tem de estar associada a um inquilino do Azure Active Directory. E tem de ter sessão iniciada no Azure com uma conta nesse Azure Active Directory. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador).

## <a name="create-a-server"></a>Criar um servidor

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

Um único [recurso Microsoft.AnalysisServices/servidores](https://docs.microsoft.com/azure/templates/microsoft.analysisservices/2017-08-01/servers) com uma regra de firewall é definido no modelo. 

### <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione o seguinte link Deploy para Azure para iniciar sessão no Azure e abra um modelo. O modelo é usado para criar um recurso de servidor de Serviços de Análise e especificar propriedades necessárias e opcionais.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json"><img src="./media/analysis-services-create-template/deploy-azure.png" alt="deploy to azure"/></a>

2. Selecione ou introduza os seguintes valores.

    Salvo especificação em contrário, utilize valores predefinidos.

    * **Subscrição**: Selecione uma subscrição Azure.
    * **Grupo de recursos**: Clique **em Criar novo,** e depois insira um nome único para o novo grupo de recursos.
    * **Localização**: Selecione uma localização predefinida para os recursos criados no grupo de recursos.
    * **Nome**do servidor : Introduza um nome para o recurso do servidor. 
    * **Localização**: Ignorar para Serviços de Análise. A localização está especificada na localização do servidor.
    * **Localização**do servidor : Introduza a localização do servidor de Serviços de Análise. Esta é frequentemente a mesma região que a localização padrão especificada para o Grupo de Recursos, mas não é necessária. Por **exemplo, centro-norte dos EUA.** Para as regiões apoiadas, consulte a disponibilidade dos Serviços de [Análise por região.](analysis-services-overview.md#availability-by-region)
    * **Nome Sku**: Introduza o nome sku para o servidor de Serviços de Análise criar. Escolha: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. A disponibilidade de Sku depende da região. S0 ou D1 é recomendado para avaliação e teste.
    * **Capacidade**: Introduza o número total de instâncias de escala de réplica de consulta. A escala de mais de um caso é apoiada apenas em regiões selecionadas.
    * **Definições de firewall**: Introduza as regras de firewall de entrada para definir para o servidor. Se não especificado, a firewall é desativada.
    * **Recipiente de reserva Blob Uri**: Introduza o SAS URI num recipiente de armazenamento azure blob privado com permissões de leitura, escrita e lista. Só é necessário se pretender utilizar [backup/restauro](analysis-services-backup.md).
    * **Aceito os termos e condições acima apresentados**: selecione.

3. Selecione **Comprar**. Depois de o servidor ter sido implementado com sucesso, obtém uma notificação:

   ![Modelo de Gestor de Recursos, implemente notificação do portal](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Validar a implementação

Utilize o portal Azure ou o Azure PowerShell para verificar se o grupo de recursos e o recurso do servidor foram criados.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize o portal Azure, O ClI azure ou o Azure PowerShell para eliminar o grupo de recursos e o recurso do servidor.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, usou um modelo de Gestor de Recursos Azure para criar um novo grupo de recursos e um recurso de servidor azure Analysis Services. Depois de criar um recurso de servidor utilizando o modelo, considere o seguinte:
- [Início Rápido: Criar um servidor – PowerShell](analysis-services-create-powershell.md)
- [Adicionar um modelo de exemplo a partir do portal](analysis-services-create-sample-model.md)
- [Configurar funções de utilizador e de administrador de servidor](tutorials/analysis-services-tutorial-roles.md)