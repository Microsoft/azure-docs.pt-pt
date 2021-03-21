---
title: Ligue e gere o dispositivo GPU do Microsoft Azure Stack Edge Pro através da interface Windows PowerShell | Microsoft Docs
description: Descreve como ligar e, em seguida, gerir o GPU Azure Stack Edge Pro através da interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 580e5aab7b7ac1edcfee58345291afcb9eb0e977
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562166"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Gerir um dispositivo GPU Azure Stack Edge Pro via Windows PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A solução Azure Stack Edge Pro permite-lhe processar dados e enviá-lo pela rede para a Azure. Este artigo descreve algumas das tarefas de configuração e gestão do seu dispositivo Azure Stack Edge Pro. Pode utilizar o portal Azure, a web UI local ou a interface Windows PowerShell para gerir o seu dispositivo.

Este artigo foca-se na forma como pode ligar-se à interface PowerShell do dispositivo e às tarefas que pode fazer utilizando esta interface. 


## <a name="connect-to-the-powershell-interface"></a>Ligue-se à interface do PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]


## <a name="view-device-information"></a>Ver informações do dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Ver informações do condutor da GPU

Se a função de cálculo estiver configurada no seu dispositivo, também pode obter a informação do controlador GPU através da interface PowerShell. 

1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Use o `Get-HcsGpuNvidiaSmi` para obter a informação do condutor da GPU para o seu dispositivo.

    O exemplo a seguir mostra a utilização deste cmdlet:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Tome nota das informações do condutor a partir da saída da amostra deste cmdlet.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Ativar o serviço multi-processo (MPS)

Um Serviço Multi-Processo (MPS) em GPUs da Nvidia fornece um mecanismo onde as GPUs podem ser partilhadas por múltiplos empregos, onde cada trabalho é atribuído em alguma percentagem dos recursos da GPU. MPS é uma funcionalidade de pré-visualização no seu dispositivo GPU Azure Stack Edge Pro. Para ativar o MPS no seu dispositivo, siga estes passos:

[!INCLUDE [Enable MPS](../../includes/azure-stack-edge-gateway-enable-mps.md)]


## <a name="reset-your-device"></a>Repor o dispositivo

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Obtenha registos de cálculo

Se a função de cálculo estiver configurada no seu dispositivo, também pode obter os registos computacional através da interface PowerShell.

1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Utilize o `Get-AzureDataBoxEdgeComputeRoleLogs` para obter os registos computacional do seu dispositivo.

    O exemplo a seguir mostra a utilização deste cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Aqui está uma descrição dos parâmetros utilizados para o cmdlet:
    - `Path`: Providenciar um caminho de rede para a partilha onde pretende criar o pacote de registo de cálculo.
    - `Credential`: Forneça o nome de utilizador para a partilha da rede. Quando executar este cmdlet, terá de fornecer a senha de partilha.
    - `FullLogCollection`: Este parâmetro garante que o pacote de registos conterá todos os registos computatórios. Por predefinição, o pacote de registo contém apenas um subconjunto de registos.


## <a name="change-kubernetes-pod-and-service-subnets"></a>Alterar sub-redes de serviço e pod do Kubernetes

Por predefinição, os Kubernetes no seu dispositivo Azure Stack Edge utilizam sub-redes 172.27.0.0/16 e 172.28.0.0/16 para pod e serviço, respectivamente. Se estas sub-redes já estiverem a ser utilizadas na sua rede, então pode executar o `Set-HcsKubeClusterNetworkInfo` cmdlet para alterar estas sub-redes.

Pretende executar esta configuração antes de configurar o computação a partir do portal Azure, uma vez que o cluster Kubernetes é criado neste passo.

