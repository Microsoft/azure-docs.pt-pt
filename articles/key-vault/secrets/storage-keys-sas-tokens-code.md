---
title: Buscar fichas de assinatura de acesso partilhado em código Cofre da Chave Azure
description: A funcionalidade de conta de armazenamento gerida proporciona uma integração perfeita entre o Azure Key Vault e uma conta de armazenamento Azure. Esta amostra utiliza o Azure SDK para .NET para gerir fichas SAS.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a0202c5259ccebedf03ade217f57b6305b9fa1b
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444933"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>Crie a definição SAS e pegue fichas de assinatura de acesso partilhado em código

Pode gerir a sua conta de armazenamento com fichas de acesso partilhado (SAS) armazenadas no cofre da chave. Para obter mais informações, consulte [Grant acesso limitado aos recursos de armazenamento Azure usando SAS](../../storage/common/storage-sas-overview.md).

> [!NOTE]
> Recomendamos a utilização do [controlo de acesso baseado em funções (Azure RBAC)](../../storage/common/storage-auth-aad.md) para garantir a sua conta de armazenamento para uma segurança superior e facilidade de utilização através da autorização da Chave Partilhada.

Este artigo fornece amostras de código .NET que criam uma definição SAS e recolhe fichas SAS. Consulte a nossa amostra [ShareLink](/samples/azure/azure-sdk-for-net/share-link/) para obter todos os detalhes, incluindo o cliente gerado para contas de armazenamento geridas pelo Key Vault. Para obter informações sobre como criar e armazenar fichas SAS, consulte as chaves da conta de armazenamento com o Key Vault e as teclas de conta de armazenamento [Azure CLI](overview-storage-keys.md) ou [Manage com Key Vault e Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Exemplos de código

No exemplo seguinte criaremos um modelo SAS:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Usando este modelo, podemos criar uma definição SAS usando o 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Uma vez criada a definição SAS, pode recuperar fichas SAS como segredos usando um `SecretClient` . Você precisa prefaciar o nome secreto com o nome da conta de armazenamento seguido de um traço:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Se o seu token de assinatura de acesso partilhado estiver prestes a expirar, pode obter o mesmo segredo novamente para gerar um novo.

Para obter um guia sobre como utilizar o token SAS do Key Vault SAS para aceder aos serviços de Armazenamento Azure, consulte [utilizar uma conta SAS para aceder ao serviço Blob](../../storage/common/storage-account-sas-create-dotnet.md#use-an-account-sas-from-a-client)

> [!NOTE]
> A sua aplicação precisa de estar preparada para refrescar o SAS se obtiver um 403 do Storage para que possa lidar com o caso em que uma chave foi comprometida e precisa rodá-los mais rapidamente do que o período normal de rotação. 

## <a name="next-steps"></a>Passos seguintes
- Saiba como [conceder acesso limitado aos recursos de armazenamento Azure utilizando SAS.](../../storage/common/storage-sas-overview.md)
- Saiba como [gerir as chaves da conta de armazenamento com o Key Vault e o Azure CLI](overview-storage-keys.md) ou [a Azure PowerShell](overview-storage-keys-powershell.md).
- Ver [amostras-chave da conta de armazenamento gerida](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)