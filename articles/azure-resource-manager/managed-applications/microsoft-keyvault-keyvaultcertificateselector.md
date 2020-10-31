---
title: Elemento de UI keyVaultCertificateSelector
description: Descreve o elemento UI microsoft.KeyVaultCertificate para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101226"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Microsoft.KeyVault.KeyVaultCertificateSelector UI elemento

Um controlo para selecionar um certificado de cofre chave.

## <a name="ui-sample"></a>Amostra de UI

O utilizador é apresentado com a opção de selecionar um certificado disponível.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft.keyVault.KeyVaultCertificateselector":::

Depois de selecionar **Selecionar um certificado,** o utilizador pode especificar um cofre e certificado chave a partir do cofre da chave.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft.keyVault.KeyVaultCertificateselector":::

O controlo é atualizado para exibir o cofre de teclas selecionado e o nome do certificado.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft.keyVault.KeyVaultCertificateselector":::

## <a name="schema"></a>Esquema

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
