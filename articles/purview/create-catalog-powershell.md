---
title: 'Quickstart: Criar uma conta Azure Purview utilizando Azure PowerShell/Azure CLI (pré-visualização)'
description: Este Quickstart descreve como criar uma conta Azure Purview usando Azure PowerShell/Azure CLI.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 11/23/2020
ms.openlocfilehash: d03e343e9158f237ee786ff1b1d06436bdd2d6e7
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555985"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Quickstart: Criar uma conta Azure Purview utilizando Azure PowerShell/Azure CLI

> [!IMPORTANT]
> A Azure Purview está atualmente em PREVIEW. Os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam às funcionalidades do Azure que estão em versão beta, pré-visualização ou ainda não lançadas para a disponibilidade geral.

Neste Quickstart, cria uma conta Azure Purview utilizando o Azure PowerShell/Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* A conta de utilizador que utiliza para iniciar seduca no Azure deve ser membro da função de contribuinte ou proprietário, ou administrador da subscrição Azure.

* Seu próprio [inquilino do Azure Ative Directory.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

* Instale o Azure PowerShell ou o Azure CLI na sua máquina de cliente para implementar o modelo: [implantação da linha de comando](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta do Azure.

## <a name="configure-your-subscription"></a>Configurar a sua subscrição

Se necessário, siga estes passos para configurar a sua subscrição para permitir que o Azure Purview seja executado na sua subscrição:

   1. No portal Azure, procure e selecione **Subscrições.**

   1. A partir da lista de subscrições, selecione a subscrição que pretende utilizar. É necessária autorização de acesso administrativo para a subscrição.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Screenshot mostrando como selecionar uma subscrição no portal Azure.":::

   1. Para a sua subscrição, selecione **Fornecedores de Recursos.** No painel de fornecedores de **recursos,** procure e registe os três fornecedores de recursos: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Se não estiverem registados, registe-o selecionando **Registo**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Screenshot mostrando como registar o fornecedor de recursos microsoft dot Azure Purview no portal Azure.":::

## <a name="create-an-azure-purview-account-instance"></a>Criar uma instância de conta Azure Purview

1. Inscreva-se com a sua credencial Azure

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Se tiver várias subscrições do Azure, selecione a subscrição que pretende utilizar:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Crie um grupo de recursos para a sua conta Purview. Pode saltar este passo se já tiver um:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Crie um ficheiro de modelo de Purview, como `purviewtemplate.json` . Pode atualizar `name` `location` , e `capacity` `4` (ou `16` ):

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Implementar o modelo de visão de purga

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. O comando de implantação retorna os resultados. Procure `ProvisioningState` para ver se o destacamento foi bem sucedido.
    
## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Purview.

Avance para o próximo artigo para saber como permitir que os utilizadores acedam à sua Conta Azure Purview. 

> [!div class="nextstepaction"]
> [Adicione utilizadores à sua Conta Azure Purview](catalog-permissions.md)