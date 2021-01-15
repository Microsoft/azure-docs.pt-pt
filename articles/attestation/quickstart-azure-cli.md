---
title: Configurar a Azure Attestation com o Azure CLI
description: Como configurar e configurar um fornecedor de atestado utilizando o Azure CLI.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: df1064128d6a4eca8497ebf3ea0c6ae8cd42255c
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208442"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Quickstart: Instale a Azure Attestation com o Azure CLI

Começa com [o Azure Attestation utilizando o Azure CLI](/cli/azure/ext/attestation/attestation?view=azure-cli-latest).

## <a name="get-started"></a>Introdução

1. Instale esta extensão utilizando o comando CLI abaixo

   ```azurecli
   az extension add --name attestation
   ```
   
1. Verifique a versão

   ```azurecli
   az extension show --name attestation --query version
   ```

1. Utilize o seguinte comando para assinar no Azure:

   ```azurecli
   az login
   ```

1. Se necessário, mude para a subscrição para Azure Attestation:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Registe o fornecedor de recursos Microsoft.Attestation na subscrição com o comando [de registo do fornecedor az:](/cli/azure/provider#az_provider_register)

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Para obter mais informações sobre os fornecedores de recursos Azure, e como configurá-los e geri-los, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > Só precisa de registar um fornecedor de recursos uma vez para uma subscrição.

1. Criar um grupo de recursos para o fornecedor de atestado. Pode colocar outros recursos Azure no mesmo grupo de recursos, incluindo uma máquina virtual com uma instância de aplicação do cliente. Executar o [grupo az criar](/cli/azure/group#az_group_create) comando para criar um grupo de recursos, ou usar um grupo de recursos existente:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Criar e gerir um fornecedor de atestado

Aqui estão os comandos que pode utilizar para criar e gerir o provedor de atestado:

1. Executar o [attestation az criar](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_create) comando para criar um provedor de atestado sem requisito de assinatura de política:

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. Executar o comando [do az attestation show](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_show) para recuperar propriedades do fornecedor de atestados tais como estado e AttestURI:

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   Este comando apresenta valores como a seguinte saída:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

Pode eliminar um fornecedor de atestado utilizando o comando [az atestado eliminar:](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_delete)

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>Gestão de políticas

Utilize os comandos aqui descritos para fornecer gestão de políticas para um provedor de atestado, um tipo de atestado de cada vez.

O comando [de política az attestation devolve](/cli/azure/ext/attestation/attestation/policy?view=azure-cli-latest#ext_attestation_az_attestation_policy_show) a política atual para o TEE especificado:

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> O comando exibe a política tanto no formato texto como no formato JWT.

São suportados os tipos DE TEE suportados:

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

Utilize o comando [de definição de política az atestado](/cli/azure/ext/attestation/attestation/policy?view=azure-cli-latest#ext_attestation_az_attestation_policy_set) para definir uma nova política para o tipo de atestado especificado.

Para definir a política em formato de texto para um dado tipo de tipo de atestado utilizando o caminho do ficheiro:

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

Para definir a política no formato JWT para um dado tipo de tipo de atestado utilizando o caminho do ficheiro:

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK -f "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>Passos seguintes

- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Implementar atestado com um enclave SGX usando amostras de código](/samples/browse/?expanded=azure&terms=attestation)
