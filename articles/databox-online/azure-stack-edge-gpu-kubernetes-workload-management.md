---
title: Compreenda a gestão da carga de trabalho da Kubernetes no dispositivo Azure Stack Edge Microsoft Docs
description: Descreve como as cargas de trabalho de Kubernetes podem ser geridas no seu dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 38c46bdcce64f726b3a7ddf74e0cfd10a14ba663
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268040"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-device"></a>Gestão da carga de trabalho da Kubernetes no seu dispositivo Azure Stack Edge

No seu dispositivo Azure Stack Edge, é criado um cluster Kubernetes quando configura o papel de computação. Uma vez criado o cluster Kubernetes, as aplicações contentorizadas podem ser implantadas no cluster Kubernetes em Pods. Existem formas distintas de implementar cargas de trabalho no seu cluster Kubernetes. 

Este artigo descreve os vários métodos que podem ser usados para implementar cargas de trabalho no seu dispositivo Azure Stack Edge.

## <a name="workload-types"></a>Tipos de carga de trabalho

Os dois tipos comuns de carga de trabalho que pode implementar no seu dispositivo Azure Stack Edge são aplicações apátridas ou aplicações imponentes.

- **As aplicações apátridas** não preservam o seu estado e não guardam dados para o armazenamento persistente. Todos os dados do utilizador e da sessão ficam com o cliente. Alguns exemplos de aplicações apátridas incluem frontends web como Nginx, e outras aplicações web.

    Pode criar uma Implantação de Kubernetes para implementar uma aplicação apátrida no seu cluster. 

- **Aplicações imponentes** exigem que o seu estado seja salvo. Aplicações imponentes utilizam armazenamento persistente, como volumes persistentes, para guardar dados para utilização pelo servidor ou por outros utilizadores. Exemplos de aplicações estatais incluem bases de dados como o MongoDB.

    Pode criar uma Implantação de Kubernetes para implementar uma aplicação imponente. 

## <a name="deployment-flow"></a>Fluxo de implantação

Para implementar aplicações num dispositivo Azure Stack Edge, seguirá estes passos: 
 
1. **Acesso de configuração**: Em primeiro lugar, utilizará o espaço de funcionamento PowerShell para criar um utilizador, criar um espaço de nome e conceder o acesso do utilizador a esse espaço de nome.
2. **Armazenamento de configuração**: Em seguida, utilizará o recurso Azure Stack Edge no portal Azure para criar volumes persistentes utilizando provisão estática ou dinâmica para as aplicações imponentes que irá implementar.
3. **Configurar a rede**: Por fim, utilizará os serviços para expor aplicações externamente e dentro do cluster Kubernetes.
 
## <a name="deployment-types"></a>Tipos de implantação

Há três formas primárias de implantar as suas cargas de trabalho. Cada uma destas metodologias de implementação permite-lhe ligar-se a um espaço de nome distinto no dispositivo e, em seguida, implementar aplicações apátridas ou imponentes.

![Implantação da carga de trabalho de Kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Implementação local**: Isto é através de uma ferramenta de acesso à linha de comando, tal como `kubectl` permite-lhe implantar Kubernetes `yamls` . Liga-se ao cluster Kubernetes no seu Azure Stack Edge que cria utilizando o `kubeconfig` ficheiro. Para mais informações, aceda a [um cluster Kubernetes via kubectl.](azure-stack-edge-gpu-create-kubernetes-cluster.md)

- **Implementação IoT Edge**: Isto é através do IoT Edge, que se conecta ao Azure IoT Hub. Liga-se ao cluster Kubernetes no seu dispositivo Azure Stack Edge através do `iotedge` espaço de nomes. Os agentes IoT Edge implantados neste espaço de nome são responsáveis pela conectividade com o Azure. Aplica a `IoT Edge deployment.json` configuração utilizando Azure DevOps CI/CD. A gestão namespace e IoT Edge é feita através do operador de nuvem.

- **Implantação Azure/Arc**: Azure Arc é uma ferramenta de gestão híbrida que lhe permitirá implementar aplicações nos seus clusters Kubernetes. Ligue o cluster Kubernetes ao seu dispositivo Azure Stack Edge através do `azure-arc namespace` . Os agentes são implantados neste espaço de nome que são responsáveis pela conectividade com o Azure. Aplica-se a configuração de implementação utilizando a gestão de configuração baseada em GitOps. O Azure Arc também lhe permitirá utilizar o Azure Monitor para que os recipientes vejam e monitorizem os seus aglomerados. Para mais informações, vá ao [Que é o Azure-Arc habilitado a Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)

## <a name="choose-the-deployment-type"></a>Escolha o tipo de implementação

Ao implementar aplicações, considere as seguintes informações:

- **Tipos simples ou múltiplos**: Pode escolher uma única opção de implantação ou uma mistura de diferentes opções de implementação.
- **Cloud versus local**: Dependendo das suas aplicações, pode escolher a implementação local através de kubectl ou cloud deployment via IoT Edge e Azure Arc. 
    - A implantação local é mais adequada para cenários de desenvolvimento. Quando escolhe uma implementação local, está restrito à rede em que o seu dispositivo Azure Stack Edge está implantado.
    - Se tiver um agente de nuvem que possa implementar, deve implantar o seu operador de nuvem e utilizar a gestão da nuvem.
- **IoT vs Azure Arc**: A escolha da implementação também depende da intenção do seu cenário de produto. Se estiver a implementar aplicações ou contentores que tenham uma integração mais profunda com o ecossistema IoT ou IoT, então deve escolher a forma de implantação de aplicações IoT Edge. Se tiver as implementações de Kubernetes existentes, a Azure Arc seria a escolha preferida.


## <a name="next-steps"></a>Passos seguintes

Para implementar localmente uma aplicação via kubectl, consulte:

- [Implemente uma aplicação apátrida no seu Azure Stack Edge via kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Para implementar uma aplicação via IoT Edge, consulte:

- [Coloque um módulo de amostra no seu Azure Stack Edge via IoT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Para implementar uma aplicação via Azure Arc, consulte:

- [Implementar uma aplicação utilizando o Arco Azure](azure-stack-edge-gpu-deploy-sample-module.md).
