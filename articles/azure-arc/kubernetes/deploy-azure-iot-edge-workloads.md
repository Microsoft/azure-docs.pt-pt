---
title: Implementar cargas de trabalho de borda Azure IoT (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Implementar cargas de trabalho azure IoT Edge
keywords: Kubernetes, Arc, Azure, K8s, contentores
ms.openlocfilehash: d82c93783d80060bc3443131191b7cec32dc4878
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680765"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Implementar cargas de trabalho de borda Azure IoT (Pré-visualização)

## <a name="overview"></a>Descrição geral

Azure Arc e Azure IoT Edge complementam muito bem as capacidades um do outro. O Azure Arc fornece mecanismos para os operadores de cluster configurarem os componentes fundamentais de um cluster, bem como aplicar e aplicar políticas de cluster. E o IoT Edge permite que os operadores de aplicações implementem e gerem remotamente as cargas de trabalho em escala com ingestão de nuvens conveniente e primitivos de comunicação bidirecionais. O diagrama abaixo ilustra isto:

![Configuração do Arco IoT](./media/edge-arc.png)

## <a name="pre-requisites"></a>Pré-requisitos

* [Registe um dispositivo IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart-linux#register-an-iot-edge-device) e implante o módulo de sensor de [temperatura simulado](https://docs.microsoft.com/azure/iot-edge/quickstart-linux#deploy-a-module). Certifique-se de que nota a cadeia de ligação do dispositivo.

* Utilize [o suporte da IoT Edge para que a Kubernetes](https://aka.ms/edgek8sdoc) o implemente através do operador de fluxo da Azure Arc.

* Descarregue o ficheiro [**values.yaml**](https://github.com/Azure/iotedge/blob/master/kubernetes/charts/edge-kubernetes/values.yaml) para ioT Edge Helm chart e substitua o espaço reservado **Do dispositivoConnectionString** no final do ficheiro com o que foi anotado no Passo 1. Pode definir quaisquer outras opções de instalação de gráficos suportados, conforme necessário. Crie um espaço de nome para a carga de trabalho ioT Edge e crie adicionar um segredo nele:

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    Também pode configurar isto remotamente utilizando o exemplo de [config](./use-gitops-connected-cluster.md)cluster .

## <a name="connect-a-cluster"></a>Ligar um cluster

Utilize a `az` extensão CLI `connectedk8s` para ligar um cluster Kubernetes ao Arco Azure:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Criar uma configuração para IoT Edge

Exemplo repo:https://github.com/veyalla/edgearc

Este repo aponta para o gráfico IoT Edge Helm e refere o segredo criado na secção de pré-requisitos.

1. Utilize a `az` extensão CLI `k8sconfiguration` para criar uma configuração para ligar o cluster ligado ao repo git:

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    Dentro de um minuto ou dois, deve ver os módulos de carga de trabalho IoT Edge implantados no espaço de `iotedge` nome no seu cluster. Pode ver os registos da `SimulatedTemperatureSensor` cápsula nesse espaço de nome para ver os valores da amostra a serem gerados. Também pode ver as mensagens chegarem ao seu hub IoT utilizando a [extensão de Toolkit Azure IoT Hub para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Limpeza

Pode remover a configuração utilizando:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Passos seguintes

[Use a Política Azure para governar a configuração do cluster](./use-azure-policy.md)
