---
title: Implementar cargas de trabalho de borda IoTure Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Implementar cargas de trabalho de borda IoTure Azure
keywords: Kubernetes, Arc, Azure, K8s, contentores
ms.openlocfilehash: e77446170e5a6adac995394d66640fd183f453b8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121733"
---
# <a name="deploy-azure-iot-edge-workloads"></a>Implementar cargas de trabalho de borda IoTure Azure

## <a name="overview"></a>Descrição Geral

Azure Arc e Azure IoT Edge complementam facilmente as capacidades uns dos outros. 

O Azure Arc fornece mecanismos para que os operadores de clusters configurem os componentes fundamentais de um cluster e apliquem e apliquem políticas de cluster. 

O Azure IoT Edge permite que os operadores de aplicações implementem e gerem remotamente as cargas de trabalho em escala com convenientes produtos de ingestão de nuvens e primitivos de comunicação bidirecional. 

O diagrama abaixo ilustra a relação de Azure Arc e Azure IoT Edge:

![Configuração do Arco IoT](./media/edge-arc.png)

## <a name="pre-requisites"></a>Pré-requisitos

* [Registe um dispositivo IoT Edge](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) e [implante o módulo de sensor de temperatura simulado](../../iot-edge/quickstart-linux.md#deploy-a-module). Note a cadeia de ligação do dispositivo para os *valores.yaml* mencionados abaixo.

* Utilize o [suporte da IoT Edge para a Kubernetes](https://aka.ms/edgek8sdoc) para o implementar através do operador de fluxo de Azure Arc.

* Descarregue o ficheiro [*values.yaml*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) para ioT Edge Helm chart e substitua o `deviceConnectionString` espaço reservado no final do ficheiro com a cadeia de ligação que observou anteriormente. Desagram quaisquer outras opções de instalação de gráficos suportados conforme necessário. Crie um espaço de nome para a carga de trabalho IoT Edge e gere um segredo nele:

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  Também pode configurar remotamente usando o [exemplo de configuração](./tutorial-use-gitops-connected-cluster.md)do cluster .

## <a name="connect-a-cluster"></a>Ligar um cluster

Utilize a `az` extensão Azure CLI `connectedk8s` para ligar um cluster Kubernetes ao Arco de Azure:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Criar uma configuração para IoT Edge

O [exemplo git repo](https://github.com/veyalla/edgearc) aponta para o gráfico IoT Edge Helm e faz referência ao segredo criado na secção pré-requisitos.

Utilize a `az` extensão Azure CLI `k8s-configuration` para criar uma configuração que ligue o cluster ligado ao repo Git:

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

Em poucos minutos, deverá ver os módulos de carga de trabalho IoT Edge implantados no espaço de nome do seu `iotedge` cluster. 

Veja os registos de `SimulatedTemperatureSensor` pod nesse espaço de nome para ver os valores da amostra a serem gerados. Também pode ver as mensagens chegarem ao seu hub IoT utilizando a [extensão Azure IoT Hub Toolkit para Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Limpeza

Remover a configuração utilizando:

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar a [Política Azure para governar a configuração do cluster](./use-azure-policy.md).
