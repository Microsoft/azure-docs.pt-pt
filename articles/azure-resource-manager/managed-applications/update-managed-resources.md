---
title: Atualizar recursos geridos
description: Descreve como trabalhar em recursos no grupo de recursos geridos para uma aplicação gerida pelo Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651218"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Trabalhar com recursos no grupo de recursos geridos para a aplicação gerida pelo Azure

Este artigo descreve como atualizar os recursos que são implantados como parte de uma aplicação gerida. Como editor de uma aplicação gerida, tem acesso aos recursos do grupo de recursos geridos. Para atualizar estes recursos, é necessário encontrar o grupo de recursos geridos associado a uma aplicação gerida e aceder ao recurso desse grupo de recursos.

Este artigo assume que implementou a aplicação gerida na [Aplicação Web Gerida (IaaS) com](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) o projeto de amostra de serviços de gestão Azure. Esta aplicação gerida inclui uma máquina virtual **Standard_D1_v2.** Se ainda não implementou essa aplicação gerida, ainda pode utilizar este artigo para se familiarizar com os passos para atualizar um grupo de recursos geridos.

A imagem seguinte mostra a aplicação gerida implantada.

![Aplicação gerida implantada](./media/update-managed-resources/deployed.png)

Neste artigo, utiliza o Azure CLI para:

* Identificar a aplicação gerida
* Identificar o grupo de recursos geridos
* Identificar os recursos das máquinas virtuais no grupo de recursos geridos
* Alterar o tamanho vm (ou para um tamanho menor se não for utilizado, ou um maior para suportar mais carga)
* Atribuir uma política ao grupo de recursos geridos que especifica as localizações permitidas

## <a name="get-managed-application-and-managed-resource-group"></a>Obtenha aplicação gerida e grupo de recursos geridos

Para obter as aplicações geridas num grupo de recursos, utilize:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Para obter a identificação do grupo de recursos geridos, use:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Redimensionar VMs em grupo de recursos geridos

Para ver as máquinas virtuais no grupo de recursos geridos, forneça o nome do grupo de recursos geridos.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Para atualizar o tamanho dos VMs, utilize:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Após a operação estar concluída, verifique se a aplicação está a decorrer no Standard D2 v2.

![Aplicação gerida utilizando o Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Aplicar a política ao grupo de recursos geridos

Obtenha o grupo de recursos gerido e atribuindo uma política nesse âmbito. A política **e56962a6-4747-49cd-b67b-bf8b01975c4c** é uma política incorporada para especificar as localizações permitidas.

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

Para ver os locais permitidos, utilize:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

A atribuição da apólice aparece no portal.

![Ver atribuição de políticas](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral das aplicações geridas](overview.md).
* Para projetos de amostra, consulte [projetos de amostra para aplicações geridas pelo Azure.](sample-projects.md)
