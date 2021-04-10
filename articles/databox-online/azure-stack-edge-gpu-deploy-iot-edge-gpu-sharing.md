---
title: Implementar carga de trabalho IoT Edge utilizando a partilha de GPU no dispositivo GPU Azure Stack Edge Pro
description: Descreve como pode implementar uma carga de trabalho partilhada pela GPU através do IoT Edge no seu dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565403"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Implemente uma carga de trabalho IoT Edge usando a partilha de GPU no seu Azure Stack Edge Pro

Este artigo descreve como as cargas de trabalho contentorizadas podem partilhar as GPUs no seu dispositivo GPU Azure Stack Edge Pro. A abordagem consiste em permitir o Serviço Multi-Processo (MPS) e, em seguida, especificar as cargas de trabalho da GPU através de uma implementação IoT Edge. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Você tem acesso a um dispositivo GPU Azure Stack Edge Pro que é [ativado](azure-stack-edge-gpu-deploy-activate.md) e tem [computo configurado](azure-stack-edge-gpu-deploy-configure-compute.md). Tem o ponto final da [API da Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) e adicionou este ponto final ao ficheiro do `hosts` seu cliente que irá aceder ao dispositivo.

1. Tem acesso a um sistema de clientes com um [sistema operativo suportado.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) Se utilizar um cliente Windows, o sistema deverá executar o PowerShell 5.0 ou mais tarde para aceder ao dispositivo.

1. Guarde a seguinte implantação `json` no seu sistema local. Utilizará informações deste ficheiro para executar a implementação do IoT Edge. Esta implantação baseia-se em [recipientes Simples CUDA](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) que estão disponíveis publicamente a partir da Nvidia. 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>Verifique o condutor da GPU, versão CUDA

O primeiro passo é verificar se o seu dispositivo está a funcionar com as versões necessárias para o condutor da GPU e para as versões CUDA.

1. [Ligue-se à interface PowerShell do seu dispositivo.](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)

1. Execute o seguinte comando:

    `Get-HcsGpuNvidiaSmi`

