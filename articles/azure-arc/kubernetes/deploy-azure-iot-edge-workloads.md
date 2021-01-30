---
title: Implementar cargas de trabalho de borda IoTure (pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Implementar cargas de trabalho de borda IoTure Azure
keywords: Kubernetes, Arc, Azure, K8s, contentores
ms.openlocfilehash: 88c480f93bfe28a424441a1c5857c623efb4e1d3
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091652"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Implementar cargas de trabalho de borda IoTure (pré-visualização)

## <a name="overview"></a>Descrição Geral

Azure Arc e Azure IoT Edge complementam muito bem as capacidades uns dos outros. O Azure Arc fornece mecanismos para os operadores de clusters configurarem os componentes fundamentais de um cluster, bem como aplicarem e imporem políticas de cluster. E o IoT Edge permite que os operadores de aplicações implementem e gerem remotamente as cargas de trabalho em escala com convenientes produtos de ingestão de nuvens e primitivos de comunicação bidirecional. O diagrama abaixo ilustra isto:

![Configuração do Arco IoT](./media/edge-arc.png)

## <a name="pre-requisites"></a>Pré-requisitos

* [Registe um dispositivo IoT Edge](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) e [implante o módulo de sensor de temperatura simulado](../../iot-edge/quickstart-linux.md#deploy-a-module). Certifique-se de que regista a ligação do dispositivo.

* Utilize o [suporte da IoT Edge para a Kubernetes](https://aka.ms/edgek8sdoc) para o implementar através do operador de fluxo de Azure Arc.

* Descarregue o ficheiro [**values.yaml**](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) para ioT Edge Helm chart e substitua o espaço reservado **para dispositivoConnectionString** no final do ficheiro com o indicado no Passo 1. Pode definir quaisquer outras opções de instalação de gráficos suportados conforme necessário. Crie um espaço de nome para a carga de trabalho IoT Edge e crie um segredo nele:

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    Também pode configurar isto remotamente usando o [exemplo de configuração](./use-gitops-connected-cluster.md)do cluster.

## <a name="connect-a-cluster"></a>Ligar um cluster

Utilize a `az` extensão CLI `connectedk8s` para ligar um cluster Kubernetes ao Arco de Azure:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Criar uma configuração para IoT Edge

Exemplo repo: https://github.com/veyalla/edgearc

Este repo aponta para o gráfico IoT Edge Helm e faz referência ao segredo criado na secção pré-requisitos.

1. Utilize a `az` extensão CLI `k8sconfiguration` para criar uma configuração para ligar o cluster ligado ao repo git:

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    Em um minuto ou dois, você deve ver os módulos de carga de carga IoT Edge implantados `iotedge` no espaço de nomes no seu cluster. Pode ver os registos da `SimulatedTemperatureSensor` cápsula nesse espaço de nome para ver os valores da amostra a serem gerados. Também pode ver as mensagens chegarem ao seu hub IoT utilizando a [extensão Azure IoT Hub Toolkit para Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Limpeza

Pode remover a configuração utilizando:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Passos seguintes

[Use a política do Azure para governar a configuração do cluster](./use-azure-policy.md)
