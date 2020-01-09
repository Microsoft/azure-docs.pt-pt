---
title: Atualizar recursos geridos
description: Descreve como trabalhar em recursos no grupo de recursos gerenciados para um aplicativo gerenciado do Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651218"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Trabalhar com recursos no grupo de recursos gerenciado para o aplicativo gerenciado do Azure

Este artigo descreve como atualizar os recursos que são implantados como parte de um aplicativo gerenciado. Como o editor de um aplicativo gerenciado, você tem acesso aos recursos no grupo de recursos gerenciado. Para atualizar esses recursos, você precisa localizar o grupo de recursos gerenciados associado a um aplicativo gerenciado e acessar o recurso nesse grupo de recursos.

Este artigo pressupõe que você implantou o aplicativo gerenciado no [aplicativo Web gerenciado (IaaS) com o projeto de exemplo dos serviços de gerenciamento do Azure](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) . Esse aplicativo gerenciado inclui uma máquina virtual **Standard_D1_v2** . Se você não tiver implantado esse aplicativo gerenciado, ainda poderá usar este artigo para se familiarizar com as etapas para atualizar um grupo de recursos gerenciado.

A imagem a seguir mostra o aplicativo gerenciado implantado.

![Aplicativo gerenciado implantado](./media/update-managed-resources/deployed.png)

Neste artigo, você usa CLI do Azure para:

* Identificar o aplicativo gerenciado
* Identificar o grupo de recursos gerenciados
* Identificar os recursos de máquina virtual no grupo de recursos gerenciados
* Alterar o tamanho da VM (para um tamanho menor, se não for utilizado, ou maior para oferecer suporte a mais carga)
* Atribuir uma política ao grupo de recursos gerenciado que especifica os locais permitidos

## <a name="get-managed-application-and-managed-resource-group"></a>Obter aplicativo gerenciado e grupo de recursos gerenciados

Para obter os aplicativos gerenciados em um grupo de recursos, use:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Para obter a ID do grupo de recursos gerenciado, use:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Redimensionar VMs no grupo de recursos gerenciado

Para ver as máquinas virtuais no grupo de recursos gerenciado, forneça o nome do grupo de recursos gerenciado.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Para atualizar o tamanho das VMs, use:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Após a conclusão da operação, verifique se o aplicativo está em execução no padrão D2 v2.

![Aplicativo gerenciado usando o padrão D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Aplicar política ao grupo de recursos gerenciado

Obtenha o grupo de recursos gerenciados e a atribuição de uma política nesse escopo. A política **e56962a6-4747-49cd-b67b-bf8b01975c4c** é uma política interna para especificar locais permitidos.

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

Para ver os locais permitidos, use:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

A atribuição de política aparece no Portal.

![Exibir atribuição de política](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral das aplicações geridas](overview.md).
* Para projetos de exemplo, consulte [projetos de exemplo para aplicativos gerenciados do Azure](sample-projects.md).
