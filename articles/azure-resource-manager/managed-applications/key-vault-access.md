---
title: Usar Key Vault ao implantar o aplicativo gerenciado
description: Mostra como usar segredos de acesso no Azure Key Vault ao implantar aplicativos gerenciados
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: d82e5aed6318e112a0daabf581aec61c8ed5fcbc
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650646"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Acessar Key Vault segredo ao implantar aplicativos gerenciados do Azure

Quando você precisar passar um valor seguro (como uma senha) como um parâmetro durante a implantação, poderá recuperar o valor de um [Azure Key Vault](../../key-vault/key-vault-overview.md). Para acessar o Key Vault ao implantar aplicativos gerenciados, você deve conceder acesso à entidade de serviço do **provedor de recursos do dispositivo** . O serviço de aplicativos gerenciados usa essa identidade para executar operações. Para recuperar com êxito um valor de um Key Vault durante a implantação, a entidade de serviço deve ser capaz de acessar o Key Vault.

Este artigo descreve como configurar o Key Vault para trabalhar com aplicativos gerenciados.

## <a name="enable-template-deployment"></a>Habilitar implantação de modelo

1. No portal, selecione seu Key Vault.

1. Selecione **Políticas de acesso**.   

   ![Selecionar políticas de acesso](./media/key-vault-access/select-access-policies.png)

1. Selecione **Clicar para mostrar as políticas de acesso avançadas**.

   ![Mostrar políticas de acesso avançadas](./media/key-vault-access/advanced.png)

1. Selecione **habilitar acesso a Azure Resource Manager para implantação de modelo**. Em seguida, selecione **Guardar**.

   ![Habilitar implantação de modelo](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Adicionar serviço como colaborador

1. Selecione **Controlo de acesso (IAM)** .

   ![Selecionar controle de acesso](./media/key-vault-access/access-control.png)

1. Selecione **adicionar atribuição de função**.

   ![Selecionar Adicionar](./media/key-vault-access/add-access-control.png)

1. Selecione **colaborador** para a função. Pesquise **provedor de recursos do dispositivo** e selecione-o nas opções disponíveis.

   ![Pesquisar provedor](./media/key-vault-access/search-provider.png)

1. Selecione **Guardar**.

## <a name="reference-key-vault-secret"></a>Segredo de Key Vault de referência

Para passar um segredo de um Key Vault para um modelo em seu aplicativo gerenciado, você deve usar um [modelo vinculado](../templates/linked-templates.md) e referenciar o Key Vault nos parâmetros para o modelo vinculado. Forneça a ID de recurso do Key Vault e o nome do segredo.

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

## <a name="next-steps"></a>Passos seguintes

Você configurou seu Key Vault para ser acessível durante a implantação de um aplicativo gerenciado.

* Para obter informações sobre como passar um valor de um Key Vault como um parâmetro de modelo, consulte [usar Azure Key Vault para passar o valor do parâmetro seguro durante a implantação](../templates/key-vault-parameter.md).
* Para obter exemplos de aplicativos gerenciados, consulte [projetos de exemplo para aplicativos gerenciados do Azure](sample-projects.md).
* Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, veja [Introdução a CreateUiDefinition](create-uidefinition-overview.md).