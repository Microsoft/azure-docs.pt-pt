---
title: Monitorize o seu dispositivo Azure Stack Edge Pro através do painel de instrumentos Kubernetes | Microsoft Docs
description: Descreve como aceder e utilizar o dashboard Kubernetes para monitorizar o seu dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 19b3595228c29814e42af88696972fd81b156190
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443051"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>Utilize o painel Kubernetes para monitorizar o seu dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como aceder e usar o dashboard Kubernetes para monitorizar o seu dispositivo GPU Azure Stack Edge Pro. Para monitorizar o seu dispositivo, pode utilizar gráficos no portal Azure, ver o dashboard de Kubernetes ou executar `kubectl` comandos através da interface PowerShell do dispositivo. 

Este artigo centra-se apenas nas tarefas de monitorização que podem ser executadas no painel de instrumentos de Kubernetes.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Aceda ao dashboard Kubernetes no seu dispositivo
> * Ver módulos implantados no seu dispositivo
> * Obtenha endereço IP para aplicações implementadas no seu dispositivo
> * Ver registos de contentores para módulos implantados no seu dispositivo


## <a name="about-kubernetes-dashboard"></a>Sobre o Painel de Kubernetes

O Dashboard do Kubernetes é uma interface de utilizador baseada na Web que pode utilizar para resolver problemas das aplicações em contentores. Kubernetes Dashboard é uma alternativa baseada na UI para a linha de comando Kubernetes. `kubectl` Para obter mais informações, veja [Dashboard do Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

No seu dispositivo Azure Stack Edge Pro, pode utilizar o Painel Kubernetes no modo *apenas de leitura* para obter uma visão geral das aplicações em execução no seu dispositivo Azure Stack Edge Pro, ver o estado dos recursos do cluster Kubernetes e ver quaisquer erros que tenham ocorrido no dispositivo.

## <a name="access-dashboard"></a>Painel de acesso

O Painel kubernetes é *apenas de leitura* e funciona no nó mestre Kubernetes na porta 31000. Siga estes passos para aceder ao painel de instrumentos: 

1. Na UI local do seu dispositivo, vá ao **Dispositivo** e, em seguida, vá para **os pontos finais do Dispositivo**. 
1. **Selecione Baixar config** para descarregar um `kubeconfig` que lhe permite aceder ao painel de instrumentos. Guarde o `config.json` ficheiro no seu sistema local.
1. Selecione o URL do painel de instrumentos Kubernetes para abrir o painel de instrumentos num browser.

    ![KUBernetes Dashboard URL na página do dispositivo na UI local](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. No sinal do **Painel de Instrumentos de Kubernetes na** página:
    
    1. **Selecione kubeconfig**. 
        ![Selecione a opção kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png) 
    1. Selecione a elipse... . Procure e aponte para o `kubeconfig` que descarregou anteriormente no seu sistema local. Selecione **Iniciar sessão**.
        ![Navegue para o ficheiro kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. Agora pode ver o Painel Kubernetes para o seu dispositivo Azure Stack Edge Pro no modo apenas de leitura.

    ![Página principal do Painel de Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Ver estado do módulo

Os módulos computacional são contentores que têm uma lógica de negócio implementada. Pode utilizar o painel de instrumentos para verificar se um módulo computacional foi implantado com sucesso no seu dispositivo Azure Stack Edge Pro.

Para ver o estado do módulo, siga estes passos no painel de instrumentos:

1. No painel esquerdo do tablier, vá ao **Namespace.** Filtrar pelo espaço de nomes onde os módulos IoT Edge são apresentados, neste caso, **iotedge**.
1. No painel esquerdo, vá a **Workloads > Deployments**.
1. No painel direito, verá todos os módulos implantados no seu dispositivo. Neste caso, um módulo GettingStartedWithGPU foi implantado no Azure Stack Edge Pro. Pode ver que o módulo foi implantado.

    ![Ver implantação do módulo](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Obtenha endereço IP para serviços ou módulos

Pode utilizar o dashboard para obter os endereços IP dos serviços ou módulos que pretende expor fora do cluster Kubernetes. 

Atribui a gama IP para estes serviços externos através da UI web local do dispositivo na página de definições da **rede Compute.** Depois de ter implantado módulos IoT Edge, é melhor que o endereço IP seja atribuído a um módulo ou serviço específico. 

Para obter o endereço IP, siga estes passos no painel de instrumentos:

1. No painel esquerdo do tablier, vá ao **Namespace.** Filtrar pelo espaço de nome onde é implantado um serviço externo, neste caso, **iotedge**.
1. No painel esquerdo, vá para **o Discovery and Load balancendo > Services.**
1. No painel direito, você verá todos os serviços que estão executando no espaço de `iotedge` nome no seu dispositivo Azure Stack Edge Pro.

    ![Obtenha IP para serviços externos](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Ver registos de contentor

Há casos em que é preciso ver os registos dos contentores. Pode utilizar o painel de instrumentos para obter registos para um recipiente específico que colocou no seu cluster Kubernetes.

Para ver os registos do contentor, siga estes passos no painel de instrumentos:

1. No painel esquerdo do tablier, vá ao **Namespace.** Filtrar pelo espaço de nomes onde os módulos IoT Edge são implantados, neste caso, **iotedge**.
1. No painel esquerdo, vá a **Workloads > Pods.**
1. No painel direito, verá todas as cápsulas a funcionar no seu dispositivo. Identifique a cápsula que está a executar o módulo para o qual pretende visualizar os registos. Selecione a elipse vertical para o pod que identificou e a partir do menu de contexto, selecione **Logs**.

    ![Ver registos de contentores 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. Os registos são apresentados num espectador de registos que é incorporado no painel de instrumentos. Também pode baixar os registos.

    ![Ver registos de contentores 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>Ver CPU, utilização da memória

O dashboard Kubernetes para o dispositivo Azure Stack Edge Pro também tem um [addon de servidor métrico](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/) que agrega o CPU e o uso da memória através dos recursos de Kubernetes.
 
Por exemplo, pode ver o CPU e a memória consumidas através de implementações em todos os espaços de nome. 

![Ver CPU e utilização da memória em todas as implementações](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

Também pode ser filtrado por um espaço de nome específico. No exemplo seguinte, pode ver o CPU e o consumo de memória apenas para implementações do Arco Azure.  

![Ver CPU e utilização da memória para implementações do Arco Azure](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

O servidor de métricas Kubernetes fornece gasodutos de autoescalagem, tais como [O Autoscaler horizontal do pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).


## <a name="next-steps"></a>Passos seguintes

Saiba como [resolver problemas com o dispositivo](azure-stack-edge-gpu-troubleshoot.md).
