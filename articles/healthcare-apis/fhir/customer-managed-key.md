---
title: Configure as chaves geridas pelo cliente para a Azure API para fHIR
description: Traga a sua própria funcionalidade de chave suportada na Azure API para FHIR através da Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: 08b5f63f1fffc2369eff620ca1937f298c2d9ca5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020388"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Configure as chaves geridas pelo cliente em repouso

Quando cria uma nova API Azure para conta FHIR, os seus dados são encriptados utilizando por padrão as teclas geridas pela Microsoft. Agora, pode adicionar uma segunda camada de encriptação para os dados usando a sua própria chave que você escolhe e gere a si mesmo.

Em Azure, isto é normalmente realizado usando uma chave de encriptação no Cofre de Chaves Azure do cliente. Azure SQL, Azure Storage e Cosmos DB são alguns exemplos que fornecem esta capacidade hoje em dia. A Azure API para fHIR aproveita este apoio da Cosmos DB. Ao criar uma conta, terá a opção de especificar uma chave URI do Cofre de Chaves Azure. Esta chave será transmitida à Cosmos DB quando a conta DB for abastado. Quando um pedido de FHIR é feito, cosmos DB pega na sua chave e usa-a para encriptar/desencriptar os dados. Para começar, pode consultar os seguintes links:

- [Registe o fornecedor de recursos DB da Azure Cosmos para a sua subscrição Azure](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Configure o seu exemplo de Cofre de Chave Azure](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Adicione uma política de acesso ao seu Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Gere uma chave no Cofre da Chave Azure](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>Com o Portal do Azure

Ao criar a sua API Azure para conta FHIR no portal Azure, pode ver uma opção de configuração de "Encriptação de Dados" no separador "Definições de Base de Dados" no separador "Definições Adicionais". Por predefinição, a opção chave gerida pelo serviço será escolhida. 

Pode escolher a sua chave no KeyPicker:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="KeyPicker":::

Ou pode especificar a sua chave Azure Key Vault aqui, selecionando a opção "chave gerida pelo cliente". Pode introduzir a chave URI aqui:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Criar Azure API para FHIR":::

Para as contas FHIR existentes, pode ver a escolha de encriptação chave (chave gerida pelo serviço ou gerida pelo cliente) na lâmina "Database" como abaixo. A opção de configuração não pode ser modificada uma vez escolhida. No entanto, pode modificar e atualizar a sua chave.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Base de dados":::

Além disso, pode criar uma nova versão da chave especificada, após a qual os seus dados serão encriptados com a nova versão sem qualquer interrupção de serviço. Também pode remover o acesso à chave para remover o acesso aos dados. Quando a chave estiver desativada, as consultas resultarão num erro. Se a chave for re-activada, as consultas voltarão a ter sucesso.




## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Com a sua chave Azure Key Vault URI, pode configurar a CMK utilizando o PowerShell executando o comando PowerShell abaixo:

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Utilizar a CLI do Azure

Tal como acontece com o método PowerShell, pode configurar a CMK passando a sua chave Azure Key Vault URI sob o `key-vault-key-uri` parâmetro e executando o comando CLI abaixo: 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Usando o modelo de gestor de recursos Azure

Com a sua chave Azure Key Vault URI, pode configurar a CMK passando-a sob a propriedade **KeyVaultKeyUri** no objeto **de propriedades.**

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

E pode implementar o modelo com o seguinte script PowerShell:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar chaves geridas pelo cliente em repouso usando o portal Azure, PowerShell, CLI e Resource Manager Template. Pode consultar a secção Azure Cosmos DB FAQ para obter perguntas adicionais que possa ter: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: como configurar a CMK](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)