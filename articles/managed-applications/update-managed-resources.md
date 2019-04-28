---
title: Atualizar recursos no Azure aplicações geridas | Documentos da Microsoft
description: Descreve como trabalhar nos recursos gerenciados da aplicação gerida pelo grupo de recursos para do Azure.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 21f4e0aa339eb0c746f9b9b06f8aaada6c4d4b71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043457"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Trabalhar com recursos gerenciados na aplicação gerida pelo grupo de recursos do Azure

Este artigo descreve como atualizar os recursos que são implementados como parte de um aplicativo gerenciado. Como o Editor de um aplicativo gerenciado, terá acesso aos recursos no grupo de recursos gerido. Para atualizar estes recursos, terá de encontrar o grupo de recursos gerido associado um aplicativo gerenciado e aceder ao recurso nesse grupo de recursos.

Este artigo pressupõe que implementou a aplicação gerida no [Managed Web Application (IaaS) com os serviços de gestão do Azure](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) projeto de exemplo. Se a aplicação gerida inclui um **Standard_D1_v2** máquina virtual. Se não tiver implementado nesse aplicativo gerenciado, pode usar este artigo para se familiarizar com os passos para atualizar um grupo de recursos gerido.

A imagem seguinte mostra a aplicação gerida implementada.

![Aplicação gerida implementada](./media/update-managed-resources/deployed.png)

Neste artigo, utilize o CLI do Azure para:

* Identificar a aplicação gerida
* Identificar o grupo de recursos gerido
* Identificar o recurso ou recursos de máquina virtual no grupo de recursos gerido
* Alterar o tamanho da VM (seja para um tamanho mais pequeno, se não utilizado, ou uma maior para dar suporte a mais carga)
* Atribuir uma política para o grupo de recursos gerido que especifica as localizações permitidas

## <a name="get-managed-application-and-managed-resource-group"></a>Obter a aplicação gerida e grupo de recursos gerido

Para obter os aplicativos gerenciados num grupo de recursos, utilize:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Para obter o ID do grupo de recursos gerido, utilize:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Redimensionar VMs no grupo de recursos gerido

Para ver as máquinas virtuais no grupo de recursos gerido, forneça o nome do grupo de recursos gerido.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Para atualizar o tamanho das VMs, utilize:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Depois de concluída a operação, certifique-se de que a aplicação está em execução no Standard D2 v2.

![Aplicação gerida com a Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Aplicar a política de grupo de recursos gerido

O grupo de recursos gerido e a atribuição de uma política, obter esse âmbito. A política **e56962a6-4747-49cd-b67b-bf8b01975c4c** é uma política incorporada para especificar localizações permitidas.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Para ver as localizações permitidas, utilize:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

A atribuição de política é apresentada no portal.

![Atribuição de política do Vista](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral das aplicações geridas](overview.md).
* Para projetos de exemplo, consulte [aplicativos gerenciados de projetos de exemplo para o Azure](sample-projects.md).