1. Na saída Nvidia smi, tome nota da versão GPU e da versão CUDA no seu dispositivo. Se estiver a executar o software Azure Stack Edge 2102, esta versão corresponderia às seguintes versões do controlador:

    - Versão do condutor da GPU: 460.32.03
    - Versão CUDA: 11.2
    
    Aqui está uma saída de exemplo:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>  
    ```

1. Mantenha esta sessão aberta pois irá usá-la para ver a saída da Nvidia smi ao longo do artigo.


## <a name="deploy-without-context-sharing"></a>Implementar sem partilha de contexto

Pode agora implementar uma aplicação no seu dispositivo quando o Serviço Multi-Processo não estiver em funcionamento e não existir partilha de contexto. A implementação é através do portal Azure no `iotedge` espaço de nomes que existe no seu dispositivo.

### <a name="create-user-in-iot-edge-namespace"></a>Criar utilizador no espaço de nomes IoT Edge

Primeiro irá criar um utilizador que irá ligar-se ao espaço de `iotedge` nomes. Os módulos IoT Edge são implantados no espaço de nomes iotedge. Para obter mais informações, consulte [os espaços de nomes de Kubernetes no seu dispositivo.](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types)

Siga estes passos para criar um utilizador e conceder ao utilizador o acesso ao `iotedge` espaço de nomes. 

1. [Ligue-se à interface PowerShell do seu dispositivo.](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)

1. Criar um novo utilizador no espaço de `iotedge` nomes. Execute o seguinte comando:

    `New-HcsKubernetesUser -UserName <user name>`

    Aqui está uma saída de exemplo:

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. Copie a saída apresentada em texto simples. Guarde a saída como ficheiro *config* (sem extensão) na `.kube` pasta do seu perfil de utilizador na sua máquina local, por exemplo, `C:\Users\<username>\.kube` . 

1. Conceda ao utilizador que criou, aceda ao espaço de `iotedge` nomes. Execute o seguinte comando:

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    Aqui está uma saída de exemplo:

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
Para obter instruções detalhadas, consulte [Connect to e gerencie um cluster Kubernetes através de kubectl no seu dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac).

### <a name="deploy-modules-via-portal"></a>Implementar módulos via portal

Implementar módulos IoT Edge através do portal Azure. Você vai implementar módulos de amostra Nvidia CUDA publicamente disponíveis que executam simulação n-body. Para obter mais informações, consulte [a simulação do corpo N.](https://physics.princeton.edu//~fpretori/Nbody/intro.htm)

1. Certifique-se de que o serviço IoT Edge está a funcionar no seu dispositivo.

    ![Serviço IoT Edge em execução.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. Selecione o azulejo IoT Edge no painel direito. Vá a **IoT Edge > Properties**. No painel direito, selecione o recurso IoT Hub associado ao seu dispositivo.

    ![Ver propriedades.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. No recurso IoT Hub, aceda à **Gestão Automática de Dispositivos > IoT Edge**. No painel direito, selecione o dispositivo IoT Edge associado ao seu dispositivo.

    ![Vai para ioT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. Selecione **Definir módulos**.

    ![Ir para definir módulos.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. **Selecione + Adicione > + módulo IoT Edge**.

    ![Adicione o módulo IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. No **separador Definições** do Módulo, forneça o nome do **módulo IoT Edge** e o **Image URI**. Definir **a política de puxar** a imagem para On **criar**.

    ![Definições do módulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. No separador **Variáveis Ambientais,** especifique **NVIDIA_VISIBLE_DEVICES** como **0**.

    ![Variáveis ambientais.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. No **separador Opções de Criação** de Recipientes, forneça as seguintes opções:

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    As opções são apresentadas da seguinte forma:

    ![O contentor cria opções.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    Selecione **Adicionar**.

1. O módulo que adicionou deve mostrar como **Running**. 

    ![Reveja e crie implementação.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. Repita todos os passos para adicionar um módulo que seguiu ao adicionar o primeiro módulo. Neste exemplo, forneça o nome do módulo como `cuda-sample2` . 

    ![Definições do módulo para o 2º módulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    Utilize a mesma variável ambiental que ambos os módulos partilharão a mesma GPU.

    ![Variável ambiental para o 2º módulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    Utilize o mesmo recipiente para criar opções que forneceu para o primeiro módulo e selecione **Adicionar**.

    ![O recipiente cria opções para os módulos 2.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. Na página **de módulos set,** selecione **'Rever + Criar'** e, em seguida, selecione **Criar**. 

    ![Reveja e crie a 2ª implantação.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. O **estado de funcionamento** de ambos os módulos deve agora mostrar como **Running**.  

    ![2º estado de implantação.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>Monitorizar a implementação da carga de trabalho

1. Abra uma nova sessão PowerShell.

1. Lista as cápsulas que estão a funcionar no espaço de `iotedge` nomes. Execute o seguinte comando:

    `kubectl get pods -n iotedge`   

    Aqui está uma saída de exemplo:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    Há duas cápsulas `cuda-sample1-97c494d7f-lnmns` e a funcionar no teu `cuda-sample2-d9f6c4688-2rld9` dispositivo.

1. Enquanto ambos os contentores estão executando a simulação n-body, veja a utilização da GPU a partir da saída Nvidia smi. Vá à interface PowerShell do dispositivo e corra `Get-HcsGpuNvidiaSmi` .

    Aqui está uma saída de exemplo quando ambos os recipientes estão executando a simulação n-corpo:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    Como podem ver, há dois contentores a funcionar com simulação n-body na GPU 0. Também pode ver a utilização correspondente da memória.

1. Uma vez concluída a simulação, a saída SMI da Nvidia mostrará que não existem processos em execução no dispositivo.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
 
1. Após a simulação n-corpo concluída, consulte os registos para entender os detalhes da implantação e o tempo necessário para que a simulação esteja concluída. 

    Aqui está um exemplo de saída do primeiro recipiente:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    Aqui está um exemplo de saída do segundo recipiente:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. Pare a colocação do módulo. No recurso IoT Hub para o seu dispositivo:
    1. Aceda à **colocação automática de dispositivos > IoT Edge**. Selecione o dispositivo IoT Edge correspondente ao seu dispositivo.

    1. Vá para **definir módulos** e selecione um módulo. 

        ![Selecione o módulo de definição.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. No **separador Módulos,** selecione um módulo.
    
        ![Selecione um módulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  No **separador Definições** do Módulo, defina o **estado do Desired** para parar. Selecione **Atualizar**.

        ![Modificar as definições do módulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. Repita os passos para parar o segundo módulo implantado no dispositivo. Selecione **Rever + criar** e, em seguida, selecione **Criar**. Isto deve atualizar a implementação.

        ![Reveja e crie uma implementação atualizada.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. Atualizar a página **de módulos de conjunto** várias vezes. até que o **estado do tempo de execução** do módulo apareça como **Stop**.

        ![Verifique o estado de implantação.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>Implementar com partilha de contexto

Pode agora colocar a simulação n-body em dois recipientes CUDA quando o MPS estiver em funcionamento no seu dispositivo. Primeiro, ativará o MPS no dispositivo.


1. [Ligue-se à interface PowerShell do seu dispositivo.](azure-stack-edge-gpu-connect-powershell-interface.md)

1. Para ativar o MPS no seu dispositivo, executar o `Start-HcsGpuMPS` comando.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Obtenha a saída SMI da Nvidia da interface PowerShell do dispositivo. Pode ver o `nvidia-cuda-mps-server` processo ou o serviço MPS está em funcionamento no dispositivo.

    Aqui está uma saída de exemplo:

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. Desdobre os módulos que parou mais cedo. Desacorda o **estado desejado** para ser funcionado através **de módulos definidos**.

    Aqui está a saída do exemplo:

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    Pode ver que os módulos estão implantados e em funcionamento no seu dispositivo.

1. Quando os módulos são implantados, a simulação n-body também começa a funcionar em ambos os recipientes. Aqui está a saída do exemplo quando a simulação tiver terminado no primeiro recipiente:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    Aqui está a saída do exemplo quando a simulação tiver terminado no segundo recipiente:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. Obtenha a saída Smi Nvidia da interface PowerShell do dispositivo quando ambos os recipientes estiverem a executar a simulação n-body. Aqui está uma saída de exemplo. Existem três processos, o `nvidia-cuda-mps-server` processo (tipo C) corresponde ao serviço MPS e os `/tmp/nbody` processos (tipo M + C) correspondem às cargas de trabalho n-body implementadas pelos módulos. 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>Passos seguintes

- [Implemente uma carga de trabalho de GPU Kubernetes partilhada no seu Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
