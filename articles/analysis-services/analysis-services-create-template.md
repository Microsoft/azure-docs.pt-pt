---
title: Quickstart - Criar um recurso de servidor de Serviços de Análise Azure utilizando o modelo do Gestor de Recursos Azure
description: Quickstart mostrando como obter um recurso de servidor Azure Analysis Services utilizando um modelo de Gestor de Recursos Azure.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/31/2020
ms.custom: subject-armqs, references_regions
ms.openlocfilehash: 3e776bf41420d38a1b208ce11a6a34e97fa92a15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89230794"
---
# <a name="quickstart-create-a-server---arm-template"></a>Quickstart: Criar um servidor - modelo ARM

Este quickstart descreve como criar um recurso de servidor de Serviços de Análise na sua subscrição Azure utilizando um modelo de Gestor de Recursos Azure (modelo ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura Azure**: Visite [o Azure Free Trial](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
* **Azure Active Directory**: a sua subscrição tem de estar associada a um inquilino do Azure Active Directory. E tem de ter sessão iniciada no Azure com uma conta nesse Azure Active Directory. Para saber mais, veja [Authentication and user permissions](analysis-services-manage-users.md) (Permissões de autenticação e utilizador).

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

Um único recurso [Microsoft.AnalysisServices/servidores](/azure/templates/microsoft.analysisservices/servers) com uma regra de firewall é definido no modelo.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione o seguinte Link Implementar para Azul para iniciar sação no Azure e abrir um modelo. O modelo é usado para criar um recurso de servidor de Serviços de Análise e especificar propriedades necessárias e opcionais.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

    Salvo especificação em contrário, utilize valores predefinidos.

    * **Assinatura**: Selecione uma subscrição Azure.
    * **Grupo de recursos**: Clique em **Criar novo** e, em seguida, insira um nome único para o novo grupo de recursos.
    * **Localização**: Selecione uma localização predefinitiva para os recursos criados no grupo de recursos.
    * **Nome do servidor**: Introduza um nome para o recurso do servidor. 
    * **Localização**: Ignore para serviços de análise. A localização é especificada na localização do servidor.
    * **Localização** do servidor : Introduza a localização do servidor Serviços de Análise. Esta é frequentemente a mesma região que a Localização padrão especificada para o Grupo de Recursos, mas não é necessária. Por **exemplo, North Central US**. Para regiões apoiadas, consulte [a disponibilidade de Serviços de Análise por região.](analysis-services-overview.md#availability-by-region)
    * **Nome Sku**: Introduza o nome sku para o servidor de Serviços de Análise para criar. Escolha: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. A disponibilidade de Sku depende da região. S0 ou D1 é recomendado para avaliação e teste.
    * **Capacidade**: Introduza o número total de casos de replicação de replicação de consulta. A escala de mais de um caso é apoiada apenas em regiões selecionadas.
    * **Definições de firewall**: Introduza as regras de firewall de entrada para definir para o servidor. Se não for especificado, a firewall é desativada.
    * **Backup Blob Container Uri**: Introduza o SAS URI num recipiente privado de armazenamento Azure Blob com permissões de leitura, escrita e lista. Só é necessário se pretender utilizar [backup/restauro.](analysis-services-backup.md)
    * **Aceito os termos e condições acima apresentados**: selecione.

3. Selecione **Comprar**. Depois de o servidor ter sido implementado com sucesso, recebe uma notificação:

   ![Modelo ARM, implementar notificação do portal](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Validar a implementação

Utilize o portal Azure ou Azure PowerShell para verificar se o grupo de recursos e o recurso do servidor foram criados.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, utilize o portal Azure CLI ou a Azure PowerShell para eliminar o grupo de recursos e o recurso do servidor.

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

---

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, usou um modelo ARM para criar um novo grupo de recursos e um recurso de servidor Azure Analysis Services. Depois de criar um recurso de servidor utilizando o modelo, considere o seguinte:

> [!div class="nextstepaction"]
> [Quickstart: Configurar a firewall do servidor – Portal](analysis-services-qs-firewall.md)   
