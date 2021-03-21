---
title: Atualizar recursos geridos
description: Descreve como trabalhar em recursos no grupo de recursos geridos para uma aplicação gerida pelo Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75651218"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Trabalhar com recursos no grupo de recursos geridos para aplicação gerida a Azure

Este artigo descreve como atualizar recursos que são implantados como parte de uma aplicação gerida. Como editor de uma aplicação gerida, tem acesso aos recursos do grupo de recursos geridos. Para atualizar estes recursos, é necessário encontrar o grupo de recursos geridos associado a uma aplicação gerida e aceder ao recurso nesse grupo de recursos.

Este artigo pressupõe que implementou a aplicação gerida na [Aplicação Web Gerida (IaaS) com o projeto de amostra de serviços de gestão Azure.](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) Esta aplicação gerida inclui uma máquina virtual **Standard_D1_v2.** Se não implementou essa aplicação gerida, ainda pode usar este artigo para se familiarizar com os passos para atualizar um grupo de recursos geridos.

A imagem a seguir mostra a aplicação gerida implementada.

![Aplicação gerida implantada](./media/update-managed-resources/deployed.png)

Neste artigo, você usa Azure CLI para:

* Identificar a aplicação gerida
* Identificar o grupo de recursos geridos
* Identificar os recursos de máquina virtual no grupo de recursos geridos
* Altere o tamanho VM (seja para um tamanho menor se não for utilizado, ou um maior para suportar mais carga)
* Atribua uma política ao grupo de recursos geridos que especifica as localizações permitidas

## <a name="get-managed-application-and-managed-resource-group"></a>Obtenha aplicação gerida e grupo de recursos geridos

Para obter as aplicações geridas num grupo de recursos, utilize:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Para obter a identidade do grupo de recursos geridos, utilize:

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

Após o funcionamento concluído, verifique se o pedido está em execução na Norma D2 v2.

![Aplicação gerida utilizando o Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Aplicar a política ao grupo de recursos geridos

Obtenha o grupo de recursos gerido e a atribuição de uma política nesse âmbito. A política **e56962a6-4747-49cd-b67b-bf8b01975c4c** é uma política incorporada para especificar locais permitidos.

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

Para ver as localizações permitidas, use:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

A atribuição de apólices aparece no portal.

![Ver atribuição de políticas](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral das aplicações geridas](overview.md).
* Para projetos de amostragem, consulte [projetos de amostra para aplicações geridas pela Azure.](sample-projects.md)
