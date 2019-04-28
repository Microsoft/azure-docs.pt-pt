---
title: Implementar um modelo do Azure com o SAS token e a CLI do Azure | Documentos da Microsoft
description: Utilize o Azure Resource Manager e a CLI do Azure para implementar recursos no Azure a partir de um modelo que está protegido com o SAS token.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: c869b76a0d1ba10bc27aefa60cbe4ed5b8d8201a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61061363"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Implementar um modelo privado do Resource Manager com o SAS token e a CLI do Azure

Quando o seu modelo reside numa conta de armazenamento, pode restringir o acesso ao modelo e fornecer um token de assinatura (SAS) de acesso partilhado durante a implementação. Este tópico explica como utilizar o Azure PowerShell com modelos do Resource Manager para fornecer um token SAS durante a implementação. 

## <a name="add-private-template-to-storage-account"></a>Adicionar modelo privado à conta de armazenamento

Pode adicionar os seus modelos para uma conta de armazenamento e a ligação aos mesmos durante a implementação com um token SAS.

> [!IMPORTANT]
> Ao seguir os passos abaixo, o blob que contém o modelo está acessível para apenas o proprietário da conta. No entanto, quando cria um token SAS para o blob, o blob é acessível a qualquer pessoa com esse URI. Se outro utilizador intercepta o URI, esse utilizador é capaz de aceder ao modelo. Através de um token SAS são uma boa forma de limitar o acesso aos seus modelos, mas não pode incluir dados confidenciais como palavras-passe diretamente no modelo.
> 
> 

O exemplo seguinte define um contentor de conta de armazenamento privado e carrega um modelo:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Fornecer o SAS token durante a implementação
Para implementar um modelo privado numa conta de armazenamento, gerar um token SAS e incluí-lo no URI para o modelo. Defina a hora de expiração para permitir tempo suficiente concluir a implementação.
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Para obter um exemplo de como utilizar um token SAS com modelos ligados, consulte [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à implantação de modelos, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy-cli.md).
* Para obter um script de exemplo completo que implementa um modelo, consulte [script de modelo de implementação Resource Manager](resource-manager-samples-cli-deploy.md)
* Para definir parâmetros no modelo, veja [criação de modelos](resource-group-authoring-templates.md#parameters).