1. [Ligue-se à interface PowerShell do dispositivo](#connect-to-the-powershell-interface).
1. A partir da interface PowerShell do dispositivo, executar:

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    Substitua-a <subnet details> por uma gama de sub-redes que pretende utilizar. 

1. Uma vez executado este comando, pode utilizar o `Get-HcsKubeClusterNetworkInfo` comando para verificar se as sub-redes de pod e serviço mudaram.

Aqui está uma amostra de saída para este comando.

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```

## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Questões de Debug Kubernetes relacionadas com ioT Edge

Antes de começar, deve ter:

- Rede de cálculo configurada. Ver [Tutorial: Configurar rede para Azure Stack Edge Pro com GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
- Papel de cálculo configurado no seu dispositivo.
    
Num dispositivo Azure Stack Edge Pro que tenha a função de computação configurada, pode resolver problemas ou monitorizar o dispositivo utilizando dois conjuntos diferentes de comandos.

- Usando `iotedge` comandos. Estes comandos estão disponíveis para operações básicas para o seu dispositivo.
- Usando `kubectl` comandos. Estes comandos estão disponíveis para um vasto conjunto de operações para o seu dispositivo.

Para executar qualquer um dos comandos acima, é necessário ligar à [interface PowerShell](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Use `iotedge` comandos

Para ver uma lista de comandos disponíveis, [ligue-se à interface PowerShell](#connect-to-the-powershell-interface) e utilize a `iotedge` função.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

A tabela a seguir tem uma breve descrição dos comandos disponíveis `iotedge` para:

|command  |Description |
|---------|---------|
|`list`     | Listar módulos         |
|`logs`     | Pegue os troncos de um módulo        |
|`restart`     | Parar e reiniciar um módulo         |

#### <a name="list-all-iot-edge-modules"></a>Listar todos os módulos IoT Edge

Para listar todos os módulos em execução no seu dispositivo, utilize o `iotedge list` comando.

Aqui está uma amostra deste comando. Este comando lista todos os módulos, configuração associada e os IPs externos associados aos módulos. Por exemplo, pode aceder à aplicação **webserver** em `https://10.128.44.244` . 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```
#### <a name="restart-modules"></a>Reiniciar módulos

Pode utilizar o `list` comando para listar todos os módulos em execução no seu dispositivo. Em seguida, identifique o nome do módulo que pretende reiniciar e usá-lo com o `restart` comando.

Aqui está uma amostra de como reiniciar um módulo. Com base na descrição de quanto tempo o módulo está a funcionar, pode ver que `cuda-sample1` foi reiniciado.

```powershell
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  ----------- ------                                          ----------- -------
edgehub      Running Up 5 days   mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:308
                                                                                             81/TCP,8883:31753/TCP
iotedged     Running Up 7 days   azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days   nvidia/samples:nbody
edgeagent    Running Up 7 days   azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 1 days   nvidia/samples:nbody

[10.100.10.10]: PS>iotedge restart cuda-sample1
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION  CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  -----------  ------                                          ----------- -------
edgehub      Running Up 5 days    mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:30
                                                                                              881/TCP,8883:31753/TC
                                                                                              P
iotedged     Running Up 7 days    azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days    nvidia/samples:nbody
edgeagent    Running Up 7 days    azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 4 minutes nvidia/samples:nbody

[10.100.10.10]: PS>

```

#### <a name="get-module-logs"></a>Obtenha registos de módulos

Utilize o `logs` comando para obter registos de qualquer módulo IoT Edge em funcionamento no seu dispositivo. 

Se houve um erro na criação da imagem do recipiente ou ao puxar a imagem, corra `logs edgeagent` . `edgeagent` é o recipiente de tempo de execução IoT Edge responsável pelo provisionamento de outros contentores. Porque `logs edgeagent` despeja todos os registos, uma boa maneira de ver os erros recentes é usar a opção `--tail ` 0'. 

Aqui está uma amostra de saída.

```powershell
[10.100.10.10]: PS>iotedge logs cuda-sample2 --tail 10
[10.100.10.10]: PS>iotedge logs edgeagent --tail 10
<6> 2021-02-25 00:52:54.828 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:52:54.829 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Plan execution ended for deployment 11
[10.100.10.10]: PS>
```

### <a name="use-kubectl-commands"></a>Use comandos kubectl

Num dispositivo Azure Stack Edge Pro que tem a função de computação configurada, todos os `kubectl` comandos estão disponíveis para monitorizar ou resolver problemas. Para ver uma lista de comandos disponíveis, corra `kubectl --help` a partir da janela de comando.

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Para obter uma lista completa dos `kubectl` comandos, vá para a [ `kubectl` batoteira.](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Para obter IP de serviço ou módulo exposto fora do cluster Kubernetes

Para obter o IP de um serviço de equilíbrio de carga ou módulos expostos fora dos Kubernetes, executar o seguinte comando:

`kubectl get svc -n iotedge`

Segue-se uma saída de amostra de todos os serviços ou módulos expostos fora do cluster Kubernetes. 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
O endereço IP na coluna IP externa corresponde ao ponto final externo para o serviço ou para o módulo. Também pode [obter o IP externo no painel kubernetes.](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)


#### <a name="to-check-if-module-deployed-successfully"></a>Para verificar se o módulo foi implantado com sucesso

Os módulos computacional são contentores que têm uma lógica de negócio implementada. Uma cápsula Kubernetes pode ter vários contentores em funcionamento. 

Para verificar se um módulo computacional é implantado com sucesso, ligue-se à interface PowerShell do dispositivo.
Executar o `get pods` comando e verificar se o recipiente (correspondente ao módulo de computação) está em funcionamento.

Para obter a lista de todas as cápsulas em execução num espaço de nome específico, executar o seguinte comando:

`get pods -n <namespace>`

Para verificar os módulos implantados via IoT Edge, execute o seguinte comando:

`get pods -n iotedge`

Segue-se uma saída de amostra de todas as cápsulas que correm no `iotedge` espaço de nomes.

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

O **estado indica** que todas as cápsulas do espaço de nome estão em funcionamento e o **Ready** indica o número de contentores implantados numa vagem. Na amostra anterior, todas as cápsulas estão em funcionamento e todos os módulos implantados em cada uma das cápsulas estão em funcionamento. 

Para verificar os módulos implantados através do Arco Azure, execute o seguinte comando:

`get pods -n azure-arc`

Em alternativa, pode [ligar ao painel de instrumentos de Kubernetes para ver as implementações do IoT Edge ou do Arco Azure.](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status)


Para uma saída mais verbosa de uma cápsula específica para um determinado espaço de nome, pode executar o seguinte comando:

`kubectl describe pod <pod name> -n <namespace>` 

A saída da amostra é mostrada aqui.

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>Para obter troncos de contentores

Para obter os registos de um módulo, executar o seguinte comando a partir da interface PowerShell do dispositivo:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Como `all-containers` a bandeira despeja todos os troncos para todos os contentores, uma boa maneira de ver os erros recentes é usar a opção. `--tail 10`

Segue-se uma saída de amostra. 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

### <a name="change-memory-processor-limits-for-kubernetes-worker-node"></a>Alterar a memória, os limites do processador para o nó de trabalhador kubernetes

Para alterar os limites de memória ou processador do nó de trabalhador kubernetes, faça os seguintes passos:

1. [Ligue-se à interface PowerShell do dispositivo](#connect-to-the-powershell-interface).
1. Para obter os recursos atuais para o nó do trabalhador e as opções de função, executar o seguinte comando:

    `Get-AzureDataBoxEdgeRole`

    Aqui está uma amostra de saída. Note os valores para `Name` e `Compute` `Resources` abaixo da secção. `MemoryInBytes` e `ProcessorCount` denotar os valores atualmente atribuídos a memória e a contagem de processador para o nó de trabalhador kubernetes.  

    ```powershell
    [10.100.10.10]: PS>Get-AzureDataBoxEdgeRole
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:12
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True    
    [10.100.10.10]: PS>
    ```
    
1. Para alterar os valores da memória e dos processadores para o nó do trabalhador, executar o seguinte comando:

    Set-AzureDataBoxEdgeRoleCompute -Nome <Name value from the output of Get-AzureDataBoxEdgeRole> -Memória <Value in Bytes> - <Do. de núcleos>

    Aqui está uma amostra de saída. 
    
    ```powershell
    [10.100.10.10]: PS>Set-AzureDataBoxEdgeRoleCompute -Name IotRole -MemoryInBytes 32GB -ProcessorCount 16
    
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:16
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
    
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True
    
    [10.100.10.10]: PS>    
    ```

Ao alterar a utilização da memória e do processador, siga estas diretrizes.

- A memória predefinida é de 25% da especificação do dispositivo.
- A contagem de processadores predefinidos é de 30% da especificação do dispositivo.
- Ao alterar os valores da contagem de memória e processador, recomendamos que varie os valores entre 15% e 60% da memória do dispositivo e a contagem do processador. 
- Recomendamos que um limite superior de 60% seja para que haja recursos suficientes para os componentes do sistema. 

## <a name="connect-to-bmc"></a>Ligar ao BMC

O controlador de gestão do rodapé (BMC) é utilizado para monitorizar e gerir remotamente o seu dispositivo. Esta secção descreve os cmdlets que podem ser utilizados para gerir a configuração do BMC. Antes de executar qualquer um destes cmdlets, [Ligue-se à interface PowerShell do dispositivo](#connect-to-the-powershell-interface).

- `Get-HcsNetBmcInterface`: Utilize este cmdlet para obter as propriedades de configuração de rede do BMC, por exemplo, `IPv4Address` , , . . . `IPv4Gateway` `IPv4SubnetMask` `DhcpEnabled` . 
    
    Eis uma saída de exemplo:
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`: Pode utilizar este cmdlet de duas formas.

    - Utilize o cmdlet para ativar ou desativar a configuração dhcp para BMC utilizando o valor adequado para o `UseDhcp` parâmetro. 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        Eis uma saída de exemplo: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - Utilize este cmdlet para configurar a configuração estática para o BMC. Pode especificar os valores para `IPv4Address` `IPv4Gateway` , e `IPv4SubnetMask` . 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>" -IPv4Gateway "<IPv4 address of the gateway>" -IPv4SubnetMask "<IPv4 address for the subnet mask>"
        ```        
        
        Eis uma saída de exemplo: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`: Utilize este cmdlet para modificar a palavra-passe BMC para `EdgeUser` . O nome de utilizador `EdgeUser` é sensível a casos.

    Eis uma saída de exemplo: 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>Sair da sessão remota

Para sair da sessão remota powerShell, feche a janela PowerShell.

## <a name="next-steps"></a>Passos seguintes

- Implementar [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) no portal Azure.