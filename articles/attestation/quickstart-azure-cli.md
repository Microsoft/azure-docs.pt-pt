---
title: Configurar a Azure Attestation com o Azure CLI
description: Como configurar e configurar um fornecedor de atestado utilizando o Azure CLI.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: dee9e7596c0a30301d9e0453ef22a6dfe9541522
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "96020947"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Quickstart: Instale a Azure Attestation com o Azure CLI

Começa com o Azure Attestation utilizando o Azure CLI para configurar o atestado.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>Introdução

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

1. Executar o [atestado az criar](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) comando para criar um provedor de atestado:

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   O parâmetro **--certs-input-path** especifica um conjunto de chaves de assinatura fidedignas. Se especificar um nome de ficheiro para este parâmetro, o provedor de atestado deve ser configurado apenas com políticas em formato JWT assinado. Caso contrário, a política pode ser configurada em texto ou num formato JWT não assinado. Para obter informações sobre o JWT, consulte [Conceitos Básicos.](basic-concepts.md) Para amostras [de certificados, consulte exemplos de um certificado de sinalização de política de atestado](policy-signer-examples.md).

1. Executar o comando [do az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) para recuperar propriedades do fornecedor de atestados tais como estado e AttestURI:

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
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

Pode eliminar um fornecedor de atestado utilizando o comando [az atestado eliminar:](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete)

```azurecli
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>Gestão de políticas

Para gerir as políticas, um utilizador AD Azure requer as seguintes permissões `Actions` para:

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

Estas permissões podem ser atribuídas a um utilizador Azure AD através de uma função como `Owner` (permissões wildcard), `Contributor` ou `Attestation Contributor` (permissões específicas apenas para Azure Attestation).  

Para ler políticas, um utilizador AZure AD requer a seguinte permissão `Actions` para:

- `Microsoft.Attestation/attestationProviders/attestation/read`

Esta permissão pode ser atribuída a um utilizador Azure AD através de uma função como `Reader` (permissões wildcard) ou `Attestation Reader` (permissões específicas apenas para Azure Attestation).

Utilize os comandos aqui descritos para fornecer gestão de políticas para um provedor de atestado, um TEE de cada vez.

O comando [de política az attestation devolve](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) a política atual para o TEE especificado:

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> O comando exibe a política tanto no formato texto como no formato JWT.

São suportados os tipos DE TEE suportados:

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

Utilize o comando [de definição de política az attestation](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) para definir uma nova política para o TEE especificado.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

A política de atestado no formato JWT deve conter uma reclamação denominada `AttestationPolicy` . Uma política assinada deve ser assinada com uma chave que corresponda a qualquer um dos certificados de signatários da política existentes.

Para amostras de política, consulte [exemplos de uma política de atestado](policy-examples.md).

O comando [de reset da política az attestation](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) define uma nova política para o TEE especificado.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>Gestão de certificados de signatários de apólices

Utilize os seguintes comandos para gerir os certificados de sinalização de política para um prestador de atestado:

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

Um certificado de sinalizador de apólice é um JWT assinado com uma reclamação denominada `maa-policyCertificate` . O valor da reclamação é um JWK, que contém a chave de assinatura fidedigna a adicionar. O JWT deve ser assinado com uma chave privada que corresponda a qualquer um dos certificados de sinalização de política existentes. Para obter informações sobre JWT e JWK, consulte [Conceitos Básicos.](basic-concepts.md)

Toda a manipulação semântica do certificado de signatário da apólice deve ser feita fora do Azure CLI. No que diz respeito ao Azure CLI, é uma simples corda.

Para amostras [de certificados, consulte exemplos de um certificado de sinalização de política de atestado](policy-signer-examples.md).

## <a name="next-steps"></a>Passos seguintes

- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Implementar atestado com um enclave SGX usando amostras de código](/samples/browse/?expanded=azure&terms=attestation)
