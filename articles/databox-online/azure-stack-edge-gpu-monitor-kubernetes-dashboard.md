---
title: Monitorize o seu dispositivo Azure Stack Edge através do painel Kubernetes Microsoft Docs
description: Descreve como aceder e utilizar o dashboard Kubernetes para monitorizar o seu dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: 7274cef73bff3fb87d55ad636ff0167c8a064796
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180682"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-gpu-device"></a>Utilize o painel Kubernetes para monitorizar o seu dispositivo GPU Azure Stack Edge

Este artigo descreve como aceder e usar o dashboard Kubernetes para monitorizar o seu dispositivo GPU Azure Stack Edge. Para monitorizar o seu dispositivo, pode utilizar gráficos no portal Azure, ver o dashboard de Kubernetes ou executar `kubectl` comandos através da interface PowerShell do dispositivo. 

Este artigo centra-se apenas nas tarefas de monitorização que podem ser executadas no painel de instrumentos de Kubernetes.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Aceda ao dashboard Kubernetes no seu dispositivo
> * Download `aseuser` config
> * Ver módulos implantados no seu dispositivo
> * Obtenha endereço IP para aplicações implementadas no seu dispositivo
> * Ver registos de contentores para módulos implantados no seu dispositivo


## <a name="about-kubernetes-dashboard"></a>Sobre o Painel de Kubernetes

O Kubernetes Dashboard é uma interface de utilizador baseada na Web que pode utilizar para resolver problemas nas suas aplicações contentorizadas. Kubernetes Dashboard é uma alternativa baseada na UI para a linha de comando Kubernetes. `kubectl` Para mais informações, consulte [o Painel de Instrumentos de Kubernetes.](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

No seu dispositivo Azure Stack Edge, pode utilizar o Painel DeResísta Kubernetes no modo *apenas de leitura* para obter uma visão geral das aplicações em execução no seu dispositivo Azure Stack Edge, visualizar o estado dos recursos do cluster Kubernetes e ver quaisquer erros que tenham ocorrido no dispositivo.

## <a name="access-dashboard"></a>Painel de acesso

O Painel kubernetes é *apenas de leitura* e funciona no nó mestre Kubernetes na porta 31000. Siga estes passos para aceder ao painel de instrumentos: 

1. Na UI local do seu dispositivo, vá ao **Dispositivo** e, em seguida, vá para **os pontos finais do Dispositivo**. Selecione o URL do painel de instrumentos Kubernetes para abrir o painel de instrumentos num browser.

    ![KUBernetes Dashboard URL na página do dispositivo na UI local](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. No sinal do **Painel de Instrumentos de Kubernetes na** página, selecione **token**. 
1. Providencie um símbolo. 
    1. Para obter o token, [ligue-se através da interface PowerShell do seu dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).
    1. Executar o comando:  `Get-HcsKubernetesDashboardToken`
    
    1. Copie a corda simbólica que lhe foi apresentada no pedido. Eis uma saída de exemplo:
        
        ```powershell
        [10.100.10.10]: PS>Get-HcsKubernetesDashboardToken
        eyJhbGciOiJSUzI1NiIsImtpZCI6IkpFTEtBYTMyZ0Ezb01OYTVFSnVaUV85OWtLdXNETTZQR0k0UlFybGdReFUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi03czZ6ayIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU3NzY3ZDAzLTJlYWUtNDlkMi1hNDEyLTNkOTU3MDFiMThiMyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.UgNrpVYVJBEaWxFlljuENUQQmzFXMYG2VsJUIYFdp2AO20zX0k5dRvwcCpeGlqSKb9MyYjG0c6RmT9uCOZk-vAwt7btszQLD7KPCwh_nn_NiIyO8ApgGRYZP8NuP8CBTX3tl_hpwfHtZ0ksbuKAduIL-0uPF0rG5wgLk9cTEw6fKSc2UZW6bIzhNSp_uSiP6MexOS6OftF9JFZejkIGd33dSp-k-tgFlm2Zy96sdFJC0q-XsH7jygiVnfxA9XMs5wqW26LkCh0rfO2WI3C1XFK-4TpufRZLJHo5WPlu-Tnsxa8xmtk2jQ3us-sXcBRrvhPNPrNKkbqc9hbjmWfGD0Q
        [10.100.10.10]: PS>
        ```
        
1. Selecione **Iniciar sessão**.

    ![Inscreva-se no Painel de Instrumentos de Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png)

6. Agora pode ver o Painel Kubernetes para o seu dispositivo Azure Stack Edge no modo apenas de leitura.

    ![Página principal do Painel de Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Ver estado do módulo

Os módulos computacional são contentores que têm uma lógica de negócio implementada. Pode utilizar o painel de instrumentos para verificar se um módulo computacional foi implantado com sucesso no seu dispositivo Azure Stack Edge.

Para ver o estado do módulo, siga estes passos no painel de instrumentos:

1. No painel esquerdo do tablier, vá ao **Namespace.** Filtrar pelo espaço de nomes onde os módulos IoT Edge são apresentados, neste caso, **iotedge**.
1. No painel esquerdo, vá a **Workloads > Deployments**.
1. No painel direito, verá todos os módulos implantados no seu dispositivo. Neste caso, foi implantado um módulo GettingStartedWithGPU no Azure Stack Edge. Pode ver que o módulo foi implantado.

    ![Ver implantação do módulo](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Obtenha endereço IP para serviços ou módulos

Pode utilizar o dashboard para obter os endereços IP dos serviços ou módulos que pretende expor fora do cluster Kubernetes. 

Atribui a gama IP para estes serviços externos através da UI web local do dispositivo na página de definições da **rede Compute.** Depois de ter implantado módulos IoT Edge, é melhor que o endereço IP seja atribuído a um módulo ou serviço específico. 

Para obter o endereço IP, siga estes passos no painel de instrumentos:

1. No painel esquerdo do tablier, vá ao **Namespace.** Filtrar pelo espaço de nome onde é implantado um serviço externo, neste caso, **iotedge**.
1. No painel esquerdo, vá para **o Discovery and Load balancendo > Services.**
1. No painel direito, você verá todos os serviços que estão executando no espaço de `iotedge` nome no seu dispositivo Azure Stack Edge.

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
    

## <a name="next-steps"></a>Passos seguintes

Saiba como resolver problemas com kubernetes <!--insert link-->.
