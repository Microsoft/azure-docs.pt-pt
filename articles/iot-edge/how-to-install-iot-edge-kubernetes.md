---
title: Como instalar o IoT Edge no kubernetes | Microsoft Docs
description: Saiba como instalar o IoT Edge no kubernetes usando um ambiente de cluster de desenvolvimento local
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d11d23cf7d96482028a9d3738196fc5a787fec91
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510214"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Como instalar o IoT Edge no kubernetes (versão prévia)

IoT Edge pode integrar com o kubernetes usando-o como uma camada de infraestrutura resiliente e altamente disponível. Ele registra um IoT Edge CRD ( *definição de recurso personalizado* ) com o servidor de API do kubernetes. Além disso, ele fornece um *operador* (agente de IOT Edge) que reconcilia o estado desejado gerenciado pela nuvem com o estado do cluster local.

O tempo de vida do módulo é gerenciado pelo Agendador kubernetes, que mantém a disponibilidade do módulo e escolhe seu posicionamento. IoT Edge gerencia a plataforma de aplicativo de borda em execução na parte superior, reconciliando continuamente o estado desejado especificado no Hub IoT com o estado no cluster de borda. O modelo de aplicativo do Edge ainda é o conhecido modelo com base em módulos e rotas IoT Edge. O operador agente IoT Edge executa a conversão *automática* para as construções nativas do kubernetes, como pods, implantações, serviços etc.

Este é um diagrama de arquitetura de alto nível:

![arquear para kubernetes](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Cada componente da implantação de borda tem como escopo um namespace kubernetes específico para o dispositivo, tornando possível compartilhar os mesmos recursos de cluster entre vários dispositivos de borda e suas implantações.

>[!NOTE]
>IoT Edge em kubernetes está em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Instalar localmente para um ambiente de teste rápido

### <a name="prerequisites"></a>Pré-requisitos

* Kubernetes 1,10 ou mais recente. Se você não tiver uma configuração de cluster existente, poderá usar o [Minikube](https://kubernetes.io/docs/setup/minikube/) para um ambiente de cluster local.

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), o Gerenciador de pacotes do kubernetes.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para exibir e interagir com o cluster.

### <a name="setup-steps"></a>Passos de configuração

1. Iniciar **Minikube**

    ``` shell
    minikube start
    ```

1. Inicializar o componente do servidor do **Helm** (*gaveta*) no cluster

    ``` shell
    helm init
    ```

1. Adicionar o repositório de IoT Edge e atualizar a instalação do Helm

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Crie um hub IOT](../iot-hub/iot-hub-create-through-portal.md), [registre um dispositivo IOT Edge](how-to-register-device.md)e anote sua cadeia de conexão.

1. Instalar o iotedged e o agente de IoT Edge no cluster

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```

1. Abra o painel do kubernetes no navegador

    ```shell
    minikube dashboard
    ```

    Nos namespaces do cluster, você verá um para o dispositivo IoT Edge após a Convenção *msiot-\<iothub-name >-\<edgedevice-name >* . O agente de IoT Edge e o iotedged pods devem estar em funcionamento neste namespace.

1. Adicione um módulo de sensor de temperatura simulado usando as etapas na seção [implantar um módulo](quickstart-linux.md#deploy-a-module) do guia de início rápido. IoT Edge gerenciamento de módulo é feito no portal do Hub IoT, assim como qualquer outro dispositivo IoT Edge. Não é recomendável fazer alterações locais na configuração de módulo por meio de ferramentas kubernetes, pois elas podem ser substituídas.

1. Em alguns segundos, a atualização da página **pods** no namespace do dispositivo de borda no painel listará o hub de IOT Edge e os pods de sensor simulados como sendo executados com o pod de hub de IOT Edge ingerindo dados no Hub IOT.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos criados pela implantação de borda, use o comando a seguir com o nome usado na etapa 5 da seção anterior.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Passos seguintes

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Implantar como um gateway de borda altamente disponível

O dispositivo de borda em um cluster kubernetes pode ser usado como um gateway IoT para dispositivos downstream. Ele pode ser configurado para ser resiliente à falha de nó, fornecendo alta disponibilidade a implantações de borda. Consulte este [passo a passos detalhado](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) para usar IOT Edge neste cenário.
