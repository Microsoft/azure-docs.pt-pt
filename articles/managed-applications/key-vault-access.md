---
title: Utilizar o Cofre de chaves do Azure com aplicações geridas | Documentos da Microsoft
description: Mostra como utilizar o acesso aos segredos no Azure Key Vault ao implementar aplicações geridas
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 55410250ccd4dfceac8ac9ae5b81d4736de0d91a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60588299"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Segredo do Key Vault acesso ao implementar aplicações geridas do Azure

Quando precisar passar um valor seguro (como uma palavra-passe) como um parâmetro durante a implementação, pode recuperar o valor de uma [do Azure Key Vault](../key-vault/key-vault-whatis.md). Para aceder ao Cofre de chave ao implementar aplicações geridas, tem de conceder acesso para o **fornecedor de recursos da aplicação** principal de serviço. O serviço de aplicações geridas utiliza esta identidade para executar operações. Para recuperar com êxito um valor de um cofre de chaves durante a implementação, o principal de serviço tem de ser capaz de aceder ao Cofre de chave.

Este artigo descreve como configurar o Cofre de chaves para trabalhar com aplicações geridas.

## <a name="enable-template-deployment"></a>Ativar a implementação do modelo

1. No portal, selecione o seu Cofre de chaves.

1. Selecione **Políticas de acesso**.   

   ![Selecione políticas de acesso](./media/key-vault-access/select-access-policies.png)

1. Selecione **Clicar para mostrar as políticas de acesso avançadas**.

   ![Mostrar as políticas de acesso avançadas](./media/key-vault-access/advanced.png)

1. Selecione **ativar o acesso ao Azure Resource Manager para a implementação de modelo**. Em seguida, selecione **Guardar**.

   ![Ativar a implementação do modelo](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Adicionar o serviço como contribuinte

1. Selecione **controlo de acesso (IAM)** .

   ![Selecione o controlo de acesso](./media/key-vault-access/access-control.png)

1. Selecione **adicionar atribuição de função**.

   ![Selecione adicionar](./media/key-vault-access/add-access-control.png)

1. Selecione **contribuinte** para a função. Procure **fornecedor de recursos da aplicação** e selecione-o nas opções disponíveis.

   ![Pesquisar por fornecedor](./media/key-vault-access/search-provider.png)

1. Selecione **Guardar**.

## <a name="reference-key-vault-secret"></a>Segredo do Key Vault de referência

Para passar um segredo a partir de um cofre de chave para um modelo na sua aplicação gerida, tem de utilizar um [modelo ligado](../azure-resource-manager/resource-group-linked-templates.md) e referenciar o Cofre de chaves nos parâmetros para o modelo ligado. Forneça o ID de recurso do Key Vault e o nome do segredo do.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>Passos Seguintes

Configurou o seu Cofre de chaves para ser acessível durante a implementação de uma aplicação gerida.

* Para obter informações sobre a transmissão de um valor de um cofre de chave como um parâmetro de modelo, consulte [do Azure com o Key Vault para transmitir o valor do parâmetro segura durante a implementação](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Para obter exemplos de aplicação gerida, veja [aplicativos gerenciados de projetos de exemplo para o Azure](sample-projects.md).
* Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, veja [Introdução a CreateUiDefinition](create-uidefinition-overview.md).