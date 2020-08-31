---
title: Conecte-se e gere o dispositivo Microsoft Azure Stack Edge através da interface Windows PowerShell Microsoft Docs
description: Descreve como ligar e, em seguida, gerir o Azure Stack Edge através da interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 26d4476217d5a6f6abea6093f453cd52d654911e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086157"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>Gerir um dispositivo GPU Azure Stack Edge via Windows PowerShell

A solução Azure Stack Edge permite-lhe processar dados e enviá-lo pela rede para a Azure. Este artigo descreve algumas das tarefas de configuração e gestão do seu dispositivo Azure Stack Edge. Pode utilizar o portal Azure, a web UI local ou a interface Windows PowerShell para gerir o seu dispositivo.

Este artigo foca-se na forma como pode ligar-se à interface PowerShell do dispositivo e às tarefas que pode fazer utilizando esta interface. 


## <a name="connect-to-the-powershell-interface"></a>Ligue-se à interface PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Também pode carregar certificados IoT Edge para permitir uma ligação segura entre o seu dispositivo IoT Edge e os dispositivos a jusante que podem ligar-se ao mesmo. Existem três certificados IoT Edge (formato *.pem)* que precisa de instalar:

- Certificado de CA raiz ou o proprietário CA
- Certificado ca dispositivo
- Certificado chave do dispositivo

O exemplo a seguir mostra a utilização deste cmdlet para instalar certificados IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Quando executar este cmdlet, será solicitado que forneça a palavra-passe para a partilha da rede.

Para obter mais informações sobre certificados, aceda aos [certificados Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) ou [instale certificados num gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

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

Um Serviço Multi-Processo (MPS) em GPUs da Nvidia fornece um mecanismo onde as GPUs podem ser partilhadas por múltiplos empregos, onde cada trabalho é atribuído em alguma percentagem dos recursos da GPU. Para ativar o MPS no seu dispositivo Azure Stack Edge, siga estes passos:

1. Antes de começar, certifique-se: 

    1. Configuraste e [Ativaste o teu dispositivo Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md) com um recurso Azure Stack Edge/Data Box Gateway em Azure.
    1. [Configurou o cálculo deste dispositivo no portal Azure.](azure-stack-edge-deploy-configure-compute.md#configure-compute)
    
1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
1. Utilize o seguinte comando para ativar MPS no seu dispositivo.

    ```powershell
    Start-HcsGpuMPS
    ```

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



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Questões de Debug Kubernetes relacionadas com ioT Edge

Quando o cluster Kubernetes é criado, um utilizador padrão `aseuser` associado a um espaço de nome do sistema também é `iotedge` criado. Para depurar quaisquer problemas relacionados com o IoT Edge, pode utilizar este espaço de nomes de utilizador e sistema.  

### <a name="create-config-file-for-system-namespace"></a>Criar ficheiro config para o espaço de nome do sistema

Para resolver problemas, crie primeiro o ficheiro correspondente ao espaço com `config` `iotedge` nomes com `aseuser` .

Executar o `Get-HcsKubernetesUserConfig -AseUser` comando e guardar a saída como ficheiro `config` (sem extensão de ficheiro). Guarde o ficheiro na `.kube` pasta do seu perfil de utilizador na máquina local.

Segue-se a saída da amostra do `Get-HcsKubernetesUserConfig` comando.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```

Num dispositivo Azure Stack Edge que tem a função de computação configurada, todos os `kubectl` comandos estão disponíveis para monitorizar ou resolver problemas. Para ver uma lista de comandos disponíveis, corra `kubectl --help` a partir da janela de comando.

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

### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Para obter IP de serviço ou módulo exposto fora do cluster Kubernetes

Para obter o IP de um serviço de equilíbrio de carga ou módulos expostos fora dos Kubernetes, executar o seguinte comando:

`kubectl get svc -n iotedge`

Segue-se uma saída de amostra de todos os serviços ou módulos expostos fora do cluster Kubernetes. 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
O endereço IP na coluna IP externa corresponde ao ponto final externo para o serviço ou para o módulo. Também pode [obter o IP externo no painel kubernetes.](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)


### <a name="to-check-if-module-deployed-successfully"></a>Para verificar se o módulo foi implantado com sucesso

Os módulos computacional são contentores que têm uma lógica de negócio implementada. Uma cápsula Kubernetes pode ter vários contentores em funcionamento. Para verificar se um módulo de computação é implantado com sucesso, verifique `get pods` se o recipiente (correspondente ao módulo de computação) está em funcionamento.

Para obter a lista de todas as cápsulas em execução num espaço de nome específico, executar o seguinte comando:

`get pods -n <namespace>`

Segue-se uma saída de amostra de todas as cápsulas que correm no `iotedge` espaço de nomes.

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

O **estado indica** que todas as cápsulas do espaço de nome estão em funcionamento e o **Ready** indica o número de contentores implantados numa vagem. Na amostra anterior, todas as cápsulas estão em funcionamento e todos os módulos implantados em cada uma das cápsulas estão em funcionamento. 

Para uma saída mais verbosa de uma cápsula específica para um determinado espaço de nome, pode executar o seguinte comando:

`kubectl describe pod <pod name> -n <namespace>` 

A saída da amostra é mostrada aqui.

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
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


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>Para obter troncos de contentores

Para obter os registos de um módulo, execute o seguinte comando:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Como `all-containers` a bandeira vai despejar todos os troncos para todos os contentores, uma boa maneira de ver os erros recentes é usar a opção. `--tail 10`

Segue-se uma saída de amostra. 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
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

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>Sair da sessão remota

Para sair da sessão remota powerShell, feche a janela PowerShell.

## <a name="next-steps"></a>Passos seguintes

- Implementar [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) no portal Azure.
