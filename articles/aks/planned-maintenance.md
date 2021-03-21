---
title: Utilize a Manutenção Planeada para o seu cluster Azure Kubernetes (AKS) (pré-visualização)
titleSuffix: Azure Kubernetes Service
description: Saiba como utilizar a Manutenção Planeada no Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 8526d7c1c436074fbf6f838caf232e1abee06339
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670380"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Utilize a Manutenção Planeada para agendar janelas de manutenção para o seu cluster Azure Kubernetes Service (AKS) (pré-visualização)

O seu cluster AKS tem uma manutenção regular efetuado nele automaticamente. Por defeito, este trabalho pode acontecer a qualquer momento. A Manutenção Planeada permite-lhe agendar janelas de manutenção semanais que irão atualizar o seu plano de controlo, bem como as suas cápsulas de sistema kube numa instância VMSS e minimizar o impacto da carga de trabalho. Uma vez programado, toda a sua manutenção ocorrerá durante a janela selecionada. Pode agendar uma ou mais janelas semanais no seu cluster especificando um intervalo de dia ou de tempo num dia específico. As janelas de manutenção são configuradas utilizando o Azure CLI.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Limitações

Ao utilizar a Manutenção Planeada, aplicam-se as seguintes restrições:

- A AKS reserva-se o direito de partir estas janelas para operações de manutenção não planeadas/reativas que sejam urgentes ou críticas.
- Atualmente, as operações de manutenção são *consideradas apenas o melhor esforço* e não estão garantidas a ocorrer dentro de uma janela especificada.
- As atualizações não podem ser bloqueadas por mais de sete dias.

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Também precisa da versão de extensão Azure CLI *de pré-visualização aks* 0.5.4 ou posterior. Instale a extensão Azure CLI *de pré-visualização aks* utilizando o comando [de adicionar extensão az.][az-extension-add] Ou instale quaisquer atualizações disponíveis utilizando o comando [de atualização de extensão az.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Permitir a manutenção todas as segundas-feiras das 13:00 às 2:00

Para adicionar uma janela de manutenção, pode utilizar o `az aks maintenanceconfiguration add` comando.

> [!IMPORTANT]
> As janelas de manutenção planeadas são especificadas no Tempo Universal Coordenado (UTC).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

A saída de exemplo a seguir mostra a janela de manutenção das 01:00 às 2:00 todas as segundas-feiras.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

Para permitir a manutenção a qualquer momento durante um dia, omita o parâmetro *de hora de início.* Por exemplo, o seguinte comando define a janela de manutenção para o dia completo todas as segundas-feiras:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Adicione uma configuração de manutenção com um ficheiro JSON

Também pode utilizar um ficheiro JSON para criar uma janela de manutenção em vez de utilizar parâmetros. Criar um `test.json` ficheiro com os seguintes conteúdos:

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

O ficheiro JSON acima especifica janelas de manutenção todas as terças-feiras às 1:00 - 3:00 da manhã e todas as quartas-feiras às 1:00 - 2:00 e às 6:00 - 7:00 da manhã. Há também uma exceção de *2021-05-26T03:00:00Z* para *2021-05-30T12:00:00Z* onde a manutenção não é permitida mesmo que se sobreponha a uma janela de manutenção. O seguinte comando adiciona as janelas de manutenção de `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Atualizar uma janela de manutenção existente

Para atualizar uma configuração de manutenção existente, utilize o `az aks maintenanceconfiguration update` comando.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Listar todas as janelas de manutenção num cluster existente

Para ver todas as janelas de configuração de manutenção corrente no seu Cluster AKS, utilize o `az aks maintenanceconfiguration list` comando.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

Na saída abaixo, pode ver que existem duas janelas de manutenção configuradas para o myAKSCluster. Uma janela é às segundas-feiras à 1:00 da manhã e outra janela é na sexta-feira às 4:00 da manhã.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Mostrar uma janela de configuração de manutenção específica num cluster AKS

Para ver uma janela de configuração de manutenção específica no seu Cluster AKS, utilize o `az aks maintenanceconfiguration show` comando.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

A saída de exemplo a seguir mostra a janela de manutenção para *o padrão:*

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Eliminar uma determinada janela de configuração de manutenção num cluster AKS existente

Para eliminar uma determinada janela de configuração de manutenção no seu Cluster AKS, utilize o `az aks maintenanceconfiguration delete` comando.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Passos seguintes

- Para começar com a atualização do seu cluster AKS, consulte [upgrade de um cluster AKS][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[aks-upgrade]: upgrade-cluster.md
