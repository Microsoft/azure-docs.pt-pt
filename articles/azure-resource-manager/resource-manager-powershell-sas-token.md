---
title: Implementar um modelo do Azure com o SAS token e o PowerShell | Documentos da Microsoft
description: Utilize o Azure Resource Manager e o Azure PowerShell para implementar recursos no Azure a partir de um modelo que está protegido com o SAS token.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: 818aa63ced56d7cf382536f10bb6150199ab74e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66128421"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Implementar um modelo privado do Resource Manager com o SAS token e o Azure PowerShell

Quando o seu modelo reside numa conta de armazenamento, pode restringir o acesso ao modelo e fornecer um token de assinatura (SAS) de acesso partilhado durante a implementação. Este tópico explica como utilizar o Azure PowerShell com modelos do Resource Manager para fornecer um token SAS durante a implementação. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-private-template-to-storage-account"></a>Adicionar modelo privado à conta de armazenamento

Pode adicionar os seus modelos para uma conta de armazenamento e a ligação aos mesmos durante a implementação com um token SAS.

> [!IMPORTANT]
> Ao seguir os passos abaixo, o blob que contém o modelo está acessível para apenas o proprietário da conta. No entanto, quando cria um token SAS para o blob, o blob é acessível a qualquer pessoa com esse URI. Se outro utilizador intercepta o URI, esse utilizador é capaz de aceder ao modelo. Através de um token SAS são uma boa forma de limitar o acesso aos seus modelos, mas não pode incluir dados confidenciais como palavras-passe diretamente no modelo.
> 
> 

O exemplo seguinte define um contentor de conta de armazenamento privado e carrega um modelo:
   
```powershell
# create a storage account for templates
New-AzResourceGroup -Name ManageGroup -Location "South Central US"
New-AzStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzStorageContainer -Name templates -Permission Off
Set-AzStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Fornecer o SAS token durante a implementação
Para implementar um modelo privado numa conta de armazenamento, gerar um token SAS e incluí-lo no URI para o modelo. Defina a hora de expiração para permitir tempo suficiente concluir a implementação.
   
```powershell
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Para obter um exemplo de como utilizar um token SAS com modelos ligados, consulte [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à implantação de modelos, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md).
* Para obter um script de exemplo completo que implementa um modelo, consulte [script de modelo de implementação Resource Manager](resource-manager-samples-powershell-deploy.md)
* Para definir parâmetros no modelo, veja [criação de modelos](resource-group-authoring-templates.md#parameters).
