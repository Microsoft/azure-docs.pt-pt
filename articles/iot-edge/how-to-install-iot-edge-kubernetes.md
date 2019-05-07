---
title: Como instalar o IoT Edge no Kubernetes | Documentos da Microsoft
description: Saiba mais sobre como instalar o IoT Edge no Kubernetes com um ambiente de cluster de desenvolvimento local
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160699"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Como instalar o IoT Edge no Kubernetes (pré-visualização)

IoT Edge pode ser integrado com o Kubernetes utilizando-o como uma camada de infraestrutura resilientes, altamente disponíveis. Ele registra um IoT Edge *definição de recurso personalizado* (CRD) com o servidor de API do Kubernetes. Além disso, fornece uma *operador* (agente do IoT Edge) que reconcilia o estado pretendido na cloud gerido com o estado do local cluster. 

Duração de módulo é gerida pelo agendador do Kubernetes, o que mantém a disponibilidade do módulo e escolhe o posicionamento das mesmas. IoT Edge gerencia a plataforma de aplicações do edge em execução na parte superior, reconciliar continuamente o estado pretendido especificado no IoT Hub com o estado no cluster de borda. O modelo de aplicativo do edge ainda é o modelo familiar, com base em módulos do IoT Edge e as rotas. O operador de agente do IoT Edge efetua *automática* tradução para os Kubernetes natives construções como pods, implementações, serviços, etc.

Este é um diagrama de arquitetura de alto nível:

![arquitetura de kubernetes](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Todos os componentes da implementação do edge é confinado para um espaço de nomes de Kubernetes específico para o dispositivo, tornando possível compartilhar os mesmos recursos de cluster entre vários dispositivos de ponta e as respetivas implementações.

>[!NOTE]
>IoT Edge no Kubernetes está no [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Instalar localmente para um ambiente de teste rápida

### <a name="prerequisites"></a>Pré-requisitos

* Kubernetes 1.10 ou mais recente. Se não tiver uma configuração de cluster existentes, pode utilizar [Minikube](https://kubernetes.io/docs/setup/minikube/) para um ambiente de local cluster. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), o Gestor de pacotes do Kubernetes.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para ver e interagir com o cluster.

### <a name="setup-steps"></a>Passos de configuração

1. Iniciar **Minikube**

    ``` shell
    minikube start
    ```

1. Inicializar o **Helm** componente de servidor (*tiller*) no seu cluster

    ``` shell
    helm init
    ```

1. Adicionar repositório de IoT Edge e atualizar a instalação do helm

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Criar um IoT Hub](../iot-hub/iot-hub-create-through-portal.md), [registar um dispositivo IoT Edge](how-to-register-device-portal.md)e anote a cadeia de ligação.

1. Instalar iotedged e o agente do IoT Edge no seu cluster

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Abra o dashboard do Kubernetes no browser

    ```shell
    minikube dashboard
    ```

    Sob os espaços de nomes de cluster, verá um para o dispositivo ao IoT Edge seguir a Convenção *msiot -\<iothub-name >-\<edgedevice-name >*. Os pods de agente e iotedged do IoT Edge devem estar em execução neste espaço de nomes.

1. Adicionar um módulo de sensor de temperatura simulada com os passos a [implementar um módulo](quickstart-linux.md#deploy-a-module) secção do início rápido. Gestão de módulo do IoT Edge é feita a partir do portal do IoT Hub, assim como qualquer outro dispositivo de IoT Edge. Não é recomendada a fazer alterações de locais para a configuração do módulo por meio de ferramentas do Kubernetes, eles podem obter substituídos.

1. Em alguns segundos, atualizar o **Pods** pods de sensor simulado como em execução com o hub IoT Edge pod ingerir dados no IoT Hub e de página no espaço de nomes de dispositivo do edge no dashboard irá listar o hub IoT Edge.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos criados pela implementação do edge, utilize o seguinte comando com o nome utilizado no passo 5 da secção anterior.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Passos Seguintes

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Implementar como um gateway de elevada disponibilidade do edge 

O dispositivo de limite de um cluster de Kubernetes pode ser utilizado como um gateway de IoT para dispositivos downstream. Ele pode ser configurado para ser resiliente a falhas de nó, portanto, fornecer elevada disponibilidade para as implantações do edge. Ver isso [instruções detalhadas sobre](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) para utilizar o IoT Edge neste cenário.