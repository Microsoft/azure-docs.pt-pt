---
title: Compreenda a gestão da carga de trabalho da Kubernetes no dispositivo Azure Stack Edge Microsoft Docs
description: Descreve como as cargas de trabalho de Kubernetes podem ser geridas no seu dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: 21845b51fdd108221d5e1bce50e953b79084d17d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085359"
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

## <a name="namespaces-types"></a>Tipos de espaços de nome

Os recursos de Kubernetes, tais como cápsulas e implementações, são logicamente agrupados num espaço de nome. Estes agrupamentos fornecem uma forma de dividir logicamente um cluster Kubernetes e restringir o acesso para criar, ver ou gerir recursos. Os utilizadores só podem interagir com recursos dentro dos espaços de nome atribuídos.

Os espaços de nome destinam-se a ser utilizados em ambientes com muitos utilizadores espalhados por várias equipas ou projetos. Para clusters com alguns a dezenas de utilizadores, você não deve precisar criar ou pensar em espaços de nome. Comece a usar espaços de nome quando precisar das funcionalidades que fornecem.

Para mais informações, consulte [os espaços de nomes de Kubernetes.](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)


O seu dispositivo Azure Stack Edge tem os seguintes espaços de nome:

- **Espaço de nome do sistema** - Este espaço de nome é onde existem recursos fundamentais, tais como funcionalidades de rede como DNS e proxy, ou o dashboard Kubernetes. Normalmente, não se implanta as suas próprias aplicações neste espaço de nome. Use este espaço de nome para depurar quaisquer problemas de cluster Kubernetes. 

    Existem vários espaços de nome do sistema no seu dispositivo e os nomes correspondentes a estes espaços de nome do sistema são reservados. Aqui está uma lista dos espaços reservados para o sistema: 
    - kube-sistema
    - metallb-sistema
    - espaço dbe-nome
    - predefinição
    - kubernetes-dashboard
    - predefinição
    - kube-node-arrendamento
    - kube-público
    - iotedge
    - azul-arco

    Certifique-se de que não utiliza nomes reservados para espaços com nomes de utilizador que criar. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Espaço com nomes de utilizador** - Estes são os espaços de nome que pode criar através **de kubectl** para implementar aplicações locais.
 
- **IoT Edge namespace** - Você conecta-se a este `iotedge` espaço de nome para implementar aplicações via IoT Edge.

- **Azure Arc namespace** - Você conecta-se a este `azure-arc` espaço de nome para implementar aplicações através do Arco Azure.

 
## <a name="deployment-types"></a>Tipos de implantação

Há três formas primárias de implantar as suas cargas de trabalho. Cada uma destas metodologias de implementação permite-lhe ligar-se a um espaço de nome distinto no dispositivo e, em seguida, implementar aplicações apátridas ou imponentes.

![Implantação da carga de trabalho de Kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Implantação local**: Isto é através de uma ferramenta de acesso à linha de comando, tal como `kubectl` permite-lhe implantar o K8 `yamls` . Liga-se ao cluster K8 no seu Azure Stack Edge que cria utilizando o `kubeconfig` ficheiro. Para mais informações, aceda a [um cluster Kubernetes via kubectl.](azure-stack-edge-gpu-create-kubernetes-cluster.md)

- **Implementação IoT Edge**: Isto é através do IoT Edge, que se conecta ao Azure IoT Hub. Ligue-se ao cluster K8 no seu dispositivo Azure Stack Edge através do `iotedge` espaço de nomes. Os agentes IoT Edge implantados neste espaço de nome são responsáveis pela conectividade com o Azure. Aplica a `IoT Edge deployment.json` configuração utilizando Azure DevOps CI/CD. A gestão namespace e IoT Edge é feita através do operador de nuvem.

- **Implantação Azure/Arc**: Azure Arc é uma ferramenta de gestão híbrida que lhe permitirá implementar aplicações nos seus clusters K8. Ligue o cluster K8 ao seu dispositivo Azure Stack Edge através do `azure-arc namespace` .  Os agentes são implantados neste espaço de nome que são responsáveis pela conectividade com o Azure. Aplica-se a configuração de implementação utilizando a gestão de configuração baseada em GitOps. O Azure Arc também lhe permitirá utilizar o Azure Monitor para que os recipientes vejam e monitorizem os seus aglomerados. Para mais informações, vá ao [Que é o Azure-Arc habilitado a Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)

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
